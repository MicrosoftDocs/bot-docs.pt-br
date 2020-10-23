---
title: Adicionar autenticação a um bot por meio do serviço de bot do Azure
description: Descreve como usar os recursos de autenticação do serviço de bot do Azure para adicionar o SSO ao bot.
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/7/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c28f019adbd1a607ef68e790d624994821556d20
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416998"
---
<!--
Related TODO:
- Check code in [Web Chat channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat?view=azure-bot-service-4.0)
- Check guidance in [DirectLine authentication](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0)


General TODO: (Feedback from CSE (Nafis))
- Add note that: token management is based on user ID
- Explain why/how to share existing website authentication with a bot.
- Risk: Even people who use a DirectLine token can be vulnerable to user ID impersonation.
    Docs/samples that show exchange of secret for token don't specify a user ID, so an attacker can impersonate a different user by modifying the ID client side. There's a [blog post](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fblog.botframework.com%2F2018%2F09%2F01%2Fusing-webchat-with-azure-bot-services-authentication%2F&data=02%7C01%7Cv-jofing%40microsoft.com%7Cee005e1c9d2c4f4e7ea508d6b231b422%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636892323874079713&sdata=b0DWMxHzmwQvg5EJtlqKFDzR7fYKmg10fXma%2B8zGqEI%3D&reserved=0) that shows how to do this properly.
"Major issues":
- This doc is a sample walkthrough, but there's no deeper documentation explaining how the Azure Bot Service is handling tokens. How does the OAuth flow work? Where is it storing my users' access tokens? What's the security and best practices around using it?

"Minor issues":
- AAD v2 steps tell you to add delegated permission scopes during registration, but this shouldn't be necessary in AAD v2 due to dynamic scopes. (Ming, "This is currently necessary because scopes are not exposed through our runtime API. We don't currently have a way for the developer to specify which scope he wants at runtime.")

- "The scope of the connection setting needs to have both openid and a resource in the Azure AD graph, such as Mail.Read." Unclear if I need to take some action at this point to make happen. Kind of out of context. I'm registering an AAD application in the portal, there's no connection setting
- Does the bot need all of these scopes for the samples? (e.g. "Read all users' basic profiles")
-->

# <a name="add-authentication-to-a-bot"></a>Adicionar autenticação a um bot

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O SDK v4 do serviço de bot do Azure facilita o desenvolvimento de bots que podem acessar recursos online que exigem autenticação do usuário. O bot não precisa gerenciar tokens de autenticação porque o Azure faz isso para você usando o OAuth 2,0 para gerar um token baseado nas credenciais de cada usuário. O bot usa o token gerado pelo Azure para acessar esses recursos. Dessa forma, o usuário não precisa fornecer a ID nem a senha para o bot para acessar um recurso protegido, mas apenas para um provedor de identidade confiável.

Para obter uma visão geral de como a estrutura de bot lida com esse tipo de autenticação, consulte [autenticação do usuário](bot-builder-concept-authentication.md).

> [!NOTE]
> A autenticação também funciona com o BotBuilder v3. No entanto, este artigo aborda apenas o código de exemplo v4.

