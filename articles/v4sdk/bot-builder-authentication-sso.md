---
title: Adicionar logon único a um bot – Serviço de Bot
description: Saiba como adicionar o SSO ao bot.
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 4/15/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 576a9acd74f11b70f7d1c294edd15921e98a44f4
ms.sourcegitcommit: 4509747791a57b3098feb2d1705e921a780df351
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91763996"
---
# <a name="add-single-sign-on-to-a-bot"></a>Adicionar logon único a um bot

<!--Added TOC entry -->

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Este artigo mostra como usar o recurso SSO (logon único) em um bot.
Para isso, ele usa um bot *consumidor*, também conhecido como bot *raiz*, para interagir com um bot de *skill*.

Depois que os usuários entram no bot raiz, eles não precisam entrar em cada bot de skill que possam usar por meio do bot raiz. Isso acontece por causa do SSO. Sem ele, os usuários teriam que entrar a cada vez que se comunicassem com um bot de skill diferente.


> [!NOTE]
> O bot *consumidor* também é chamado de bot *raiz* ou *pai*. O bot de *skill* também é chamado de bot *filho*.\
> Este artigo usa os termos bot **raiz** e bot de **skill**.\
> Com os skills, os bots raiz e de skill são bots separados, sendo executados em servidores potencialmente diferentes, cada um com suas memórias e estados separados.

### <a name="web-chat-and-direct-line-considerations"></a>Considerações de Webchat e Direct Line

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
| SSO com Consumidor de Skill Simples e Skill no [**CSharp**][cs-auth-sample] | v4 | Suporte a SSO |


## <a name="about-the-samples"></a>Sobre as amostras

Este artigo faz referência a dois exemplos: o **RootBot** e o **SkillBot**. O **RootBot** encaminha as atividades para o **SkillBot**. Eles modelam o seguinte cenário *típico* de skill:

- Um bot *raiz* chama um ou mais bots de skill.
- Os bots raiz e de skill implementam a autenticação básica descrita no artigo [Adicionar autenticação a um bot](bot-builder-authentication.md).
- O usuário faz logon no bot raiz.
- Como o SSO já está conectado ao bot raiz, ele também está conectado ao bot de skill sem precisar de uma nova interação do usuário.


Para obter uma visão geral de como a estrutura de bot manipula a autenticação, consulte [autenticação do usuário](bot-builder-concept-authentication.md).
Confira mais informações em [Logon único](bot-builder-concept-sso.md).

O **RootBot** dá suporte ao SSO do usuário. Ele se comunica com o **SkillBot** em nome do usuário, sem que o usuário precise se autenticar novamente no *SkillBot*.

Para cada projeto no exemplo, você precisa do seguinte:

