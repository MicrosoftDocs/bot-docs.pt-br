---
title: Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure | Microsoft Docs
description: Saiba como usar os recursos de autenticação do Serviço de Bot do Azure para adicionar o SSO ao seu bot.
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/22/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: da7c9f76c65a70acc8cbb4a12ee93fc8f99de53b
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299558"
---
<!-- 

ms.author: kamrani

Related TODO:
- Check code in [Web Chat channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat?view=azure-bot-service-4.0)
- Check guidance in [DirectLine authentication](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0)
-->

<!-- General TODO: (Feedback from CSE (Nafis))
- Add note that: token management is based on user ID
- Explain why/how to share existing website authentication with a bot.
- Risk: Even people who use a DirectLine token can be vulnerable to user ID impersonation.
    Docs/samples that show exchange of secret for token don't specify a user ID, so an attacker can impersonate a different user by modifying the ID client side. There's a [blog post](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fblog.botframework.com%2F2018%2F09%2F01%2Fusing-webchat-with-azure-bot-services-authentication%2F&data=02%7C01%7Cv-jofing%40microsoft.com%7Cee005e1c9d2c4f4e7ea508d6b231b422%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636892323874079713&sdata=b0DWMxHzmwQvg5EJtlqKFDzR7fYKmg10fXma%2B8zGqEI%3D&reserved=0) that shows how to do this properly.
"Major issues":
- This doc is a sample walkthrough, but there's no deeper documentation explaining how the Azure Bot Service is handling tokens. How does the OAuth flow work? Where is it storing my users' access tokens? What's the security and best practices around using it?

"Minor issues":
- AAD v2 steps tell you to add delegated permission scopes during registration, but this shouldn't be necessary in AAD v2 due to dynamic scopes. (Ming, "This is currently necessary because scopes are not exposed through our runtime API. We don’t currently have a way for the developer to specify which scope he wants at runtime.")

- "The scope of the connection setting needs to have both openid and a resource in the Azure AD graph, such as Mail.Read." Unclear if I need to take some action at this point to make happen. Kind of out of context. I'm registering an AAD application in the portal, there's no connection setting
- Does the bot need all of these scopes for the samples? (e.g. "Read all users' basic profiles")
-->

# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

O Serviço de Bot do Azure e o SDK v4 incluem novos recursos de autenticação do bot, fornecendo funcionalidades para facilitar o desenvolvimento de um bot que autentica usuários em vários provedores de identidade como o Azure AD (Azure Active Directory), GitHub, Uber e assim por diante. Essas funcionalidades podem aprimorar a experiência do usuário, eliminando a _verificação de código mágico_ para alguns clientes.

Antes disso, seu bot precisa incluir controladores de OAuth e links de logon, armazenar as IDs e segredos de cliente de destino e executar o gerenciamento de token de usuário. O bot solicitava que o usuário entrasse em um site, o qual gerava um _código mágico_ que poderia ser usado para o usuário verificar sua identidade.

Agora, os desenvolvedores de bots não precisam mais hospedar controladores OAuth ou gerenciar o ciclo de vida do token, já que tudo isso agora pode ser feito pelo Serviço de Bot do Azure.

Os recursos incluem:

- Melhorias para os canais para dar suporte a novos recursos de autenticação, como novas bibliotecas de WebChat e DirectLineJS para eliminar a necessidade da verificação de código mágico de 6 dígitos.
- Melhorias para o Portal do Azure para adicionar, excluir e definir as configurações de conexão para vários provedores de identidade OAuth.
- Suporte para uma variedade de provedores de identidade de out-of-the-box, incluindo o Azure AD (pontos de extremidade v1 e v2), GitHub e outros.
- Atualizações dos SDKs do Bot Framework para Node.js e C# para recuperar tokens, criar OAuthCards e manipular eventos de TokenResponse.
- Exemplos de como criar um bot que autentica para o Azure AD.

Para obter mais informações de como o Serviço de Bot do Azure lida com a autenticação, confira [Autenticação de usuário dentro de uma conversa](bot-builder-concept-authentication.md).

