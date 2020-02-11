---
title: Adicionar autenticação ao bot por meio do Serviço de Bot do Azure – Serviço de Bot
description: Saiba como usar os recursos de autenticação do Serviço de Bot do Azure para adicionar o SSO ao seu bot.
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ROBOTS: NOINDEX
ms.date: 11/14/2019
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: aab49803939a42974a59b66f47a1f004019421b4
ms.sourcegitcommit: 4e1af50bd46debfdf9dcbab9a5d1b1633b541e27
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2020
ms.locfileid: "76752778"
---
# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]  

Este tutorial usa os novos recursos de autenticação do bot no Serviço de Bot do Azure, fornecendo recursos para facilitar o desenvolvimento de um bot que autentica usuários em vários provedores de identidade como o Azure AD (Azure Active Directory), GitHub, Uber e assim por diante. Essas atualizações também avançam em direção a uma experiência de usuário aprimorada, eliminando a _verificação de código mágico_ para alguns clientes.

Antes disso, seu bot precisa incluir controladores de OAuth e links de logon, armazenar as IDs e segredos de cliente de destino e executar o gerenciamento de token de usuário.
<!--
These capabilities were bundled in the BotAuth and AuthBot samples that are on GitHub.
-->

Agora, os desenvolvedores de bots não precisam mais hospedar controladores OAuth ou gerenciar o ciclo de vida do token, já que tudo isso agora pode ser feito pelo Serviço de Bot do Azure.

Os recursos incluem:

- Melhorias para os canais para dar suporte a novos recursos de autenticação, como novas bibliotecas de WebChat e DirectLineJS para eliminar a necessidade da verificação de código mágico de 6 dígitos.
- Melhorias para o Portal do Azure para adicionar, excluir e definir as configurações de conexão para vários provedores de identidade OAuth.
- Suporte para uma variedade de provedores de identidade de out-of-the-box, incluindo o Azure AD (pontos de extremidade v1 e v2), GitHub e outros.
- Atualizações dos SDKs do Bot Framework para Node.js e C# para recuperar tokens, criar OAuthCards e manipular eventos de TokenResponse.
- Exemplos de como criar um bot que autentica para o Azure AD (pontos de extremidade v1 e v2) e para o GitHub.

Você pode extrapolar das etapas neste artigo para adicionar recursos a um bot existente. A seguir estão os bots de exemplo que demonstram os novos recursos de autenticação