1. Um aplicativo do Azure AD para registrar um recurso de bot no Azure.
1. Um aplicativo de provedor de identidade do Azure AD para fornecer a autenticação.
    > [!NOTE]
    > No momento, há suporte apenas para o provedor de identidade do [Azure AD v2](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-identity-providers?view=azure-bot-service-4.0&tabs=adv2%2Cga2#azure-ad-v2-1&preserve-view=true).

## <a name="create-the-azure-rootbot-registration"></a>Criar o registro do RootBot no Azure

1. Crie no [portal do Azure][azure-portal] um registro de bot para `RootBot`. Siga as etapas descritas em [Criar registro de bot no Azure](bot-builder-authentication.md#create-the-azure-bot-registration).
1. Copie e salve a **ID do aplicativo** e o **segredo do cliente** do registro do bot.

## <a name="create-the-azure-ad-identity-for-rootbot"></a>Criar a identidade do Azure AD para RootBot

O Azure AD é um serviço de identidade de nuvem que permite criar aplicativos que conectam usuários com segurança usando protocolos padrão do setor, como o OAuth2.0.

1. Crie um aplicativo de identidade para o `RootBot` que usa o Azure AD v2 para autenticar o usuário. Siga as etapas descritas em [criar o provedor de identidade do Azure ad](bot-builder-authentication.md#create-the-azure-ad-identity-provider).

1. No painel esquerdo, clique em **Manifesto**.
1. Defina `accessTokenAcceptedVersion` como 2.
1. Clique em **Save** (Salvar).
1. No painel esquerdo, clique em **Expor uma API**.
1. No painel direito, clique em **Adicionar um escopo**.
1. Na extrema direita da seção *Adicionar um escopo*, clique em **Salvar e continuar**.
1. Na janela exibida, em *Quem pode consentir?* , selecione **Administradores e usuários**.
1. Insira as outras informações necessárias.
1. Clique em **Adicionar escopo**.
1. Copie e salve o valor do escopo.

### <a name="create-an-oauth-connection-settings"></a>Definir as configurações de conexão do OAuth

1. Crie uma conexão do Azure AD V2 no registro de bot `RootBot` e insira os valores descritos em [Azure AD v2](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-identity-providers?view=azure-bot-service-4.0&tabs=adv2%2Cga2#azure-ad-v2-1&preserve-view=true) e o valor descrito abaixo.

1. Deixe vazia a **URL de troca de token**.
1. Na caixa **Escopos**, insira o valor de escopo `RootBot` que você salvou nas etapas anteriores.
1. Copie e salve o nome da conexão.

## <a name="create-the-azure-skillbot-registration"></a>Criar o registro do SkillBot no Azure

1. Crie no [portal do Azure][azure-portal] um registro de bot para `SkillBot`. Siga as etapas descritas em [Criar registro de bot no Azure](bot-builder-authentication.md#create-the-azure-bot-registration).
1. Copie e salve a **ID do aplicativo** e o **segredo do cliente** do registro do bot.

## <a name="create-the-azure-ad-identity-for-skillbot"></a>Criar a identidade do Azure AD para SkillBot

O Azure AD é um serviço de identidade de nuvem que permite criar aplicativos que conectam usuários com segurança usando protocolos padrão do setor, como o OAuth2.0.

1. Crie um aplicativo de identidade para o `SkillBot` que usa o Azure AD v2 para autenticar o bot. Siga as etapas descritas em [criar o provedor de identidade do Azure ad](bot-builder-authentication.md#create-the-azure-ad-identity-provider).

1. No painel esquerdo, clique em **Manifesto**.
1. Defina `accessTokenAcceptedVersion` como 2.
1. Clique em **Save** (Salvar).
1. No painel esquerdo, clique em **Expor uma API**.
1. No painel direito, clique em **Adicionar um escopo**.
1. Na extrema direita da seção *Adicionar um escopo*, clique em **Salvar e continuar**.
1. Na janela exibida, em *Quem pode consentir?* , selecione **Administradores e usuários**.
1. Insira as outras informações necessárias.
1. Clique em **Adicionar escopo**.
1. Copie e salve o valor do escopo.
1. Clique em **Adicionar um aplicativo cliente**. Na seção na extrema direita, na caixa **ID do cliente**, insira a ID do aplicativo da **Identidade do RootBot** que você salvou anteriormente. Certifique-se de usar a identidade do *RootBot*, e não a ID do aplicativo de registro.
1. Em **Escopo autorizado**, marque a caixa pelo valor de escopo.
1. Clique em **Adicionar aplicativo**.
1. No painel de navegação à esquerda, clique em **Permissões de API**. Uma prática recomendada é definir explicitamente as permissões de API para o aplicativo.

   1. No painel direito, clique em **Adicionar uma permissão**.
   1. Selecione as **APIs da Microsoft** e do **Microsoft Graph**.
   1. Escolha **Permissões delegadas** e verifique se as permissões necessárias estão selecionadas. Este exemplo requer as permissões relacionadas abaixo.
      > [!NOTE]
      > As permissões marcadas como **CONSENTIMENTO DO ADMINISTRADOR NECESSÁRIO** exigirão que um usuário e um administrador de locatário façam logon.

      - **openid**
      - **profile**
      - **User.Read**
      - **User.ReadBasic.All**

   1. Clique em **Adicionar permissões**.

### <a name="create-an-oauth-connection-settings"></a>Definir as configurações de conexão do OAuth

1. Crie uma conexão do Azure AD V2 no registro de bot `SkillBot` e insira os valores descritos em [Azure AD v2](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-identity-providers?view=azure-bot-service-4.0&tabs=adv2%2Cga2#azure-ad-v2-1&preserve-view=true) e os valores descritos abaixo.
1. Na caixa **URL de Troca de Token**, insira o valor de escopo `SkillBot` que você salvou nas etapas anteriores.
1. Na caixa **Escopos**, insira os valores a seguir separados por espaços em branco: `profile` `User.Read` `User.ReadBasic.All` `openid`.

1. Copie e salve o nome da conexão em um arquivo.

## <a name="test-the-connection"></a>Testar a conexão

1. Clique na entrada de conexão para abrir a conexão que você acabou de criar.
1. Clique em **Testar Conexão** na parte superior do painel **Configuração de Conexão do Provedor de Serviços**.
1. Na primeira vez, deve abrir uma nova guia do navegador listando as permissões que seu aplicativo está solicitando e solicitará que você aceite.
1. Clique em **Aceitar**.
1. Isso deve, então, redirecioná-lo para uma página **Teste da conexão com \<your-connection-name> bem-sucedido**.

Confira mais informações em [Visão geral do Azure Active Directory para desenvolvedores (v1.0)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-overview) e [Visão geral da plataforma de identidade da Microsoft (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).
Para obter informações sobre as diferenças entre os pontos de extremidade v1 e v2, confira [Por que atualizar para a plataforma de identidade da Microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare). Para obter informações completas, confira [Plataforma de identidade da Microsoft (conhecida anteriormente como Azure Active Directory para desenvolvedores)](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="prepare-the-samples-code"></a>Preparar os exemplos de códigos

Você deve atualizar o arquivo `appsettings.json` nos dois exemplos, como descrito abaixo.

# <a name="c"></a>[C#](#tab/csharp)

1. No repositório do GitHub, clone o exemplo [SSO com Consumidor de Skill Simples e Skill][cs-auth-sample]

 1. Abra o arquivo `appsettings.json` do projeto `SkillBot`. No arquivo salvo, atribua os seguintes valores:

    ```json
    {
        "MicrosoftAppId": "<SkillBot registration app ID>",
        "MicrosoftAppPassword": "<SkillBot registration password>",
        "ConnectionName": "<SkillBot connection name>",
        "AllowedCallers": [ "<RootBot registration app ID>" ]
    }

1. Open the `RootBot` project `appsettings.json` file. From the saved file, assign the following values:

    ```json
    {
        "MicrosoftAppId": "<RootBot registration app ID>",
        "MicrosoftAppPassword": "<RootBot registration password>",
        "ConnectionName": "<RootBot connection name>",
        "SkillHostEndpoint": "http://localhost:3978/api/skills/",
        "BotFrameworkSkills": [
                {
                "Id": "SkillBot",
                "AppId": "<SkillBot registration app ID>",
                "SkillEndpoint": "http://localhost:39783/api/messages"
                }
            ]
    }
    ```

<!--
# [JavaScript](#tab/javascript)

TBD

# [Python](#tab/python)

TBD
-->

---

## <a name="test-the-samples"></a>Testar os exemplos

Use o seguinte para testar:

- Comandos de `RootBot`

    - O `login` permite que o usuário entre no registro do Azure AD usando o `RootBot`. Depois de conectado, o SSO também entra no `SkillBot`. O usuário não precisa entrar novamente.
    - `token` exibe o token do usuário.
    - `logout` desconecta o usuário do `RootBot`.

- Comandos de `SkillBot`

    - `skill login` permite que o `RootBot` entre no `SkillBot` em nome do usuário. O usuário não será exibido no cartão de entrada, se já tiver se conectado, a menos que o SSO falhe.
    - `skill token` exibe o token do usuário de `SkillBot`.
    - `skill logout` desconecta o usuário do `SkillBot`

>[!NOTE]
> Na primeira vez que os usuários tentarem o SSO em um skill, eles poderão receber um cartão OAuth para fazer logon. Isso ocorre porque eles ainda não receberam consentimento para o aplicativo do Azure AD do skill. Para evitar isso, eles podem obter o consentimento de um administrador para qualquer permissão de grafo solicitada pelo aplicativo Azure AD.

# <a name="emulator"></a>[Emulador](#tab/eml)

### <a name="test-using-the-emulator"></a>Testar usando o emulador

Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme). Consulte também como [Depurar com o emulador](../bot-service-debug-emulator.md).

Para que o logon de exemplo do bot funcione é necessário configurar o emulador, conforme mostrado em [Configurar o emulador para autenticação](../bot-service-debug-emulator.md#configure-the-emulator-for-authentication).

Após configurar o mecanismo de autenticação é possível executar o teste de exemplo do bot real.

1. No Visual Studio, abra a solução `SSOWithSkills.sln` e configure-a para iniciar a [depuração com vários processos](https://docs.microsoft.com/visualstudio/debugger/debug-multiple-processes?view=vs-2019#start-debugging-with-multiple-processes&preserve-view=true).
1. Execute o bot em seu computador local.
Observe que, no arquivo `appsettings.json` do projeto `RootBot`, você tem as seguintes configurações:

```json
    "SkillHostEndpoint": "http://localhost:3978/api/skills/"
    "SkillEndpoint": "http://localhost:39783/api/messages"
```
> [!NOTE]
> Essas configurações implicam que `RootBot` e `SkillBot` estão em execução no computador local. O emulador se comunica com `RootBot` na porta 3978 e `RootBot` se comunica com `SkillBot` na porta 39783. Assim que você inicia a depuração, duas janelas de navegador padrão são abertas. Uma na porta 3978 e a outra na porta 39783.

1. Inicie o emulador.
1. É necessário fornecer a ID e a senha do aplicativo do `RootBot` ao se conectar ao bot.
1. Digite `hi` para iniciar a conversa.
1. Digite **login**.  O `RootBot` exibirá o cartão de autenticação para *Entrar no AAD*.

    ![Logon na raiz](media/how-to-auth/auth-bot-sso-test-root-signin.PNG)

1. Clique em **Entrar**. A caixa de diálogo pop-up *Confirmar Abertura de URL* é exibida.

    ![URL de confirmação de raiz](media/how-to-auth/auth-bot-test-confirm-url.PNG)

1. Clique em **Confirmar**. Você será conectado, e o token `RootBot` será exibido.
1. Digite **token** para exibir o token novamente.

    ![Imagem do token raiz](media/how-to-auth/auth-bot-sso-test-token.PNG)

    Agora você está pronto para se comunicar com o `SkillBot`. Quando você entrar usando `RootBot`, não precisará fornecer suas credenciais novamente até sair. Isso demonstra que o SSO está funcionando.

1. Digite **skill login** na caixa do emulador. O logon não será solicitado novamente. Em vez disso, o token do SkillBot é exibido.

1. Continue e digite **skill token** para exibir o token novamente.

     ![Imagem do token de habilidade](media/how-to-auth/auth-bot-sso-test-skill-token.PNG)

1. Agora você pode digitar **skill logout** para se desconectar do `SkillBot`. Em seguida, digite **logout** para sair do `SimpleRootBoot`.

# <a name="web-chat"></a>[Web Chat](#tab/wct)

### <a name="test-using-web-chat"></a>Testar usando o Webchat

1. Implante o bot raiz e o bot de skill no Azure. Para obter mais informações sobre implantação, confira [Tutorial: Criar e implantar um bot básico](bot-builder-tutorial-basic-deploy.md).
1. No editor de código, como o Visual Studio, substitua os endereços de localhost no arquivo `appsetting.js` do projeto `RootBot` pelos endereços reais do Azure, como mostrado abaixo.

    ```json
    "SkillHostEndpoint": "https://<your root bot deployed name>.azurewebsites.net/api/skills"
    "SkillEndpoint": "https://<your skill bot deployed name>.azurewebsites.net/api/messages"
    ````

1. Em seu navegador, navegue até o [portal do Azure][azure-portal].
1. Abra o registro do bot raiz. No painel esquerdo, clique em **Teste no Webchat**. A janela da caixa de diálogo com o bot raiz é exibida com a mensagem de saudação do bot.
1. Inicie a conversa com o bot inserindo *olá*, por exemplo. O bot ecoará a mensagem de volta.
1. Digite **login**. O `RootBot` exibirá o cartão de autenticação para *Entrar no AAD*.

    ![Entrada no skill](media/how-to-auth/auth-bot-sso-test-webchat-root-signin.PNG)

1. Clique em **Entrar**. Uma página da Web com um código de validação é exibida.
1. Copie o código e insira na caixa de entrada para concluir. O token de `RootBot` é exibido.
1. Digite **token** para exibir o token novamente.

    ![Webchat do token raiz](media/how-to-auth/auth-bot-sso-test-webchat-token.PNG)

    Agora você está pronto para se comunicar com o `SkillBot`. Quando você tiver entrado em `RootBot`, não precisará fornecer novamente suas credenciais até sair. Isso demonstra que o SSO está funcionando.

1. Digite **skill login**.  O token do SkillBot é exibido.

    ![Webchat do token de habilidades](media/how-to-auth/auth-bot-sso-test-webchat-skill-token.PNG)

1. Continue e digite **skill token** para exibir o token novamente.
Isso deixa claro que você está se comunicando com o `SkillBot` sem a necessidade de entrar novamente. SSO em ação!
1. Agora você pode digitar **skill logout** para se desconectar do `SkillBot`. Em seguida, digite **logout** para sair do `SimpleRootBoot`.

---

## <a name="additional-information"></a>Informações adicionais

O diagrama de sequência de tempo a seguir aplica-se aos exemplos usados no artigo e mostra a interação entre os vários componentes envolvidos. *ABS* significa *Serviço de Bot do Azure*.

![Fluxo do token de habilidade](media/how-to-auth/auth-bot-sso-sample-flow-diagram.PNG)

<!-- verify -->
1. Na primeira vez, o usuário digita o comando `login` para o **RootBot**.
1. O **RootBot** envia um **OAuthCard** solicitando que o usuário entre.
1. O usuário digita as credenciais de autenticação, que são enviadas para o **ABS** (Serviço de Bot do Azure).
1. O **ABS** envia o token de autenticação, gerado com base nas credenciais do usuário, para o **RootBot**.
1. O **RootBot** exibe o token da raiz para o usuário.
1. O usuário digita o comando `skill login` para o **SkillBot**.
1. O **SkillBot** envia um **OAuthCard** para o **RootBot**.
1. O **RootBot** solicita um **token intercambiável** do **ABS**.
1. Neste ponto, a "dança" do SSO entra em cena e termina com o **token do skill** enviado pelo **SkillBot** ao **RootBot**.
1. O **RootBot** exibe o token do skill para o usuário. Observe que o token do skill foi gerado sem que o usuário precisasse entrar no **SKillBot**. Isso acontece por causa do SSO.

Para ver como ocorre a troca de tokens, confira o exemplo mostrado abaixo. A função pode ser encontrada em [TokenExchangeSkillHandler.cs](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/sso-with-skills/RootBot/TokenExchangeSkillHandler.cs).

# <a name="c"></a>[C#](#tab/csharp)

<!-- Fixed link -->
[!code-csharp[sso-token-exchange](~/../botbuilder-samples/experimental/sso-with-skills/RootBot/TokenExchangeSkillHandler.cs?range=92-136)]

---

## <a name="further-reading"></a>Leitura adicional

- [Logon único](bot-builder-concept-sso.md) proporciona informações adicionais
- [Provedores de identidade](bot-builder-concept-identity-providers.md) fornecem autenticação de usuário como um serviço
- [Autenticação do usuário](bot-builder-concept-authentication.md) para acessar recursos online protegidos em nome do usuário

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

[cs-auth-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/sso-with-skills
[js-auth-sample]: https://aka.ms/v4js-bot-auth-sample
[python-auth-sample]: https://aka.ms/bot-auth-python-sample-code

