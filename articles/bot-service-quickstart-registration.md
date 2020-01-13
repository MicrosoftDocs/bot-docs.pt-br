---
title: Criar um Registro de Canais de Bot com o Serviço de Bot | Microsoft Docs
description: Saiba como registrar um bot existente com o Serviço de Bot.
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 78de3676332561aafd4b24f755962b614cb9a27d
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491567"
---
# <a name="register-a-bot-with-azure-bot-service"></a>Registrar um bot com o Serviço de Bot do Azure

Este tópico mostra como criar um recurso do **Serviço de Bot do Azure** para registrar seu bot. Você precisará disso se o bot estiver hospedado em outro lugar e você quiser disponibilizá-lo no Azure e conectá-lo aos canais do Serviço de Bot do Azure.

Isso permite que você crie, conecte e gerencie seu bot para interagir com os usuários, onde quer que estejam, pelo Cortana, Skype, Messenger e muitos outros serviços.

> [!IMPORTANT] 
> Você só precisará registrar o seu bot se ele não estiver hospedado no Azure. Se você [criou um bot](v4sdk/abs-quickstart.md) pelo portal do Azure, ele já foi registrado no serviço.

## <a name="create-a-registration-resource"></a>Criar um recurso de registro

1. Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com).

    > [!TIP]
    > Se você não tiver uma assinatura, poderá se inscrever para obter uma <a href="https://azure.microsoft.com/free/" target="_blank">conta gratuita</a>.

1. No painel esquerdo, clique em **Criar um recurso**.
1. Na caixa de seleção do painel direito, insira *bot*. E, na lista suspensa, selecione o **Registro de Canais de Bot** ou o **Bot do Aplicativo Web**, dependendo do seu aplicativo.
No **Aplicativo Web do bot**, siga as etapas descritas no artigo: [Criar um bot com o Serviço de Bot do Azure](v4sdk/abs-quickstart.md). Você criará um bot no Azure que é registrado automaticamente no Serviço de Bot do Azure.
1. Clique no botão **Criar** para iniciar o processo.
1. Na folha **Serviço de Bot**, forneça as informações solicitadas sobre o bot, conforme especificado na tabela abaixo da imagem.  

   ![Criar uma folha de bot de registro](media/azure-bot-quickstarts/registration-create-bot-service-blade.png)

   |Configuração |Valor sugerido|DESCRIÇÃO|
   |---|---|--|
   |**Nome do bot** <img width="300px">|O nome de exibição do bot|O nome de exibição do bot exibido nos canais e nos diretórios. Esse nome pode ser alterado a qualquer momento.|
   |**Assinatura**|Sua assinatura|Selecione a assinatura do Azure que deseja usar.|
   |**Grupo de recursos**|myResourceGroup|Crie um [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolha um existente.|
   |**Localidade**|Oeste dos EUA|Escolha um local perto de onde o seu bot está implantado ou perto de outros serviços que o seu bot acessará.|
   |**Tipo de preços**|F0|Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/).|
   |**Ponto de extremidade de mensagens**|URL|Insira a URL do ponto de extremidade de mensagens do seu bot.|
   |**Application Insights**|Por| Decida se deseja deixar o [Application Insights](bot-service-manage-analytics.md) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. |
   |**ID do Aplicativo e senha da Microsoft**| Criar ID do Aplicativo e senha automaticamente |Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Confira a próxima seção [Registro manual do aplicativo](#manual-app-registration). Caso contrário, uma nova ID do Aplicativo da Microsoft e uma nova senha da Microsoft serão criadas no processo de criação de registro. |

    > [!IMPORTANT]
    > Não se esqueça de inserir a URL do ponto de extremidade de mensagens do seu bot.

1. Selecione o botão **Criar**. Aguarde até que o recurso seja criado. Ele será exibido em sua lista de recursos.

### <a name="get-registration-password"></a>Obter a senha de registro

Depois que o registro for concluído, o Azure Active Directory atribuirá uma ID de aplicativo exclusiva ao seu registro e você será levado à página *Visão geral* do seu aplicativo.

Para obter a senha, siga as etapas descritas a seguir.

1. Na lista de recursos, clique no recurso do Serviço de Aplicativo do Azure recém-criado.
1. No painel direito, na folha de recursos, clique em **Configurações**. A página *Configurações* do recurso é exibida.
1. Na página Configurações, copie a **ID do Aplicativo da Microsoft** e salve-a em um arquivo.
1. Clique no link **Gerenciar** da *ID do Aplicativo da Microsoft*.

    ![Criar uma folha de bot de registro](media/azure-bot-quickstarts/bot-channels-registration-app-settings.png)

1. Na página exibida *Certificados e segredos*, clique no botão **Novo segredo do cliente**.

    ![Criar uma folha de bot de registro](media/azure-bot-quickstarts/bot-channels-registration-app-secrets.png)

1. Adicione a descrição, selecione a hora de expiração e clique no botão **Adicionar**.

    ![Criar uma folha de bot de registro](media/azure-bot-quickstarts/bot-channels-registration-app-secrets-create.png)

    Isso irá gerar uma nova senha para o seu bot. Copie esta senha e salve-a em um arquivo. Esta é a única vez que você verá a senha. Se você não tiver a senha completa salva, será preciso repetir o processo para criar uma nova senha caso precise dela posteriormente.

## <a name="manual-app-registration"></a>Registro manual do aplicativo

Um registro manual é necessário para situações como:

- Você não consegue fazer os registros em sua organização e precisa de terceiros para criar a ID do aplicativo para o bot que você está criando.
- É necessário criar sua própria ID do aplicativo (e senha).

Confira [Perguntas frequentes – Registro do Aplicativo](bot-service-resources-bot-framework-faq.md#app-registration).

> [!IMPORTANT]
> Na seção *Dá suporte aos tipos de conta*, você deve escolher um dos dois tipos de multilocatário que seja: *Contas em qualquer diretório organizacional (Qualquer Azure AD – Multilocatário)* ou *Contas em qualquer diretório organizacional (Qualquer Azure AD – Multilocatário) e contas Microsoft pessoais (por ex., Skype, Xbox, Outlook.com)* , ao criar o aplicativo; caso contrário, o bot não funcionará. Para saber mais, confira [Registrar um novo aplicativo usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal).

## <a name="update-the-bot"></a>Atualizar o bot

Se estiver usando o SDK do Bot Framework para .NET, defina os seguintes valores de chave no arquivo web.config:

- `MicrosoftAppId = <appId>`
- `MicrosoftAppPassword = <appSecret>`

Se estiver usado o SDK do Bot Framework para Node.js, ajuste as seguintes variáveis de ambiente:

- `MICROSOFT_APP_ID = <appId>`
- `MICROSOFT_APP_PASSWORD = <appSecret>`

## <a name="test-the-bot"></a>Testar o bot

Agora que o serviço de bot foi criado, [teste-o no Webchat](bot-service-manage-test-webchat.md). Insira uma mensagem e o bot deverá responder.

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu como registrar o seu bot hospedado no Serviço de Bot. A próxima etapa é saber como gerenciar seu Serviço de Bot.

> [!div class="nextstepaction"]
> [Gerenciar um bot](bot-service-manage-overview.md)
