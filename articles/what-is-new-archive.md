---
title: Novidades | Microsoft Docs
description: Conheça as novidades do Bot Framework.
keywords: bot framework, serviço de bot do azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0b7342274e37ff33afb8695e8f25fbf0fa707178
ms.sourcegitcommit: b053c0ca7f2e9e60679f7e82e583c57ae83fcb50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336747"
---
# <a name="whats-new-in-bot-framework-may-2019"></a>Novidades no Bot Framework (maio de 2019)

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|. |[4.4.3][1] | [4.4.0][2] | [4.4.0b1 (versão prévia)][3] | [4.0.0a6 (versão prévia)][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7] , [TypeScript][8], [es6][9]  | [Python][111] | | 

[1a]:https://github.com/microsoft/botframework-sdk/#readme
[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[4]:https://github.com/Microsoft/botbuilder-java#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_typescript
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[111]:https://github.com/Microsoft/botbuilder-python/tree/master/samples

<a name="V4-whats-new"></a>
## <a name="bot-framework-sdk-new-in-preview"></a>SDK do Bot Framework (Novo! Em versão prévia)

- [Caixa de diálogo adaptável][47] | [docs][48] | [Exemplos de C#][49]: Os Diálogos Adaptáveis permitem aos desenvolvedores criar conversas que podem ser alteradas dinamicamente conforme o andamento da conversa.  Tradicionalmente, os desenvolvedores mapeiam o fluxo inteiro de uma conversa com antecedência, o que limita sua flexibilidade.  Os Diálogos Adaptáveis permitem que elas sejam mais flexíveis, para responder a alterações no contexto e inserir novas etapas ou subdiálogos inteiros na conversa durante sua evolução. 

- [Geração de linguagem][43] | [docs][44] | [Exemplos de C#][45]: geração de linguagem, que permite ao desenvolvedor extrair as cadeias de caracteres inseridas dos seus arquivos de código e de recursos e gerenciá-las por meio de um formato arquivo e do tempo de execução de Geração de Linguagem.  A Geração de Linguagem permite que os clientes definam diversas variações de uma frase, executem expressões simples com base no contexto e consultem a memória de conversação; ao longo do tempo, isso nos permitirá criar outras funcionalidades que levarão a uma experiência de conversa mais natural.

- [Linguagem de Expressão Comum][40] | [api][41]: tanto os Diálogos Adaptáveis quanto a Geração de Linguagem usam e dependem de uma linguagem de expressão comum para alimentar conversas de bot.

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

## <a name="botkit"></a>Botkit
O [Botkit][100] é uma ferramenta de desenvolvedor e um SDK de criação de chatbots, aplicativos e integrações personalizadas para as principais plataformas de mensagens. Bots do Botkit: gatilhos `hear()`, perguntas `ask()` e respostas `say()`. Os desenvolvedores podem usar essa sintaxe para criar diálogos; agora compatíveis com a versão mais recente do SDK do Bot Framework. 

Além disso, o Botkit traz seis adaptadores de plataforma, permitindo que os aplicativos de bot JavaScript se comuniquem diretamente com as plataformas de mensagens: [Slack][102], [Webex Teams][103], [Google Hangouts][104], [Facebook Messenger][105], [Twilio][106] e [Webchat][107].

O Botkit faz parte do Microsoft Bot Framework e foi lançado com a [licença de software livre do MIT][101]

[100]:https://github.com/howdyai/botkit#readme
[101]:https://github.com/howdyai/botkit/blob/master/LICENSE.md
[102]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-slack#readme
[103]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-webex#readme
[104]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-hangouts#readme
[105]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-facebook#readme
[106]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-twilio-sms#readme
[107]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-web#readme

## <a name="bot-framework-solutions-new-in-preview"></a>Soluções do Bot Framework (Novo! Em versão prévia)

O [repositório de Soluções do Bot Framework](https://github.com/Microsoft/AI#readme) é a base para um conjunto de modelos, aceleradores de soluções e habilidades para ajudar a criar experiências de conversação avançadas que soam como um assistente.

| NOME | DESCRIÇÃO |  
|:------------|:------------| 
|[**Assistente Virtual**](https://github.com/Microsoft/AI/tree/master/docs#virtual-assistant) | Os clientes precisam realmente fornecer um assistente de conversação adaptado à sua marca, personalizado para seus usuários e disponibilizado em uma ampla variedade de telas e dispositivos. <br/><br/> O modelo empresarial simplifica bastante a criação de um projeto novo do bot, que inclui: intenções de conversação básicas, integração com o Dispatch, QnA Maker, Application Insights e uma implantação automatizada.|
|[**Habilidades**](https://github.com/Microsoft/AI/blob/master/docs/overview/skills.md)| Os desenvolvedores podem compor experiências de conversação combinando recursos de conversa reutilizáveis, conhecidos como Habilidades. As próprias Habilidades são Bots, chamados remotamente, e um modelo de desenvolvedor (.NET, TS) está disponível para facilitar a criação de novas Habilidades. 
|[**Analytics**](https://github.com/Microsoft/AI/blob/master/docs/readme.md#analytics)| Obtenha insights essenciais sobre a integridade e o comportamento do seu bot com as soluções de análise de inteligência Artificial de Conversação. Examine os dados telemétricos disponíveis, os exemplos de consultas do Application Insights e os painéis do Power BI para compreender a amplitude das conversas do seu bot com os usuários. |

## <a name="azure-bot-service"></a>Serviço de Bot do Azure
O Serviço de Bot do Azure permite que você hospede bots inteligentes e de nível empresarial com total controle e propriedade sobre seus dados. Os desenvolvedores podem registrar e conectar seus bots a usuários no Skype, no Microsoft Teams, na Cortana, no Web Chat e muito mais. [Azure][27]  |  [docs][28] | [conectar-se aos canais][29] 

* **Cliente JS Direct Line**: se você quiser usar o canal Direct Line no Serviço de Bot do Azure e não estiver usando o cliente WebChat, o cliente JS Direct Line poderá ser usado no seu aplicativo personalizado. Vá para o [GitHub][30] para obter mais informações.

<a name="ABS-whats-new"></a>

* **Novo! Canal de Fala do Direct Line**: estamos reunindo o Bot Framework e os Serviços de Fala da Microsoft para oferecer um canal que permita fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot.  Para obter mais informações, confira como adicionar [canal de fala ao bot](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0).

[27]:https://azure.microsoft.com/services/bot-service/
[28]:https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[29]:https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0
[30]:https://github.com/Microsoft/BotFramework-DirectLineJS/blob/master/README.md


## <a name="bot-framework-emulator"></a>Bot Framework Emulator
O [Bot Framework Emulator][60] é um aplicativo de área de trabalho multiplataforma que permite aos desenvolvedores testar e depurar bots criados usando o SDK do Bot Framework. Você pode usar o Bot Framework Emulator para testar bots em execução localmente no computador ou para se conectar a bots em execução remotamente.

- [Baixar a versão mais recente][61] | [Docs][62]

<a name="Emulator-whats-new"></a>
### <a name="bot-inspector-new-in-preview"></a>Inspetor de Bot (Novo! Em versão prévia)

O Bot Framework Emulator lançou uma versão beta do novo recurso Inspetor de Bot. Ele fornece uma maneira de depurar e testar seus bots do SDK do Bot Framework v4 em canais como Microsoft Teams, Slack, Cortana, Facebook Messenger, Skype etc. Durante a conversa, as mensagens serão espelhadas para o Bot Framework Emulator, no qual você poderá inspecionar os dados da mensagem que o bot recebeu. Além disso, é renderizado um instantâneo do estado do bot em todos os turnos entre o canal e o bot. Leia mais sobre o [Inspetor de Bot](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md).

[60]:https://github.com/Microsoft/BotFramework-Emulator#readme
[61]:https://github.com/Microsoft/BotFramework-Emulator/releases/latest
[62]:https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0


## <a name="related-services"></a>Serviços Relacionados

### <a name="language-understanding"></a>Reconhecimento Vocal 
Um serviço baseado em aprendizado de máquina para criar experiências de linguagem natural. Crie rapidamente modelos personalizados e prontos para a empresa que melhoram continuamente. O [LUIS (Serviço Inteligente de Reconhecimento Vocal)][30] permite que o aplicativo entenda o que uma pessoa quer com suas próprias palavras.

<a name="LUIS-whats-new"></a>

- **Novo! Funções, entidades externas e entidades dinâmicas**: o LUIS adicionou vários recursos que permitem aos desenvolvedores extrair informações mais detalhadas do texto, para que os usuários agora possam criar soluções mais inteligentes com menos esforço. O LUIS também estendeu funções a todos os tipos de entidade, o que permite que essas entidades sejam classificadas com subtipos diferentes com base no contexto. Os desenvolvedores agora têm um controle mais granular sobre o que eles podem fazer com o LUIS, incluindo a capacidade de identificar e atualizar modelos no tempo de execução por meio de listas dinâmicas e entidades externas. As listas dinâmicas são usadas para acrescentar às entidades de lista no tempo de previsão, permitindo que informações específicas do usuário tenham correspondência exata. Extratores de entidade suplementares separados são executados com entidades externas, e essas informações podem ser acrescentadas ao LUIS como sinais fortes para outros modelos.

- **Novo! Painel de Análise**: o LUIS está lançando um painel de análise abrangente mais detalhado e visualmente sofisticado. Seu design amigável realça os problemas comuns que a maioria dos usuários enfrenta ao criar aplicativos, fornecendo explicações simples sobre como resolvê-los para que os usuários obtenham mais insights sobre a qualidade dos seus modelos, possíveis problemas de dados e diretrizes para a adoção das melhores práticas.

[Docs][31] | [Add language understanding to your bot][32] 

[18]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#readme
[19]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#readme
[30]:https://www.luis.ai
[31]:https://docs.microsoft.com/azure/cognitive-services/LUIS/Home
[32]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=csharp

### <a name="qna-maker"></a>QnA Maker
O [QnA Maker][33] é um serviço de API baseado em nuvem que cria uma camada de perguntas e respostas em formato de conversação sobre seus dados. Com o QnA Maker, você pode compilar, treinar e publicar em minutos um bot simples de perguntas e respostas com base em URLs de perguntas frequentes, documentos estruturados, manuais de produto ou conteúdo editorial.

<a name="QnA-whats-new"></a>

- **Novo! Pipeline de extração**: agora você pode extrair informações hierárquicas de URLs, arquivos e SharePoint
- **Novo! Inteligência**: modelos de classificação contextuais, sugestões de aprendizado ativo
- **Novo! Conversa**: conversas em vários turnos no QnA Maker.

[Docs][34]  | [add qnamaker to your bot][35] 

[33]:https://www.qnamaker.ai/
[34]:https://aka.ms/qnamaker-docs-home
[35]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=cs

### <a name="speech-services"></a>Serviços de Fala
Os [Serviços de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/) convertem áudio em texto, realizam tradução de fala e conversão de texto em fala com os serviços de Fala unificados. Com os serviços de fala, você pode integrar fala ao seu bot, criar palavras de ativação personalizadas e criar em vários idiomas.

### <a name="adaptive-cards"></a>Cartões Adaptáveis
Os [Cartões Adaptáveis](https://adaptivecards.io) são um padrão aberto para desenvolvedores poderem trocar conteúdo de cartão de maneira comum e consistente, e são usados por desenvolvedores do Bot Framework para criar ótimas experiências de conversa entre canais.

## <a name="additional-information"></a>Informações adicionais
- Visite a [página do GitHub](https://github.com/Microsoft/botframework/blob/master/whats-new.md#whats-new) para obter mais informações.
