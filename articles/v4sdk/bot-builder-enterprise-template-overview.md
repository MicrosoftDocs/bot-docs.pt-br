---
title: Visão geral detalhada do Modelo de Bot do Enterprise | Microsoft Docs
description: Saiba mais sobre as decisões de design por trás do Modelo de Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 582ec21d36e15fcbaef2d26616a9e55a04d8e5f2
ms.sourcegitcommit: b2245df2f0a18c5a66a836ab24a573fd70c7d272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568213"
---
# <a name="enterprise-bot-template---overview"></a>Enterprise Bot Template - visão geral

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

O Enterprise Bot Template fornece uma base sólida de melhores práticas e serviços necessários para criar uma experiência de conversação, reduzindo esforços e aumentando o nível de qualidade. O modelo utiliza o [Bot Builder SDK v4](https://github.com/Microsoft/botbuilder) e as [Ferramentas do Bot Builder](https://github.com/Microsoft/botbuilder-tools) para oferecer os seguintes recursos:

Recurso      | DESCRIÇÃO |
------------ | -------------
Introdução | Mensagem de introdução com um [Cartão Adaptável]() no início da conversa
Indicadores de digitação  | Indicadores visuais de digitação automatizados durante as conversas e repetição em operações de execução prolongada
Modelo LUIS básico  | Oferece suporte a intenções comuns, como **Cancelar**, **Ajuda**, **Escalonar** etc.
Diálogos básicos | Fluxos de diálogo para captura de informações básicas do usuário, bem como lógica de interrupção das intenções de cancelar e de ajuda
Respostas de base  | Respostas de texto e fala para intenções de base e diálogos
Perguntas frequentes | Integração com [QnA Maker](https://www.qnamaker.ai) para responder a perguntas gerais de uma base de conhecimento 
Bate-papo | Um modelo de bate-papo profissional para fornecer respostas padrão às consultas comuns ([saiba mais](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base))
Dispatcher | Um modelo [Dispatch](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) integrado para identificar se um determinado enunciado deve ser processado por LUIS ou QnA Maker.
Suporte ao idioma | Disponível em inglês, francês, italiano, alemão, espanhol e chinês
Transcrições | Transcrições de todas as conversas guardadas no Armazenamento do Azure
Telemetria  | Integração do [Application Insights](https://azure.microsoft.com/en-gb/services/application-insights/) para coletar telemetria de todas as conversas
Análise | Um exemplo de painel do Power BI para ajudar você a começar a usar insights em suas experiências de conversação.
Implantação automatizada | Implantação fácil de todos os serviços já mencionados utilizando [Ferramentas do Bot Builder](https://github.com/Microsoft/botbuilder-tools)

# <a name="background"></a>Segundo plano

## <a name="introduction-card"></a>Cartão de apresentação
Um cartão de apresentação melhora as conversas, oferecendo uma visão geral dos recursos do bot, bem como exemplos de enunciado para facilitar a introdução. Ele também estabelece a personalidade do bot para o usuário.

## <a name="base-luis-model-with-common-intents"></a>Modelo LUIS básico com intenções comuns
O modelo LUIS básico incluído no modelo aborda uma seleção das intenções mais comuns que a maioria dos bots precisará processar. As seguintes intenções estão disponíveis para uso em seu bot:

Intenção       | Exemplo de enunciados |
-------------|-------------|
Cancelar       |*cancel*, *nevermind*|
Escalonar     |*can I talk to a person?*|
FinishTask   |*done*, *all finished*|
GoBack       |*go back*|
Ajuda         |*can you help me?*|
Repetir       |*can you say that again?*|
SelectAny    |*any of these*|
SelectItem   |*the first one*|
SelectNone   |*none of these*|
ShowNext     |*show more*|
ShowPrevious |*show previous*|
StartOver    |*restart*|
Stop         |*stop*|

## <a name="qna-maker"></a>QnA Maker

O [QnA Maker](https://www.qnamaker.ai/) permite que não desenvolvedores gerem uma coleção de pares de perguntas e respostas para uso em um bot. Esse conhecimento pode ser importado de fontes de dados de Perguntas frequentes, de manuais de produto ou criados manualmente no portal do QnA Maker.

Dois exemplos de modelos do QnA Maker são fornecidos no modelo, um de Perguntas frequentes e outro para [Bate-papo Profissional](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base). 

## <a name="dispatch"></a>Dispatch

O serviço Dispatch é usado para gerenciar o roteamento entre vários modelos LUIS e bases de conhecimento do QnA Maker, extraindo enunciados de cada serviço e criando um modelo LUIS de expedição central.

Isso permite que um bot identifique rapidamente qual componente deve lidar com determinado enunciado e faz com que os dados do QnA Maker sejam considerados no nível superior do processamento de intenções e não no final de uma hierarquia.

Essa ferramenta Dispatch também permite avaliar os modelos que destacam enunciados sobrepostos e discrepâncias entre os serviços.

## <a name="telemetry"></a>Telemetria

Fornecer insights a conversas de bot pode ajudar você a reconhecer os níveis de participação dos usuários, quais recursos eles estão usando e as perguntas que o bot não consegue processar.

O [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) captura telemetria operacional para o serviço do bot e também para eventos específicos de conversa. Esses eventos podem ser agregados em informações acionáveis, usando ferramentas como o [Power BI](https://powerbi.microsoft.com/en-us/what-is-power-bi/). Um exemplo de painel do Power BI está disponível para uso com o Enterprise Bot Template, que demonstra esse recurso.

# <a name="next-steps"></a>Próximas etapas
Consulte [Introdução](bot-builder-enterprise-template-getting-started.md) para saber como criar e implantar seu Enterprise Bot. 

# <a name="resources"></a>Recursos
O código-fonte completo do Enterprise Bot Template pode ser encontrado no [GitHub](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template).
