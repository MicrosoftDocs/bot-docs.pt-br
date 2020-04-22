---
title: Novidades (arquivo) – Serviço de Bot
description: Conheça as novidades do Bot Framework.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 11/28/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f3d33f9f3f19a32b7b01aa92b809b4e16cae76e1
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80648173"
---
# <a name="whats-new-november-2019"></a>Novidades em novembro de 2019

[!INCLUDE[applies-to](includes/applies-to.md)]

O SDK do Bot Framework v4 é um [SDK open-source](https://github.com/microsoft/botframework-sdk/#readme) que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.


|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Versão |[4.6 GA][1] | [4.6 GA][2] | [Beta 4][3] | [Versão prévia 3][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | | | 

#### <a name="bot-framework-sdk-for-microsoft-teams-ga"></a>SDK do Bot Framework para Microsoft Teams (GA)
A versão SDK do Bot Framework v4.6 integra totalmente o suporte para a criação de bots do Teams, permitindo que os usuários os usem em conversas de chat de canal ou grupo. Ao adicionar um bot a uma equipe ou chat, todos os usuários da conversa podem aproveitar a funcionalidade do bot diretamente na conversa.  [[Docs](https://docs.microsoft.com/azure/bot-service/bot-builder-basics-teams)]

#### <a name="bot-framework-for-power-virtual-agent-preview"></a>Bot Framework para Power Virtual Agent (Versão prévia)

O Power Virtual Agent foi projetado para permitir que os usuários corporativos criem bots dentro de uma experiência SaaS de criação de bots baseada em interface do usuário, sem precisar codificar nem gerenciar serviços de IA específicos. Os Power Virtual Agents podem ser estendidos com o Microsoft Bot Framework, permitindo que os desenvolvedores e usuários de negócios colaborem na criação de bots para suas organizações. [[Docs](https://docs.microsoft.com/dynamics365/ai/customer-service-virtual-agent/overview)]


#### <a name="bot-framework-sdk-for-skills-preview"></a>SDK do Bot Framework para Habilidades (Versão prévia)

- **Habilidades para bots**: crie habilidades de conversa reutilizáveis para adicionar funcionalidade a um bot. Aproveite as habilidades predefinidas, como as habilidades Calendário, Email, Tarefa, Ponto de Interesse, Automotivo, Clima e Notícias. As habilidades incluem modelos de linguagem, caixas de diálogo, QnA e o código de integração entregue para personalizar e estender, conforme necessário. [[Docs](https://microsoft.github.io/botframework-solutions/overview/skills/)]

- **Habilidades para o Power Virtual Agent – Em breve!** : Para bots criados com Power Virtual Agents, você pode criar habilidades para esses bots usando o Bot Framework e os Serviços Cognitivos do Azure sem precisar criar um bot do zero. 

#### <a name="adaptive-dialogs-preview"></a>Caixas de Diálogo Adaptáveis (Versão prévia)
As Caixas de Diálogo Adaptáveis permitem que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente prático ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa. [[Docs][48] | [Exemplos de C#][49]] 

#### <a name="language-generation-preview"></a>Geração de Linguagem (Versão prévia)
Com a Geração de Linguagem, os desenvolvedores podem separar a lógica usada para gerar respostas do bot, incluindo a capacidade de definir diversas variações em uma frase, executar expressões simples baseadas em contexto e consultar a memória conversacional. [[Docs][44] | [Exemplos de C#][45]]

#### <a name="common-expression-language-preview"></a>Linguagem de Expressão Comum (Versão prévia)
A Linguagem de Expressão Comum permite que você avalie o resultado de uma lógica baseada em condição em runtime. A linguagem comum pode ser usada em todos os componentes de SDK do Bot Framework e de AI conversacional, como Caixas de Diálogo Adaptáveis e Geração de Linguagem. [[Docs][40] | [API][41]]

## <a name="whats-new-july-2019"></a>Quais são as novidades? (julho de 2019)

[!INCLUDE[applies-to](includes/applies-to.md)]

O SDK do Bot Framework v4 é um [SDK open-source][1a] que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|   | C#  | JS  | Python |   
|---|:---:|:---:|:------:|
|. |[4.5][1] | [4.5][2] | [4.4.0b2 (versão prévia)][3] | 
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11] | | 


### <a name="bot-framework-channels"></a>Canais do Bot Framework
- [Direct Line Speech (versão prévia pública)](https://aka.ms/streaming-extensions) | [documentos:](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0) O Bot Framework e os Serviços de Fala da Microsoft fornecem um canal que permite fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot, usando WebSockets.  

- [Extensão do Serviço de Aplicativo Direct Line (versão prévia pública)](https://portal.azure.com) | [documentos](https://aka.ms/directline-ase): Uma versão da Direct Line que permite que os clientes se conectem diretamente a bots usando a Direct Line API. Isso oferece muitos benefícios, incluindo melhor desempenho e mais isolamento. A extensão do Serviço de Aplicativo Direct Line está disponível em todos os Serviços de Aplicativo do Azure, incluindo aqueles hospedados em um Ambiente do Serviço de Aplicativo do Azure. Um Ambiente do Serviço de Aplicativo do Azure oferece isolamento e é ideal para trabalhar em uma VNet. Uma VNet permite que você crie seu próprio espaço privado no Azure e é crucial para sua rede de nuvem, pois oferece isolamento, segmentação e outros benefícios importantes. 

### <a name="bot-framework-sdk"></a>SDK do Bot Framework
- [Caixa de Diálogo Adaptável (SDK v4.6 versão prévia)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme) | [documentos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs) | [exemplos de C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore): A Caixa de Diálogo Adaptável agora permite que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente útil ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa. 
  
- [SDK do Python do Bot Framework (versão prévia 2)](https://github.com/microsoft/botbuilder-python) | [exemplos](https://github.com/Microsoft/botbuilder-python/tree/master/samples): O SDK do Python agora dá suporte a OAuth, Prompts, CosmosDB e inclui todas as principais funcionalidades no SDK 4.5. Além disso, há exemplos para ajudá-lo a saber mais sobre os novos recursos do SDK.

### <a name="bot-framework-testing"></a>Testes do Bot Framework
- [Docs](https://aka.ms/testing-framework) | Pacotes de teste de unidade ([C#](https://aka.ms/nuget-botbuilder-testing)/ [JavaScript](https://aka.ms/npm-botbuilder-testing)) | [Exemplo de C#](https://aka.ms/cs-core-test-sample) | [Exemplo de JS](https://aka.ms/js-core-test-sample): Respondendo à solicitação dos clientes e desenvolvedores por melhores ferramentas de teste, a versão de julho do SDK apresenta uma nova funcionalidade de teste de unidade. O pacote Microsoft.Bot.Builder.testing simplifica o processo de caixas de diálogo de teste de unidade em seu bot.  

- [Testes de canal](https://github.com/Microsoft/BotFramework-Emulator/releases) | [docs](https://aka.ms/channel-testing): 

Introduzido no Microsoft Build 2019, o Inspetor de Bot é um novo recurso no Bot Framework Emulator que permite depurar e testar bots em canais como Microsoft Teams, Slack, Cortana e muito mais. Conforme você usa o bot em canais específicos, as mensagens serão espelhadas para o Bot Framework Emulator, no qual você poderá inspecionar os dados da mensagem que o bot tiver recebido. Além disso, é renderizado um instantâneo do estado da memória do bot em todos os turnos entre o canal e o bot.

### <a name="web-chat"></a>Chat na Web
- Com base em solicitações de clientes corporativos, adicionamos um [exemplo de Webchat](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps#single-sign-on-demo-for-enterprise-apps-using-oauth) que mostra como autorizar um usuário a acessar recursos em um aplicativo empresarial com um bot. Dois tipos de recursos são usados para demonstrar a interoperabilidade do OAuth com o Microsoft Graph e a API do GitHub.

### <a name="solutions"></a>Soluções
- [Acelerador da solução Assistente Virtual](https://github.com/Microsoft/botframework-solutions#readme): Fornece um conjunto de modelos, aceleradores de solução e habilidades para ajudar a criar experiências de conversa sofisticadas. Novo cliente de aplicativo Android para o assistente virtual que se integra com o Direct-Line Speech e o Assistente Virtual, demonstrando como um cliente de dispositivo pode interagir com seu assistente virtual e renderizar Cartões Adaptáveis. As atualizações também incluem suporte para o Direct-Line Speech e o Microsoft Teams.
  
- [Dynamics 365 Virtual Agent for Customer Service (versão prévia pública)](https://dynamics.microsoft.com/en-us/ai/virtual-agent-for-customer-service/): Com a versão prévia pública, você pode fornecer um atendimento ao cliente excepcional com agentes virtuais inteligentes e adaptáveis. Os especialistas em atendimento ao cliente podem criar e aprimorar bots facilmente com informações baseadas em IA.
  
- [Chat para Dynamics 365](https://www.powerobjects.com/powerpacks/powerchat/): O Chat para Dynamics 365 oferece vários recursos para garantir que os agentes de suporte e os usuários finais possam interagir com eficiência e permaneçam altamente produtivos. Converse ao vivo e acompanhe conversas de visitantes em seu site no Microsoft Dynamics 365.

## <a name="whats-new-may-2019"></a>Quais são as novidades? (maio de 2019)

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|. |[4.4.3][1] | [4.4.0][2] | [4.4.0b1 (versão prévia)][3] | [4.0.0a6 (versão prévia)][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11] | | 

<a name="V4-whats-new"></a>
## <a name="bot-framework-sdk-new-in-preview"></a>SDK do Bot Framework (Novo! Em versão prévia)

- [Diálogo Adaptável][47] | [docs][48] | [exemplos em C#][49]: Os Diálogos Adaptáveis permitem aos desenvolvedores criar conversas que podem ser alteradas dinamicamente conforme o andamento da conversa.  Tradicionalmente, os desenvolvedores mapeiam o fluxo inteiro de uma conversa com antecedência, o que limita sua flexibilidade.  Os Diálogos Adaptáveis permitem que elas sejam mais flexíveis, para responder a alterações no contexto e inserir novas etapas ou subdiálogos inteiros na conversa durante sua evolução. 

- [Geração de linguagem][43] | [docs][44] | [exemplos em C#][45]: geração de linguagem, que permite ao desenvolvedor extrair as cadeias de caracteres inseridas dos seus arquivos de código e de recursos e gerenciá-las por meio de um formato arquivo e do runtime de Geração de Linguagem.  A Geração de Linguagem permite que os clientes definam diversas variações de uma frase, executem expressões simples com base no contexto e consultem a memória de conversação; ao longo do tempo, isso nos permitirá criar outras funcionalidades que levarão a uma experiência de conversa mais natural.

- [Linguagem de Expressão Comum][40] | [API][41]: tanto os Diálogos Adaptáveis quanto a Geração de Linguagem usam e dependem de uma linguagem de expressão comum para alimentar conversas de bot.

## <a name="botkit"></a>Botkit
O [Botkit][100] é uma ferramenta de desenvolvedor e um SDK de criação de chatbots, aplicativos e integrações personalizadas para as principais plataformas de mensagens. Bots do Botkit: gatilhos `hear()`, perguntas `ask()` e respostas `say()`. Os desenvolvedores podem usar essa sintaxe para criar diálogos; agora compatíveis com a versão mais recente do SDK do Bot Framework. 

Além disso, o Botkit traz seis adaptadores de plataforma, permitindo que os aplicativos de bot JavaScript se comuniquem diretamente com as plataformas de mensagens: [Slack][102], [Webex Teams][103], [Google Hangouts][104], [Facebook Messenger][105], [Twilio][106] e [Webchat][107].

O Botkit faz parte do Microsoft Bot Framework e foi lançado com a [licença de software livre do MIT][101]

## <a name="bot-framework-solutions-new-in-preview"></a>Soluções do Bot Framework (Novo! Em versão prévia)

O [repositório de Soluções do Bot Framework](https://github.com/Microsoft/AI#readme) é a base para um conjunto de modelos, aceleradores de soluções e habilidades para ajudar a criar experiências de conversação avançadas que soam como um assistente.

| Nome | Descrição |  
|:------------|:------------| 
|[**Assistente Virtual**](https://github.com/Microsoft/AI/tree/master/docs#virtual-assistant) | Os clientes precisam realmente fornecer um assistente de conversação adaptado à sua marca, personalizado para seus usuários e disponibilizado em uma ampla variedade de telas e dispositivos. <br/><br/> O modelo empresarial simplifica bastante a criação de um projeto novo do bot, que inclui: intenções de conversação básicas, integração com o Dispatch, QnA Maker, Application Insights e uma implantação automatizada.|
|[**Habilidades**](https://github.com/Microsoft/AI/blob/master/docs/overview/skills.md)| Os desenvolvedores podem compor experiências de conversação combinando recursos de conversa reutilizáveis, conhecidos como Habilidades. As próprias Habilidades são Bots, chamados remotamente, e um modelo de desenvolvedor (.NET, TS) está disponível para facilitar a criação de novas Habilidades. 
|[**Analytics**](https://github.com/Microsoft/AI/blob/master/docs/readme.md#analytics)| Obtenha insights essenciais sobre a integridade e o comportamento do seu bot com as soluções de análise de inteligência Artificial de Conversação. Examine os dados telemétricos disponíveis, os exemplos de consultas do Application Insights e os painéis do Power BI para compreender a amplitude das conversas do seu bot com os usuários. |

## <a name="azure-bot-service"></a>Serviço de Bot do Azure
O Serviço de Bot do Azure permite que você hospede bots inteligentes e de nível empresarial com total controle e propriedade sobre seus dados. Os desenvolvedores podem registrar e conectar seus bots a usuários no Microsoft Teams, na Cortana, no Webchat e muito mais. [Azure][27]  |  [docs][28] | [conectar-se a canais][29] 

* **Cliente JS Direct Line**: se você quiser usar o canal Direct Line no Serviço de Bot do Azure e não estiver usando o cliente WebChat, o cliente JS Direct Line poderá ser usado no seu aplicativo personalizado. Vá para o [GitHub][30] para obter mais informações.

<a name="ABS-whats-new"></a>

* **Novo! Canal de Fala do Direct Line**: estamos reunindo o Bot Framework e os Serviços de Fala da Microsoft para oferecer um canal que permita fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot.  Para obter mais informações, confira como adicionar [canal de fala ao bot](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0).


## <a name="bot-framework-emulator"></a>Bot Framework Emulator
O [Bot Framework Emulator][60] é um aplicativo de área de trabalho multiplataforma que permite aos desenvolvedores testar e depurar bots criados usando o SDK do Bot Framework. Você pode usar o Bot Framework Emulator para testar bots em execução localmente no computador ou para se conectar a bots em execução remotamente.

- [Baixar a versão mais recente][61] | [Docs][62]

<a name="Emulator-whats-new"></a>
### <a name="bot-inspector-new-in-preview"></a>Inspetor de Bot (Novo! Em versão prévia)

O Bot Framework Emulator lançou uma versão beta do novo recurso Inspetor de Bot. Ele fornece um modo de depurar e testar seus bots do SDK do Bot Framework v4 em canais como Microsoft Teams, Slack, Cortana, Facebook Messenger etc. Durante a conversa, as mensagens serão espelhadas para o Bot Framework Emulator, no qual você poderá inspecionar os dados da mensagem que o bot recebeu. Além disso, é renderizado um instantâneo do estado do bot em todos os turnos entre o canal e o bot. Leia mais sobre o [Inspetor de Bot](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md).


## <a name="related-services"></a>Serviços Relacionados

### <a name="language-understanding"></a>Reconhecimento Vocal 
Um serviço baseado em aprendizado de máquina para criar experiências de linguagem natural. Crie rapidamente modelos personalizados e prontos para a empresa que melhoram continuamente. O [LUIS (Serviço Inteligente de Reconhecimento Vocal)][30] permite que o aplicativo entenda o que uma pessoa quer com suas próprias palavras.

<a name="LUIS-whats-new"></a>

- **Novo! Funções, entidades externas e entidades dinâmicas**: o LUIS adicionou vários recursos que permitem aos desenvolvedores extrair informações mais detalhadas do texto, para que os usuários agora possam criar soluções mais inteligentes com menos esforço. O LUIS também estendeu funções a todos os tipos de entidade, o que permite que essas entidades sejam classificadas com subtipos diferentes com base no contexto. Os desenvolvedores agora têm um controle mais granular sobre o que eles podem fazer com o LUIS, incluindo a capacidade de identificar e atualizar modelos no runtime por meio de listas dinâmicas e entidades externas. As listas dinâmicas são usadas para acrescentar às entidades de lista no tempo de previsão, permitindo que informações específicas do usuário tenham correspondência exata. Extratores de entidade suplementares separados são executados com entidades externas, e essas informações podem ser acrescentadas ao LUIS como sinais fortes para outros modelos.

- **Novo! Painel de Análise**: o LUIS está lançando um painel de análise abrangente mais detalhado e visualmente sofisticado. Seu design amigável realça os problemas comuns que a maioria dos usuários enfrenta ao criar aplicativos, fornecendo explicações simples sobre como resolvê-los para que os usuários obtenham mais insights sobre a qualidade dos seus modelos, possíveis problemas de dados e diretrizes para a adoção das melhores práticas.

[Docs][31] | [Adicionar reconhecimento vocal ao bot][32] 

### <a name="qna-maker"></a>QnA Maker
O [QnA Maker][33] é um serviço de API baseado em nuvem que cria uma camada de perguntas e respostas em formato de conversação sobre seus dados. Com o QnA Maker, você pode compilar, treinar e publicar em minutos um bot simples de perguntas e respostas com base em URLs de perguntas frequentes, documentos estruturados, manuais de produto ou conteúdo editorial.

<a name="QnA-whats-new"></a>

- **Novo! Pipeline de extração**: agora você pode extrair informações hierárquicas de URLs, arquivos e SharePoint
- **Novo! Inteligência**: modelos de classificação contextuais, sugestões de aprendizado ativo
- **Novo! Conversa**: conversas em vários turnos no QnA Maker.

[Docs][34]  | [Adicionar o QnA Maker ao bot][35] 

### <a name="speech-services"></a>Serviços de Fala
Os [Serviços de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/) convertem áudio em texto, realizam tradução de fala e conversão de texto em fala com os serviços de Fala unificados. Com os serviços de fala, você pode integrar fala ao seu bot, criar palavras de ativação personalizadas e criar em vários idiomas.

### <a name="adaptive-cards"></a>Cartões Adaptáveis
Os [Cartões Adaptáveis](https://adaptivecards.io) são um padrão aberto para desenvolvedores trocarem conteúdo de cartão de maneira comum e consistente e são usados por desenvolvedores do Bot Framework para criar ótimas experiências de conversa entre canais.

## <a name="additional-information"></a>Informações adicionais
- Visite a [página do GitHub](https://github.com/Microsoft/botframework/blob/master/whats-new.md#whats-new) para obter mais informações.

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[1a]:https://github.com/microsoft/botframework-sdk/#readme
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[11]:https://github.com/Microsoft/botbuilder-python/tree/master/samples

[18]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#readme
[19]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#readme

[27]:https://azure.microsoft.com/services/bot-service/
[28]:https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[29]:https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0

[30]:https://www.luis.ai
[31]:https://docs.microsoft.com/azure/cognitive-services/LUIS/Home
[32]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=csharp
[33]:https://www.qnamaker.ai/
[34]:https://aka.ms/what-is-qnamaker
[35]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=cs

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

[60]:https://github.com/Microsoft/BotFramework-Emulator#readme
[61]:https://github.com/Microsoft/BotFramework-Emulator/releases/latest
[62]:https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0

[100]:https://github.com/howdyai/botkit#readme
[101]:https://github.com/howdyai/botkit/blob/master/LICENSE.md
[102]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-slack#readme
[103]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-webex#readme
[104]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-hangouts#readme
[105]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-facebook#readme
[106]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-twilio-sms#readme
[107]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-web#readme
