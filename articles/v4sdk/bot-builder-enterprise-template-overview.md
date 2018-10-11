---
title: Visão geral do Modelo de Bot do Enterprise | Microsoft Docs
description: Saiba mais sobre os recursos do Modelo de Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9636b1c88bd99b94f3bf266a3a321d8f6e2747d4
ms.sourcegitcommit: 87b5b0ca9b0d5e028ece9f7cc4948c5507062c2b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47029784"
---
# <a name="enterprise-bot-template"></a>Modelo de Bot do Enterprise 

> [!NOTE]
> Este tópico aplica-se à versão v4 do SDK. 

A criação de uma experiência de conversação de alta qualidade exige um conjunto básico de recursos. Para ajudá-lo a ter sucesso na criação de ótimas experiências de conversação, criamos um Modelo de Bot do Enterprise. Este modelo reúne todas as práticas recomendadas e componentes de suporte que identificamos através da criação de experiências de conversação. 

Este modelo simplifica muito a criação de um novo projeto de bot. O modelo fornecerá os seguintes recursos prontos para uso, aproveitando o [SDK v4 do Bot Builder](https://github.com/Microsoft/botbuilder) e as [Ferramentas do Bot Builder](https://github.com/Microsoft/botbuilder-tools).

Recurso | DESCRIÇÃO |
------------ | -------------
Mensagem de introdução | Mensagem de introdução com um Cartão Adaptável no início da conversa. Ele explica os recursos dos bots e fornece botões para orientar perguntas iniciais. Os desenvolvedores podem personalizá-los conforme apropriado.
Indicadores automatizados de digitação  | Envie indicadores visuais de digitação durante as conversas e repita isso em operações de longa execução.
Configuração controlada pelo arquivo .bot | Todas as informações sobre a configuração do Bot, por exemplo, LUIS, Dispatcher Endpoints, Application Insights, são incorporadas ao arquivo .bot e usadas para impulsionar a Inicialização do Bot.
Intenções básicas de conversação  | Intenções básicas (Saudação, Adeus, Ajuda, Cancelar, etc.) em inglês, francês, italiano, alemão e espanhol. Elas são disponibilizadas nos arquivos .LU (reconhecimento vocal) e é muito simples modificá-las.
Respostas básicas de conversação  | Respostas para intenções básicas de conversação abstraídas em classes View separadas. No futuro, elas serão transferidas para os novos arquivos de geração de linguagem (LG).
Conteúdo inadequado ou PII (informação de identificação do usuário)  |Detecte dados inadequados ou de PII em conversas recebidas utilizando o [Content Moderator](https://azure.microsoft.com/en-us/services/cognitive-services/content-moderator/) em um componente de middleware.
Transcrições  | Transcrições de todas as conversas guardadas no Armazenamento do Azure
Dispatcher | Um modelo [Dispatch](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) integrado para identificar se uma determinada expressão deveria ser processada pelo LUIS + Code ou passada para o QnAMaker.
Integração do QnAMaker  | Integração com o [QnAMaker](https://www.qnamaker.ai) para responder a perguntas gerais a partir de uma Base de Conhecimento que pode aproveitar fontes de dados existentes (por exemplo, manuais em PDF).
Insights de conversas  | Integração com o [Application Insights](https://azure.microsoft.com/en-gb/services/application-insights/) para coletar telemetria para todas as conversas e um painel de exemplo do Power BI para começar com insights sobre suas experiências de conversação.

Além disso, todos os recursos do Azure de que o Bot precisa são implantados automaticamente: registro do Bot, Serviço de Aplicativo do Azure, LUIS, QnAMaker, Content Moderator, CosmosDB, Armazenamento do Azure e Application Insights. Além disso, os modelos básicos do LUIS, QnAMaker e Dispatch são criados, treinados e publicados para permitir testes imediatos de intenções e roteamento básicos.

Após a criação do modelo e da execução das etapas de implantação, pressione F5 para testar de ponta a ponta. Isso fornecerá uma base sólida sobre a qual a experiência de conversação será inciada, reduzindo a carga de vários dias de trabalho que cada projeto teve que realizar, e eleva o nível de qualidade da conversa.

Para começar, continue a [criar o projeto](bot-builder-enterprise-template-create-project.md). Para saber mais sobre as práticas recomendadas e lições aprendidas que orientaram os recursos acima, confira o tópico sobre [detalhes do modelo](bot-builder-enterprise-template-overview-detail.md). 

O código-fonte completo para o Modelo de Bot do Enterprise está disponível neste [local do GitHub](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template)
