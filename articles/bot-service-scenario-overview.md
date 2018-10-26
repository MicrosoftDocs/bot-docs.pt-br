---
title: Visão geral dos cenários de Serviço de Bot | Microsoft Docs
description: Explore os principais cenários para bots avançados e eficazes construídos com Serviço de Bot.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e195f83eefd5f162b74f8891f3b174efc8934700
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997955"
---
# <a name="bot-scenarios"></a>Cenário de bots

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Este tópico explora os principais cenários para bots poderosos e eficazes construídos usando o Serviço de Bot.

É possível baixar ou clonar o código-fonte para todos os cenários de exemplos de bot em [Exemplos para cenários comuns do Framework Bot](https://aka.ms/bot/scenarios).

## <a name="commerce-bot-scenario"></a>Cenário de bot comercial
O cenário de [bot Comercial](bot-service-scenario-commerce.md) descreve um bot que substitui as interações tradicionais de email e telefone normalmente usadas por serviços de recepção de um hotel. O bot aproveita os Serviços Cognitivos para processar melhor as solicitações do cliente por meio de voz e texto com contexto obtido pela integração com serviços de back-end.

No cenário de bot Comercial, um cliente pode fazer uma solicitação para serviços de recepção de um hotel. A autenticação é por meio de um ponto de extremidade de autenticação do Azure Active Directory v2. O bot pode procurar as reservas do cliente e fornecer diferentes opções de serviço. Por exemplo, o cliente pode reservar um quiosque à beira da piscina. O bot usa o LUIS (Serviço Inteligente de Reconhecimento Vocal) para analisar a solicitação e, em seguida, o bot conduz o usuário pelo processo de reservar um quiosque de uma reserva existente.

## <a name="cortana-skill-bot-scenario"></a>Cenário de bot da Habilidade da Cortana
O [bot da Habilidade da Cortana](bot-service-scenario-cortana-skill.md) utiliza as vantagens da Cortana. Usando a interface natural da sua voz e um bot personalizado da Habilidade da Cortana, você pode pedir para a Cortana falar com uma organização, como um centro de estética automotiva, para ajudá-lo a agendar uma visita com base na localização onde você está quando faz a ligação. O bot pode fornecer uma lista de serviços, horários disponíveis e duração.

## <a name="enterprise-productivity-bot-scenario"></a>Cenário de bot de Produtividade Empresarial
O [bot de Produtividade Empresarial](bot-service-scenario-enterprise-productivity.md) mostra como integrar um bot ao calendário do Office 365 e a outros serviços para aumentar a produtividade.

O bot integra-se ao Office 365 para tornar mais rápido e fácil a criação de uma solicitação de reunião com outra pessoa. Para esse processo, você pode acessar serviços adicionais como o Dynamics CRM. Esse exemplo fornece o código necessário para integração com Office 365 com autenticação via Azure Active Directory. Ele fornece pontos de entrada simulados para serviços externos como um exercício para o leitor.

## <a name="information-bot-scenario"></a>Cenário de bot de Informações
Esse [bot de Informações](bot-service-scenario-informational.md) pode responder perguntas definidas em um conjunto de conhecimentos ou perguntas frequentes usando o QnA Maker dos Serviços Cognitivos e responder a mais perguntas em aberto do Azure Search.

Frequentemente, as informações são arquivadas em armazenamentos de dados estruturados, como SQL Server, que podem facilmente ser exibidas por meio de pesquisa. Imagine pesquisar o status do pedido de um cliente por meio de comandos de conversa simples. Com o QnA Maker dos Serviços Cognitivos, o usuário recebe um conjunto de opções válidas de pesquisa, por exemplo, pesquisa de um cliente, analisar o pedido de mais recente do cliente etc. Com o formato do QnA definido, o usuário pode fazer perguntas respaldadas no Azure Search, o qual pode procurar dados armazenados em um Banco de Dados SQL.

## <a name="iot-bot-scenario"></a>Cenário de bot de IoT
Esse bot de [IoT (Internet das Coisas)](bot-service-scenario-internet-things.md) facilita o controle dos dispositivos de sua residência, como uma lâmpada Philips Hue, usando comandos de chat interativos.

Usando esse bot simples, é possível controlar as lâmpadas Philips Hue em conjunto com o serviço gratuito IFTTT (If This Then That). Como um dispositivo IoT, a Philips Hue pode ser controlada localmente por meio da API exposta. Contudo, essa API não é exposta para acesso geral externo à rede local. No entanto, o IFTTT é um “[Friend of Hue](http://www2.meethue.com/en-us/friends-of-hue/ifttt/)” e, portanto, tem exposto uma série de comandos de controle que podem ser emitidos como ligar e desligar as lâmpadas, alterar a cor da luz ou a intensidade da luz.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem uma visão geral dos cenários, aprofunde-se em cada um deles.

> [!div class="nextstepaction"]
> [Bot Comercial](bot-service-scenario-commerce.md)
