---
title: Autenticação – Serviço de Bot
description: Saiba como autenticar solicitações de API na API de Linha Direta versão 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/22/2019
ms.openlocfilehash: 4ae5e5a7972caa0376f2b11dfaab0be4b1e9e924
ms.sourcegitcommit: d24fe2178832261ac83477219e42606f839dc64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071774"
---
# <a name="authentication"></a>Autenticação

Um cliente pode autenticar solicitações para a API de Linha Direta 3.0 usando um **segredo** que você [obtém da página de configuração de canal de Linha Direta](../bot-service-channel-connect-directline.md) no Portal Bot Framework ou usando um  **token** obtido em runtime. O segredo ou o token deve ser especificado no cabeçalho `Authorization` de cada solicitação, usando este formato:

```http
Authorization: Bearer SECRET_OR_TOKEN
```

## <a name="secrets-and-tokens"></a>Segredos e tokens

Um **segredo** de Linha Direta é uma chave mestra que pode ser usada para acessar qualquer conversa que pertença ao bot associado. Um **segredo** também pode ser usado para obter um **token**. Os segredos não expiram.

Um **token** de Linha Direta é uma chave que pode ser usada para acessar uma única conversa. Um token expira, mas pode ser atualizado.

Decidir quando ou se usar a chave **secreta** ou um **token** deve ser baseado em considerações de segurança.
Expor a chave secreta poderia ser aceitável se fosse feito intencionalmente e com cuidado. Na verdade, esse é o comportamento padrão porque permite que o Direct Line descubra se o cliente é legítimo.
No entanto, em geral, a segurança será uma preocupação se você estiver tentando manter os dados do usuário.
Para saber mais, confira a seção [Considerações de segurança](#security-considerations).

Se você estiver criando um aplicativo de serviço a serviço, a abordagem mais simples poderá ser especificar o **segredo** no cabeçalho `Authorization` de solicitações da API da Linha Direta. Se estiver gravando um aplicativo em que o cliente é executado em um navegador da Web ou em um aplicativo móvel, talvez convenha trocar seu segredo por um token (que funciona somente para uma única conversa e irá expirar se não for atualizado) e especificar o **token** no cabeçalho `Authorization` de solicitações de API da Linha Direta. Escolha o modelo de segurança que funcione melhor para você.

> [!NOTE]
> Suas credenciais de cliente de Linha Direta são diferentes das credenciais do seu bot. Isso permite revisar as chaves de forma independente e compartilhar os tokens de cliente sem revelar a senha do seu bot.

## <a name="get-a-direct-line-secret"></a>Obter um segredo de Linha Direta

Você pode [obter um segredo da Direct Line](../bot-service-channel-connect-directline.md) por meio da página de configuração do canal da Direct Line do bot no [Portal do Azure](https://portal.azure.com):

![Configuração da Linha Direta](../media/direct-line-configure.png)

## <a id="generate-token"></a> Gerar um token de Linha Direta

Para gerar um token da Direct Line que possa ser usado para acessar uma única conversa, primeiro obtenha o segredo da Direct Line na página de configuração do canal da Direct Line no [Portal do Azure](https://portal.azure.com). Em seguida, envie essa solicitação para trocar seu segredo da Linha Direta para um token da Linha Direta:

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer SECRET
```

No cabeçalho `Authorization` dessa solicitação, substitua **SECRET** pelo valor do seu segredo de Linha Direta.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Gerar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

O conteúdo da solicitação, que inclui os parâmetros de token, é opcional mas recomendado. Ao gerar um token que possa ser retornado para o serviço Direct Line, forneça o conteúdo a seguir para tornar a conexão mais segura. Com a inclusão desses valores, o Direct Line poderá executar a validação de segurança adicional de ID e nome de usuário, inibindo sua violação por clientes mal-intencionados. Sua inclusão também melhorará a habilidade do Direct Line enviar a atividade de _atualização da conversa_, permitindo que a atualização seja gerada imediatamente quando o usuário ingressar. Se essas informações não forem fornecidas, o usuário terá que enviar o conteúdo antes do Direct Line poder enviar a atualização da conversa.

```json
{
  "user": {
    "id": "string",
    "name": "string"
  },
  "trustedOrigins": [
    "string"
  ]
}
```

| Parâmetro | Type | Descrição |
| :--- | :--- | :--- |
| `user.id` | string | Opcional. ID de usuário específica do canal para codificação dentro do token. Para um usuário do Direct Line, isso deve começar com `dl_`. Você pode criar uma ID de usuário exclusiva para cada conversa, e para uma melhor segurança, deve tornar essa ID indecifrável. |
| `user.name` | string | Opcional. O nome de exibição amigável do usuário a ser codificado dentro do token. |
| `trustedOrigins` | Matriz de cadeia de caracteres | Opcional. Uma lista de domínios confiáveis para inserir no token. Esses são os domínios que podem hospedar o cliente de bate-papo Web do bot. Eles devem estar na lista da página de configuração do Direct Line de seu bot. |

### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, a resposta conterá um `token`, válido para uma conversa, e um valor `expires_in`, que indica o número de segundos até o token expirar. Para continuar a usar o token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

### <a name="generate-token-versus-start-conversation"></a>Gerar Token versus Iniciar Conversa

A operação Gerar Token (`POST /v3/directline/tokens/generate`) é semelhante à operação [Iniciar Conversa](bot-framework-rest-direct-line-3-0-start-conversation.md) (`POST /v3/directline/conversations`), já que ambas retornam um `token`, que pode ser usado para acessar uma única conversa. No entanto, ao contrário da operação Iniciar Conversa, a operação Gerar Token não inicia a conversa, não entra em contato com o bot e não cria uma URL WebSocket de streaming.

Se você planeja distribuir o token para clientes e deseja que eles iniciem a conversa, use a operação Gerar Token. Se você pretende iniciar a conversa imediatamente, use a operação [Iniciar Conversa](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a id="refresh-token"></a> Atualizar um token de Linha Direta

Um token de Linha Direta pode ser atualizado uma quantidade ilimitada de vezes, desde que não tenha expirado. Um token expirado não pode ser atualizado. Para atualizar um token de Linha Direta, emita esta solicitação:

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

No cabeçalho `Authorization` dessa solicitação, substitua **TOKEN_TO_BE_REFRESHED** pelo token de Linha Direta que você deseja atualizar.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Atualizar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Resposta

Se a solicitação for bem-sucedida, a resposta conterá um novo `token`, válido para a mesma conversa como o token anterior, e um valor `expires_in` que indica o número de segundos até que o novo token expire. Para continuar a usar o novo token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0",
  "expires_in": 1800
}
```

## <a name="azure-bot-service-authentication"></a>Autenticação do Serviço de Bot do Azure

As informações apresentadas nesta seção se baseiam no artigo [Adicionar autenticação ao bot por meio do Serviço de Bot do Azure](../v4sdk/bot-builder-authentication.md).

A **autenticação do Serviço de Bot do Azure** permite que você autentique usuários e obtenha **tokens de acesso** de uma variedade de provedores de identidade, como o *Azure Active Directory*, *GitHub*, *Uber* e assim por diante. Você também pode configurar a autenticação de um provedor de identidade **OAuth2** personalizado. Tudo isso permite que você escreva **uma parte do código de autenticação** que funciona em todos os provedores de identidade e canais com suporte. Para utilizar essas funcionalidades, é necessário seguir estas etapas:

1. Configure estatisticamente o `settings` em seu bot que contém os detalhes do seu registro de aplicativo com um provedor de identidade.
2. Use um `OAuthCard`, respaldado pelas informações do aplicativo fornecidas na etapa anterior, para conectar um usuário.
3. Recupere tokens de acesso por meio da **API do Serviço de Bot do Azure**.

### <a name="security-considerations"></a>Considerações de segurança

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

Quando você usa a *autenticação do Serviço de Bot do Azure* com o [Webchat](../bot-service-channel-connect-webchat.md), há algumas considerações importantes sobre segurança que você precisa ter em mente.

1. **Representação**. Representação aqui significa um invasor que faz o bot achar que ele é outra pessoa. No Webchat, um invasor pode representar outra pessoa, **alterando a ID de usuário** da sua instância de Webchat. Para evitar isso, é recomendável que os desenvolvedores de bot tornem a **ID de usuário indecifrável**.

    Se você habilitar as opções de **autenticação aprimorada**, o Serviço de Bot do Azure poderá detectar e rejeitar ainda mais qualquer alteração da ID de usuário. Isso significa que a ID de usuário (`Activity.From.Id`) em mensagens do Direct Line para seu bot sempre será a mesma com a qual você inicializou o Webchat. Observe que esse recurso requer que a ID de usuário comece com `dl_`.

    > [!NOTE]
    > Quando uma *User.Id* é fornecida durante a troca de um segredo para um token, essa *User.Id* é inserida no token. A DirectLine garante que as mensagens enviadas para o bot tenham essa ID como a *From.Id* da atividade. Se um cliente enviar uma mensagem para a DirectLine com uma *From.Id* diferente, ela será alterada para a **ID no token** antes de encaminhar a mensagem para o bot. Portanto, você não pode usar outra ID de usuário depois que um segredo de canal é inicializado com uma ID de usuário

1. **Identidades do usuário**. Você deve saber que está lidando com duas identidades de usuário:

    1. A identidade do usuário em um canal.
    1. A identidade do usuário em um provedor de identidade no qual o bot está interessado.

    Quando um bot solicita que o usuário A em um canal conecte-se a um provedor de identidade P, o processo de conexão deve garantir que o usuário A seja aquele que se conecta em P. Se outro usuário B tiver permissão para se conectar, então o usuário A terá acesso ao recurso do usuário B por meio do bot. No Webchat, temos dois mecanismos para verificar se o usuário correto entrou conforme descrito a seguir.

    1. No final da credencial, no passado, o usuário recebia um código de seis dígitos gerado aleatoriamente (também conhecido como código mágico). O usuário deveria digitar esse código na conversa que iniciava a conexão para concluir o processo de entrada. Esse mecanismo tende a resultar em uma experiência de usuário inadequada. Além disso, ele ainda está suscetível a ataques de phishing. Um usuário mal-intencionado pode induzir outro usuário a se conectar e a obter o código mágico por meio de phishing.

    2. Devido aos problemas com a abordagem anterior, o Serviço de Bot do Azure acabou com a necessidade do código mágico. O Serviço de Bot do Azure garante que o processo de conexão só possa ser concluído na **mesma sessão do navegador** que a do próprio Webchat.
    Para habilitar essa proteção, como desenvolvedor de bot, você deve iniciar o Webchat com um **token do Direct Line** que contenha uma **lista de domínios confiáveis que possam hospedar o cliente do Webchat do bot**. Antes, você só poderia obter esse token passando um parâmetro opcional não documentado para a API de token do Direct Line. Agora, com as opções de autenticação avançada, você pode especificar estaticamente a lista de domínios confiáveis (origem) na página de configuração do Direct Line.

    Confira também [Adicionar autenticação ao bot por meio do Serviço de Bot do Azure](../v4sdk/bot-builder-authentication.md).

### <a name="code-examples"></a>Exemplos de código

O seguinte controlador do .NET trabalha com opções de autenticação avançadas habilitadas e retorna um Token do Direct Line e uma ID de usuário.

```csharp
public class HomeController : Controller
{
    public async Task<ActionResult> Index()
    {
        var secret = GetSecret();

        HttpClient client = new HttpClient();

        HttpRequestMessage request = new HttpRequestMessage(
            HttpMethod.Post,
            $"https://directline.botframework.com/v3/directline/tokens/generate");

        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", secret);

        var userId = $"dl_{Guid.NewGuid()}";

        request.Content = new StringContent(
            JsonConvert.SerializeObject(
                new { User = new { Id = userId } }),
                Encoding.UTF8,
                "application/json");

        var response = await client.SendAsync(request);
        string token = String.Empty;

        if (response.IsSuccessStatusCode)
        {
            var body = await response.Content.ReadAsStringAsync();
            token = JsonConvert.DeserializeObject<DirectLineToken>(body).token;
        }

        var config = new ChatConfig()
        {
            Token = token,
            UserId = userId
        };

        return View(config);
    }
}

public class DirectLineToken
{
    public string conversationId { get; set; }
    public string token { get; set; }
    public int expires_in { get; set; }
}
public class ChatConfig
{
    public string Token { get; set; }
    public string UserId { get; set; }
}

```

O seguinte controlador do JavaScript trabalha com opções de autenticação avançadas habilitadas e retorna um Token do Direct Line e uma ID de usuário.

```javascript
var router = express.Router(); // get an instance of the express Router

// Get a directline configuration (accessed at GET /api/config)
const userId = "dl_" + createUniqueId();

router.get('/config', function(req, res) {
    const options = {
        method: 'POST',
        uri: 'https://directline.botframework.com/v3/directline/tokens/generate',
        headers: {
            'Authorization': 'Bearer ' + secret
        },
        json: {
            User: { Id: userId }
        }
    };

    request.post(options, (error, response, body) => {
        if (!error && response.statusCode < 300) {
            res.json({
                    token: body.token,
                    userId: userId
                });
        }
        else {
            res.status(500).send('Call to retrieve token from Direct Line failed');
        }
    });
});

```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Conectar um bot à Linha Direta](../bot-service-channel-connect-directline.md)
- [Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure](../bot-builder-tutorial-authentication.md)
