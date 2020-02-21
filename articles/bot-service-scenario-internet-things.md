---
title: Cenários de bot da Internet das Coisas – Serviço de Bot
description: Explore o cenário de bot de Internet das Coisas com o Bot Framework.
author: BrianRandell
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a6714a7eabb2af22e9a06b2a151cab1c9fa8a2f2
ms.sourcegitcommit: e5bf9a7fa7d82802e40df94267bffbac7db48af7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441584"
---
# <a name="internet-of-things-iot-bot-scenario"></a>Cenário de Bot de IoT (Internet das Coisas)

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Este Bot de IoT (Internet das Coisas) facilita o controle dos dispositivos de sua residência, como uma lâmpada Philips Hue usando comandos de voz ou de bate-papo interativo.

As pessoas adoram conversar com as próprias coisas. Desde a época da primeira TV com controle remoto, as pessoas adoram não precisar se movimentar para afetar o ambiente. Este bot de IoT permite que uma pessoa gerencie uma Philips Hue por meio de comandos simples de chat ou de voz. Além disso, ao usar o chat, uma pessoa pode receber opções visuais relacionadas às cores para escolha.

![Diagrama do Bot de Internet das Coisas](~/media/scenarios/bot-service-scenario-iot-bot.png)

Este é o fluxo lógico de um bot de IoT:

1. O usuário faz logon no Skype e acessa o bot IoT.
2. Com o uso da voz, o usuário pede ao bot que ligue as luzes por meio do dispositivo IoT.
3. A solicitação é retransmitida para um serviço de terceiros que tenha acesso à rede de dispositivos IoT.
4. Os resultados do comando são fornecidos ao usuário.
5. O Application Insights coleta a telemetria do runtime para ajudar o desenvolvimento com o desempenho e uso do bot.

## <a name="sample-bot"></a>Bot de exemplo
O bot de IoT permitirá que você use rapidamente comandos de chat de canais como Skype ou Slack para controlar sua Hue. Para facilitar o acesso remoto, você chamará miniaplicativos IFTTT predefinidos para trabalhar com a Hue.

Baixe ou clone o código-fonte desse bot de exemplo em [Exemplos para cenários comuns do Bot Framework](https://aka.ms/abs-scenarios).

## <a name="components-youll-use"></a>Componentes que serão utilizados
O Bot de IoT (Internet das Coisas) usa os seguintes componentes:
-   Philips Hue
-   IFTTT (If This Then That)
-   Application Insights

### <a name="philips-hue"></a>Philips Hue
As lâmpadas e ponte conectadas da Philips Hue permitem que você assuma controle total da sua iluminação. Não importa o que você queira fazer com a iluminação, a Hue consegue. A Hue tem uma API que você pode usar em sua rede local. No entanto, você quer poder acessar seus dispositivos e luzes controlados pela Hue de qualquer lugar usando uma interface de Bot amigável. Por isso, você acessará a Hue via IFTTT.

### <a name="ifttt"></a>IFTTT
IFTTT é um serviço gratuito baseado na Web usado pelas pessoas para criar cadeias de instruções condicionais simples, chamadas de miniaplicativos. Você pode disparar um miniaplicativo de seu Bot para que ele faça algo em seu nome. Há diversos miniaplicativos predefinidos da Hue disponíveis para acender e apagar luzes, alterar o ambiente e muito mais.

### <a name="application-insights"></a>Application Insights
O Application Insights ajuda a obter insights práticos pelo APM (gerenciamento de desempenho de aplicativo) e por análises instantâneas. Pronto para uso, é possível obter monitoramento de desempenho avançado, alertas eficientes e painéis fáceis de usar para ajudar a garantir que o Bot esteja disponível e executando conforme o esperado. Veja rapidamente se há algum problema e faça uma análise da causa raiz para encontrar e corrigi-lo.
