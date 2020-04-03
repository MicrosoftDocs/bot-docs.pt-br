---
title: Serviço de Bot do Azure de provedores de identidade – Serviço de Bot
description: Saiba mais sobre provedores de identidade no Serviço de Bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/11/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 966e484e4179c8256522c51b7873c1cb106e47a0
ms.sourcegitcommit: 64b25f796f89e8bb6fa53d3c824b73b8ce4d6ed8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80250185"
---
# <a name="identity-providers"></a>Provedores de identidade

Um provedor de identidade autentica as identidades do usuário ou do cliente e emite tokens de segurança consumíveis. Ele fornece autenticação de usuário como um serviço.

Os aplicativos cliente, como aplicativos Web, delegam a autenticação a um provedor de identidade confiável. Esses aplicativos cliente são considerados federados, ou seja, usam identidade federada.

O uso de um provedor de identidade confiável:

- Habilita recursos de SSO (logon único), permitindo que um aplicativo acesse vários recursos protegidos.
- Facilita conexões entre usuários e recursos de computação em nuvem, diminuindo a necessidade de autenticar os usuários novamente.

## <a name="single-sign-on"></a>Logon único

O logon único refere-se a um processo de autenticação que permite que um usuário faça logon em um sistema uma vez com um único conjunto de credenciais para acessar vários aplicativos ou serviços.

Um usuário faz logon com uma única ID e senha para obter acesso a qualquer um dos vários sistemas de software relacionados. Para obter mais informações, confira [Logon único](./bot-builder-concept-sso.md).

Muitos provedores de identidade dão suporte a uma operação de saída que revoga o token do usuário e encerra o acesso aos aplicativos e serviços associados.


> [!IMPORTANT]
> O SSO aprimora a usabilidade reduzindo o número de vezes que um usuário deve inserir as credenciais. Ele também fornece maior segurança reduzindo a possível superfície de ataque.

## <a name="azure-active-directory-identity-provider"></a>Provedor de identidade do Azure Active Directory

O Azure AD (Azure Active Directory) é o serviço de identidade no Microsoft Azure que fornece gerenciamento de identidades e recursos de controle de acesso. Ele permite que você conecte usuários com segurança usando protocolos padrão do setor, como **OAuth2.0**.

Você pode escolher entre duas implementações do provedor de identidade do AD que têm configurações diferentes, conforme mostrado abaixo.

> [!Note]
> Use as configurações descritas aqui ao definir as **Configurações de Conexão OAuth** no aplicativo de registro do bot do Azure. Confira [Adicionar autenticação a um bot](bot-builder-authentication.md).

# <a name="azure-ad-v1"></a>[Azure AD v1](#tab/adv1)

### <a name="azure-ad-v1"></a>Azure AD v1

Use as configurações mostradas para configurar a plataforma do desenvolvedor do Azure AD (v1.0), também conhecida como ponto de extremidade do **Azure AD v1,**   que permite criar aplicativos que conectam usuários com segurança a uma conta corporativa ou de estudante da Microsoft.
Para obter mais informações, confira a [visão geral do Azure Active Directory para desenvolvedores (v1.0)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-overview).

[!INCLUDE [azure-ad-v1-settings](~/includes/authentication/auth-aad-v1-settings.md)]

# <a name="azure-ad-v2"></a>[Azure AD v2](#tab/adv2)

### <a name="azure-ad-v2"></a>Azure AD v2

Use as configurações mostradas para configurar a plataforma de identidade da Microsoft (v2.0), também conhecida como ponto de extremidade do **Azure AD v2,**  que é uma evolução da plataforma do Azure AD (v1.0).  Ela permite que um bot obtenha tokens para chamar APIs da Microsoft, como APIs do Microsoft Graph ou personalizadas. 
Para obter mais informações, confira a [visão geral da plataforma de identidade da Microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

As configurações do AD v2 permitem que um bot acesse dados do Office 365 por meio da API do Microsoft Graph.

[!INCLUDE [azure-ad-v2-settings](~/includes/authentication/auth-aad-v2-settings.md)]

---

Confira também

- [Por que atualizar para a plataforma de identidade da Microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare)

- [Plataforma de identidade da Microsoft (conhecida anteriormente como Azure Active Directory para desenvolvedores)](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="other-identity-providers"></a>Outros provedores de identidade

O Azure é compatível com vários provedores de identidade. Você pode obter uma lista completa, juntamente com os detalhes relacionados, executando este comando do console do Azure:

```cmd
az bot authsetting list-providers
```

Você também pode ver a lista desses provedores no [portal do Azure](https://ms.portal.azure.com/) ao definir as configurações de conexão OAuth para um aplicativo de registro de bot.

![provedores de identidade do azure](media/concept-bot-authentication/bot-auth-identity-providers.png)


### <a name="oauth-generic-providers"></a>Provedores de OAuth genéricos

O Azure é compatível com OAuth2 genérico, que permite que você use seus próprios provedores de identidade.

Você pode escolher entre duas implementações do provedor de identidade genérico que têm configurações diferentes, conforme mostrado abaixo.

> [!Note]
> Use as configurações descritas aqui ao definir as **Configurações de Conexão OAuth** no aplicativo de registro do bot do Azure.


# <a name="generic-oauth-2"></a>[OAuth 2 Genérico](#tab/ga2)

### <a name="generic-oauth-2"></a>OAuth 2 Genérico

Use esse provedor para configurar qualquer provedor de identidade OAuth2 genérico que tenha expectativas semelhantes às do provedor do Azure AD, especialmente o AD v2. Você tem um número limitado de propriedades porque as cadeias de consulta e as cargas do corpo da solicitação são fixas. Para os valores inseridos, você pode ver como os parâmetros para os vários URls, cadeias de consulta e corpos estão entre chaves {}.

[!INCLUDE [generic-oauth2-settings](~/includes/authentication/auth-generic-oauth2-settings.md)]


# <a name="oauth-2-generic-provider"></a>[Provedor genérico do OAuth 2](#tab/a2gp)

### <a name="oauth-2-generic-provider"></a>Provedor genérico do OAuth 2

Use esse provedor para configurar qualquer provedor de serviços OAuth 2 genérico quando precisar de mais flexibilidade. Ele requer mais parâmetros de configuração. Com essa configuração, você especifica os modelos de URL, os modelos de cadeia de consulta e os modelos de corpo para autorização, atualização e conversão de token. Para os valores inseridos, você pode ver como os parâmetros para os vários URls, cadeias de consulta e corpos estão entre chaves {}.

[!INCLUDE [generic-provider-oauth2-settings](~/includes/authentication/auth-generic-provider-oauth2-settings.md)]

---
