---
title: Criar um bot com o Serviço de Bot do Azure – Serviço de Bot
description: Saiba como criar um bot com o Serviço de Bot, um ambiente de desenvolvimento de bot integrado e dedicado.
keywords: Início Rápido, criar um bot, serviço de bot, bot de aplicativo Web
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/30/2020
ms.openlocfilehash: 29d9e7de8f991065e5d18ae0565be0e71bb9c15d
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417040"
---
# <a name="create-a-bot-with-azure-bot-service"></a>Criar um bot com o Serviço de Bot do Azure

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

O Serviço de Bot do Azure fornece os principais componentes para a criação de bots, incluindo o SDK do Bot Framework para o desenvolvimento de bots e o serviço de bot para conectar os bots aos canais.
Este artigo mostra como criar um bot usando o SDK do bot Framework v4 e escolhendo o modelo .NET ou Node.js.

Você tem duas abordagens para criar um bot com o Azure:

1. **Aplicativo Web**. Crie um bot e registre-o com o Azure usando um aplicativo Web, conforme mostrado neste artigo. Você usará essa abordagem se desenvolver e hospedar um bot no Azure.
1. **Registro de canais de bot**. Crie e desenvolva o bot localmente e hospede-o em uma plataforma diferente do Azure. Ao registrar o bot, você fornece o endereço da Web onde o bot está hospedado. Você ainda pode hospedá-lo no Azure. Siga as etapas descritas no artigo de [registro de canais de bot](~/bot-service-quickstart-registration.md) .

[!INCLUDE [Azure vs local development](~/includes/snippet-quickstart-paths.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [Azure](https://portal.azure.com)

### <a name="create-a-new-bot-service"></a>Criar um serviço de bot

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Clique no link **criar um recurso** localizado no canto superior esquerdo da portal do Azure.
1. Na caixa de pesquisa, digite *bot* e, na lista suspensa, selecione **bot do aplicativo Web**.

    ![bot do aplicativo Web](../media/azure-bot-quickstarts/web-app-bot.png)

1. Na página **bot do aplicativo Web** , clique no botão **criar** .

    ![criação de bot de aplicativo Web](../media/azure-bot-quickstarts/web-app-bot-create.png)

1. No formulário **bot do aplicativo Web** , forneça as informações solicitadas sobre o bot conforme especificado na tabela abaixo da imagem.

    ![Folha Criar Bot de Aplicativo Web](../media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

 | Configuração | Valor sugerido | Descrição |
 | ---- | ---- | ---- |
 | **Nome do bot** | O nome de exibição do bot | O nome de exibição do bot exibido nos canais e nos diretórios. Esse nome pode ser alterado a qualquer momento. |
 | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar. |
 | **Grupo de recursos** | myResourceGroup | Crie um [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolha um existente. |
 | **Localidade** | A localização padrão | Selecione a localização geográfica do grupo de recursos. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher um local mais próximo ao cliente. O local não pode ser alterado após a criação do bot. |
 | **Tipo de preços** | F0 | Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/). |
 | **Nome do aplicativo** | Um nome exclusivo | O nome exclusivo de URL do bot. Por exemplo, se você nomear o bot *myawesomebot*, a URL do bot será `http://myawesomebot.azurewebsites.net`. O nome precisa usar somente caracteres alfanuméricos e de sublinhado. Há um limite de 35 caracteres para esse campo. O nome do Aplicativo não pode ser alterado após a criação do bot. |
 | **Modelo de bot** | Bot de Eco | Escolha **SDK v4**. Escolha C# ou Node.js para este início rápido e, em seguida, clique em **Selecionar**.
 | **Localização/plano de serviço de aplicativo** | O Plano do Serviço de Aplicativo  | Selecione um local para o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/). Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **Contas do LUIS** _Somente disponíveis para o modelo de Bot Básico_ | Nome do Recurso do Azure do LUIS | Depois de [migrar recursos do LUIS para um recurso do Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring), insira o nome do recurso do Azure para associar esse aplicativo do LUIS a esse recurso do Azure.
 | **Application Insights** | Por | Decida se deseja deixar o [Application Insights](/bot-framework/bot-service-manage-analytics) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **ID do Aplicativo e senha da Microsoft** | Criar ID do Aplicativo e senha automaticamente | Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Caso contrário, uma nova ID do Aplicativo e uma nova senha da Microsoft serão criados para você no processo de criação de bot. Ao criar um registro de aplicativo manualmente para o Serviço de Bot, verifique se os tipos de conta compatíveis estão definidos como "Contas em qualquer diretório organizacional" ou "Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Outlook.com, Xbox etc.)" |

4. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique em **ir para** o link de recursos para abrir a página de recursos do bot.

Agora que o bot foi criado, teste-o no Webchat.

## <a name="test-the-bot"></a>Testar o bot
Na seção **Gerenciamento de Bot**, clique em **Testar no Webchat**. O Serviço de Bot do Azure carregará o controle de Webchat e se conectará ao bot.

![teste de bot de aplicativo Web](../media/azure-bot-quickstarts/web-app-bot-test.png)

Insira uma mensagem e o bot deverá responder.

## <a name="manual-app-registration"></a>Registro manual do aplicativo

Um registro manual é necessário para situações como:

- Você não consegue fazer os registros em sua organização e precisa de terceiros para criar a ID do aplicativo para o bot que você está criando.
- É necessário criar sua própria ID do aplicativo (e senha).

Confira [Perguntas frequentes – Registro do Aplicativo](~/bot-service-resources-faq-azure.md#how-do-i-create-my-own-app-registration).


## <a name="download-code"></a>Código de download
Você pode baixar o código para trabalhar nele localmente.
1. Na seção **Gerenciamento do Bot**, clique em **Compilar**.
1. Clique no link **Baixar o código-fonte do Bot** no painel à direita.
1. Siga os prompts para baixar o código e, em seguida, descompacte a pasta.
    1. [!INCLUDE [download keys snippet](../includes/snippet-abs-key-download.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de baixar o código, você pode continuar a desenvolver o bot localmente em seu computador. Uma vez que você testar o bot e estiver pronto para carregar seu código para o portal do Azure, siga as instruções listadas no tópico [configurar implantação contínua](../bot-service-build-continuous-deployment.md) para atualizar o código automaticamente após fazer alterações.
