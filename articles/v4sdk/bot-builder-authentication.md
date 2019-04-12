---
title: Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure | Microsoft Docs
description: Saiba como usar os recursos de autenticação do Serviço de Bot do Azure para adicionar o SSO ao seu bot.
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 04/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 27c97d257261a6f3b9d867503aee40382b685e20
ms.sourcegitcommit: 562dd44e38abacaa31427da5675da556a970cf11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59477099"
---
# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O Serviço de Bot do Azure e o SDK v4 incluem novos recursos de autenticação do bot, fornecendo funcionalidades para facilitar o desenvolvimento de um bot que autentica usuários em vários provedores de identidade como o Azure AD (Azure Active Directory), GitHub, Uber e assim por diante. Essas funcionalidades podem aprimorar a experiência do usuário, eliminando a _verificação de código mágico_ para alguns clientes.

Antes disso, seu bot precisa incluir controladores de OAuth e links de logon, armazenar as IDs e segredos de cliente de destino e executar o gerenciamento de token de usuário. O bot solicitava que o usuário entrasse em um site, o qual gerava um _código mágico_ que poderia ser usado para o usuário verificar sua identidade.

Agora, os desenvolvedores de bots não precisam mais hospedar controladores OAuth ou gerenciar o ciclo de vida do token, já que tudo isso agora pode ser feito pelo Serviço de Bot do Azure.

Os recursos incluem:

- Melhorias para os canais para dar suporte a novos recursos de autenticação, como novas bibliotecas de WebChat e DirectLineJS para eliminar a necessidade da verificação de código mágico de 6 dígitos.
- Melhorias para o Portal do Azure para adicionar, excluir e definir as configurações de conexão para vários provedores de identidade OAuth.
- Suporte para uma variedade de provedores de identidade de out-of-the-box, incluindo o Azure AD (pontos de extremidade v1 e v2), GitHub e outros.
- Atualizações dos SDKs do Bot Framework para Node.js e C# para recuperar tokens, criar OAuthCards e manipular eventos de TokenResponse.
- Exemplos de como criar um bot que autentica para o Azure AD.

Você pode extrapolar das etapas neste artigo para adicionar recursos a um bot existente. Estes bots de exemplo demonstram os novos recursos de autenticação.

> [!NOTE]
> Os recursos de autenticação também trabalham com o BotBuilder v3. No entanto, este artigo aborda apenas o código de exemplo v4.

### <a name="about-this-sample"></a>Sobre este exemplo

Você precisa criar um recurso de bot do Azure e precisa criar um novo aplicativo do Azure Active Directory (v1 ou v2) para permitir que seu bot acesse o Office 365. O recurso de bot registra as credenciais do seu bot, as quais são necessárias para testar os recursos de autenticação, até mesmo ao executar o código de seu bot localmente.

> [!IMPORTANT]
> Sempre que você registra um bot no Azure, um aplicativo do Azure Active Directory é atribuído a ele. No entanto, esse aplicativo protege o acesso do canal ao bot.
Você precisa de um aplicativo AAD adicional para cada aplicativo que você deseja que possa ser autenticado pelo bot em nome do usuário.

Este artigo cria um bot de exemplo que se conecta ao Microsoft Graph usando um token do Azure AD v1 ou v2. Ele também aborda como criar e registrar o aplicativo associado do Azure Active Directory. Como parte desse processo, você usará o código do repositório do GitHub [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples). Este artigo aborda esses processos.

- **Criar seu recurso de bot**
- **Criar um aplicativo Azure AD**
- **Registrar seu aplicativo do Azure AD com seu bot**
- **Preparar os códigos de exemplo de bot**

Depois de concluir, você terá um bot sendo executado localmente e que pode responder a algumas tarefas simples em relação a um aplicativo do Azure Active Directory, como verificar e enviar um email ou exibir quem é você e quem é o seu gerente. Para fazer isso, seu bot usará um token de um aplicativo do Azure AD com a biblioteca Microsoft.Graph. Você não precisa publicar seu bot para testar os recursos de entrada do OAuth. No entanto, seu bot precisará de uma ID válida de aplicativo do Azure e senha.

