---
title: Autenticar solicitações – Serviço de Bot
description: Saiba como autenticar solicitações de API na API Bot Connector e API Bot State.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 60a246d60f3b74b037f793a306d58df1a5398141
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790784"
---
# <a name="authentication"></a>Autenticação

O bot comunica-se com o serviço do Bot Connector usando HTTP em um canal seguro (SSL/TLS). Quando o bot envia uma solicitação ao serviço do Connector, ele deve incluir informações que o serviço do Connector pode usar para verificar a identidade do bot. Da mesma forma, quando o serviço do Connector envia uma solicitação ao bot, ele deve incluir informações que o bot pode usar para verificar a identidade do serviço. Este artigo descreve as tecnologias de autenticação e os requisitos para a autenticação de nível de serviço que ocorre entre um bot e o serviço do Bot Connector. Se você estiver escrevendo seu próprio código de autenticação, é necessário implementar os procedimentos de segurança descritos neste artigo para permitir ao bot trocar mensagens com o serviço do Bot Connector.

> [!IMPORTANT]
> Se você estiver escrevendo seu próprio código de autenticação, é crítico implementar corretamente todos os procedimentos de segurança. Ao implementar todas as etapas neste artigo, é possível reduzir o risco de um invasor conseguir ler as mensagens enviadas ao bot, enviar mensagens que representem o bot e roubar chaves secretas. 

Caso esteja usando o [SDK do Bot Framework para .NET](../dotnet/bot-builder-dotnet-overview.md) ou o [SDK do Bot Framework para Node.js](../nodejs/index.md), não é necessário implementar os procedimentos de segurança descritos neste artigo porque o SDK faz isso automaticamente para você. Basta configurar o projeto com a ID do aplicativo e a senha que você obteve para o bot durante o [registro](../bot-service-quickstart-registration.md) e o SDK cuidará do restante.

