---
title: Perguntas frequentes do bot Framework-serviço de bot
description: Perguntas frequentes sobre o bot Framework geral.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/10/2020
ms.openlocfilehash: ffc7f367e0c210ad7464910823d0126f741382be
ms.sourcegitcommit: 42f3472bd6ecfa4b1541e5375a6044f6b0bf40c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86542402"
---
# <a name="bot-framework-general"></a>Do bot Framework geral

## <a name="why-doesnt-the-typing-activity-do-anything"></a>Por que a atividade de Digitação não gera nenhuma ação?

Alguns canais não dão suporte a atualizações de digitação transitórias nos clientes.

## <a name="what-is-the-difference-between-the-connector-library-and-builder-library-in-the-sdk"></a>Qual é a diferença entre a biblioteca do Connector e a biblioteca do Builder no SDK?

A biblioteca do Connector é a exposição da API REST. A biblioteca do Builder inclui o modelo de programação de diálogo conversacional e outros recursos, como prompts, cascatas, cadeias e preenchimento de formulário guiado. A biblioteca do Builder também fornece acesso a serviços cognitivos como o LUIS.

## <a name="how-do-user-messages-relate-to-https-method-calls"></a>Como as mensagens do usuário estão relacionadas às chamadas de método HTTPS?

Quando o usuário enviar uma mensagem através de um canal, o serviço Web do Bot Framework emitirá um HTTPS POST para o ponto de extremidade do serviço Web do bot. O bot pode enviar zero, uma ou muitas mensagens de volta ao usuário naquele canal, emitindo um HTTPS POST separado para o Bot Framework para cada mensagem que envia.


## <a name="how-can-i-intercept-all-messages-between-the-user-and-my-bot"></a>Como é possível interceptar todas as mensagens entre o usuário e o bot?

Com o SDK do Bot Framework para .NET, é possível fornecer implementações das interfaces `IPostToBot` e `IBotToUser` para o contêiner de injeção da dependência `Autofac`. Com o SDK do Bot Framework para qualquer linguagem, é possível usar o middleware para a mesma finalidade. O repositório [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure) contém as bibliotecas do C# e Node.js que registrarão esses dados em uma tabela do Azure.

## <a name="what-is-the-idialogstackforward-method-in-the-bot-framework-sdk-for-net"></a>O que é o método IDialogStack.Forward no SDK do Bot Framework para .NET?

A finalidade principal de `IDialogStack.Forward` é reutilizar um diálogo filho existente que seja frequentemente "reativo", em que o diálogo filho (em `IDialog.StartAsync`) aguarda um objeto `T` com algum manipulador `ResumeAfter`. Em particular, se tiver um diálogo filho que aguarda um `IMessageActivity``T`, você poderá encaminhar o `IMessageActivity` de entrada (já recebido por algum diálogo pai) usando o método `IDialogStack.Forward`. Por exemplo, para encaminhar um `IMessageActivity` de entrada para um `LuisDialog`, chame `IDialogStack.Forward` para efetuar push de `LuisDialog` para a pilha de diálogo, execute o código em `LuisDialog.StartAsync` até agendar uma espera pela próxima mensagem e, em seguida, imediatamente satisfazer essa espera com o `IMessageActivity` encaminhado.

`T` é geralmente um `IMessageActivity`, uma vez que `IDialog.StartAsync` é normalmente construído para aguardar esse tipo de atividade. É possível usar `IDialogStack.Forward` como `LuisDialog` como um mecanismo para interceptar mensagens do usuário para algum processamento antes de encaminhar a mensagem para um `LuisDialog` existente. Alternativamente, também é possível usar `DispatchDialog` com `ContinueToNextGroup` para essa finalidade.

Você esperaria encontrar o item encaminhado no primeiro manipulador `ResumeAfter` (p. ex. `LuisDialog.MessageReceived`) agendado por `StartAsync`.

## <a name="what-is-the-difference-between-proactive-and-reactive"></a>Qual é a diferença entre "proativo" e "reativo"?