Esses recursos de autenticação também funcionam com outros tipos de bots. No entanto, este artigo usa um bot de somente registro.

### <a name="web-chat-and-direct-line-considerations"></a>Considerações de Webchat e Direct Line

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

Há alguns problemas de segurança importantes a considerar ao usar a autenticação do Serviço de Bot do Azure com o Webchat.

1. Evite a representação, que é quando um invasor faz o bot pensar que ele é outra pessoa. No Webchat, um invasor pode representar outra pessoa, alterando a ID de usuário da sua instância de Webchat.

    Para evitar isso, crie uma ID de usuário que não possa ser adivinhada. Quando você habilita as opções de autenticação aprimoradas no canal do Direct Line, o Serviço de Bot do Azure pode detectar e rejeitar qualquer alteração na ID de usuário. A ID de usuário em mensagens do Direct Line para seu bot será sempre a mesma usada para inicializar o Webchat. Observe que esse recurso requer que a ID de usuário comece com `dl_`.

1. Verifique se o usuário correto está conectado. O usuário tem duas identidades: sua identidade em um canal e sua identidade com o provedor de identidade. No Webchat, o Serviço de Bot do Azure pode garantir que o processo de entrada seja concluído na própria sessão do navegador do Webchat.

    Para habilitar essa proteção, inicie o Webchat com um token do Direct Line que contém uma lista de domínios confiáveis que podem hospedar o cliente do Webchat do bot. Depois, especifique estaticamente a lista de domínios confiáveis (origem) na página de configuração do Direct Line.

Use o ponto de extremidade REST `/v3/directline/tokens/generate` do Direct Line para gerar um token para a conversa e especifique a ID de usuário no conteúdo da solicitação. Para obter um exemplo de código, confira a postagem no blog [Recursos de autenticação aprimorados do Direct Line](https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/).

<!-- The eventual article about this should talk about the tokens/generate endpoint and its parameters: user, trustedOrigins, and [maybe] eTag.
Sample payload
{
  "user": {
    "id": "string",
    "name": "string",
    "aadObjectId": "string",
    "role": "string"
  },
  "trustedOrigins": [
    "string"
  ],
  "eTag": "string"
}
 -->

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [noções básicas do bot][concept-basics] e do [estado de gerenciamento][concept-state].
- Conhecimento do desenvolvimento do Azure e do OAuth 2.0.
- Visual Studio 2017 ou posterior, Node.js, npm e git.
- Um desses exemplos.

| Amostra | Versão do BotBuilder | Demonstra |
|:---|:---:|:---|
| **Autenticação de bot** em [**CSharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] | v4 | Suporte de OAuthCard |
| **Autenticação de bot MSGraph** em [**CSharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] | v4 |  Suporte da API do Microsoft Graph com o OAuth 2 |

## <a name="create-your-bot-resource-on-azure"></a>Criar seu recurso de bot no Azure