Você pode extrapolar das etapas neste artigo para adicionar recursos a um bot existente. Estes bots de exemplo demonstram os novos recursos de autenticação.

> [!NOTE]
> Os recursos de autenticação também trabalham com o BotBuilder v3. No entanto, este artigo aborda apenas o código de exemplo v4.

### <a name="about-this-sample"></a>Sobre este exemplo

Você precisa criar um recurso de bot do Azure e:

1. Um registro de aplicativos do Azure AD para permitir que o bot acesse um recurso externo, como o Office 365.
1. Um recurso de bot separado. O recurso de bot registra as credenciais do bot e você precisa dessas credenciais para testar os recursos de autenticação, mesmo ao executar o código do bot localmente.

> [!IMPORTANT]
> Sempre que você registra um bot no Azure, um aplicativo do Azure Active Directory é atribuído a ele. No entanto, esse aplicativo protege o acesso do canal ao bot. Você precisa de um aplicativo AAD adicional para cada aplicativo que você deseja que possa ser autenticado pelo bot em nome do usuário.

Este artigo cria um bot de exemplo que se conecta ao Microsoft Graph usando um token do Azure AD v1 ou v2. Ele também aborda como criar e registrar o aplicativo associado do Azure Active Directory. Como parte desse processo, você usará o código do repositório do GitHub [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples). Este artigo aborda esses processos.

- **Criar seu recurso de bot**
- **Criar um aplicativo do Azure AD**
- **Registrar seu aplicativo do Azure AD com seu bot**
- **Prepare os códigos de exemplo de bot.**

Depois de concluir, você terá um bot sendo executado localmente e que pode responder a algumas tarefas simples em relação a um aplicativo do Azure Active Directory, como verificar e enviar um email ou exibir quem é você e quem é o seu gerente. Para fazer isso, seu bot usará um token de um aplicativo do Azure AD com a biblioteca Microsoft.Graph. Você não precisa publicar seu bot para testar os recursos de entrada do OAuth. No entanto, seu bot precisará de uma ID válida de aplicativo do Azure e senha.

### <a name="web-chat-and-direct-line-considerations"></a>Considerações de Webchat e Direct Line

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