Do ponto de vista do bot, "reativo" significa que o usuário inicia a conversa enviando uma mensagem para o bot e o bot reage respondendo a essa mensagem. Em contraste, "proativo" significa que o bot inicia a conversa, enviando a primeira mensagem ao usuário. Por exemplo, um bot pode enviar uma mensagem proativa para notificar um usuário quando um temporizador expirar ou ocorrer um evento.

## <a name="how-can-i-send-proactive-messages-to-the-user"></a>Como enviar mensagens proativas ao usuário?

Para obter exemplos que mostram como enviar mensagens proativas, consulte [Exemplos em C# V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) e [Exemplos em Node.js V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) no repositório BotBuilder-Samples no GitHub.

## <a name="how-can-i-reference-non-serializable-services-from-my-c-dialogs-in-sdk-v3"></a>Como é possível referenciar serviços não serializáveis dos meus diálogos em C#? no SDK v3?

Há várias opções:

* Resolva a dependência através de `Autofac` e `FiberModule.Key_DoNotSerialize`. Essa é a solução mais limpa.
* Use atributos [NonSerialized](https://msdn.microsoft.com/library/system.nonserializedattribute(v=vs.110).aspx) e [OnDeserialized](https://msdn.microsoft.com/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx) para restaurar a dependência da desserialização. Essa é a solução mais simples.
* Não armazene essa dependência, para que não seja serializada. Essa solução, embora tecnicamente viável, não é recomendável.
* Use o substituto de serialização de reflexão. Essa solução pode não ser viável em alguns casos e corre o risco de serializar demasiadamente.

## <a name="what-is-an-etag--how-does-it-relate-to-bot-data-bag-storage"></a>O que é um ETag?  Qual a relação disso com o armazenamento de recipiente de dados de bot?

Um [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) é um mecanismo para [controle de simultaneidade otimista](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). O armazenamento de recipiente de dados de bot usa ETags para impedir atualizações conflitantes nos dados. Um erro de ETag com o código de status HTTP 412 "Falha na pré-condição" indica que foram recebidas várias mensagens em paralelo antes que o bot pudesse concluir a primeira operação.

O estado e a pilha de diálogo são armazenados em recipientes de dados de bot. Por exemplo, será possível ver o erro de ETag "Falha na Pré-Condição" se o bot ainda estiver processando uma mensagem anterior quando receber uma nova mensagem para essa conversa.

<!-- Retired, re: https://github.com/MicrosoftDocs/bot-docs/issues/1698
## What are some community-authored dialogs?

* [BotAuth](https://www.nuget.org/packages/BotAuth) - Azure Active Directory authentication
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) - Regular expression-based dispatch of user text to dialog methods

## What are some community-authored templates?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) - ES6 Bot Builder template
-->

## <a name="what-is-rate-limiting"></a>O que é a limitação de taxa?

O serviço Bot Framework precisa proteger a si próprio e a seus clientes contra padrões de chamada abusivos (por exemplo, ataque de negação de serviço), de modo que nenhum bot possa prejudicar o desempenho de outros bots. Para obter esse tipo de proteção, adicionamos limites de taxa (também conhecidos como limitação) aos nossos pontos de extremidade. Impondo um limite de taxa, podemos restringir a frequência com a qual um cliente ou bot pode fazer uma chamada específica. Por exemplo: com a limitação de taxa habilitada, se um bot desejar postar um grande número de atividades, ele precisará espaçá-las por um período. Observe que a finalidade da limitação de taxa não é limitar o volume total de um bot. Ela se destina a prevenir abusos da infraestrutura de conversa que não seguem os padrões de conversa humana. Por exemplo, inundar duas conversas com mais conteúdo do que duas pessoas jamais poderiam consumir.

## <a name="how-will-i-know-if-im-impacted"></a>Como poderei saber se fui afetado?

É improvável que você enfrente a limitação de taxa, mesmo com um alto volume. A maior parte da limitação de taxa só ocorrerá devido ao envio em massa de atividades (de um bot ou de um cliente), um teste de carga extrema ou um bug. Quando uma solicitação é limitada, uma resposta HTTP 429 (Número Excessivo de Solicitações) é retornada juntamente com um cabeçalho Retry-After, que indica o tempo (em segundos) que é necessário aguardar antes do êxito da nova tentativa da solicitação. Colete essas informações habilitando a análise no bot por meio do Azure Application Insights. Se preferir, adicione um código ao bot para registrar mensagens em log.

## <a name="how-does-rate-limiting-occur"></a>Como ocorre a limitação de taxa?

Isso pode ocorrer se:

* Um bot envia mensagens com muita frequência
* Um cliente de um bot envia mensagens com muita frequência
* os clientes da Linha Direta solicitam um novo Soquete da Web com muita frequência

### <a name="what-are-the-rate-limits"></a>Quais são os limites de taxa?

Estamos continuamente ajustando os limites de taxa para torná-los os mais brandos possíveis, ao mesmo tempo em que protegemos nosso serviço e nossos usuários. Como os limites serão alterados ocasionalmente, não estamos publicando os números no momento. Caso você seja afetado pela limitação de taxa, fique à vontade para falar conosco pelo email [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

## <a name="how-to-implement-human-handoff"></a>Como implementar a entrega humana?

Às vezes, é necessário transferir (entrega) uma conversa de um bot para um humano. Isso ocorre por exemplo, se o bot não entender o usuário, ou se a solicitação não puder ser automatizada. Nesses casos, o bot fornece uma transição para os seres humanos.

<!-- Handoff are [event activities](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#event-activity) from bot to channels. These events are used for the handoff between a bot and the agent hub and are also known as **handoff events**.

When a bot detects the need to hand the conversation off to an agent, it signals its intent by sending a **handoff initiation event**. See this [handoff protocol](~/bot-service-design-pattern-handoff-human.md#handoff-protocol) example. -->

O SDK do bot Framework dá suporte à entrega a um humano. Há alguns **tipos de evento** para a sinalização de operações de entrega. Esses eventos são trocados entre um **bot** e um **Hub de agente**, também chamado de Hub de envolvimento. Esse Hub de agente é definido como um aplicativo ou um sistema que permite que agentes, normalmente humanos, recebam e manipulem solicitações de usuários, bem como solicitações de escalonamento de bots.

Para obter informações detalhadas, confira [conversações de transição do bot para](~/bot-service-design-pattern-handoff-human.md) o artigo humano.

<!-- See [Handoff Library](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library#handoff-library).

You can select one of the following models for integration with the agent hubs:

- [Bot as an agent](~/bot-service-design-pattern-handoff-human.md#bot-as-an-agent)
- [Bot as a proxy](~/bot-service-design-pattern-handoff-human.md#bot-as-a-proxy)

The handoff protocol is identical for both models, however the onboarding details differ between the models and the agent engagement hubs. The protocol is centered around events for initiation (sent by the bot to the channel) and status update (sent by the channel to the bot). For more information, see the [protocol](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library#protocol) of the **hand off library**.

> [!NOTE]
> DirectLine channel supports handoff in the [bot as an agent](~/bot-service-design-pattern-handoff-human.md#bot-as-an-agent) scenario.  This is because there is an application that handles the handoff event.

In the case of channels that do not handle the handoff, the middleware is used to transform the handoff event into API calls specific to the agent hub.
-->

<!--
### ??Questions??

1. The [Transition conversations from bot to human](~/bot-service-design-pattern-handoff-human.md) article refers to the following:

    - [Integration with Microsoft Dynamics Omnichannel for Customer Service](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/handoff-library/csharp_dotnetcore/samples)
    - [Integration with LiverPerson LiveEngage platform](https://developers.liveperson.com/third-party-bots-microsoft-bot-framework.html)

    **Q1: Do we need to show how to use them perhaps in a specific how to article?**

1. The [PR 1786](https://github.com/MicrosoftDocs/bot-docs/issues/1786) says *We and customers have implemented connectors (middleware + adapter) for LivePerson proxy, ServiceNow, RingCentral and others.*

    **Q2: Do we need to refer to this?**: [HandoffMiddleware.cs](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/handoff-library/csharp_dotnetcore/samples/LivePersonAgentBot/Middleware/HandoffMiddleware.cs).
>