| Amostra | Versão do BotBuilder | Descrição |
|:---|:---:|:---|
| [AadV1Bot](https://aka.ms/AadV1Bot) | v3 | Demonstra o suporte de OAuthCard no SDK do C# v3, usando o ponto de extremidade do Azure AD v1 |
| [AadV2Bot](https://aka.ms/AadV2Bot) | v3 |  Demonstra o suporte de OAuthCard no SDK do C# v3, usando o ponto de extremidade do Azure AD v2 |
| [GitHubBot](https://aka.ms/GitHubBot) | v3 |  Demonstra o suporte de OAuthCard no SDK do C# v3, usando o GitHub |
| [BasicOAuth](https://aka.ms/BasicOAuth) | v3 |  Demonstra o suporte de OAuth 2.0 no SDK do C# v3 |

> [!NOTE]
> Os recursos de autenticação também trabalham com Node. js com BotBuilder v3. No entanto, este artigo aborda apenas o código C# de exemplo.

Para obter mais informações e suporte, consulte [Recursos adicionais do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help).

## <a name="overview"></a>Visão geral

Este tutorial cria um bot de exemplo que se conecta ao Microsoft Graph usando um token do Azure AD v1 ou v2. <!--verify this info and fix wording--> Como parte desse processo, você usará o código de um repositório GitHub, e este tutorial descreve como configurar tudo, incluindo o aplicativo bot.

- [Crie seu bot e um aplicativo de autenticação](#create-your-bot-and-an-authentication-application)
- [Prepare os códigos de exemplo de bot.](#prepare-the-bot-sample-code)
- [Use o Emulador para testar seu bot](#use-the-emulator-to-test-your-bot)

Para concluir essas etapas, você precisará do Visual Studio 2017, o npm, o node e o git instalados. Você também deve ter alguma familiaridade com o Azure, OAuth 2.0 e o desenvolvimento de bot.

Depois de concluir, você terá um bot que pode responder a algumas tarefas simples em relação a um aplicativo do Azure AD, como verificar e enviar um email ou exibir quem você é e quem seu gerente é. Para fazer isso, seu bot usará um token de um aplicativo do Azure AD com a biblioteca Microsoft.Graph.

A seção final divide uma parte do código do bot

- [Observações sobre o fluxo de recuperação de token](#notes-on-the-token-retrieval-flow)

## <a name="create-your-bot-and-an-authentication-application"></a>Criar seu bot e um aplicativo de autenticação

Você precisa criar um bot de registro em que você definirá o ponto de extremidade de mensagens para o seu código de bot implantado, e precisa criar um aplicativo do Azure AD (v1 ou v2) para permitir que seu bot acesse o Office 365.

> [!NOTE]
> Esses recursos de autenticação funcionam com outros tipos de bots. No entanto, este tutorial usa um bot somente do registro.

### <a name="register-an-application-in-azure-ad"></a>Registrar um aplicativo no Azure AD

Você precisa de um aplicativo do Azure AD que seu bot possa usar como um provedor de identidade para se conectar à API do Microsoft Graph.

Para este bot, você pode usar pontos de extremidade do Azure AD v1 ou v2.
Para obter informações sobre as diferenças entre os pontos de extremidade v1 e v2, consulte a [comparação v1-v2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) e a [visão geral de ponto de extremidade do Azure AD v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

#### <a name="create-an-azure-ad-identity-provider-application"></a>Criar um aplicativo de provedor de identidade do Azure AD 

Use essas etapas para criar um novo aplicativo do Azure Active Directory. Você pode usar os pontos de extremidade v1 ou v2 com o aplicativo criado.

> [!TIP]
> Você precisará criar e registrar o aplicativo do Azure AD em um locatário no qual você pode consentir em delegar permissões solicitadas por um aplicativo.

1. Abra o painel [Azure Active Directory][azure-aad-blade] no portal do Azure.
    Se você não estiver no locatário correto, clique em **Trocar de diretório** para trocar para o locatário correto. (Para obter instruções sobre como criar um locatário, confira [Acessar o portal e criar um locatário](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).)
1. Abra o painel **Registros de aplicativo**.
1. No painel **Registros de aplicativo**, clique em **Novo registro**.
1. Preencha os campos obrigatórios e crie o registro do aplicativo.

   1. Nome do seu aplicativo.
   1. Selecione os **Tipos de conta compatíveis** para o aplicativo.
   1. Para o **URI de redirecionamento**
       1. Selecione **Web**.
       1. Defina a URL como `https://token.botframework.com/.auth/web/redirect`.
   1. Clique em **Registrar**.

      - Depois de criado, o Azure exibe a página **Visão geral** do aplicativo.
      - Registre o valor da **ID do aplicativo (cliente)** . Você usará esse valor posteriormente como a _ID do cliente_ ao registrar seu aplicativo do Azure AD com seu bot.
      - Registre também o valor da **ID do diretório (locatário)** . Você também usará essas informações para registrar esse aplicativo no bot.
 
    > [!NOTE]
    > Quando os tipos de conta compatíveis forem definidos como locatário único, se você usar uma assinatura pessoal em vez de uma conta Microsoft, o emulador emitirá o erro: *A ID do aplicativo Microsoft do bot ou a senha do aplicativo Microsoft está incorreta.* Nesse caso, os tipos de conta compatíveis devem ser definidos como *Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas pessoais Microsoft (por exemplo, Skype, Xbox)* .

1. No painel de navegação, clique em **Certificados e segredos** para criar um segredo para o aplicativo.

   1. Em **Segredos de cliente**, clique em **Novo segredo do cliente**.
   1. Adicione uma descrição para identificar esse segredo em relação a outros que talvez você precise criar para esse aplicativo, como `bot login`.
   1. Defina **Expira** como **Nunca**.
   1. Clique em **Adicionar**.
   1. Antes de sair desta página, registre o segredo. Você usará esse valor posteriormente como o _Segredo do cliente_ ao registrar seu aplicativo do Azure AD com seu bot.

1. No painel de navegação, clique em **Permissões de API** para abrir o painel **Permissões de API**. Uma prática recomendada é definir explicitamente as permissões de API para o aplicativo.

   1. Clique em **Adicionar uma permissão** para mostrar o painel **Permissões da API de solicitação**.
   1. Para este exemplo, selecione as **APIs da Microsoft** e o **Microsoft Graph**.
   1. Escolha **Permissões delegadas** e verifique se as permissões necessárias estão selecionadas. Este exemplo requer estas permissões.

      > [!NOTE]
      > As permissões marcadas como **CONSENTIMENTO DO ADMINISTRADOR NECESSÁRIO** exigirão um usuário e um administrador de locatário para fazer logon, portanto, evite-os para seu bot.

      - **openid**
      - **profile**
      - **Mail.Read**
      - **Mail.Send**
      - **User.Read**
      - **User.ReadBasic.All**

   1. Clique em **Adicionar permissões**. (Na primeira vez que um usuário acessar esse aplicativo por meio do bot, ele precisará dar consentimento.)

Agora você tem um aplicativo do Azure AD configurado.

### <a name="create-your-bot-on-azure"></a>Criar seu bot no Azure

Crie um **Registro de Canais de Bot** usando o [Portal do Azure](https://portal.azure.com/).

### <a name="register-your-azure-ad-application-with-your-bot"></a>Registrar seu aplicativo do Azure AD com seu bot

A próxima etapa é registrar com seu bot o aplicativo do Azure AD que você acabou de criar.

# <a name="azure-ad-v1tabaadv1"></a>[Azure AD v1](#tab/aadv1)

1. Navegue até a página de recurso do seu bot no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você usará esse nome em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo (cliente) que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira o segredo que você criou para permitir que o bot acesse o aplicativo do Azure AD.
    1. Para **Tipo de concessão**, insira `authorization_code`.
    1. Para **URL de logon**, insira `https://login.microsoftonline.com`.
    1. Para **ID do locatário**, insira o diretório (locatário) da ID que você registrou anteriormente para o aplicativo do Azure AD.

       Esse será o locatário associado aos usuários que podem ser autenticados.

    1. Para a **URL do Recurso**, insira `https://graph.microsoft.com/`.
    1. Deixe **Escopos** em branco.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

# <a name="azure-ad-v2tabaadv2"></a>[Azure AD v2](#tab/aadv2)

1. Navegue até a página Registro de Canais de Bot do seu bot no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você o usará em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory v2**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo (cliente) que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira o segredo que você criou para permitir que o bot acesse o aplicativo do Azure AD.
    1. Para **ID do locatário**, insira o diretório (locatário) da ID que você registrou anteriormente para o aplicativo do Azure AD.

       Esse será o locatário associado aos usuários que podem ser autenticados.

    1. Para **Escopos**, digite os nomes da permissão que você escolheu no registro do aplicativo: `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Para o Azure AD v2, **Escopos** usa uma lista de valores que diferencia maiúsculas de minúsculas separadas por espaços.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

---

#### <a name="to-test-your-connection"></a>Para testar sua conexão

1. Abra a conexão que você acabou de criar.
1. Clique em **Testar Conexão** na parte superior do painel **Configuração de Conexão do Provedor de Serviços**.
1. Na primeira vez, deve abrir uma nova guia do navegador listando as permissões que seu aplicativo está solicitando e solicitará que você aceite.
1. Clique em **Aceitar**.
1. Em seguida, deverá direcioná-lo para uma página **Testar conexão para '< your-connection-name >' bem sucedido**.

## <a name="prepare-the-bot-sample-code"></a>Preparar os códigos de exemplo de bot

1. Clone o repositório Github em https://github.com/Microsoft/BotBuilder.
1. Abra e crie a solução, `BotBuilder\CSharp\Microsoft.Bot.Builder.sln`.
1. Feche essa solução e abra, `BotBuilder\CSharp\Samples\Microsoft.Bot.Builder.Samples.sln`.
1. Defina o projeto de inicialização.
    - Para um bot que usa o aplicativo do Azure AD v1, use o projeto `Microsoft.Bot.Sample.AadV1Bot`.
    - Para um bot que usa o aplicativo do Azure AD v2, use o projeto `Microsoft.Bot.Sample.AadV2Bot`.
1. Abra o arquivo `Web.config` e modifique as configurações do aplicativo da seguinte maneira:
    1. Defina o `ConnectionName` com o valor usado ao definir a configuração de conexão OAuth 2.0 do seu bot.
    1. Defina o valor `MicrosoftAppId` para a ID do aplicativo do seu bot.
    1. Defina o valor `MicosoftAppPassword` para o segredo do seu bot.

    > [!IMPORTANT]
    > Dependendo dos caracteres no seu segredo, você pode precisar que XML ignore a senha. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    ```xml
    <appSettings>
        <add key="ConnectionName" value="<your-AAD-connection-name>"/>
        <add key="MicrosoftAppId" value="<your-bot-appId>" />
        <add key="MicrosoftAppPassword" value="<your-bot-password>" />
    </appSettings>
    ```

    Se você não souber como obter seus valores de **ID do aplicativo Microsoft** e **senha de aplicativo do Microsoft**, você pode criar uma nova senha conforme descrito aqui: [bot-channels-registration-password](bot-service-quickstart-registration.md#get-registration-password); ou recuperar a **ID do aplicativo Microsoft** e a **senha de aplicativo do Microsoft** provisionadas com o **registro de canais do Bot** com a seguinte implantação:  [find-your-azure-bots-appid-and-appsecret](https://blog.botframework.com/2018/07/03/find-your-azure-bots-appid-and-appsecret)

    > [!NOTE]
    > Agora você pode publicar esse código de bot em sua assinatura do Azure (clique com o botão direito no projeto e escolha **Publicar**), mas não é necessário para este tutorial. Você precisará definir uma configuração de publicação que usa o aplicativo e o plano de hospedagem que você usou ao configurar o bot no Portal do Azure.

## <a name="use-the-emulator-to-test-your-bot"></a>Usar o Emulador para testar seu bot

Você precisará instalar o [Emulador de Bot](https://github.com/Microsoft/BotFramework-Emulator) para testar seu bot localmente. Você pode usar o Emulador v3 ou v4.

1. Inicie seu bot (com ou sem depuração).
1. Anote o número da porta do localhost da página. Essa informação será necessária para interagir com o bot.
1. Inicie o Emulador.
1. Conecte-se ao seu bot.

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

Quando um usuário pede ao bot para fazer algo que requer que o bot tenha o usuário conectado, o bot pode usar o `Microsoft.Bot.Builder.Dialogs.GetTokenDialog` para iniciar a recuperação de um token para uma determinada conexão. Os próximos dois snippets de código são obtidos da classe `GetTokenDialog`.

### <a name="check-for-a-cached-token"></a>Verificação de um token em cache

Nesse código, primeiro o bot faz uma verificação rápida para determinar se o Serviço de Bot do Azure já tem um token para o usuário (que é identificado pelo remetente da atividade atual) e o ConnectionName determinado (que é o nome de conexão usada na configuração). O Serviço de Bot do Azure ou já terá um token armazenado em cache ou não terá. A chamada para GetUserTokenAsync executa essa 'verificação rápida'. Se o Serviço de Bot do Azure tem um token e retorna-o, o token pode ser usado imediatamente. Se o Serviço de Bot do Azure não tem um token, esse método retornará null. Nesse caso, o bot pode enviar um OAuthCard personalizado para o usuário entrar.

```csharp
// First ask Bot Service if it already has a token for this user
var token = await context.GetUserTokenAsync(ConnectionName).ConfigureAwait(false);
if (token != null)
{
    // use the token to do exciting things!
}
else
{
    // If Bot Service does not have a token, send an OAuth card to sign in
    await SendOAuthCardAsync(context, (Activity)context.Activity);
}
```

### <a name="send-an-oauthcard-to-the-user"></a>Enviar um OAuthCard ao usuário

Você pode personalizar o OAuthCard com qualquer texto e texto de botão desejado. As partes importantes são:

- Defina o `ContentType` para `OAuthCard.ContentType`.
- Defina a propriedade `ConnectionName` para o nome da conexão que você deseja usar.
- Inclua um botão com um `CardAction` dos `Type` `ActionTypes.Signin`. Observe que você não precisa especificar nenhum valor para o link de entrada.

No final dessa chamada, o bot precisa "aguardar o token" antes de voltar. Essa espera ocorre no fluxo de atividade principal porque pode haver muitos que o usuário precisa fazer para entrar.

```csharp
private async Task SendOAuthCardAsync(IDialogContext context, Activity activity)
{
    await context.PostAsync($"To do this, you'll first need to sign in.");

    var reply = await context.Activity.CreateOAuthReplyAsync(_connectionName, _signInMessage, _buttonLabel).ConfigureAwait(false);
    await context.PostAsync(reply);

    context.Wait(WaitForToken);
}
```

### <a name="wait-for-a-tokenresponseevent"></a>Aguardar um TokenResponseEvent

Neste código, a classe de caixa de diálogo do Bot está aguardando um `TokenResponseEvent` (mais sobre como isso é roteado para a pilha da caixa de diálogo está abaixo). O método `WaitForToken` primeiro determina se esse evento foi enviado. Se foi enviado, ele pode ser usado pelo bot. Se não, o método `WaitForToken` usa qualquer texto que foi enviado para o bot e passa-o para `GetUserTokenAsync`. O motivo disso é que alguns clientes (como WebChat) não precisam do código de verificação Magic Code e podem enviar diretamente o Token no `TokenResponseEvent`. Outros clientes ainda exigem que o magic code (como Facebook ou Slack). O Serviço de Bot do Azure apresentará esses clientes com um magic code de seis dígitos e pedir ao usuário digitar isso na janela de bate-papo. Embora não seja ideal, esse é o comportamento de fallback, e se `WaitForToken` recebe um código, o bot pode enviar esse código para o Serviço de Bot do Azure e obter um token de volta. Se essa chamada também falhar, você pode decidir relatar um erro ou fazer algo diferente. Na maioria dos casos, o bot agora terá um token de usuário.

Se você procurar no arquivo **MessageController.cs**, você verá que as atividades `Event` desse tipo também são roteadas para a pilha de caixa de diálogo.

```csharp
private async Task WaitForToken(IDialogContext context, IAwaitable<object> result)
{
    var activity = await result as Activity;

    var tokenResponse = activity.ReadTokenResponseContent();
    if (tokenResponse != null)
    {
        // Use the token to do exciting things!
    }
    else
    {
        if (!string.IsNullOrEmpty(activity.Text))
        {
            tokenResponse = await context.GetUserTokenAsync(ConnectionName,
                                                               activity.Text);
            if (tokenResponse != null)
            {
                // Use the token to do exciting things!
                return;
            }
        }
        await context.PostAsync($"Hmm. Something went wrong. Let's try again.");
        await SendOAuthCardAsync(context, activity);
    }
}
```

### <a name="message-controller"></a>Controlador de mensagem

Em chamadas subsequentes para o bot, observe que o token nunca é armazenado em cache por este bot de exemplo. Isso ocorre porque o bot sempre pode pedir o token para o Serviço de Bot do Azure. Isso evita o necessidade de gerenciar o ciclo de vida de token, atualizar o token, etc., já que o Serviço de Bot do Azure faz tudo isso para você.

```csharp
else if(message.Type == ActivityTypes.Event)
{
    if(message.IsTokenResponseEvent())
    {
        await Conversation.SendAsync(message, () => new Dialogs.RootDialog());
    }
}
```
## <a name="additional-resources"></a>Recursos adicionais
[SDK do Bot Framework](https://github.com/microsoft/botbuilder)
