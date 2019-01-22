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
ms.date: 01/08/2019
ms.openlocfilehash: da809023338847374715f7576481fc8d17a21ded
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225391"
---
::: moniker range="azure-bot-service-3.0"

# <a name="create-a-bot-with-azure-bot-service"></a>Criar um bot com o Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

O Serviço de Bot fornece os principais componentes para a criação de bots, incluindo o SDK do Bot Framework para o desenvolvimento de bots e o Bot Framework para conectar os bots aos canais. O Serviço de Bot oferece cinco opções de modelos para a criação dos bots com suporte para .NET e Node.js. Neste tópico, saiba como usar o Serviço de Bot para criar um novo bot que usa o SDK do Bot Framework.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no [Portal do Azure](http://portal.azure.com).

> [!TIP]
> Se você ainda não tiver uma assinatura, poderá se inscrever para uma <a href="https://azure.microsoft.com/en-us/free/" target="_blank">conta gratuita</a>.

## <a name="create-a-new-bot-service"></a>Criar um serviço de bot

1. Clique no link **Criar recurso** localizado no canto superior esquerdo do portal do Azure e, em seguida, selecione **IA + Machine Learning > bot de Aplicativo Web**. 

2. Uma nova folha será aberta com informações sobre o **Bot de Aplicativo Web**.  

3. Na folha **Serviço de Bot**, forneça as informações solicitadas sobre o bot, conforme especificado na tabela abaixo da imagem.  <br/>
   ![Folha Criar Bot de Aplicativo Web](./media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

   | Configuração | Valor sugerido | DESCRIÇÃO |
   | ---- | ---- | ---- |
   | **Nome do bot** | O nome de exibição do bot | O nome de exibição do bot exibido nos canais e nos diretórios. Esse nome pode ser alterado a qualquer momento. |
   | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar. |
   | **Grupo de recursos** | myResourceGroup | Crie um [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolha um existente. |
   | **Localidade** | A localização padrão | Selecione a localização geográfica do grupo de recursos. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher um local mais próximo ao cliente. O local não pode ser alterado após a criação do bot. |
   | **Tipo de preços** | F0 | Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/en-us/pricing/details/bot-service/). |
   | **Nome do aplicativo** | Um nome exclusivo | O nome exclusivo de URL do bot. Por exemplo, se você nomear o bot *myawesomebot*, a URL do bot será `http://myawesomebot.azurewebsites.net`. O nome precisa usar somente caracteres alfanuméricos e de sublinhado. Há um limite de 35 caracteres para esse campo. O nome do Aplicativo não pode ser alterado após a criação do bot. |
   | **Modelo de bot** | Basic | Escolha **C#** ou **Node.js** e selecione o modelo **Básico** para este início rápido. Em seguida, clique em **Selecionar**. O modelo Básico cria um bot de eco. [Saiba mais](bot-service-concept-templates.md) sobre os modelos. |
   | **Localização/plano de serviço de aplicativo** | O Plano do Serviço de Aplicativo  | Selecione um local para o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/). Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher um local mais próximo ao cliente. (Não disponível para o Bot do Functions.) |
   | **Armazenamento do Azure** | Sua conta de armazenamento do Azure | Você pode criar uma conta de armazenamento de dados ou usar uma existente. Por padrão, o bot usará o [Armazenamento de Tabelas](/azure/storage/common/storage-introduction#table-storage). |
   | **Application Insights** | Por | Decida se deseja deixar o [Application Insights](/bot-framework/bot-service-manage-analytics) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do local do serviço de bot. |
   | **ID do Aplicativo e senha da Microsoft** | Criar ID do Aplicativo e senha automaticamente | Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Caso contrário, uma nova ID do Aplicativo e uma nova senha da Microsoft serão criados para você no processo de criação de bot. |

   > [!NOTE]
   > 
   > Caso esteja criando um **Bot do Functions**, você não verá um campo **Plano do Serviço de Aplicativo/Local**. Em vez disso, verá um campo *Plano de hospedagem*. Nesse caso, escolha um [Plano de hospedagem](bot-service-overview-readme.md#hosting-plans).

4. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir a folha de recursos do bot.

 > [!TIP] 
 > Por motivos de desempenho, o **Bot do Functions** executa modelos do Node.js que já foram empacotados usando a ferramenta *Pacote do Azure Functions*. A ferramenta *Pacote do Azure Functions* reuniu todos os módulos do Node.js e os combinou em um só arquivo *.js.
 > Para obter mais informações, confira [Pacote do Azure Functions](https://github.com/Azure/azure-functions-pack).
 
## <a name="test-the-bot"></a>Testar o bot
Agora que o bot foi criado, teste-o no [Webchat](bot-service-manage-test-webchat.md). Insira uma mensagem e o bot deverá responder.

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu a criar um Bot de Aplicativo Web/Bot do Functions **Básico** usando o Serviço de Bot e verificou a funcionalidade do bot usando o controle de Webchat interno no Azure. Agora, saiba como gerenciar o bot e começar a trabalhar com seu código-fonte.

> [!div class="nextstepaction"]
> [Gerenciar um bot](bot-service-manage-overview.md)

::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="create-a-bot-with-azure-bot-service"></a>Criar um bot com o Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

O Serviço de Bot do Azure fornece os principais componentes para a criação de bots, incluindo o SDK do Bot Framework para o desenvolvimento de bots e o serviço de bot para conectar os bots aos canais. No tópico, você pode escolher o modelo .NET ou Node.js para criar um bot usando o SDK do Bot Framework v4.

## <a name="prerequisites"></a>Pré-requisitos
- Conta do [Azure](http://portal.azure.com)

### <a name="create-a-new-bot-service"></a>Criar um serviço de bot

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
1. Clique no link **Criar novo recurso** localizado no canto superior esquerdo do portal do Azure e, em seguida, selecione **IA + Machine Learning** > **Bot de Aplicativo Web**. 

![Crie o bot](~/media/azure-bot-quickstarts/abs-create-blade.png)

2. Uma **nova folha** será aberta com informações sobre o *Bot de Aplicativo Web*.  

3. Na folha **Serviço de Bot**, forneça as informações solicitadas sobre o bot, conforme especificado na tabela abaixo da imagem.  <br/>
 ![Folha Criar Bot de Aplicativo Web](~/media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

 | Configuração | Valor sugerido | DESCRIÇÃO |
 | ---- | ---- | ---- |
 | **Nome do bot** | O nome de exibição do bot | O nome de exibição do bot exibido nos canais e nos diretórios. Esse nome pode ser alterado a qualquer momento. |
 | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar. |
 | **Grupo de recursos** | myResourceGroup | Crie um [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolha um existente. |
 | **Localidade** | A localização padrão | Selecione a localização geográfica do grupo de recursos. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher um local mais próximo ao cliente. O local não pode ser alterado após a criação do bot. |
 | **Tipo de preços** | F0 | Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/en-us/pricing/details/bot-service/). |
 | **Nome do aplicativo** | Um nome exclusivo | O nome exclusivo de URL do bot. Por exemplo, se você nomear o bot *myawesomebot*, a URL do bot será `http://myawesomebot.azurewebsites.net`. O nome precisa usar somente caracteres alfanuméricos e de sublinhado. Há um limite de 35 caracteres para esse campo. O nome do Aplicativo não pode ser alterado após a criação do bot. |
 | **Modelo de bot** | Bot de Eco | Escolha **SDK v4**. Escolha C# ou Node.js para este início rápido e, em seguida, clique em **Selecionar**.  
 | **Localização/plano de serviço de aplicativo** | O Plano do Serviço de Aplicativo  | Selecione um local para o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/). Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **Armazenamento do Azure** | Sua conta de armazenamento do Azure | Você pode criar uma conta de armazenamento de dados ou usar uma existente. Por padrão, o bot usará o [Armazenamento de Tabelas](/azure/storage/common/storage-introduction#table-storage). |
 | **Application Insights** | Por | Decida se deseja deixar o [Application Insights](/bot-framework/bot-service-manage-analytics) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. Sua escolha de localização pode ser qualquer local listado, embora geralmente seja melhor escolher o mesmo do serviço de bot. |
 | **ID do Aplicativo e senha da Microsoft** | Criar ID do Aplicativo e senha automaticamente | Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Caso contrário, uma nova ID do Aplicativo e uma nova senha da Microsoft serão criados para você no processo de criação de bot. |

4. Clique em **Criar** para criar o serviço e implantar o bot na nuvem. Esse processo pode levar vários minutos.

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir a folha de recursos do bot.

Agora que o bot foi criado, teste-o no Webchat. 

## <a name="test-the-bot"></a>Testar o bot
Na seção **Gerenciamento de Bot**, clique em **Testar no Webchat**. O Serviço de Bot do Azure carregará o controle de Webchat e se conectará ao bot. 

![Teste do Webchat do Azure](./media/azure-bot-quickstarts/azure-webchat-test.png)

Insira uma mensagem e o bot deverá responder.

## <a name="download-code"></a>Código de download
Você pode baixar o código para trabalhar nele localmente. 
1. Na seção **Gerenciamento do Bot**, clique em **Compilar**. 
1. Clique no link **Baixar o código-fonte do Bot** no painel à direita. 
1. Siga os prompts para baixar o código e, em seguida, descompacte a pasta.

O código que você baixou usa um [arquivo .bot](./v4sdk/bot-file-basics.md) criptografado. Você precisará atualizar totalmente `botFilePath` e `botFileSecret` no arquivo appsettings.json ou .env. 
Para fazer isso, acesse o portal do Azure. Selecione seu bot no portal, em seguida, na seção **Configurações do Serviço de Aplicativo**, clique em **Configurações do aplicativo**. No painel **Configurações do aplicativo**, você verá os valores `botFilePath` e `botFileSecret`. Copie esses valores e atualize o arquivo .env ou appsettings.json. 

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu a criar um Bot de Aplicativo Web **Eco** usando o Serviço de Bot do Azure e verificou a funcionalidade do bot usando o controle de Webchat interno. Agora, saiba como gerenciar o bot e começar a trabalhar com seu código-fonte.

> [!div class="nextstepaction"]
> [Como funcionam os bots](~/v4sdk/bot-builder-basics.md)

::: moniker-end