> [!IMPORTANT]
> Tenha em mente estas importantes [Considerações de segurança](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md#security-considerations).

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [fundamentos do bot][concept-basics], [gerenciamento de estado][concept-state], [biblioteca de caixas de diálogo][concept-dialogs], como [implementar o fluxo de conversa sequencial][simple-dialog] e como [reutilizar caixas de diálogo][component-dialogs].
- Conhecimento do desenvolvimento do Azure e do OAuth 2.0.
- Visual Studio 2017 ou posterior, Node.js, npm e git.
- Um desses exemplos.

| Amostra | Versão do BotBuilder | Demonstra |
|:---|:---:|:---|
| **Autenticação de bot** em [**CSharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] | v4 | Suporte de OAuthCard |
| **Autenticação de bot MSGraph** em [**CSharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] | v4 |  Suporte da API do Microsoft Graph com o OAuth 2 |

## <a name="create-your-bot-resource-on-azure"></a>Criar seu recurso de bot no Azure

Criar um **recurso de bot** usando o [portal do Azure](https://portal.azure.com/).

Para obter mais informações, confira [Criar um bot com o Serviço de Bot do Azure](./abs-quickstart.md).

## <a name="create-and-register-an-azure-ad-application"></a>Criar e registrar um aplicativo Azure Active Directory

Você precisa de um aplicativo do Azure AD que seu bot possa usar para se conectar à API do Microsoft Graph.

Para este bot, você pode usar pontos de extremidade do Azure AD v1 ou v2.
Para obter informações sobre as diferenças entre os pontos de extremidade v1 e v2, consulte a [comparação v1-v2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) e a [visão geral de ponto de extremidade do Azure AD v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

### <a name="create-your-azure-ad-application"></a>Criar seu aplicativo do Azure Active Directory

Use essas etapas para criar um novo aplicativo do Azure Active Directory. Você pode usar os pontos de extremidade v1 ou v2 com o aplicativo criado.

> [!TIP]
> Você precisará criar e registrar o aplicativo do Azure AD em um locatário no qual você pode consentir em delegar permissões solicitadas por um aplicativo.

1. Abra o painel do [Azure Active Directory][azure-aad-blade] no portal do Azure.
    Se você não estiver no locatário correto, clique em **Trocar de diretório** para trocar para o locatário correto. (Para obter instruções sobre como criar um locatário, confira [Acessar o portal e criar um locatário](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).)
1. Abra o painel **Registros de aplicativo**.
1. No painel **Registros de aplicativo**, clique em **Novo registro**.
1. Preencha os campos obrigatórios e crie o registro do aplicativo.

   1. Nome do seu aplicativo.
   1. Selecione os **Tipos de conta compatíveis** para o aplicativo. (Qualquer uma dessas opções funcionará com este exemplo.)
   1. Para o **URI de redirecionamento**
       1. Selecione **Web**.
       1. Defina a URL como `https://token.botframework.com/.auth/web/redirect`.
   1. Clique em **Registrar**.

      - Depois de criado, o Azure exibe a página **Visão geral** do aplicativo.
      - Registre o valor da **ID do aplicativo (cliente)** . Você usará esse valor posteriormente como a _ID do cliente_ ao registrar seu aplicativo do Azure AD com seu bot.
      - Registre também o valor da **ID do diretório (locatário)** . Você também usará essas informações para registrar esse aplicativo no bot.

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

### <a name="register-your-azure-ad-application-with-your-bot"></a>Registrar seu aplicativo do Azure AD com seu bot

A próxima etapa é registrar com seu bot o aplicativo do Azure AD que você acabou de criar.

#### <a name="azure-ad-v1"></a>Azure AD v1

1. Navegue até a página de recurso do seu bot no [Portal do Azure](http://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você usará esse nome em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo (cliente) que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira o segredo que você criou para permitir que o bot acesse o aplicativo do Azure AD.
    1. Para **Tipo de concessão**, insira `authorization_code`.
    1. Para **URL de logon**, insira `https://login.microsoftonline.com`.
    1.Para **ID do Locatário**, insira a **ID do diretório (locatário)** que você registrou anteriormente para seu aplicativo AAD ou **comum** dependendo dos tipos de conta com suporte selecionados quando você criou o aplicativo AAD. Para decidir qual valor atribuir, siga estes critérios:

        - Ao criar o aplicativo AAD, se você tiver selecionado *Contas neste diretório organizacional apenas (somente Microsoft – Locatário único)* ou *Contas em qualquer diretório organizacional (diretório do Microsoft AAD – multilocatário)* , insira a **ID do locatário** que você registrou anteriormente para o aplicativo do AAD.

        - Contudo, se você tiver selecionado *Contas em qualquer diretório organizacional (Qualquer diretório do AAD – contas Microsoft multilocatário e pessoais, por ex. Skype, Xbox, Outlook.com)* , insira a palavra **comum** em vez de uma ID de locatário. Caso contrário, o aplicativo AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.

       Esse será o locatário associado aos usuários que podem ser autenticados.

    1. Para a **URL do Recurso**, insira `https://graph.microsoft.com/`.
    1. Deixe **Escopos** em branco.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

#### <a name="azure-ad-v2"></a>Azure AD v2

1. Navegue até a página Registro de Canais de Bot do seu bot no [Portal do Azure](http://portal.azure.com/).
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você o usará em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory v2**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo (cliente) que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira o segredo que você criou para permitir que o bot acesse o aplicativo do Azure AD.
    1. Para **ID do Locatário**, insira a **ID do diretório (locatário)** que você registrou anteriormente para seu aplicativo AAD ou **comum** dependendo dos tipos de conta com suporte selecionados quando você criou o aplicativo AAD. Para decidir qual valor atribuir, siga estes critérios:

        - Ao criar o aplicativo AAD, se você tiver selecionado *Contas neste diretório organizacional apenas (somente Microsoft – Locatário único)* ou *Contas em qualquer diretório organizacional (diretório do Microsoft AAD – multilocatário)* , insira a **ID do locatário** que você registrou anteriormente para o aplicativo do AAD.

        - Contudo, se você tiver selecionado *Contas em qualquer diretório organizacional (Qualquer diretório do AAD – contas Microsoft multilocatário e pessoais, por ex. Skype, Xbox, Outlook.com)* , insira a palavra **comum** em vez de uma ID de locatário. Caso contrário, o aplicativo AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.

       Esse será o locatário associado aos usuários que podem ser autenticados.

    1. Para **Escopos**, digite os nomes da permissão que você escolheu no registro do aplicativo: `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Para o Azure AD v2, **Escopos** usa uma lista de valores que diferencia maiúsculas de minúsculas separadas por espaços.

1. Clique em **Save** (Salvar).

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.

### <a name="test-your-connection"></a>Testar sua conexão

1. Clique na entrada de conexão para abrir a conexão que você acabou de criar.
1. Clique em **Testar Conexão** na parte superior do painel **Configuração de Conexão do Provedor de Serviços**.
1. Na primeira vez, deve abrir uma nova guia do navegador listando as permissões que seu aplicativo está solicitando e solicitará que você aceite.
1. Clique em **Aceitar**.
1. Isso deverá direcionar você para uma página **Testar conexão para \<nome-da-sua-conexão> bem-sucedida**.

Agora você pode usar esse nome de conexão no código do bot para recuperar tokens de usuário.

## <a name="prepare-the-bot-code"></a>Como preparar o código do bot

Você precisará da ID de aplicativo e da senha do seu bot para concluir este processo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

<!-- TODO: Add guidance (once we have it) on how not to hard-code IDs and ABS auth. -->

1. Clone do repositório do GitHub o exemplo com o qual deseja trabalhar: [**Autenticação de bot**][cs-auth-sample] ou [**Autenticação de bot MSGraph**][cs-msgraph-sample].
1. Atualize **appsettings.json**:

    - Defina `ConnectionName` como o nome da configuração de conexão do OAuth que você adicionou ao seu bot.
    - Defina `MicrosoftAppId` e `MicrosoftAppPassword` como a ID e o segredo de aplicativo do seu bot.

      Dependendo dos caracteres do segredo do bot, você pode precisar ignorar a senha com XML. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    [!code-json[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/appsettings.json)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Crie um clone a partir do repositório do github contendo aquilo com o que você deseja trabalhar: [**Autenticação de bot**][js-auth-sample] ou [**Autenticação de bot MSGraph**][js-msgraph-sample].
1. Atualize **.env**:

    - Defina `connectionName` como o nome da configuração de conexão do OAuth que você adicionou ao seu bot.
    - Defina os valores `MicrosoftAppId` e `MicrosoftAppPassword` como a ID e o segredo de aplicativo de seu bot.

      Dependendo dos caracteres do segredo do bot, você pode precisar ignorar a senha com XML. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    [!code-txt[.env](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/.env)]

---

Se você não souber como obter os valores da **ID do aplicativo Microsoft** e da **senha do aplicativo Microsoft**, crie uma senha [conforme a descrição abaixo](../bot-service-quickstart-registration.md#get-registration-password)

> [!NOTE]
> Agora você poderia publicar esse código de bot em sua assinatura do Azure (clique com o botão direito no projeto e escolha **Publicar**), mas isso não é necessário para este artigo. Você precisará definir uma configuração de publicação que usa o aplicativo e o plano de hospedagem que você usou ao configurar o bot no Portal do Azure.

## <a name="test-the-bot"></a>Testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte-se ao seu bot e envie as mensagens.

    - Você terá que fornecer a ID e a senha de aplicativo do seu bot quando se conectar com ele.

        - Se você precisar usar um escape XML na senha no código do bot, isso também precisará ser feito aqui.

    - Digite `help` para ver uma lista dos comandos disponíveis para o bot e testar os recursos de autenticação.
    - Quando você tiver entrado, você não precisa fornecer suas credenciais novamente até sair.
    - Para sair e cancelar a sua autenticação, digite `logout`.

> [!NOTE]
> A autenticação do bot requer o uso do Serviço de Conector de Bot. O serviço acessa as informações de registro de canais do seu bot.

# <a name="bot-authenticationtabbot-oauth"></a>[Autenticação de bot](#tab/bot-oauth)

No exemplo **Autenticação de Bot**, o diálogo foi projetado para recuperar o token de usuário após este se conectar.

![Saída de exemplo](media/how-to-auth/auth-bot-test.png)

# <a name="bot-authentication-msgraphtabbot-msgraph-auth"></a>[Autenticação de bot MSGraph](#tab/bot-msgraph-auth)

No exemplo **Autenticação de Bot MSGraph**, o diálogo é projetado para aceitar um conjunto limitado de comandos depois que o usuário faz logon.

![Saída de exemplo](media/how-to-auth/msgraph-bot-test.png)

---

## <a name="additional-information"></a>Informações adicionais

Quando um usuário pede ao bot para fazer algo que requer que o bot tenha o usuário conectado, o bot pode usar um `OAuthPrompt` para iniciar a recuperação de um token para uma determinada conexão. O `OAuthPrompt` cria um fluxo de recuperação de token composto por:

1. Verificação se o Serviço de Bot do Azure já tem um token para o usuário e a conexão atuais. Se houver um token, o token será retornado.
1. Se o Serviço de Bot do Azure não tiver um token em cache, um `OAuthCard` será criado, o qual é um botão de entrada no qual o usuário pode clicar.
1. Após o usuário clicar no botão `OAuthCard`, o Serviço de Bot do Azure enviará diretamente ao bot o token do usuário ou apresentará ao usuário um código de autenticação de 6 dígitos para inserir na janela de chat.
1. Se o usuário receber um código de autenticação, o bot trocará esse código de autenticação pelo token do usuário.

As seções a seguir descrevem como o exemplo implementa algumas tarefas de autenticação comuns.

### <a name="use-an-oauth-prompt-to-sign-the-user-in-and-get-a-token"></a>Use um prompt do OAuth para conectar o usuário e obter um token

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

![Arquitetura de bot](media/how-to-auth/architecture.png)

<!-- The two authentication samples have nearly identical architecture. Using 18.bot-authentication for the sample code. -->

**Dialogs\MainDialog.cs**

Adicione um prompt do OAuth a **MainDialog** em seu construtor. Aqui, o valor para o nome da conexão foi recuperado do arquivo **appsettings.JSON**.

[!code-csharp[Add OAuthPrompt](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=23-31)]

Dentro de uma etapa de diálogo, use `BeginDialogAsync` para iniciar o prompt do OAuth, que pede ao usuário para entrar.

- Se o usuário já estiver conectado, isso irá gerar um evento de resposta de token, sem avisar o usuário.
- Caso contrário, será solicitado que o usuário entre. O Serviço de Bot do Azure envia o evento de resposta do token depois que o usuário tenta entrar.

[!code-csharp[Use the OAuthPrompt](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=49)]

Dentro da etapa de diálogo a seguir, procure a presença de um token no resultado da etapa anterior. Se não for nulo, o usuário foi conectado com êxito.

[!code-csharp[Get the OAuthPrompt result](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=54-56)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![Arquitetura de bot](media/how-to-auth/architecture-js.png)

**dialogs/mainDialog.js**

Adicione um prompt do OAuth a **MainDialog** em seu construtor. Aqui, o valor para o nome da conexão foi recuperado do arquivo **.env**.

[!code-javascript[Add OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=24-29)]

Dentro de uma etapa de diálogo, use `beginDialog` para iniciar o prompt do OAuth, que pede ao usuário para entrar.

- Se o usuário já estiver conectado, isso irá gerar um evento de resposta de token, sem avisar o usuário.
- Caso contrário, será solicitado que o usuário entre. O Serviço de Bot do Azure envia o evento de resposta do token depois que o usuário tenta entrar.

[!code-javascript[Use OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=57)]

Dentro da etapa de diálogo a seguir, procure a presença de um token no resultado da etapa anterior. Se não for nulo, o usuário foi conectado com êxito.

[!code-javascript[Get OAuthPrompt result](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=62-63)]

---

### <a name="wait-for-a-tokenresponseevent"></a>Aguardar um TokenResponseEvent

Quando iniciamos um prompt do OAuth, ele aguarda um evento de resposta de token, a partir do qual ele recupera o token do usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\AuthBot.cs**

**AuthBot** deriva de `ActivityHandler` e lida explicitamente com atividades de evento de resposta de token. Aqui, damos sequência ao diálogo ativo, que permite que o prompt do OAuth processe o evento e recupere o token.

[!code-csharp[OnTokenResponseEventAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Bots/AuthBot.cs?range=32-38)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/authBot.js**

**AuthBot** deriva de `ActivityHandler` e lida explicitamente com atividades de evento de resposta de token. Aqui, damos sequência ao diálogo ativo, que permite que o prompt do OAuth processe o evento e recupere o token.

[!code-javascript[onTokenResponseEvent](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/bots/authBot.js?range=29-31)]

---

### <a name="log-the-user-out"></a>Desconecte o usuário

Considera-se melhor prática permitir que os usuários saiam ou se desconectem explicitamente, em vez de esperar que a conexão atinja o tempo limite.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\LogoutDialog.cs**

[!code-csharp[Allow logout](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/LogoutDialog.cs?range=44-61&highlight=11)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/logoutDialog.js**

[!code-javascript[Allow logout](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/logoutDialog.js?range=31-42&highlight=7)]

---

### <a name="adding-teams-authentication"></a>Como adicionar autenticação do Teams

O Teams se comporta de maneira um pouco diferente de outros canais em relação ao OAuth e exige algumas alterações para implementar a autenticação corretamente. Adicionaremos o código do exemplo de Bot de Autenticação do Teams ([C#][cs-teams-auth-sample]/[JavaScript][js-teams-auth-sample]).
 
Uma diferença entre outros canais e o Temas é o Teams envia uma atividade de *invocação* para o bot, em vez de uma atividade de *evento*. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
**Bots/TeamsBot.cs** [!code-csharp[Invoke Activity](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/TeamsBot.cs?range=34-42&highlight=1)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
**bots/teamsBot.js** [!code-javascript[Invoke Activity](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/teamsBot.js?range=27-32&highlight=3)]

---

Se você usar um *prompt OAuth*, essa atividade de invocação deverá ser encaminhada para a caixa de diálogo. Faremos isso no `TeamsActivityHandler`. Adicione o código a seguir ao arquivo de caixa de diálogo principal. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
**Bots/DialogBot.cs** [!code-csharp[Dialogs Handler](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/DialogBot.cs?range=18)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
**Bots/dialogBot.js** [!code-javascript[Dialogs Handler](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/dialogBot.js?range=4-6)]

---
Por fim, não deixe de adicionar um arquivo `TeamsActivityHandler` apropriado (`TeamsActivityHandler.cs` para bots C# e `teamsActivityHandler.js` para bots do JavaScript) no nível mais alto na pasta do bot.

O `TeamsActivityHandler` também envia atividades de *reação de mensagem*. Uma atividade de reação de mensagem faz referência à atividade original usando o campo *responder à ID*. Essa atividade também deve ser visível por meio do [Feed de Atividades][teams-activity-feed] no Microsoft Teams.

> [!NOTE]
> Você precisa criar um manifesto e incluir `token.botframework.com` na seção `validDomains`; caso contrário, o botão **Entrar** do OAuthCard não abrirá a janela de autenticação. Use o [App Studio](https://docs.microsoft.com/en-us/microsoftteams/platform/get-started/get-started-app-studio) para gerar seu manifesto.

### <a name="further-reading"></a>Leitura adicional

- Os [Recursos adicionais do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help) incluem links para obter suporte adicional.
- O repositório [SDK do Bot Framework](https://github.com/microsoft/botbuilder) tem mais informações sobre repositórios, amostras, ferramentas e especificações associadas ao SDK do Bot Builder.

<!-- Footnote-style links -->

[Azure portal]: https://ms.portal.azure.com
[azure-aad-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview
[aad-registration-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview

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
[cs-teams-auth-sample]:https://aka.ms/cs-teams-auth-sample
[js-teams-auth-sample]:https://aka.ms/js-teams-auth-sample
[teams-activity-feed]:[https://aka.ms/teams-activity-feed