> [!WARNING]
> Em dezembro de 2016, a v3.1 do protocolo de segurança do Bot Framework introduziu alterações em diversos valores que são usados durante a geração e validação de token. No fim de 2017, a v3.2 do protocolo de segurança do Bot Framework foi apresentada e incluiu alterações em valores usados durante a geração e validação de token.
> Para obter mais informações, consulte [Alterações do protocolo de segurança](#security-protocol-changes).

## <a name="authentication-technologies"></a>Tecnologias de autenticação

Quatro tecnologias de autenticação são usadas para estabelecer a relação de confiança entre um bot e o Bot Connector:

| Tecnologia | Descrição |
|----|----|
| **SSL/TLS** | SSL/TLS é usada para todas as conexões de serviço a serviço. Certificados `X.509v3` são usados para estabelecer a identidade de todos os serviços HTTPS. **Os clientes sempre devem inspecionar certificados de serviço para garantir que eles sejam confiáveis e válidos.** (Certificados de cliente NÃO são usados como parte desse esquema.) |
| **OAuth 2.0** | O OAuth 2.0 usa o serviço de logon da conta do Azure AD (Azure Active Directory) v2 para gerar um token seguro que um bot pode usar para enviar mensagens. Esse token é um token de serviço a serviço; nenhum logon de usuário é exigido. |
| **JWT (Token Web JSON)** | Tokens Web JSON são usados para codificar os tokens que são enviados para e do bot. **Os clientes totalmente devem verificar integralmente todos os tokens JWT que recebem**, de acordo com os requisitos descritos neste artigo. |
| **Metadados do OpenID** | O serviço do Bot Connector publica uma lista de tokens válidos que ele usa para assinar seus próprios tokens JWT para metadados do OpenID em um ponto de extremidade estático bem conhecido. |

Este artigo descreve como usar essas tecnologias por meio de JSON e HTTPS padrão. Nenhum SDK especial é necessário, embora você possa achar que os auxiliares para OpenID etc. sejam úteis.

## <a name="authenticate-requests-from-your-bot-to-the-bot-connector-service"></a><a id="bot-to-connector"></a> Autenticar solicitações do bot para o serviço do Bot Connector

Para comunicar-se com o serviço do Bot Connector, é necessário especificar um token de acesso no cabeçalho `Authorization` de cada solicitação de API, usando este formato: 

```http
Authorization: Bearer ACCESS_TOKEN
```

Este diagrama mostra as etapas para a autenticação de bot ao conector:

![Autenticar para o serviço de logon da MSA e depois para o bot](../media/connector/auth_bot_to_bot_connector.png)

### <a name="step-1-request-an-access-token-from-the-azure-ad-v2-account-login-service"></a>Etapa 1: Solicitar um token de acesso do serviço de logon da conta do Azure AD v2

> [!IMPORTANT]
> Se você ainda não fez isso, é necessário [registrar seu bot](../bot-service-quickstart-registration.md) com o Bot Framework para obter sua AppID e a senha. Para solicitar um token de acesso, são necessárias a ID do aplicativo e a senha do bot.

Para solicitar um token de acesso do serviço de logon, emita a seguinte solicitação, substituindo **MICROSOFT-APP-ID** e **MICROSOFT-APP-PASSWORD** pela AppID e a senha do bot, as quais você obteve ao [registrar](../bot-service-quickstart-registration.md) o bot com o Bot Framework.

```http
POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=MICROSOFT-APP-ID&client_secret=MICROSOFT-APP-PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default
```

### <a name="step-2-obtain-the-jwt-token-from-the-the-azure-ad-v2-account-login-service-response"></a>Etapa 2: Obter o token JWT da resposta do serviço de logon da conta do Azure AD v2

Se o aplicativo estiver autorizado pelo serviço de logon, o corpo da resposta JSON especificará o token de acesso, seu tipo e sua validade (em segundos).

Ao adicionar o token ao cabeçalho `Authorization` de uma solicitação, você deve usar o valor exato especificado nesta resposta (ou seja, não escape ou codifique o valor do token). O token de acesso é válido até que ele expire. Para impedir que a expiração do token afete o desempenho do bot, é possível optar por armazenar em cache e atualizar proativamente o token.

Este exemplo mostra uma resposta do serviço de logon da conta do Azure AD v2:

```http
HTTP/1.1 200 OK
... (other headers)
```

```json
{
    "token_type":"Bearer",
    "expires_in":3600,
    "ext_expires_in":3600,
    "access_token":"eyJhbGciOiJIUzI1Ni..."
}
```

### <a name="step-3-specify-the-jwt-token-in-the-authorization-header-of-requests"></a>Etapa 3: especificar o token JWT no cabeçalho de autorização das solicitações

Ao enviar uma solicitação de API ao serviço do Bot Connector, especifique o token de acesso no cabeçalho `Authorization` da solicitação usando este formato:

```http
Authorization: Bearer ACCESS_TOKEN
```

Todas as solicitações que você enviar ao serviço do Bot Connector devem incluir o token de acesso no cabeçalho `Authorization`.
Se o token for formado corretamente, não estiver expirado e tiver sido gerado pelo serviço de logon da conta do Azure AD v2, o serviço do Bot Connector autorizará a solicitação. Verificações adicionais são realizadas para garantir que o token pertence ao bot que enviou a solicitação.

O exemplo a seguir mostra como especificar o token de acesso no cabeçalho `Authorization` da solicitação. 

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/12345/activities 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
    
(JSON-serialized Activity message goes here)
```

> [!IMPORTANT]
> Especifique apenas o token JWT no cabeçalho `Authorization` das solicitações que você enviar para o serviço do Bot Connector.
> Não envie o token por canais não seguros e NÃO o inclua em solicitações HTTP enviadas a outros serviços.
> O token JWT que você obtém do serviço de logon da conta do Azure AD v2 é como uma senha e deve ser manipulado com bastante cuidado. Qualquer pessoa que possua o token pode usá-lo para executar operações em nome do seu bot.

#### <a name="bot-to-connector-example-jwt-components"></a>Bot ao Conector: componentes JWT de exemplo

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "https://api.botframework.com",
  iss: "https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/",
  nbf: 1481049243,
  exp: 1481053143,
  appid: "<YOUR MICROSOFT APP ID>",
  ... other fields follow
}
```

> [!NOTE]
> Os campos reais podem variar na prática. Crie e valide todos os tokens JWT conforme especificado acima.

## <a name="authenticate-requests-from-the-bot-connector-service-to-your-bot"></a><a id="connector-to-bot"></a> Autenticar solicitações do serviço do Bot Connector para o bot

Quando o serviço do Bot Connector envia uma solicitação ao bot, ele especifica um token JWT assinado no cabeçalho `Authorization` da solicitação. O bot pode autenticar chamadas de serviço do Bot Connector, verificando a autenticidade do token JWT assinado. 

Este diagrama mostra as etapas para a autenticação de bot ao conector:

![Autenticar chamadas do Bot Connector ao bot](../media/connector/auth_bot_connector_to_bot.png)

### <a name="step-2-get-the-openid-metadata-document"></a><a id="openid-metadata-document"></a> Etapa 2: obter o documento de metadados do OpenID

O documento de metadados do OpenID especifica o local de um segundo documento que lista as chaves de assinatura válidas do serviço do Bot Connector. Para obter o documento de metadados do OpenID, emita esta solicitação por meio de HTTPS:

```http
GET https://login.botframework.com/v1/.well-known/openidconfiguration
```

> [!TIP]
> Essa é uma URL estática que você pode codificar no aplicativo. 

O exemplo a seguir mostra um documento de metadados do OpenID que é retornado na resposta à solicitação `GET`. A propriedade `jwks_uri` especifica o local do documento que contém as chaves de assinatura válidas do serviço do Bot Connector.

```json
{
    "issuer": "https://api.botframework.com",
    "authorization_endpoint": "https://invalid.botframework.com",
    "jwks_uri": "https://login.botframework.com/v1/.well-known/keys",
    "id_token_signing_alg_values_supported": [
      "RS256"
    ],
    "token_endpoint_auth_methods_supported": [
      "private_key_jwt"
    ]
}
```

### <a name="step-3-get-the-list-of-valid-signing-keys"></a><a id="connector-to-bot-step-3"></a> Etapa 3: obter a lista de chaves de assinatura válidas

Para obter uma lista das chaves de assinatura válidas, emita uma solicitação `GET` por meio de HTTPS para a URL especificada pela propriedade `jwks_uri` no documento de metadados do OpenID. Por exemplo:

```http
GET https://login.botframework.com/v1/.well-known/keys
```

O corpo da resposta especifica o documento no [formato JWK](https://tools.ietf.org/html/rfc7517), mas também inclui uma propriedade adicional para cada chave: `endorsements`. A lista das chaves é relativamente estável e pode ser armazenada em cache durante longos períodos (por padrão, 5 dias dentro do SDK do Bot Framework).

A propriedade `endorsements` dentro de cada chave contém uma ou mais cadeias de caracteres de endosso que você pode usar para verificar se a ID de canal especificada na propriedade `channelId` dentro do objeto de [Atividade][] da solicitação de entrada é autêntica. A lista de IDs de canal que exigem endossos é configurável em cada bot. Por padrão, essa será a lista de todas as IDs de canal publicadas, embora os desenvolvedores de bots possam substituir os valores da ID de canal selecionados de qualquer forma. 

### <a name="step-4-verify-the-jwt-token"></a>Etapa 4: verificar o token JWT

Para verificar a autenticidade do token que foi enviado pelo serviço do Bot Connector, você deve extrair o token do cabeçalho `Authorization` da solicitação, analisar o token, verificar seu conteúdo e verificar a assinatura. 

Bibliotecas de análise de JWT estão disponíveis para várias plataformas e a maioria implementa análise segura e confiável para tokens JWT, embora normalmente seja necessário configurar essas bibliotecas para exigir que certas características do token (seu emissor, audiência, etc.) contenham valores corretos. Ao analisar o token, é necessário configurar a biblioteca de análise ou gravar sua própria validação para garantir que o token atenda a esses requisitos:

1. O token foi enviado no cabeçalho `Authorization` HTTP com o esquema de "Portador".
2. O token é um JSON válido que está em conformidade com o [padrão JWT](http://openid.net/specs/draft-jones-json-web-token-07.html).
3. O token contém uma declaração "emissor" com valor de `https://api.botframework.com`.
4. O token contém uma declaração "audiência" com um valor igual à ID de Aplicativo da Microsoft do bot.
5. O token está dentro do período de validade. A defasagem horária padrão do setor é de 5 minutos.
6. O token tem uma assinatura de criptografia válida, com uma chave listada no documento de chaves do OpenID que foi recuperado na [Etapa 3](#connector-to-bot-step-3), usando o algoritmo de assinatura especificado na propriedade `id_token_signing_alg_values_supported` do documento de metadados do Open ID que foi recuperado [Etapa 2](#openid-metadata-document).
7. O token contém uma declaração "serviceUrl" com um valor que corresponde à propriedade `servieUrl` na raiz do objeto de [Atividade][] da solicitação de entrada. 

Se o endosso para uma ID de canal for necessário:

- É necessário exigir que qualquer objeto `Activity` enviado ao bot com essa ID de canal seja acompanhado por um token JWT assinado com um endosso para esse canal. 
- Se o endosso não estiver presente, o bot deve rejeitar a solicitação retornando um código de status **HTTP 403 (proibido)** .

> [!IMPORTANT]
> Todos esses requisitos são importantes, especialmente os requisitos 4 e 6. A não implementação desses requisitos de verificação deixará o bot aberto a ataques que podem fazer o bot divulgar seu token JWT.

Os implementadores não devem expor uma maneira de desabilitar a validação do token JWT que é enviada ao bot.

#### <a name="connector-to-bot-example-jwt-components"></a>Conector ao Bot: componentes JWT de exemplo

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "<YOU MICROSOFT APP ID>",
  iss: "https://api.botframework.com",
  nbf: 1481049243,
  exp: 1481053143,
  ... other fields follow
}
```

> [!NOTE]
> Os campos reais podem variar na prática. Crie e valide todos os tokens JWT conforme especificado acima.

## <a name="authenticate-requests-from-the-bot-framework-emulator-to-your-bot"></a><a id="emulator-to-bot"></a> Autenticar solicitações do serviço do Bot Framework Emulator para o bot

> [!WARNING]
> No fim de 2017, a v3.2 do protocolo de segurança do Bot Framework será introduzida. Essa nova versão inclui um novo valor de "emissor" dentro de tokens que são trocados entre o Bot Framework Eumaltor e o bot. Para se preparar para essa alteração, as etapas a seguir descrevem como verificar os valores de emissor da v 3.1 e v3.2. 

O [Bot Framework Emulator](../bot-service-debug-emulator.md) é uma ferramenta da área de trabalho que você pode usar para testar a funcionalidade do bot. Embora o Bot Framework Emulator use as mesmas [tecnologias de autenticação](#authentication-technologies) descritas acima, não é possível representar o serviço do Bot Connector real. Em vez disso, ele usa a ID de Aplicativo da Microsoft e a Senha de Aplicativo da Microsoft que você especifica ao conectar o emulador ao bot para criar tokens que são idênticos aos criados pelo bot. Quando o emulador envia uma solicitação ao bot, ele especifica o token JWT no cabeçalho `Authorization` da solicitação – em essência, usando as próprias credenciais do bot para autenticar a solicitação. 

Se você estiver implementando uma biblioteca de autenticação e quer aceitar solicitações do Bot Framework Emulator, é necessário adicionar este caminho de verificação adicional. O caminho é estruturalmente semelhante ao caminho de verificação [Connector -> Bot](#connector-to-bot), mas usa o documento do OpenID da MSA, em vez do documento do OpenID do Bot Connector.

Este diagrama mostra as etapas para a autenticação de emulador ao bot:

![Autenticar chamadas do Bot Framework Emulator para o bot](../media/connector/auth_bot_framework_emulator_to_bot.png)

---
### <a name="step-2-get-the-msa-openid-metadata-document"></a>Etapa 2: obter o documento de metadados do OpenID da MSA

O documento de metadados do OpenID especifica o local de um segundo documento que lista as chaves de assinatura válidas. Para obter o documento de metadados do OpenID da MSA, emita esta solicitação por meio de HTTPS:

```http
GET https://login.microsoftonline.com/botframework.com/v2.0/.well-known/openid-configuration
```

O exemplo a seguir mostra um documento de metadados do OpenID que é retornado na resposta à solicitação `GET`. A propriedade `jwks_uri` especifica o local do documento que contém as chaves de assinatura válidas.

```json
{
    "authorization_endpoint":"https://login.microsoftonline.com/common/oauth2/v2.0/authorize",
    "token_endpoint":"https://login.microsoftonline.com/common/oauth2/v2.0/token",
    "token_endpoint_auth_methods_supported":["client_secret_post","private_key_jwt"],
    "jwks_uri":"https://login.microsoftonline.com/common/discovery/v2.0/keys",
    ...
}
```

### <a name="step-3-get-the-list-of-valid-signing-keys"></a><a id="emulator-to-bot-step-3"></a> Etapa 3: obter a lista de chaves de assinatura válidas

Para obter uma lista das chaves de assinatura válidas, emita uma solicitação `GET` por meio de HTTPS para a URL especificada pela propriedade `jwks_uri` no documento de metadados do OpenID. Por exemplo:

```http
GET https://login.microsoftonline.com/common/discovery/v2.0/keys 
Host: login.microsoftonline.com
```

O corpo da resposta especifica o documento no [formato JWK](https://tools.ietf.org/html/rfc7517). 

### <a name="step-4-verify-the-jwt-token"></a>Etapa 4: verificar o token JWT

Para verificar a autenticidade do token que foi enviado pelo emulador, você deve extrair o token do cabeçalho `Authorization` da solicitação, analisar o token, verificar seu conteúdo e verificar a assinatura. 

Bibliotecas de análise de JWT estão disponíveis para várias plataformas e a maioria implementa análise segura e confiável para tokens JWT, embora normalmente seja necessário configurar essas bibliotecas para exigir que certas características do token (seu emissor, audiência, etc.) contenham valores corretos. Ao analisar o token, é necessário configurar a biblioteca de análise ou gravar sua própria validação para garantir que o token atenda a esses requisitos:

1. O token foi enviado no cabeçalho `Authorization` HTTP com o esquema de "Portador".
2. O token é um JSON válido que está em conformidade com o [padrão JWT](http://openid.net/specs/draft-jones-json-web-token-07.html).
3. O token contém uma declaração "emissor" com valor de `https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/` ou `https://sts.windows.net/f8cdef31-a31e-4b4a-93e4-5f571e91255a/`. (Verificar ambos os valores de emissor garantirá que você está verificando o valores de emissor do protocolo de segurança v3.1 e v3.2)
4. O token contém uma declaração "audiência" com um valor igual à ID de Aplicativo da Microsoft do bot.
5. O token contém uma declaração "appid" com um valor igual à ID de Aplicativo da Microsoft do bot.
6. O token está dentro do período de validade. A defasagem horária padrão do setor é de 5 minutos.
7. O token tem uma assinatura de criptografia válida com uma chave listada no documento de chaves do OpenID que foi recuperado na [Etapa 3](#emulator-to-bot-step-3).

> [!NOTE]
> O requisito 5 é específico para o caminho de verificação do emulador. 

Se o token não atender a todos esses requisitos, o bot deve encerrar a solicitação retornando um código de status **HTTP 403 (proibido)** .

> [!IMPORTANT]
> Todos esses requisitos são importantes, especialmente os requisitos 4 e 7. A não implementação desses requisitos de verificação deixará o bot aberto a ataques que podem fazer o bot divulgar seu token JWT.

#### <a name="emulator-to-bot-example-jwt-components"></a>Emulador ao Bot: componentes JWT de exemplo

```json
header:
{
  typ: "JWT",
  alg: "RS256",
  x5t: "<SIGNING KEY ID>",
  kid: "<SIGNING KEY ID>"
},
payload:
{
  aud: "<YOUR MICROSOFT APP ID>",
  iss: "https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/",
  nbf: 1481049243,
  exp: 1481053143,
  ... other fields follow
}
```

> [!NOTE]
> Os campos reais podem variar na prática. Crie e valide todos os tokens JWT conforme especificado acima.

## <a name="security-protocol-changes"></a>Alterações do protocolo de segurança

> [!WARNING]
> O suporte para v3.0 do protocolo de segurança foi descontinuado em **31 de julho de 2017**. Se você tiver gravado seu próprio código de autenticação (ou seja, não tenha usado o SDK do Bot Framework para criar o bot), é necessário atualizar para a v3.1 do protocolo de segurança, atualizando o aplicativo para usar os valores da v3.1 que estão listados abaixo. 

### <a name="bot-to-connector-authentication"></a>[Autenticação de Bot ao Conector](#bot-to-connector)

#### <a name="oauth-login-url"></a>URL de logon do OAuth

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | `https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token` |

#### <a name="oauth-scope"></a>Escopo do OAuth

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 |  `https://api.botframework.com/.default` |

### <a name="connector-to-bot-authentication"></a>[Autenticação de Conector ao Bot](#connector-to-bot)

#### <a name="openid-metadata-document"></a>Documento de metadados do OpenID

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | `https://login.botframework.com/v1/.well-known/openidconfiguration` |

#### <a name="jwt-issuer"></a>Emissor JWT

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | `https://api.botframework.com` |

### <a name="emulator-to-bot-authentication"></a>[Autenticação de Emulador ao Bot](#emulator-to-bot)

#### <a name="oauth-login-url"></a>URL de logon do OAuth

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | `https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token` |

#### <a name="oauth-scope"></a>Escopo do OAuth

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 |  ID de Aplicativo da Microsoft do bot + `/.default` |

#### <a name="jwt-audience"></a>Audiência JWT

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | ID de Aplicativo da Microsoft do bot |

#### <a name="jwt-issuer"></a>Emissor JWT

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 | `https://sts.windows.net/d6d49420-f39b-4df7-a1dc-d59a935871db/` |
| v3.2 | `https://sts.windows.net/f8cdef31-a31e-4b4a-93e4-5f571e91255a/` |

#### <a name="openid-metadata-document"></a>Documento de metadados do OpenID

| Versão do protocolo | Valor válido |
|----|----|
| v3.1 e v3.2 | `https://login.microsoftonline.com/botframework.com/v2.0/.well-known/openid-configuration` |

## <a name="additional-resources"></a>Recursos adicionais

- [Solucionar problemas de autenticação do Bot Framework](../bot-service-troubleshoot-authentication-problems.md)
- [Esquema de atividade Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Token Web JSON (JWT) draft-jones-json-web-token-07](http://openid.net/specs/draft-jones-json-web-token-07.html)
- [Assinatura Web JSON (JWS) draft-jones-json-web-signature-04](https://tools.ietf.org/html/draft-jones-json-web-signature-04)
- [Chave Web JSON (JWK) RFC 7517](https://tools.ietf.org/html/rfc7517)

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