Este artigo faz referência a dois exemplos. Um deles mostra como obter um token de autenticação. A outra é mais complexa e mostra como acessar [Microsoft Graph](https://developer.microsoft.com/en-us/graph) em nome do usuário. Em ambos os casos, você pode usar o Azure AD (Azure Active Directory) v1 ou o Azure AD v2 como um provedor de identidade para obter um token OAuth para o bot.
Este artigo explica como:

- [Criar o registro de bot do Azure](#create-the-azure-bot-registration)
- [Criar o provedor de identidade do Azure AD](#create-the-azure-ad-identity-provider)
- [Registrar o provedor de identidade do Azure AD com o bot](#register-the-azure-ad-identity-provider-with-the-bot)
- [Preparar o código do bot](#prepare-the-bot-code)

Depois de concluir este artigo, você terá um bot que pode responder a algumas tarefas simples. No caso do exemplo do Microsoft Graph, você pode enviar um email, mostrar quem você é e verificar os emails recentes. Você não precisa publicar o bot para testar os recursos de entrada do OAuth. No entanto, o bot precisará de uma ID do aplicativo e senha do Azure válidas.

### <a name="web-chat-and-direct-line-considerations"></a>Considerações de Webchat e Direct Line

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

> [!IMPORTANT]
> Quando você usa a autenticação do Serviço de Bot do Azure com o Webchat, há algumas considerações importantes sobre segurança que você precisa ter em mente. Para obter mais informações, confira a seção [considerações sobre segurança](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md#security-considerations) no artigo de autenticação REST.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [fundamentos do bot][concept-basics], [gerenciamento de estado][concept-state], [biblioteca de caixas de diálogo][concept-dialogs], como [implementar o fluxo de conversa sequencial][simple-dialog] e como [reutilizar caixas de diálogo][component-dialogs].
- Conhecimento do desenvolvimento do Azure e do OAuth 2.0.
- Visual Studio 2017 ou posterior para .NET.
- Node.js para Javascript.
- Python 3.6 ou 3.7 para Python.
- Um dos exemplos listados abaixo.

| Amostra | Versão do BotBuilder | Demonstra |
|:---|:---:|:---|
| **Autenticação** em [**Csharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] ou  [**Python**][python-auth-sample] | v4 | Suporte de OAuthCard |
| **MSGraph de autenticação** em [**Csharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] ou [**Python**](https://aka.ms/bot-auth-msgraph-python-sample-code)| v4 |  Suporte da API do Microsoft Graph com o OAuth 2 |

### <a name="about-the-samples"></a>Sobre as amostras

Para executar os exemplos referenciados neste artigo, você precisará do seguinte:

1. Um aplicativo do Azure AD (Azure Active Directory) para registrar um recurso de bot no Azure. Esse aplicativo permite que o bot acesse um recurso protegido externo, como o Microsoft Graph. Ele também permite que o usuário se comunique com o bot por meio de vários canais, como o WebChat.
1. Um aplicativo do Azure AD separado que funciona como o provedor de identidade. Esse aplicativo fornece as credenciais necessárias para estabelecer uma conexão OAuth entre o bot e o recurso protegido. Observe que este artigo usa o Active Directory como um provedor de identidade. Muitos outros provedores também são compatíveis.

> [!IMPORTANT]
> Sempre que você registra um bot no Azure, um aplicativo do Azure AD é atribuído a ele. No entanto, esse aplicativo protege o acesso do canal ao bot. Você precisa de um aplicativo do Azure AD adicional para cada recurso protegido externo que deseja que o bot acesse em nome do usuário.

## <a name="create-the-azure-bot-registration"></a>Criar o registro de bot do Azure

Esta seção mostra como registrar um recurso de bot com o Azure para hospedar o código do bot.

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/).
1. No painel esquerdo, selecione Criar um recurso.
1. No painel direito, pesquise os tipos de recursos que incluem a palavra *bot* e escolha **Registro de Canais do Bot**.
1. Clique em **Criar**.
1. No painel **Registro de Canais do Bot**, insira as informações necessárias. A imagem a seguir mostra um exemplo.

    ![registro de canais do bot](./media/how-to-auth/bot-channels-registratiopn.PNG)


1. Clique em **Criar automaticamente a ID do aplicativo e a senha** e selecione **Criar**.
1. Clique em **Criar ID do aplicativo no Portal de Registro de Aplicativo**. Isso abrirá uma nova página.
1. Na página **Registro do aplicativo**, clique em **Novo registro** no canto superior esquerdo.
1. Insira o nome do aplicativo de bot que você está registrando. Este artigo usa *TestingBotAuth* para o nome, mas cada bot precisa de um nome exclusivo.
1. Para **Tipos de conta com suporte**, selecione *Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas pessoais Microsoft (por exemplo, Skype, Xbox)* .
1. Clique em **Registrar**. Depois de concluído, o Azure exibe uma página de visão geral para o registro do aplicativo.
1. Copie a **ID do aplicativo (cliente)** e salve-a em um arquivo.
1. No painel esquerdo, clique em **Certificado e segredos**.
    1. Em *Segredos de cliente*, clique em **Novo segredo do cliente**.
    1. Adicione uma descrição para identificar esse segredo em relação a outros que talvez você precise criar para esse aplicativo.
    1. Defina **Expira** como **Nunca**.
    1. Clique em **Adicionar**.
    1. Copie o novo segredo do cliente e salve-o em um arquivo.
        > [!WARNING]
        > Registre o segredo por tempo suficiente para que o bot seja configurado.
        > Não mantenha uma cópia delas, a menos que você tenha um bom motivo, nesse caso, mantenha-a em um local seguro.
1. Volte para a janela *Registro do Canal do Bot* e copie a **ID do Aplicativo** e o **Segredo do cliente** nas caixas **ID do Aplicativo da Microsoft** e **Senha**, respectivamente.
1. Clique em **OK**.
1. Por fim, clique em **Criar**.

> [!NOTE]
> Você atribuirá a **ID do aplicativo (cliente)** e o **segredo do cliente**, salvo em um arquivo, às variáveis de configuração do bot: `MicrosoftAppId` e `MicrosoftAppPassword` . Consulte a seção [preparar o código de bot](#prepare-the-bot-code) .

Depois que o Azure tiver concluído o registro, o registro de canais de bot e o serviço de aplicativo bot serão incluídos no grupo de recursos selecionado.

## <a name="azure-ad-identity-service"></a>Serviço de identidade do Azure AD

O Azure AD (Azure Active Directory) é um serviço de identidade de nuvem que permite que você crie aplicativos que conectam usuários com segurança usando protocolos padrão do setor, como o OAuth2.0.

Você pode usar um destes dois serviços de identidade:

1. Plataforma do desenvolvedor do Azure AD (v1.0). Também conhecida como o ponto de extremidade do **Azure AD v1**, que permite que você crie aplicativos que conectam usuários com segurança a uma conta corporativa ou de estudante da Microsoft.
Para obter mais informações, confira a [visão geral do Azure Active Directory para desenvolvedores (v1.0)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-overview).
1. Plataforma de identidade da Microsoft (v2.0). Também conhecida como o ponto de extremidade do **Azure AD v2**, que é uma evolução da plataforma do Azure AD (v1.0). Ela permite que você crie aplicativos que se conectam em todos os provedores de identidade da Microsoft e obtenha tokens para chamar APIs da Microsoft, como o Microsoft Graph, ou outras APIs que os desenvolvedores criaram. Para obter mais informações, confira a [visão geral da plataforma de identidade da Microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

Para obter informações sobre as diferenças entre os pontos de extremidade v1 e v2, confira [Por que atualizar para a plataforma de identidade da Microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare). Para obter informações completas, confira [Plataforma de identidade da Microsoft (conhecida anteriormente como Azure Active Directory para desenvolvedores)](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="create-the-azure-ad-identity-provider"></a>Criar o provedor de identidade do Azure AD

Esta seção mostra como criar um provedor de identidade do Azure AD que usa OAuth2 para autenticar o bot. Você pode usar os pontos de extremidade do Azure AD v1 ou Azure AD v2.

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
      - Registre o valor da **ID do aplicativo (cliente)** . Você usará esse valor posteriormente como a _ID do cliente_ ao criar a cadeia de conexão e registrar o provedor do AD do Azure com o registro de bot.
      - Registre também o valor da **ID do diretório (locatário)** . Você também usará isso para registrar este aplicativo de provedor com o bot.

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

> [!NOTE]
> Você atribuirá a **ID do aplicativo (cliente)** e o **segredo do cliente**, quando criar a cadeia de conexão e registrar o provedor de identidade com o registro do bot. Consulte a próxima seção.

### <a name="register-the-azure-ad-identity-provider-with-the-bot"></a>Registrar o provedor de identidade do Azure AD com o bot

A próxima etapa é registrar o aplicativo do Azure AD que você acabou de criar com o bot.

# <a name="azure-ad-v2"></a>[Azure AD v2](#tab/aadv2)

#### <a name="azure-ad-v2"></a>Azure AD v2

1. Navegue até a página Registro de Canais de Bot do seu bot no [Portal do Azure][azure-portal].
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. **Nome**. Digite um nome para a conexão. Você o usará em seu código de bot.
    1. **Provedor de serviços**. Selecione **Azure Active Directory v2**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. **ID do cliente**. Insira a ID do aplicativo (cliente) que você registrou para seu provedor de identidade do Azure AD v2.
    1. **Segredo do cliente**. Insira o segredo que você gravou para seu provedor de identidade do Azure AD v2.
    1. **URL de troca de token**. Deixe em branco porque ele é usado somente para SSO no Azure AD v2.
    1. **ID do locatário**. Insira a **ID do diretório (locatário)** que você registrou anteriormente para seu aplicativo AAD ou **comum** , dependendo dos tipos de conta com suporte selecionados quando você criou o aplicativo DD do Azure. Para decidir qual valor atribuir, siga estes critérios:

        - Ao criar o aplicativo do Azure AD, se você tiver selecionado *Contas neste diretório organizacional somente (somente Microsoft – locatário único)* insira a **ID de locatário** que você registrou antes para o aplicativo AAD.
        - No entanto, se você selecionou *Contas em qualquer diretório organizacional (qualquer conta Microsoft pessoal e multilocatário do diretório do AAD, por exemplo, XBox, Outlook.com)* ou *Contas em qualquer diretório organizacional (diretório do Microsoft Azure AD – multilocatário)* , insira a palavra **common**, em vez de uma ID de locatário. Caso contrário, o aplicativo AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.

        Esse será o locatário associado aos usuários que podem ser autenticados. Para obter mais informações, confira [Locação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/single-and-multi-tenant-apps).

    1. Para **escopos**, insira os nomes da permissão escolhida no registro do aplicativo. Para fins de teste, você pode simplesmente inserir: `openid profile` .

        > [!NOTE]
        > Para o Azure AD v2, o campo **Escopos** usa uma lista de valores separada por espaços que diferencia maiúsculas de minúsculas.

1. Clique em **Save** (Salvar).

# <a name="azure-ad-v1"></a>[Azure AD v1](#tab/aadv1)

#### <a name="azure-ad-v1"></a>Azure AD v1

1. Navegue até a página de recurso do seu bot no [Portal do Azure][azure-portal].
1. Clique em **Configurações**.
1. Em **Configurações de Conexão do OAuth** na parte inferior da página, clique em **Adicionar configuração**.
1. Preencha o formulário da seguinte maneira:

    1. Para **Nome**, insira um nome para sua conexão. Você usará esse nome em seu código de bot.
    1. Para **Provedor de serviços**, selecione **Azure Active Directory**. Depois que você selecionar esta opção, os campos específicos do Azure AD serão exibidos.
    1. Para **ID do cliente**, insira a ID do aplicativo (cliente) que você registrou para o aplicativo do Azure AD v1.
    1. Para **Segredo do cliente**, insira o segredo que você criou para permitir que o bot acesse o aplicativo do Azure AD.
    1. Para **Tipo de concessão**, insira `authorization_code`.
    1. Para **URL de logon**, insira `https://login.microsoftonline.com`.
    1. Para **ID do Locatário**, insira a **ID do diretório (locatário)** que você registrou anteriormente para seu aplicativo do Azure AD ou **comum** dependendo dos tipos de conta com suporte selecionados quando você criou o aplicativo do ADD. Para decidir qual valor atribuir, siga estes critérios:

        - Ao criar o aplicativo do Azure AD, se você tiver selecionado *Contas neste diretório organizacional somente (somente Microsoft – locatário único)* insira a **ID de locatário** que você registrou antes para o aplicativo AAD.
        - No entanto, se você selecionou *Contas em qualquer diretório organizacional (qualquer conta Microsoft pessoal e multilocatário do diretório do AAD, por exemplo, XBox, Outlook.com)* ou *Contas em qualquer diretório organizacional (diretório do Microsoft Azure AD – multilocatário)* , insira a palavra **common**, em vez de uma ID de locatário. Caso contrário, o aplicativo AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.

       Esse será o locatário associado aos usuários que podem ser autenticados. Para obter mais informações, confira [Locação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/single-and-multi-tenant-apps).

    1. Para a **URL do Recurso**, insira `https://graph.microsoft.com/`.
    1. Deixe **Escopos** em branco.

1. Clique em **Save** (Salvar).

---

> [!NOTE]
> Esses valores permitem que seu aplicativo acesse os dados do Office 365 por meio da API do Microsoft Graph.
> Além disso, a **URL de Troca de Token** deve ser deixada em branco porque é usada para SSO somente no Azure AD v2.

### <a name="test-your-connection"></a>Testar sua conexão

1. Clique na entrada de conexão para abrir a conexão que você acabou de criar.
1. Clique em **Testar Conexão** na parte superior do painel **Configuração de Conexão do Provedor de Serviços**.
1. Na primeira vez, deve abrir uma nova guia do navegador listando as permissões que seu aplicativo está solicitando e solicitará que você aceite.
1. Clique em **Aceitar**.
1. Isso deve, então, redirecioná-lo para uma página **Teste da conexão com \<your-connection-name> bem-sucedido**.

Agora você pode usar esse nome de conexão no código do bot para recuperar tokens de usuário.

## <a name="prepare-the-bot-code"></a>Como preparar o código do bot

Você precisará da ID de aplicativo e da senha do seu bot para concluir este processo.

# <a name="c"></a>[C#](#tab/csharp)

<!-- TODO: Add guidance (once we have it) on how not to hard-code IDs and ABS auth. -->

1. Clone do repositório do GitHub o exemplo com o qual deseja trabalhar: [**Autenticação de bot**][cs-auth-sample] ou [**Autenticação de bot MSGraph**][cs-msgraph-sample].
1. Atualize **appsettings.json**:

    - Defina `ConnectionName` como o nome da configuração de conexão do OAuth que você adicionou ao seu bot.
    - Defina `MicrosoftAppId` e `MicrosoftAppPassword` como a ID e o segredo de aplicativo do seu bot.

      Dependendo dos caracteres do segredo do bot, você pode precisar ignorar a senha com XML. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    [!code-json[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/appsettings.json)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Crie um clone no repositório do github com o qual você deseja trabalhar: [**Autenticação de bot**][js-auth-sample] ou [**Autenticação de bot MSGraph**][js-msgraph-sample].
1. Atualize **.env**:

    - Defina `connectionName` como o nome da configuração de conexão do OAuth que você adicionou ao seu bot.
    - Defina os valores `MicrosoftAppId` e `MicrosoftAppPassword` como a ID e o segredo de aplicativo de seu bot.

      Dependendo dos caracteres do segredo do bot, você pode precisar ignorar a senha com XML. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    [!code-txt[.env](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/.env)]

# <a name="python"></a>[Python](#tab/python)

1. Clone o exemplo de [**Autenticação do bot**][python-auth-sample] do repositório do github.
1. Atualize **config.py**:

    - Defina `ConnectionName` como o nome da configuração de conexão do OAuth que você adicionou ao seu bot.
    - Defina `MicrosoftAppId` e `MicrosoftAppPassword` como a ID e o segredo de aplicativo do seu bot.

      Dependendo dos caracteres do segredo do bot, você pode precisar ignorar a senha com XML. Por exemplo, qualquer "e" comercial (&) será necessário a ser codificado como `&amp;`.

    [!code-python[config](~/../botbuilder-samples/samples/python/18.bot-authentication/config.py)]

---

Para obter a **ID do aplicativo** da Microsoft e os valores de **senha do aplicativo da Microsoft** , consulte [obter senha de registro](~/bot-service-manage-settings.md#get-registration-password).

> [!NOTE]
> Agora você poderia publicar esse código de bot em sua assinatura do Azure (clique com o botão direito no projeto e escolha **Publicar**), mas isso não é necessário para este artigo. Você precisaria definir uma configuração de publicação que usasse o aplicativo e o plano de hospedagem que você usou ao configurar o bot no portal do Azure.

## <a name="test-the-bot-using-the-emulator"></a>Testar o bot usando o emulador

Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme). Consulte também como [Depurar com o emulador](../bot-service-debug-emulator.md).

<!-- auth config steps -->
Para que o logon de exemplo do bot funcione é necessário configurar o emulador, conforme mostrado em [Configurar o emulador para autenticação](../bot-service-debug-emulator.md#configure-the-emulator-for-authentication).

### <a name="testing"></a>Testando

Após configurar o mecanismo de autenticação é possível executar o teste de exemplo do bot real.

> [!NOTE]
> Talvez seja necessário inserir um *código mágico* devido à maneira como a amostra de bot é implementada. Este código mágico faz parte do [RFC#7636](https://tools.ietf.org/html/rfc7636#page-5) e está lá para adicionar um elemento de segurança extra. Ao remover o código mágico, há um risco maior à segurança. É possível mitigar isso usando a segurança avançada do Direct Line que permite a configuração de domínios *válidos* que têm permissão para serem autenticados.
>
> Confira [Conectar um bot ao Direct Line](~/bot-service-channel-connect-directline.md#configure-settings) e [Recursos Avançados de Autenticação do Direct Line](https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/).


1. Execute o exemplo do bot localmente em seu computador.
1. Inicie o emulador.
1. Será necessário fornecer a ID e a senha de aplicativo do seu bot ao se conectar com ele.
    - Obtenha a ID do aplicativo e a senha do registro de aplicativo do Azure. Esses são os mesmos valores atribuídos ao aplicativo bot no arquivo `appsettings.json` ou `.env`. No emulador, atribua esses valores no arquivo de configuração ou na primeira vez que se conectar ao bot.
    - Se você precisar usar um escape XML na senha no código do bot, isso também precisará ser feito aqui.
1. Digite `help` para ver uma lista dos comandos disponíveis para o bot e testar os recursos de autenticação.
1. Quando você tiver entrado, você não precisa fornecer suas credenciais novamente até sair.
1. Para sair e cancelar a sua autenticação, digite `logout`.

> [!NOTE]
> A autenticação do bot requer o uso do Serviço de Conector de Bot. O serviço acessa as informações de registro de canais do seu bot.

## <a name="authentication-example"></a>Exemplo de autenticação

No exemplo **Autenticação de Bot**, o diálogo foi projetado para recuperar o token de usuário após este se conectar.

![imagem de teste do bot de autenticação](media/how-to-auth/auth-bot-test.png)

## <a name="authentication-msgraph-example"></a>Exemplo de MSGraph de autenticação

No exemplo **Autenticação de Bot MSGraph**, o diálogo é projetado para aceitar um conjunto limitado de comandos depois que o usuário faz logon.

![imagem de teste do bot msgraph](media/how-to-auth/msgraph-bot-test.png)

---

## <a name="additional-information"></a>Informações adicionais

Quando um usuário pede ao bot para fazer algo que requer que o bot tenha o usuário conectado, o bot pode usar um `OAuthPrompt` para iniciar a recuperação de um token para uma determinada conexão. O `OAuthPrompt` cria um fluxo de recuperação de token composto por:

1. Verificação se o Serviço de Bot do Azure já tem um token para o usuário e a conexão atuais. Se houver um token, o token será retornado.
1. Se o Serviço de Bot do Azure não tiver um token em cache, um `OAuthCard` será criado, o qual é um botão de entrada no qual o usuário pode clicar.
1. Após o usuário clicar no botão `OAuthCard`, o Serviço de Bot do Azure enviará diretamente ao bot o token do usuário ou apresentará ao usuário um código de autenticação de 6 dígitos para inserir na janela de chat.
1. Se o usuário receber um código de autenticação, o bot trocará esse código de autenticação pelo token do usuário.

As seções a seguir descrevem como o exemplo implementa algumas tarefas de autenticação comuns.

### <a name="use-an-oauth-prompt-to-sign-the-user-in-and-get-a-token"></a>Use um prompt do OAuth para conectar o usuário e obter um token

# <a name="c"></a>[C#](#tab/csharp)

![imagem da arquitetura Csharp](media/how-to-auth/architecture.png)

<!-- Submit changes for line break issues -->
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

![imagem da arquitetura js](media/how-to-auth/architecture-js.png)

**dialogs/mainDialog.js**

Adicione um prompt do OAuth a **MainDialog** em seu construtor. Aqui, o valor para o nome da conexão foi recuperado do arquivo **.env**.

[!code-javascript[Add OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=24-29)]

Dentro de uma etapa de diálogo, use `beginDialog` para iniciar o prompt do OAuth, que pede ao usuário para entrar.

- Se o usuário já estiver conectado, isso irá gerar um evento de resposta de token, sem avisar o usuário.
- Caso contrário, será solicitado que o usuário entre. O Serviço de Bot do Azure envia o evento de resposta do token depois que o usuário tenta entrar.

[!code-javascript[Use OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=57)]

Dentro da etapa de diálogo a seguir, procure a presença de um token no resultado da etapa anterior. Se não for nulo, o usuário foi conectado com êxito.

[!code-javascript[Get OAuthPrompt result](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=62-63)]

# <a name="python"></a>[Python](#tab/python)

![imagem de arquitetura Python](media/how-to-auth/architecture-python.png)

**dialogs/main_dialog.py**

Adicione um prompt do OAuth a **MainDialog** em seu construtor. Aqui, o valor do nome da conexão foi recuperado do arquivo **config.py**.

[!code-python[Add OAuthPrompt](~/../botbuilder-samples/samples/python/18.bot-authentication/dialogs/main_dialog.py?range=34-44)]

Dentro de uma etapa de diálogo, use `begin_dialog` para iniciar o prompt do OAuth, que pede ao usuário para entrar.

- Se o usuário já estiver conectado, isso irá gerar um evento de resposta de token, sem avisar o usuário.
- Caso contrário, será solicitado que o usuário entre. O Serviço de Bot do Azure envia o evento de resposta do token depois que o usuário tenta entrar.

[!code-python[Add OAuthPrompt](~/../botbuilder-samples/samples/python/18.bot-authentication/dialogs/main_dialog.py?range=49)]

Dentro da etapa de diálogo a seguir, procure a presença de um token no resultado da etapa anterior. Se não for nulo, o usuário foi conectado com êxito.

[!code-python[Add OAuthPrompt](~/../botbuilder-samples/samples/python/18.bot-authentication/dialogs/main_dialog.py?range=54-65)]

---

### <a name="wait-for-a-tokenresponseevent"></a>Aguardar um TokenResponseEvent

Quando iniciamos um prompt do OAuth, ele aguarda um evento de resposta de token, a partir do qual ele recupera o token do usuário.

# <a name="c"></a>[C#](#tab/csharp)

**Bots\AuthBot.cs**

**AuthBot** deriva de `ActivityHandler` e lida explicitamente com atividades de evento de resposta de token. Aqui, damos sequência ao diálogo ativo, que permite que o prompt do OAuth processe o evento e recupere o token.

[!code-csharp[OnTokenResponseEventAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Bots/AuthBot.cs?range=32-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/authBot.js**

**AuthBot** deriva de `ActivityHandler` e lida explicitamente com atividades de evento de resposta de token. Aqui, damos sequência ao diálogo ativo, que permite que o prompt do OAuth processe o evento e recupere o token.

[!code-javascript[onTokenResponseEvent](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/bots/authBot.js?range=29-31)]

# <a name="python"></a>[Python](#tab/python)

**bots/auth_bot.py**

**AuthBot** lida explicitamente com atividades de evento de resposta de token. Aqui, damos sequência ao diálogo ativo, que permite que o prompt do OAuth processe o evento e recupere o token.

[!code-python[on_token_response_event](~/../botbuilder-samples/samples/python/18.bot-authentication/bots/auth_bot.py?range=38-44)]

---

### <a name="log-the-user-out"></a>Desconecte o usuário

Considera-se melhor prática permitir que os usuários saiam ou se desconectem explicitamente, em vez de esperar que a conexão atinja o tempo limite.

# <a name="c"></a>[C#](#tab/csharp)

**Dialogs\LogoutDialog.cs**

[!code-csharp[Allow logout](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/LogoutDialog.cs?range=44-61&highlight=11)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/logoutDialog.js**

[!code-javascript[Allow logout](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/logoutDialog.js?range=31-42&highlight=7)]

# <a name="python"></a>[Python](#tab/python)

**dialogs/logout_dialog.py**

[!code-python[allow logout](~/../botbuilder-samples/samples/python/18.bot-authentication/dialogs/logout_dialog.py?range=27-34&highlight=6)]

---

### <a name="adding-teams-authentication"></a>Como adicionar autenticação do Teams

O Teams se comporta de maneira um pouco diferente de outros canais em relação ao OAuth e exige algumas alterações para implementar a autenticação corretamente. Adicionaremos o código do exemplo de Bot de Autenticação do Teams ([C#][cs-teams-auth-sample]/[JavaScript][js-teams-auth-sample]).

Uma diferença entre outros canais e o Temas é o Teams envia uma atividade de *invocação* para o bot, em vez de uma atividade de *evento*.

# <a name="c"></a>[C#](#tab/csharp)

**Bots/TeamsBot.cs**

[!code-csharp[Invoke Activity](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/TeamsBot.cs?range=34-42&highlight=1)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/teamsBot.js**

[!code-javascript[Invoke Activity](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/teamsBot.js?range=16-25&highlight=1)]

# <a name="python"></a>[Python](#tab/python)

Atualmente, o Microsoft Teams é ligeiramente diferente quanto à maneira da integração da autenticação com o bot. Consulte a [Documentação do Teams](https://aka.ms/teams-docs) na autenticação.

---

Se você usar um *prompt OAuth*, essa atividade de invocação deverá ser encaminhada para a caixa de diálogo. Faremos isso no `TeamsActivityHandler`. Adicione o código a seguir ao arquivo de caixa de diálogo principal.

# <a name="c"></a>[C#](#tab/csharp)

**Bots/DialogBot.cs**

[!code-csharp[Dialogs Handler](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/DialogBot.cs?range=19)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**Bots/dialogBot.js**

[!code-javascript[Dialogs Handler](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/dialogBot.js?range=6)]

# <a name="python"></a>[Python](#tab/python)

Atualmente, o Microsoft Teams é ligeiramente diferente quanto à maneira da integração da autenticação com o bot. Consulte a [Documentação do Teams](https://aka.ms/teams-docs) na autenticação.

---

Por fim, não deixe de adicionar um arquivo `TeamsActivityHandler` apropriado (`TeamsActivityHandler.cs` para bots C# e `teamsActivityHandler.js` para bots do JavaScript) no nível mais alto na pasta do bot.

O `TeamsActivityHandler` também envia atividades de *reação de mensagem*. Uma atividade de reação de mensagem faz referência à atividade original usando o campo *responder à ID*. Essa atividade também deve ser visível por meio do [Feed de Atividades][teams-activity-feed] no Microsoft Teams.

> [!NOTE]
> Você precisa criar um manifesto e incluir `token.botframework.com` na seção `validDomains`; caso contrário, o botão **Entrar** do OAuthCard não abrirá a janela de autenticação. Use o [App Studio](https://docs.microsoft.com/microsoftteams/platform/get-started/get-started-app-studio) para gerar seu manifesto.

### <a name="further-reading"></a>Leitura adicional

- Os [Recursos adicionais do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help) incluem links para obter suporte adicional.
- O repositório [SDK do Bot Framework](https://github.com/microsoft/botbuilder) tem mais informações sobre repositórios, amostras, ferramentas e especificações associadas ao SDK do Bot Builder.

<!-- Footnote-style links -->

[azure-portal]: https://ms.portal.azure.com
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
[python-auth-sample]: https://aka.ms/bot-auth-python-sample-code

[cs-msgraph-sample]: https://aka.ms/v4cs-auth-msgraph-sample
[js-msgraph-sample]: https://aka.ms/v4js-auth-msgraph-sample
[cs-teams-auth-sample]:https://aka.ms/cs-teams-auth-sample
[js-teams-auth-sample]:https://aka.ms/js-teams-auth-sample
[teams-activity-feed]:https://aka.ms/teams-activity-feed
