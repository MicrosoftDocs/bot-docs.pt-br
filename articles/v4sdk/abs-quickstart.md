---
title: Criar um bot com o Serviço de Bot | Microsoft Docs
description: Saiba como criar um bot com o Serviço de Bot, um ambiente de desenvolvimento de bot integrado e dedicado.
keywords: Início Rápido, criar um bot, serviço de bot, bot de aplicativo Web
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 07/19/2019
ms.openlocfilehash: c4274df084fb0f25535aacb0173e19bc93f20ba1
ms.sourcegitcommit: 23a1808e18176f1704f2f6f2763ace872b1388ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68484413"
---
# <a name="create-a-bot-with-azure-bot-service"></a>Criar um bot com o Serviço de Bot do Azure

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

O Serviço de Bot do Azure fornece os principais componentes para a criação de bots, incluindo o SDK do Bot Framework para o desenvolvimento de bots e o serviço de bot para conectar os bots aos canais. No tópico, você pode escolher o modelo .NET ou Node.js para criar um bot usando o SDK do Bot Framework v4.

[!INCLUDE [Azure vs local development](~/includes/snippet-quickstart-paths.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [Azure](http://portal.azure.com)

### <a name="create-a-new-bot-service"></a>Criar um serviço de bot

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
1. Clique no link **Criar recurso** localizado no canto superior esquerdo do portal do Azure e, em seguida, selecione **IA + Machine Learning** > **Bot de Aplicativo Web**. 

![Crie o bot](../media/azure-bot-quickstarts/abs-create-blade.png)

2. Uma **nova folha** será aberta com informações sobre o *Bot de Aplicativo Web*.  

3. Na folha **Serviço de Bot**, forneça as informações solicitadas sobre o bot, conforme especificado na tabela abaixo da imagem.  <br/>
 ![Folha Criar Bot de Aplicativo Web](../media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

 | Configuração | Valor sugerido | DESCRIÇÃO |
 | ---- | ---- | ---- |
 | **Nome do bot** | O nome de exibição do bot | O nome de exibição do bot exibido nos canais e nos diretórios. Esse nome pode ser alterado a qualquer momento. |
 | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar. |
 | **Grupo de recursos** | myResourceGroup | Crie um [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolha um existente. |
 | **Localidade** | A localização padrão | Selecione a localização geográfica do grupo de recursos. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher um local mais próximo ao cliente. O local não pode ser alterado após a criação do bot. |
 | **Tipo de preços** | F0 | Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/). |
 | **Nome do aplicativo** | Um nome exclusivo | O nome exclusivo de URL do bot. Por exemplo, se você nomear o bot *myawesomebot*, a URL do bot será `http://myawesomebot.azurewebsites.net`. O nome precisa usar somente caracteres alfanuméricos e de sublinhado. Há um limite de 35 caracteres para esse campo. O nome do Aplicativo não pode ser alterado após a criação do bot. |
 | **Modelo de bot** | Bot de Eco | Escolha **SDK v4**. Escolha C# ou Node.js para este início rápido e, em seguida, clique em **Selecionar**.  
 | **Localização/plano de serviço de aplicativo** | O Plano do Serviço de Aplicativo  | Selecione um local para o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/). Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **Application Insights** | Por | Decida se deseja deixar o [Application Insights](/bot-framework/bot-service-manage-analytics) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **ID do Aplicativo e senha da Microsoft** | Criar ID do Aplicativo e senha automaticamente | Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Caso contrário, uma nova ID do Aplicativo e uma nova senha da Microsoft serão criados para você no processo de criação de bot. Ao criar um registro de aplicativo manualmente para o Serviço de Bot, verifique se os tipos de conta compatíveis estão definidos como 'Contas em qualquer diretório organizacional' ou 'Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Outlook.com, Xbox etc.)' |

4. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir a folha de recursos do bot.

Agora que o bot foi criado, teste-o no Webchat. 

## <a name="test-the-bot"></a>Testar o bot
Na seção **Gerenciamento de Bot**, clique em **Testar no Webchat**. O Serviço de Bot do Azure carregará o controle de Webchat e se conectará ao bot. 

![Teste do Webchat do Azure](../media/azure-bot-quickstarts/azure-webchat-test.png)

Insira uma mensagem e o bot deverá responder.

## <a name="download-code"></a>Código de download
Você pode baixar o código para trabalhar nele localmente. 
1. Na seção **Gerenciamento do Bot**, clique em **Compilar**. 
1. Clique no link **Baixar o código-fonte do Bot** no painel à direita. 
1. Siga os prompts para baixar o código e, em seguida, descompacte a pasta.
    1. [!INCLUDE [download keys snippet](../includes/snippet-abs-key-download.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de baixar o código, você pode continuar a desenvolver o bot localmente em seu computador. Uma vez que você testar o bot e estiver pronto para carregar seu código para o portal do Azure, siga as instruções listadas no tópico [configurar implantação contínua](../bot-service-build-continuous-deployment.md) para atualizar o código automaticamente após fazer alterações.