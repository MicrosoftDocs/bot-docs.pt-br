---
title: Novidades – Serviço de Bot
description: Conheça as novidades do Bot Framework.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5b8b9e23168bdf45180c354edcfba99801d653e3
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77441534"
---
# <a name="whats-new-december-2019"></a>Novidades em dezembro de 2019

[!INCLUDE[applies-to](includes/applies-to.md)]

O SDK do Bot Framework v4 é um [SDK open-source](https://github.com/microsoft/botframework-sdk/#readme) que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Versão |[GA][1] | [GA][2] | [GA][3] | [Versão prévia 3][3a]|
|Docs | [docs][5] |[docs][5] |[docs][5]  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11a] | | 

#### <a name="python-sdk-ga"></a>SDK do Python (disponibilidade geral)
Temos o prazer de anunciar a disponibilidade geral do SDK do Python. O SDK dá suporte ao runtime do bot principal, a conectores, middleware, caixas de diálogo, prompts, ao LUIS (Reconhecimento vocal) e ao QnA Maker. 

#### <a name="bot-framework-sdk-for-skills-ga"></a>SDK do Bot Framework para Habilidades (disponibilidade geral)

- **Habilidades para bots**: crie habilidades de conversa reutilizáveis para adicionar funcionalidade a um bot. Aproveite as habilidades predefinidas, como as habilidades de Calendário, Email, Tarefa, Ponto de Interesse, Automotivo, Clima e Notícias. As habilidades incluem modelos de linguagem, caixas de diálogo, QnA e o código de integração entregue para personalizar e estender, conforme necessário. [[Docs](https://aka.ms/skills-docs)]

- **Habilidades para o Power Virtual Agent – Em breve!** : Para bots criados com Power Virtual Agents, você pode criar habilidades para esses bots usando o Bot Framework e os Serviços Cognitivos do Azure sem precisar criar um bot do zero. 

#### <a name="bot-framework-for-power-virtual-agent-ga"></a>Bot Framework para Power Virtual Agent (disponibilidade geral)

O Power Virtual Agent foi projetado para permitir que os usuários corporativos criem bots dentro de uma experiência SaaS de criação de bots baseada em interface do usuário, sem precisar codificar nem gerenciar serviços de IA específicos. Os Power Virtual Agents podem ser estendidos com o Microsoft Bot Framework, permitindo que os desenvolvedores e usuários de negócios colaborem na criação de bots para suas organizações. [[Docs](https://docs.microsoft.com/dynamics365/ai/customer-service-virtual-agent/overview)]

#### <a name="adaptive-dialogs-preview"></a>Caixas de Diálogo Adaptáveis (Versão prévia)
As Caixas de Diálogo Adaptáveis permitem que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente prático ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa. [[Docs][48] | [Exemplos de C#][49]] 

#### <a name="language-generation-preview"></a>Geração de Linguagem (Versão prévia)
Com a Geração de Linguagem, os desenvolvedores podem separar a lógica usada para gerar as respostas do bot, incluindo a capacidade de definir diversas variações em uma frase, executar expressões simples baseadas em contexto e consultar a memória conversacional. [[Docs][44] | [Exemplos de C#][45]]

#### <a name="common-expression-language-preview"></a>Linguagem de Expressão Comum (Versão prévia)
A Linguagem de Expressão Comum permite que você avalie o resultado de uma lógica baseada em condição em runtime. A linguagem comum pode ser usada em todos os componentes de SDK do Bot Framework e de AI conversacional, como Caixas de Diálogo Adaptáveis e Geração de Linguagem. [[Docs][40] | [API][41]]

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[11a]:https://aka.ms/python-sample-repo


[40]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/common-expression-language#readme
[41]:https://github.com/Microsoft/BotBuilder-Samples/blob/master/experimental/common-expression-language/api-reference.md
[43]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation#readme
[44]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/docs
[45]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/csharp_dotnetcore
[46]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/javascript_nodejs/13.core-bot
[47]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme
[48]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs
[49]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore
[50]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/declarative

## <a name="additional-information"></a>Informações adicionais
- Você pode ver os comunicados anteriores [aqui](what-is-new-archive.md).
