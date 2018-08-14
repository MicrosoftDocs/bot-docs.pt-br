---
title: Cenário de bot de Habilidades da Cortana | Microsoft Docs
description: Explore o cenário de bot de Habilidades da Cortana com o Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2483366c6d325e5c18a77c2a89b63cc0350aef85
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296742"
---
# <a name="cortana-skills-bot-scenario"></a>Cenário de Bot de Habilidades da Cortana
O Bot de Habilidades da Cortana estende a Cortana para facilitar o agendamento no dispositivo móvel de um compromisso de manutenção automotiva usando a voz com o contexto do calendário.

A Cortana é sua assistente pessoal. Usando a interface natural de sua voz e um Bot de Habilidade da Cortana personalizado, você pode solicitar que a Cortana fale com uma organização, como uma oficina automotiva, para ajudá-lo a criar um compromisso. O serviço pode fornecer uma lista de serviços, as horas disponíveis e a duração. A Cortana pode observar o calendário para ver se você tem algo em um horário conflitante e, se não tiver, criará o compromisso e o adicionará ao calendário.

![O diagrama do bot de Habilidade da Cortana](~/media/scenarios/bot-service-scenario-cortana-skill.png)

Este é o fluxo de lógica de um bot de Habilidades da Cortana para uma oficina automotiva:

1. O usuário acessa a Cortana no computador ou no dispositivo móvel.
2. Usando comandos de texto ou de voz, o usuário solicita um compromisso de manutenção automotiva.
3. Como o bot é integrado à Cortana, ela tem acesso ao calendário do usuário e aplica a lógica à solicitação.
4. Com essas informações, o bot pode consultar o serviço automotivo para obter compromissos válidos.
5. Recebendo as opções contextuais, o usuário pode reservar o compromisso.
6. O Application Insights coleta a telemetria do tempo de execução para ajudar o desenvolvimento com o desempenho e o uso do bot.

## <a name="sample-bot"></a>Bot de exemplo
Com um Bot de Habilidades da Cortana, tudo se resume ao contexto pessoal. Usando a Cortana, você pode usar a voz para solicitar que a "Manutenção Móvel do Fábio" venha consertar seu carro com base em sua localização. Usando as informações pessoais expostas por meio da Cortana, o bot pode confirmar a localização de acordo com o local em que o usuário está no momento da conversa com o bot.

Baixe ou clone o código-fonte desse bot de exemplo em [Amostras de cenários comuns do Bot Framework](https://aka.ms/bot/scenarios).

## <a name="components-youll-use"></a>Componentes que você usará
O Bot da Cortana usa os seguintes componentes:
-   Cortana
-   Application Insights

### <a name="cortana"></a>Cortana
Agora você pode adicionar suporte ao Bot criando uma Habilidade da Cortana. Use o kit de Habilidades da Cortana para criar recursos (chamados de habilidades) para a Cortana. Uma habilidade é um constructo que permite à Cortana fazer mais. Crie habilidades para integrá-las aos Bots, permitindo que a Cortana realize tarefas e faça coisas. Como parte do processo de invocação, a Cortana pode (com o consentimento do usuário) passar informações sobre o usuário para uma habilidade em tempo de execução, de modo que a habilidade possa personalizar sua experiência de acordo. O conhecimento contextual da Cortana possibilita que o Bot seja útil e, possivelmente, até mesmo mais inteligente para o usuário. Quando invocados, alguns tipos de habilidades podem manipular a interface da Cortana para ter uma conversa entre a habilidade e o usuário final. Depois que a habilidade é publicada, os usuários podem ver e usar a habilidade na Cortana para a Atualização de Aniversário do Windows 10+ (Desktop e Mobile), no iOS e no Android.

### <a name="application-insights"></a>Application Insights
O Application Insights ajuda você a obter insights práticos por meio do APM (gerenciamento de desempenho de aplicativos) e de análises instantâneas. Você conta, imediatamente, com o monitoramento de desempenho avançado, alertas eficientes e painéis fáceis de usar para ajudar a garantir que seu Bot esteja disponível e com o desempenho esperado. Veja rapidamente se há algum problema e, em seguida, faça uma análise de causa raiz para encontrar o problema e corrigi-lo.

## <a name="next-steps"></a>Próximas etapas
A seguir, saiba mais sobre o cenário de bot de Produtividade Empresarial.

> [!div class="nextstepaction"]
> [Cenário de bot de Produtividade Empresarial](bot-service-scenario-enterprise-productivity.md)
