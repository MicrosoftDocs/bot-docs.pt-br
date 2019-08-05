---
title: Novidades | Microsoft Docs
description: Conheça as novidades do Bot Framework.
keywords: bot framework, serviço de bot do azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.subservice: abs
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f7083c45e67d8731e25e14577f6b061732ffefd5
ms.sourcegitcommit: f3fda6791f48ab178721b72d4f4a77c373573e38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68671394"
---
# <a name="whats-new-in-bot-framework-july-2019"></a>Novidades no Bot Framework (julho de 2019)

[!INCLUDE[applies-to](includes/applies-to.md)]

O SDK do Bot Framework v4 é um [SDK open-source][1a] que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|   | C#  | JS  | Python |   
|---|:---:|:---:|:------:|
|. |[4.5][1] | [4.5][2] | [4.4.0b2 (versão prévia)][3] | 
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][111] | | 

[1a]:https://github.com/microsoft/botframework-sdk/#readme
[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_typescript
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[111]:https://github.com/Microsoft/botbuilder-python/tree/master/samples


## <a name="bot-framework-channels"></a>Canais do Bot Framework
- [Direct Line Speech (versão prévia pública)](https://aka.ms/streaming-extensions) | [documentos:](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0) O Bot Framework e os Serviços de Fala da Microsoft fornecem um canal que permite fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot, usando WebSockets.  

- [Extensão do Serviço de Aplicativo Direct Line (versão prévia pública)](https://portal.azure.com) | [documentos](https://aka.ms/directline-ase): Uma versão da Direct Line que permite que os clientes se conectem diretamente a bots usando a Direct Line API. Isso oferece muitos benefícios, incluindo melhor desempenho e mais isolamento. A extensão do Serviço de Aplicativo Direct Line está disponível em todos os Serviços de Aplicativo do Azure, incluindo aqueles hospedados em um Ambiente do Serviço de Aplicativo do Azure. Um Ambiente do Serviço de Aplicativo do Azure oferece isolamento e é ideal para trabalhar em uma VNet. Uma VNet permite que você crie seu próprio espaço privado no Azure e é crucial para sua rede de nuvem, pois oferece isolamento, segmentação e outros benefícios importantes. 

## <a name="bot-framework-sdk"></a>SDK do Bot Framework
- [Caixa de Diálogo Adaptável (SDK v4.6 versão prévia)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme) | [documentos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs) | [exemplos de C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore): A Caixa de Diálogo Adaptável agora permite que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente útil ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa. 
  
- [SDK do Python do Bot Framework (versão prévia 2)](https://github.com/microsoft/botbuilder-python) | [exemplos](https://github.com/Microsoft/botbuilder-python/tree/master/samples): O SDK do Python agora dá suporte a OAuth, Prompts, CosmosDB e inclui todas as principais funcionalidades no SDK 4.5. Além disso, há exemplos para ajudá-lo a saber mais sobre os novos recursos do SDK.

## <a name="bot-framework-testing"></a>Testes do Bot Framework
- [Teste de unidade](http://aka.ms/bot-test-package) | [documentos](https://aka.ms/testing-framework) | [exemplo de C#](https://aka.ms/corebot-test) | [exemplo de JS](https://aka.ms/js-core-test-sample): Respondendo à solicitação dos clientes e desenvolvedores por melhores ferramentas de teste, a versão de julho do SDK apresenta uma nova funcionalidade de teste de unidade. O pacote Microsoft.Bot.Builder.testing simplifica o processo de caixas de diálogo de teste de unidade em seu bot. 

- [Testes de canal](https://github.com/Microsoft/BotFramework-Emulator/releases) | [docs](https://aka.ms/channel-testing): 

Introduzido no Microsoft Build 2019, o Inspetor de Bot é um novo recurso no Bot Framework Emulator que permite depurar e testar bots em canais como Microsoft Teams, Slack, Cortana e muito mais. Conforme você usa o bot em canais específicos, as mensagens serão espelhadas para o Bot Framework Emulator, no qual você poderá inspecionar os dados da mensagem que o bot tiver recebido. Além disso, é renderizado um instantâneo do estado da memória do bot em todos os turnos entre o canal e o bot.

## <a name="web-chat"></a>Chat na Web
- Com base em solicitações de clientes corporativos, adicionamos um [exemplo de Webchat](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps#single-sign-on-demo-for-enterprise-apps-using-oauth) que mostra como autorizar um usuário a acessar recursos em um aplicativo empresarial com um bot. Dois tipos de recursos são usados para demonstrar a interoperabilidade do OAuth com o Microsoft Graph e a API do GitHub.

## <a name="solutions"></a>Soluções
- [Acelerador da solução Assistente Virtual](https://github.com/Microsoft/botframework-solutions#readme): Fornece um conjunto de modelos, aceleradores de solução e habilidades para ajudar a criar experiências de conversa sofisticadas. Novo cliente de aplicativo Android para o assistente virtual que se integra com o Direct-Line Speech e o Assistente Virtual, demonstrando como um cliente de dispositivo pode interagir com seu assistente virtual e renderizar Cartões Adaptáveis. As atualizações também incluem suporte para o Direct-Line Speech e o Microsoft Teams.
  
- [Dynamics 365 Virtual Agent for Customer Service (versão prévia pública)](https://dynamics.microsoft.com/en-us/ai/virtual-agent-for-customer-service/): Com a versão prévia pública, você pode fornecer um atendimento ao cliente excepcional com agentes virtuais inteligentes e adaptáveis. Os especialistas em atendimento ao cliente podem criar e aprimorar bots facilmente com informações baseadas em IA.
  
- [Chat para Dynamics 365](https://www.powerobjects.com/powerpacks/powerchat/): O Chat para Dynamics 365 oferece vários recursos para garantir que os agentes de suporte e os usuários finais possam interagir com eficiência e permaneçam altamente produtivos. Converse ao vivo e acompanhe conversas de visitantes em seu site no Microsoft Dynamics 365.

## <a name="additional-information"></a>Informações adicionais
- Você pode ver os comunicados anteriores [aqui](what-is-new-archive.md).
