---
title: Como funciona o Serviço de Bot | Microsoft Docs
description: Saiba mais sobre os recursos e funcionalidades do Serviço de Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 85ef0fde39980bab1b891518e338fddbd56b275a
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224451"
---
# <a name="how-bot-service-works"></a>Como funciona o Serviço de Bot

O Serviço de Bot fornece os principais componentes para a criação de bots, incluindo o SDK do Bot Framework para o desenvolvimento de bots e o Bot Framework para conectar os bots aos canais. O Serviço de Bot oferece cinco opções de modelos para a criação dos bots com suporte para .NET e Node.js.

> [!IMPORTANT]
> É preciso ter uma assinatura do Microsoft Azure para poder usar o Serviço de Bot. Se você ainda não tiver uma assinatura, poderá se inscrever para uma <a href="https://azure.microsoft.com/en-us/free/" target="_blank">conta gratuita</a>.

## <a name="hosting-plans"></a>Planos de hospedagem
O Serviço de Bot oferece dois planos de hospedagem para os seus bots. Escolha um plano de hospedagem que atenda às suas necessidades.

### <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

Um bot que usa um plano do Serviço de Aplicativo é um aplicativo Web do Azure padrão que você pode definir para alocar uma capacidade predefinida com custos previsíveis e dimensionamento. Com o bot que usa este plano de hospedagem, você pode:

* Editar o código-fonte do bot online usando um editor de código avançado no navegador.
* Baixar, depurar e publicar novamente seu bot em C# usando o Visual Studio.
* Configurar uma implantação contínua com facilidade para o Visual Studio Online e Github.
* Usar o código de exemplo preparado para o SDK do Bot Framework.

### <a name="consumption-plan"></a>Plano de consumo
Um bot que usa um plano de Consumo é um bot sem servidor que é executado no <a href="http://go.microsoft.com/fwlink/?linkID=747839" target="_blank">Azure Functions</a> e usa os preços do Azure Functions de pagar por execução. Um bot que usa este plano de hospedagem pode ser dimensionado para lidar com grandes picos de tráfego. Você pode editar o código-fonte do bot online usando um editor de código básico no navegador. Para saber mais sobre o ambiente de tempo de execução de um bot de plano de Consumo em <a target='_blank' href='/azure/azure-functions/functions-scale'>Planos de Serviço de Aplicativo e Consumo do Azure Functions</a>.

## <a name="templates"></a>Modelos

O Serviço de Bot permite criar de forma rápida e fácil um bot em C# ou em Node.js usando um dos cinco modelos.

[!INCLUDE [Bot Service templates](~/includes/snippet-abs-templates.md)]

[Saiba mais](bot-service-concept-templates.md) sobre os diferentes modelos e como eles podem ajudar você a criar bots.

## <a name="develop-and-deploy"></a>Desenvolver e implantar

Por padrão, o Serviço de Bot permite desenvolver seu bot diretamente no navegador usando o Editor de Códigos Online, sem a necessidade de uma cadeia de ferramentas. 

Você pode desenvolver e depurar seu bot localmente com o SDK do Bot Framework e um IDE, como o Visual Studio 2017. Você também pode publicar o seu bot diretamente no Azure usando o Visual Studio 2017 ou a CLI do Azure. Também é possível [configurar a implantação contínua](bot-service-continuous-deployment.md) com o sistema de controle do código-fonte da sua preferência, como o VSTS ou o GitHub. Com a implantação contínua configurada, é possível desenvolver e depurar em um IDE no seu computador local, e qualquer alteração do código que você confirmar para o controle do código-fonte será implantada automaticamente no Azure.  

> [!TIP]
> Depois de habilitar a implantação contínua, modifique o seu código apenas pela implantação contínua e não por outros mecanismos para evitar conflitos.

## <a name="manage-your-bot"></a>Gerenciar o seu bot 

Durante o processo de criação de um bot usando o Serviço de Bot, você especificará o nome para o seu bot, configurará o plano de hospedagem, escolherá o tipo de preço e definirá outras configurações. Depois que o seu bot tiver sido criado, você poderá alterar as configurações dele, configurá-lo para um ou mais canais e testá-lo com o Webchat. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um bot com o Serviço de Bot](bot-service-quickstart.md)