Crie um **Registro de Canais de Bot** usando o [Portal do Azure](https://portal.azure.com/).

## <a name="create-and-register-an-azure-ad-application"></a>Criar e registrar um aplicativo Azure Active Directory

Você precisa de um aplicativo do Azure AD que seu bot possa usar para se conectar à API do Microsoft Graph.

Para este bot, você pode usar pontos de extremidade do Azure AD v1 ou v2.
Para obter informações sobre as diferenças entre os pontos de extremidade v1 e v2, consulte a [comparação v1-v2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) e a [visão geral de ponto de extremidade do Azure AD v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

### <a name="create-your-azure-ad-application"></a>Criar seu aplicativo do Azure Active Directory

Use essas etapas para criar um novo aplicativo do Azure Active Directory. Você pode usar os pontos de extremidade v1 ou v2 com o aplicativo criado.

> [!TIP]
> Você precisará criar e registrar o aplicativo do Azure Active Directory em um locatário no qual você tem direitos de administrador.

1. Abra o painel do [Azure Active Directory][azure-aad-blade] no portal do Azure.
    Se você não estiver no locatário correto, clique em **Trocar de diretório** para trocar para o locatário correto. (Para obter instruções sobre como criar um locatário, confira [Acessar o portal e criar um locatário](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).)
1. Abra o painel **Registros de aplicativo**.
1. No painel **Registros de aplicativo**, clique em **Novo registro de aplicativo**.
1. Preencha os campos obrigatórios e crie o registro do aplicativo.

   1. Nome do seu aplicativo.
   1. Defina o **Tipo de aplicativo** como **Aplicativo Web / API**.
   1. Defina a **URL de logon** para `https://token.botframework.com/.auth/web/redirect`.
   1. Clique em **Criar**.

      - Depois de criado, ele é exibido em um painel **Aplicativo registrado**.
      - Registre o valor **ID do Aplicativo**. Você usará esse valor posteriormente como a _ID do cliente_ ao registrar seu aplicativo do Azure AD com seu bot.

1. Clique em **Configurações** para configurar seu aplicativo.
1. Clique em **Chaves** para abrir o painel **Chaves**.

   1. Em **Senhas**, crie uma chave `BotLogin`.
   1. Defina sua **duração** para **Nunca expira**.
   1. Clique em **Salvar** e registre o valor da chave. Você usará esse valor posteriormente como o _Segredo do cliente_ ao registrar seu aplicativo do Azure AD com seu bot.
   1. Feche o painel **Chaves**.

1. Clique em **Permissões necessárias** para abrir o painel **Permissões necessárias**.

   1. Clique em **Adicionar**.
   1. Clique em **Selecionar uma API**, em seguida, selecione **Microsoft Graph** e clique em **Selecionar**.
   1. Clique em **Selecionar permissões**. Escolha as permissões delegadas que o aplicativo usará.

      > [!NOTE]
      > Nenhuma permissão marcada como **Requer administração** exigirá um usuário e um administrador de locatários para fazer logon, portanto para seu bot evite-as.

      Selecione as seguintes permissões delegadas do Microsoft Graph:
      - Ler os perfis básicos de todos os usuários
      - Ler email de usuário
      - Enviar email como um usuário
      - Entrar e ler o perfil do usuário
      - Exibir o perfil básico dos usuários
      - Exibir o endereço de email dos usuários

   1. Clique em **Selecionar** e, em seguida, clique em **Concluído**.
   1. Feche o painel **Permissões necessárias**.

Agora você tem um aplicativo do Azure AD v1 configurado.

### <a name="register-your-azure-ad-application-with-your-bot"></a>Registrar seu aplicativo do Azure AD com seu bot

A próxima etapa é usar seu bot para registrar o aplicativo do Azure Active Directory que você criou.

# [<a name="azure-ad-v1"></a>Azure AD v1](#tab/aadv1)

1. Navegue até a página de recurso do seu bot no [Portal do Azure](http://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você usará esse nome em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira a chave que você registrou para a chave `BotLogin` do seu aplicativo.
    1. Para **Tipo de concessão**, insira `authorization_code`.
    1. Para **URL de logon**, insira `https://login.microsoftonline.com`.
    1. Para **ID do locatário**, insira a ID do locatário para o Azure Active Directory, por exemplo `microsoft.com` ou `common`.

       Esse será o locatário associado aos usuários que podem ser autenticados. Para permitir que qualquer pessoa se autentique por meio do bot, use o locatário `common`.

    1. Para a **URL do Recurso**, insira `https://graph.microsoft.com/`.
    1. Deixe **Escopos** em branco.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

# [<a name="azure-ad-v2"></a>Azure AD v2](#tab/aadv2)

1. Navegue até a página Registro de Canais de Bot do seu bot no [Portal do Azure](http://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você o usará em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory v2**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira sua ID de aplicativo do Azure AD v2 do registro do aplicativo.
    1. Para **Segredo do cliente**, insira sua senha de aplicativo do Azure AD v2 do registro do aplicativo.
    1. Para **ID do locatário**, insira a ID do locatário para o Azure Active Directory, por exemplo `microsoft.com` ou `common`.

        Esse será o locatário associado aos usuários que podem ser autenticados. Para permitir que qualquer pessoa se autentique por meio do bot, use o locatário `common`.

    1. Para **Escopos**, digite os nomes da permissão que você escolheu no registro do aplicativo: `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Para o Azure AD v2, **Escopos** usa uma lista de valores que diferencia maiúsculas de minúsculas separadas por espaços.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

---

### <a name="test-your-connection"></a>Testar sua conexão

1. Clique na entrada de conexão para abrir a conexão que você acabou de criar.
1. Clique em **Testar Conexão** na parte superior do painel **Configuração de Conexão do Provedor de Serviços**.
1. Na primeira vez, deve abrir uma nova guia do navegador listando as permissões que seu aplicativo está solicitando e solicitará que você aceite.
1. Clique em **Aceitar**.
1. Isso deverá direcionar você para uma página **Testar conexão para \<nome-da-sua-conexão> bem-sucedida**.

Agora você pode usar esse nome de conexão no código do bot para recuperar tokens de usuário.

## <a name="prepare-the-bot-sample-code"></a>Preparar os códigos de exemplo de bot

Dependendo do exemplo escolhido, você trabalhará com C# ou Node.

| Amostra | Versão do BotBuilder | Demonstra |
|:---|:---:|:---|
| **Autenticação de bot** em [**CSharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] | v4 | Suporte de OAuthCard |
| **Autenticação de bot MSGraph** em [**CSharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] | v4 |  Suporte da API do Microsoft Graph com o OAuth 2 |

1. Clique em um dos links de exemplo acima e clone o repositório do github.
1. Siga as instruções na página do leiame do GitHub para saber como executar esse bot específico (C# ou Node).
1. Se você estiver usando o exemplo de Autenticação de Bot em C#:

    1. Defina a variável `ConnectionName` no arquivo `AuthenticationBot.cs` com o valor usado ao definir a configuração de conexão OAuth 2.0 do seu bot.
    1. Defina o valor `appId` no arquivo `BotConfiguration.bot` como a ID do aplicativo do seu bot.
    1. Defina o valor `appPassword` no arquivo `BotConfiguration.bot` como o segredo do seu bot.

1. Se você estiver usando o exemplo de Autenticação de Bot em Node/JS:

    1. Defina a variável `CONNECTION_NAME` no arquivo `bot.js` com o valor usado ao definir a configuração de conexão OAuth 2.0 do seu bot.
    1. Defina o valor `appId` no arquivo `bot-authentication.bot` como a ID do aplicativo do seu bot.
    1. Defina o valor `appPassword` no arquivo `bot-authentication.bot` como o segredo do seu bot.

    > [!IMPORTANT]
    > Dependendo dos caracteres no seu segredo, você pode precisar que XML ignore a senha. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    ```json
    {
        "name": "BotAuthentication",
        "secretKey": "",
        "services": [
            {
            "appId": "",
            "id": "http://localhost:3978/api/messages",
            "type": "endpoint",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages",
            "name": "BotAuthentication"
            }
        ]
    }
    ```

    Se você não souber como obter seus valores de **ID do aplicativo Microsoft** e **senha de aplicativo do Microsoft**, procure no **ApplicationSettings** do serviço de aplicativo do Azure que foi provisionado para seu bot no Portal do Azure.

    > [!NOTE]
    > Agora você pode publicar esse código de bot para sua assinatura do Azure (clique com o botão direito no projeto e escolha **Publicar**), mas não é necessário para este tutorial. Você precisará definir uma configuração de publicação que usa o aplicativo e o plano de hospedagem que você usou ao configurar o bot no Portal do Azure.

## <a name="use-the-emulator-to-test-your-bot"></a>Usar o Emulador para testar seu bot

Você precisará instalar o [Emulador de Bot](https://github.com/Microsoft/BotFramework-Emulator) para testar seu bot localmente. Você pode usar o Emulador v3 ou v4.

1. Inicie seu bot (com ou sem depuração).
1. Anote o número da porta do localhost da página. Essa informação será necessária para interagir com o bot.
1. Inicie o Emulador.
1. Conecte-se ao seu bot. Verifique se a configuração do bot usa a **ID do aplicativo da Microsoft** e a **senha do aplicativo do Microsoft** ao usar a autenticação
1. Certifique-se de que nas configurações do Emulador, **Usar um código de verificação de entrada para OAuthCards** esteja marcado e **ngrok** esteja habilitado para que o Serviço de Bot do Azure possa retornar o token ao emulador quando ele estiver disponível.

   Se você ainda não tiver configurado a conexão, forneça o endereço e a ID e senha de aplicativo Microsoft do seu bot. Adicione `/api/messages` à URL do bot. Sua URL será semelhante a `http://localhost:portNumber/api/messages`.

1. Digite `help` para ver uma lista dos comandos disponíveis para o bot e testar os recursos de autenticação.
1. Quando você tiver entrado, você não precisa fornecer suas credenciais novamente até sair.
1. Para sair e cancelar a sua autenticação, digite `signout`.

<!--To restart completely from scratch you also need to:
1. Navigate to the **AppData** folder for your account.
1. Go to the **Roaming/botframework-emulator** subfolder.
1. Delete the **Cookies** and **Coolies-journal** files.
-->

> [!NOTE]
> A autenticação do bot requer o uso do Serviço de Conector de Bot. O serviço acessa as informações de registro de canais de bot para seu bot, por isso, você precisará definir o ponto de extremidade de mensagens do seu bot no portal. A autenticação também exige o uso de HTTPS, por isso, você precisará criar um endereço de encaminhamento HTTPS para seu bot executar localmente.

<!--The following is necessary for WebChat:
1. Use the **ngrok** command-line tool to get a forwarding HTTPS address for your bot.
   - For information on how to do this, see [Debug any Channel locally using ngrok](https://blog.botframework.com/2017/10/19/debug-channel-locally-using-ngrok/).
   - Any time you exit **ngrok**, you will need to redo this and the following step before starting the Emulator.
1. On the Azure Portal, go to the **Settings** blade for your bot.
   1. In the **Configuration** section, change the **Messaging endpoint** to the HTTPS forwarding address generated by **ngrok**.
   1. Click **Save** to save your change.
-->

## <a name="notes-on-the-token-retrieval-flow"></a>Observações sobre o fluxo de recuperação de token

Quando um usuário pede ao bot para fazer algo que requer que o bot tenha o usuário conectado, o bot pode usar um `OAuthPrompt` para iniciar a recuperação de um token para uma determinada conexão. O `OAuthPrompt` cria um fluxo de recuperação de token composto por:

1. Verificação se o Serviço de Bot do Azure já tem um token para o usuário e a conexão atuais. Se houver um token, o token será retornado.
1. Se o Serviço de Bot do Azure não tiver um token em cache, um `OAuthCard` será criado, o qual é um botão de entrada no qual o usuário pode clicar.
1. Após o usuário clicar no botão `OAuthCard`, o Serviço de Bot do Azure enviará diretamente ao bot o token do usuário ou apresentará ao usuário um código de autenticação de 6 dígitos para inserir na janela de chat.
1. Se o usuário receber um código de autenticação, o bot trocará esse código de autenticação pelo token do usuário.

Os dois próximos snippets de código são obtidos do `OAuthPrompt`, mostrando como estas etapas funcionam no prompt.

### <a name="check-for-a-cached-token"></a>Verificação de um token em cache

Nesse código, primeiro o bot faz uma verificação rápida para determinar se o Serviço de Bot do Azure já tem um token para o usuário (que é identificado pelo remetente da atividade atual) e o ConnectionName determinado (que é o nome de conexão usada na configuração). O Serviço de Bot do Azure ou já terá um token armazenado em cache ou não terá. A chamada para GetUserTokenAsync executa essa verificação rápida. Se o Serviço de Bot do Azure tem um token e retorna-o, o token pode ser usado imediatamente. Se o Serviço de Bot do Azure não tem um token, esse método retornará null. Nesse caso, o bot pode enviar um OAuthCard personalizado para o usuário entrar.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
// First ask Bot Service if it already has a token for this user
var token = await adapter.GetUserTokenAsync(turnContext, connectionName, null, cancellationToken).ConfigureAwait(false);
if (token != null)
{
    // use the token to do exciting things!
}
else
{
    // If Bot Service does not have a token, send an OAuth card to sign in
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
public async getUserToken(context: TurnContext, code?: string): Promise<TokenResponse|undefined> {
    // Get the token and call validator
    const adapter: any = context.adapter as any; // cast to BotFrameworkAdapter
    return await adapter.getUserToken(context, this.settings.connectionName, code);
}
```

---

### <a name="send-an-oauthcard-to-the-user"></a>Enviar um OAuthCard ao usuário

Você pode personalizar o OAuthCard com qualquer texto e texto de botão desejado. As partes importantes são:

- Defina o `ContentType` para `OAuthCard.ContentType`.
- Defina a propriedade `ConnectionName` para o nome da conexão que você deseja usar.
- Inclua um botão com um `CardAction` dos `Type` `ActionTypes.Signin`. Observe que você não precisa especificar nenhum valor para o sinal no link.

No final dessa chamada, o bot precisa "aguardar o token" antes de voltar. Essa espera ocorre no fluxo de atividade principal porque pode haver muitos que o usuário precisa fazer para entrar.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
private async Task SendOAuthCardAsync(ITurnContext turnContext, IMessageActivity message, CancellationToken cancellationToken = default(CancellationToken))
{
    if (message.Attachments == null)
    {
        message.Attachments = new List<Attachment>();
    }

    message.Attachments.Add(new Attachment
    {
        ContentType = OAuthCard.ContentType,
        Content = new OAuthCard
        {
            Text = "Please sign in",
            ConnectionName = connectionName,
            Buttons = new[]
            {
                new CardAction
                {
                    Title = "Sign In",
                    Text = "Sign In",
                    Type = ActionTypes.Signin,
                },
            },
        },
    });

    await turnContext.SendActivityAsync(message, cancellationToken).ConfigureAwait(false);
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
private async sendOAuthCardAsync(context: TurnContext, prompt?: string|Partial<Activity>): Promise<void> {
    // Initialize outgoing message
    const msg: Partial<Activity> =
        typeof prompt === 'object' ? {...prompt} : MessageFactory.text(prompt, undefined, InputHints.ExpectingInput);
    if (!Array.isArray(msg.attachments)) { msg.attachments = []; }

    const cards: Attachment[] = msg.attachments.filter((a: Attachment) => a.contentType === CardFactory.contentTypes.oauthCard);
    if (cards.length === 0) {
        // Append oauth card
        msg.attachments.push(CardFactory.oauthCard(
            this.settings.connectionName,
            this.settings.title,
            this.settings.text
        ));
    }

    // Send prompt
    await context.sendActivity(msg);
}
```

---

### <a name="wait-for-a-tokenresponseevent"></a>Aguardar um TokenResponseEvent

No código, o Bot está aguardando um `TokenResponseEvent` (veja abaixo mais informações sobre como isso é roteado para a pilha de diálogos). O método `WaitForToken` primeiro determina se esse evento foi enviado. Se foi enviado, ele pode ser usado pelo bot. Se não, o método `RecognizeTokenAsync` usa qualquer texto que foi enviado para o bot e passa-o para `GetUserTokenAsync`. O motivo disso é que alguns clientes (como WebChat) não precisam do código de verificação Magic Code e podem enviar diretamente o Token no `TokenResponseEvent`. Outros clientes ainda exigem que o magic code (como Facebook ou Slack). O Serviço de Bot do Azure apresentará esses clientes com um magic code de seis dígitos e pedir ao usuário digitar isso na janela de bate-papo. Embora não seja ideal, esse é o comportamento de fallback, e se `RecognizeTokenAsync` recebe um código, o bot pode enviar esse código para o Serviço de Bot do Azure e obter um token de volta. Se essa chamada também falhar, você pode decidir relatar um erro ou fazer algo diferente. Na maioria dos casos, o bot agora terá um token de usuário.

Se você examinar o código do bot de cada exemplo, verá que as atividades `Event` e `Invoke` também são roteadas para a pilha de diálogos.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
// This can be called when the bot receives an Activity after sending an OAuthCard
private async Task<TokenResponse> RecognizeTokenAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (IsTokenResponseEvent(turnContext))
    {
        // The bot received the token directly
        var tokenResponseObject = turnContext.Activity.Value as JObject;
        var token = tokenResponseObject?.ToObject<TokenResponse>();
        return token;
    }
    else if (IsTeamsVerificationInvoke(turnContext))
    {
        var magicCodeObject = turnContext.Activity.Value as JObject;
        var magicCode = magicCodeObject.GetValue("state")?.ToString();

        var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, magicCode, cancellationToken).ConfigureAwait(false);
        return token;
    }
    else if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // make sure it's a 6-digit code
        var matched = _magicCodeRegex.Match(turnContext.Activity.Text);
        if (matched.Success)
        {
            var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, matched.Value, cancellationToken).ConfigureAwait(false);
            return token;
        }
    }

    return null;
}

private bool IsTokenResponseEvent(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Event && activity.Name == "tokens/response";
}

private bool IsTeamsVerificationInvoke(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Invoke && activity.Name == "signin/verifyState";
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
private async recognizeToken(context: TurnContext): Promise<PromptRecognizerResult<TokenResponse>> {
    let token: TokenResponse|undefined;
    if (this.isTokenResponseEvent(context)) {
        token = context.activity.value as TokenResponse;
    } else if (this.isTeamsVerificationInvoke(context)) {
        const code: any = context.activity.value.state;
        await context.sendActivity({ type: 'invokeResponse', value: { status: 200 }});
        token = await this.getUserToken(context, code);
    } else if (context.activity.type === ActivityTypes.Message) {
        const matched: RegExpExecArray = /(\d{6})/.exec(context.activity.text);
        if (matched && matched.length > 1) {
            token = await this.getUserToken(context, matched[1]);
        }
    }

    return token !== undefined ? { succeeded: true, value: token } : { succeeded: false };
}

private isTokenResponseEvent(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Event && activity.name === 'tokens/response';
}

private isTeamsVerificationInvoke(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Invoke && activity.name === 'signin/verifyState';
}
```

---

### <a name="message-controller"></a>Controlador de mensagem

Em chamadas subsequentes para o bot, observe que o token nunca é armazenado em cache por este bot de exemplo. Isso ocorre porque o bot sempre pode pedir o token para o Serviço de Bot do Azure. Isso evita o necessidade de gerenciar o ciclo de vida de token, atualizar o token, etc., já que o Serviço de Bot do Azure faz tudo isso para você.

### <a name="further-reading"></a>Leitura adicional

- Os [Recursos adicionais do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help) incluem links para obter suporte adicional.
- O repositório [SDK do Bot Framework](https://github.com/microsoft/botbuilder) tem mais informações sobre repositórios, amostras, ferramentas e especificações associadas ao SDK do Bot Builder.

<!-- Footnote-style links -->

[Azure portal]: https://ms.portal.azure.com
[azure-aad-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview
[aad-registration-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[simple-dialog]: bot-builder-dialog-manage-conversation-flow.md
[dialog-prompts]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-auth-sample]: https://aka.ms/v4cs-bot-auth-sample
[js-auth-sample]: https://aka.ms/v4js-bot-auth-sample
[cs-msgraph-sample]: https://aka.ms/v4cs-auth-msgraph-sample
[js-msgraph-sample]: https://aka.ms/v4js-auth-msgraph-sample
