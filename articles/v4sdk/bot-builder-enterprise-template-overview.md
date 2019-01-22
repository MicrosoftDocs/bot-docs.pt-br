---
title: Visão geral do Modelo de Bot do Enterprise | Microsoft Docs
description: Saiba mais sobre os recursos do Modelo de Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a0b56b77990c095d1cf8bb28235d06444837d164
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224592"
---
# <a name="enterprise-bot-template"></a>Modelo de Bot do Enterprise 

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

A criação de uma experiência de conversação de alta qualidade exige um conjunto básico de recursos. Para ajudá-lo a ter sucesso na criação de ótimas experiências de conversação, criamos um Modelo de Bot do Enterprise. Este modelo reúne todas as práticas recomendadas e componentes de suporte que identificamos através da criação de experiências de conversação. 

Este modelo simplifica muito a criação de um novo projeto de bot. O modelo fornecerá os seguintes recursos prontos para uso, aproveitando o [SDK v4 do Bot Framework](https://github.com/Microsoft/botbuilder) e as [Ferramentas do Bot Framework](https://github.com/Microsoft/botbuilder-tools).

Recurso | DESCRIÇÃO |
------------ | -------------
Mensagem de introdução | Mensagem de introdução com um Cartão Adaptável no início da conversa. Ele explica os recursos dos bots e fornece botões para orientar perguntas iniciais. Os desenvolvedores podem personalizá-los conforme apropriado.
Indicadores automatizados de digitação  | Envie indicadores visuais de digitação durante as conversas e repita isso em operações de longa execução.
Configuração controlada pelo arquivo .bot | Todas as informações sobre a configuração do Bot, por exemplo, LUIS, Dispatcher Endpoints, Application Insights, são incorporadas ao arquivo .bot e usadas para impulsionar a Inicialização do Bot.
Intenções básicas de conversação  | Intenções básicas (Saudação, Adeus, Ajuda, Cancelar, etc.) em inglês, francês, italiano, alemão, espanhol e chinês. Elas são disponibilizadas nos arquivos .LU (reconhecimento vocal) e é muito simples modificá-las.
Respostas básicas de conversação  | Respostas para intenções básicas de conversação abstraídas em classes View separadas. No futuro, elas serão transferidas para os novos arquivos de geração de linguagem (LG).
Conteúdo inadequado ou PII (informação de identificação do usuário)  |Detecte dados inadequados ou de PII em conversas recebidas utilizando o [Content Moderator](https://azure.microsoft.com/en-us/services/cognitive-services/content-moderator/) em um componente de middleware.
Transcrições  | Transcrições de todas as conversas guardadas no Armazenamento do Azure
Dispatcher | Um modelo [Dispatch](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) integrado para identificar se uma determinada expressão deveria ser processada pelo LUIS + Code ou passada para o QnA Maker.
Integração com o QnA Maker  | Integração com o [QnAMaker](https://www.qnamaker.ai) para responder a perguntas gerais a partir de uma Base de Conhecimento que pode aproveitar fontes de dados existentes (por exemplo, manuais em PDF). Um modelo de bate-papo do QnA Maker também está incluído para fornecer respostas padrão às consultas comuns ([saiba mais](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base)).
Insights de conversas  | Integração com o [Application Insights](https://azure.microsoft.com/en-gb/services/application-insights/) para coletar telemetria para todas as conversas e um painel de exemplo do Power BI para começar com insights sobre suas experiências de conversação.

Além disso, todos os recursos do Azure necessários para o Bot são implantados automaticamente: Registro de bot, Serviço de Aplicativo do Azure, LUIS, QnA Maker, Content Moderator, CosmosDB, Armazenamento do Azure e Application Insights. Além disso, os modelos básicos do LUIS, QnA Maker e Dispatch são criados, treinados e publicados para permitir testes imediatos de intenções e roteamento básicos.

Após a criação do modelo e da execução das etapas de implantação, pressione F5 para testar de ponta a ponta. Isso fornecerá uma base sólida sobre a qual a experiência de conversação será inciada, reduzindo a carga de vários dias de trabalho que cada projeto teve que realizar, e eleva o nível de qualidade da conversa.

Para começar, continue a [criar o projeto](bot-builder-enterprise-template-create-project.md). Para saber mais sobre as práticas recomendadas e lições aprendidas que orientaram os recursos acima, confira o tópico sobre [detalhes do modelo](bot-builder-enterprise-template-overview-detail.md). 

O código-fonte completo para o Modelo de Bot do Enterprise está disponível neste [local do GitHub](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template)
