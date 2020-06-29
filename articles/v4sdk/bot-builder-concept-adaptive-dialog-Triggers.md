---
title: Eventos e gatilhos em caixas de diálogo adaptáveis
description: Eventos e gatilhos em caixas de diálogo adaptáveis
keywords: bot, usuário, eventos, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
ms.openlocfilehash: d18a8ab6fc79398af0e2df9751e40a46f0c04da7
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073952"
---
# <a name="events-and-triggers-in-adaptive-dialogs"></a>Eventos e gatilhos em caixas de diálogo adaptáveis

As caixas de diálogo adaptáveis apresentam uma nova abordagem baseada em eventos para o modelo de conversas. Qualquer subsistema em seu bot pode emitir eventos, e todas as caixas de diálogo adaptáveis contêm um ou mais manipuladores de eventos chamados _gatilhos_ que permitem reagir a esses eventos.  Sempre que um evento é acionado, os gatilhos da caixa de diálogo adaptável ativa são avaliados e, se qualquer gatilho corresponder ao evento atual, as [ações][2] associadas a esse gatilho serão executadas. Se um evento não for tratado na caixa de diálogo ativa, ele será passado para a caixa de diálogo pai a ser avaliada. Esse processo continuará até ser tratado ou atingir a caixa de diálogo raiz dos bots. Se nenhum manipulador de eventos (_gatilho_) for encontrado, o evento será ignorado, e nenhuma ação será executada.

<!--TODO P3: preBubble/consultation/postBubble phases - Possibly document in an advanced section at some point.
From: v-jofin: (https://github.com/MicrosoftDocs/bot-docs-pr/pull/2109#discussion_r418164608)
There is also the post-bubble/trailing-edge process where everyone who didn't handle the event on the leading-edge gets a second chance on the trailing edge, from the root back down to the leaf.
From Stevenic: (https://github.com/MicrosoftDocs/bot-docs-pr/pull/2109#discussion_r425335486)
"If anything it seems like these details would be in an "Advanced Event Concepts" section as I'm not sure most devs ever need to worry about this or even understand it. I had to create this mechanism to get our consultation stuff to work but it's a super advanced concept."
--->

## <a name="prerequisites"></a>Pré-requisitos

* [Como funcionam os bots][10]
* [Introdução às caixas de diálogo adaptáveis][1]
* [Bibliotecas de caixa de diálogo][5]

## <a name="anatomy-of-a-trigger"></a>Anatomia de um gatilho

 Um gatilho é composto de uma condição e uma ou mais ações. O SDK do Bot Framework oferece vários gatilhos, cada um com um conjunto de condições predefinidas que examinam o eventName ou o eventValue. Você pode adicionar outras condições a um gatilho, dando a você controle adicional quando o gatilho é executado.

O SDK do Bot Framework fornece vários gatilhos predefinidos projetados para lidar com tipos de eventos comuns.  Por exemplo, o gatilho `OnIntent` é acionado sempre que o [reconhecedor][8] detecta uma [intenção][6]. Se você estiver usando um reconhecedor [LUIS][7], ele também retornará uma [pontuação de previsão][9] que mede o grau de confiança que o LUIS tem para seus resultados de previsão. Para aumentar a confiabilidade e a precisão do bot, talvez seja conveniente executar o gatilho `OnIntent` somente no caso de uma classificação de confiança de 80% ou superior. Você pode fazer isso adicionando uma _condição_. Todos os gatilhos contêm uma propriedade `Condition` opcional que, quando definida, deve ser avaliada como _true_ para que o gatilho seja executado. A propriedade `Condition` é uma cadeia de caracteres, mas deve conter uma [expressão adaptável][4] válida para funcionar. A propriedade `Condition` dos exemplos acima seria semelhante a: `Condition = "#<IntentName>.Score >= 0.8"`. As expressões adaptáveis permitem condições sofisticadas que podem lidar praticamente com qualquer cenário que você possa ter.

Todos os gatilhos também contêm uma lista de _Ações_. As ações representam o que o bot faz em resposta a um gatilho.  Esse é o coração do gatilho. Saiba mais sobre ações e quais ações internas são fornecidas no SDK do Bot Framework no artigo [Ações em caixas de diálogo adaptáveis][2].

## <a name="trigger-types"></a>Tipos de gatilho

Os _gatilhos_ permitem que você capture e responda a eventos. O gatilho mais amplo do qual todos os outros gatilhos são derivados é o gatilho `OnCondition`, que permite capturar e anexar uma lista de ações a serem executadas quando um evento específico é emitido por qualquer um dos subsistemas de bots.

Os gatilhos são listados nas seções a seguir, categorizados e agrupados por tipo de gatilho.

Cada tabela nas seções a seguir lista todos os gatilhos com suporte de caixas de diálogo adaptáveis atualmente no SDK, bem como os eventos nos quais eles se baseiam.

### <a name="base-trigger"></a>Gatilho base

O gatilho `OnCondition` é o gatilho base do qual todos os gatilhos derivam. Em uma caixa de diálogo adaptável, os gatilhos são definidos como uma lista de objetos `OnCondition`, conforme demonstrado no seguinte exemplo:

```csharp
Triggers = new List<OnCondition>()
{
    ...
}
```

### <a name="recognizer-event-triggers"></a>Gatilhos de evento de reconhecedores

Os [reconhecedores][8] extraem informações significativas da entrada de um usuário na forma de _intenções_ e _entidades_ e, quando fazem isso, eles emitem eventos. Por exemplo, o evento `recognizedIntent` é acionado quando o reconhecedor seleciona uma intenção (ou extrai entidades) de determinado enunciado de usuário. Lide com esses eventos usando os _gatilhos de evento de reconhecedores_.

| Causa do evento               | Nome do gatilho  | Evento base    | Descrição                                                       |
| ------------------------- | ------------- | ------------- | ----------------------------------------------------------------- |
| Escolher intenção | `OnChooseIntent` |`ChooseIntent` | Esse gatilho é executado quando uma ambiguidade é detectada entre intenções de vários reconhecedores em um [CrossTrainedRecognizerSet][11].|
| Intenção reconhecida| `OnIntent` | `RecognizedIntent` | Ações a serem executadas quando a intenção especificada for reconhecida.           |
|Intenção QnAMatch|`OnQnAMatch`| `RecognizedIntent` |Esse gatilho será executado quando o [QnAMakerRecognizer][12] tiver retornado uma intenção de `QnAMatch`. A entidade `@answer` terá a resposta `QnAMaker`.|
|Intenção desconhecida reconhecida| `OnUnknownIntent` | `UnknownIntent` | Ações a serem executadas quando a entrada do usuário não for reconhecida ou nenhuma correspondência for encontrada em nenhum dos gatilhos `OnIntent`. Você também pode usar isso como o seu primeiro gatilho no diálogo raiz no lugar de `OnBeginDialog` para realizar as tarefas necessárias quando o diálogo for iniciado pela primeira vez. |

#### <a name="recognizer-trigger-examples"></a>Exemplos de gatilhos de reconhecedores

Exemplos de gatilhos `OnIntent` e `OnUnknownIntent` são fornecidos abaixo para demonstrar o uso dos gatilhos de reconhecedores.

> [!NOTE]
>
> * O gatilho `OnIntent` permite que você manipule o evento `recognizedIntent`. O evento `recognizedIntent` é acionado pelo [reconhecedor][8]. Todos os reconhecedores internos do SDK do Bot Framework emitem esse evento quando identificam com êxito a _entrada_ de um usuário para que o bot possa responder adequadamente.
> * Use o gatilho `OnUnknownIntent` para capturar e responder quando um evento `recognizedIntent` não tiver sido capturado e nem for manipulado por nenhum dos outros gatilhos. <!--This is especially helpful to capture and handle cases where your dialog wishes to participate in consultation.-->

``` C#
// Create the root dialog as an Adaptive dialog.
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog));

// Add a regex recognizer
rootDialog.Recognizer = new RegexRecognizer()
{
    Intents = new List<IntentPattern>()
    {
        new IntentPattern()
        {
            Intent = "HelpIntent",
            Pattern = "(?i)help"
        },
        new IntentPattern()
        {
            Intent = "CancelIntent",
            Pattern = "(?i)cancel|never mind"
        }
    }
};

// Create an OnIntent trigger named "helpTrigger" that handles the intent named "HelpIntent".
var helpTrigger = new OnIntent("HelpIntent");

// Create a list of actions to execute when the trigger named "helpTrigger" fires.
var helpActions = new List<Dialog>();
helpActions.Add(new SendActivity("Hello, I'm the samples bot. At the moment, I respond to only help!"));
helpTrigger.Actions = helpActions;

// Add the OnIntent trigger "helpTrigger" to the root dialog
rootDialog.Triggers.Add(helpTrigger);

// Create a trigger to handle the unhandled intent events. The unknown intent trigger fires when a  
// recognizedIntent event raised by the recognizer is not handled by any OnIntent trigger in the dialog.
// Given the RegEx recognizer added to this dialog, this trigger will fire when the user says 'cancel'.
// The RegexRecognizer returned the 'cancel' intent, however, we have no trigger attached to handled it.
// The OnUnknownIntent trigger will also fire when user says 'yo' (or any other word that does not map
// to any intent in the recognizer). When the recognizer parses the user input and does not detect an
// intent, it will return a 'none' intent and since there is no OnIntent trigger to handle  a 'none'
// intent, the unknown intent trigger fires.
var unhandledIntentTrigger = new OnUnknownIntent();
var unhandledIntentActions = new List<Dialog>();
unhandledIntentActions.Add(new SendActivity("Sorry, I did not recognize that"));
unhandledIntentTrigger.Actions = unhandledIntentActions;

// Add the OnUnknownIntent trigger "unhandledIntentTrigger" to the root dialog
rootDialog.Triggers.Add(unhandledIntentTrigger);
```

### <a name="dialog-events"></a>Eventos de caixa de diálogo

A caixa de diálogo dispara eventos específicos de caixa de diálogo relacionados ao "ciclo de vida" da caixa.  Atualmente, há seis gatilhos de caixa de diálogo no SDK do Bot Framework, e todos eles derivam da classe `OnDialogEvent`.

> [!TIP]
> Eles não são como manipuladores de eventos de interrupção normais, em que as ações do filho continuarão em execução depois que as ações dos manipuladores forem concluídas. Para todos os eventos abaixo, o bot executará um novo conjunto de ações e encerrará o turno quando essas ações forem concluídas.

> Você deve usar _gatilhos de caixa de diálogo_ para:
>
> * Tomar uma atitude imediatamente quando a caixa de diálogo for iniciada, mesmo antes da chamada ao reconhecedor.
> * Executar ações quando um evento de _cancelamento_ ocorrer.
> * Executar ações em mensagens recebidas ou enviadas.
> * Avaliar e tomar medidas com base no conteúdo de uma atividade de entrada.

| Nome do gatilho     | Evento base   | Descrição                                                                                                                         |
| ---------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| `OnBeginDialog`    | `BeginDialog`  | Ações a serem executadas quando a caixa de diálogo for iniciada. Para uso somente com caixas de diálogo secundárias, não deve ser usado na caixa de diálogo raiz; em caixa de diálogo raiz, use `OnUnknownIntent` para executar atividades de inicialização de caixa de diálogo.|
| `OnCancelDialog`   | `CancelDialog` | Esse evento permite impedir que o diálogo atual seja cancelado devido a um diálogo secundário estar executando uma ação `CancelAllDialogs`. |
| `OnEndOfActions`   | `EndOfActions` | Esse evento ocorrerá assim que todas as ações e eventos de ambiguidade forem processados.                                                        |
| `OnError`          | `Error`        | Ação a ser executada quando ocorrer um evento do diálogo `Error`. Esse evento é semelhante a `OnCancelDialog`, pois você está impedindo que a caixa de diálogo atual termine, neste caso devido a um erro em uma caixa de diálogo secundária.|
| `OnRepromptDialog` |`RepromptDialog`| Ações a serem executadas quando o evento `RepromptDialog` ocorrer.                                                                              |

> [!TIP]
> A maioria das caixas de diálogo secundárias inclui um gatilho `OnBeginDialog` que responde ao evento `BeginDialog`. Esse gatilho é acionado automaticamente quando a caixa de diálogo é iniciada, o que pode permitir que o bot responda imediatamente com uma [mensagem de boas-vindas](#dialog-event-trigger-example) ou um [prompt de entrada do usuário][14].

> [!IMPORTANT]
> Não use o gatilho `OnBeginDialog` na caixa de diálogo raiz, pois isso pode causar problemas. Em vez disso, você pode usar o gatilho `OnUnknownIntent`, que será acionado quando a caixa de diálogo raiz for executada.

#### <a name="dialog-event-trigger-example"></a>Exemplo de gatilho de evento de caixa de diálogo

Este exemplo demonstra como enviar uma mensagem de boas-vindas para o usuário usando o gatilho `OnBeginDialog`.

```cs
var adaptiveDialog = new AdaptiveDialog()
{
    Triggers = new List<OnCondition>()
    {
        new OnBeginDialog()
        {
            Actions = new List<Dialog>()
            {
                new SendActivity("Hello world!")
            }
        }
    }
};
```

### <a name="activity-events"></a>Eventos de atividade

Os gatilhos de atividade permitem associar ações a qualquer atividade de entrada do cliente, como quando um novo usuário ingressa e o bot inicia uma nova conversa. Informações adicionais sobre atividades podem ser encontradas no [esquema de Atividade do Bot Framework][3].

Todos os eventos de atividade têm um evento base de `ActivityReceived` e são refinados pelo seu _tipo de atividade_. A classe base da qual todos os gatilhos de atividade derivam é `OnActivity`.

| Causa do evento         | activityType   | Nome do gatilho                   | Descrição                                                                       |
| ------------------- | -------------- | ------------------------------ | --------------------------------------------------------------------------------- |
| Saudação            | `ConversationUpdate` | `OnConversationUpdateActivity` | Manipule os eventos acionados quando um usuário inicia uma nova conversa com o bot. |
| Conversa encerrada  | `EndOfConversation` | `OnEndOfConversationActivity`  | Ações a serem executadas no recebimento de uma atividade com o tipo `EndOfConversation`.  |
| Evento recebido      | `Event`        | `OnEventActivity`              | Ações a serem executadas no recebimento de uma atividade com o tipo `Event`.                   |
| Transferir para um humano   | `Handoff`      | `OnHandoffActivity`            | Ações a serem executadas no recebimento de uma atividade com o tipo `HandOff`.                 |
| Conversa invocada| `Invoke`       | `OnInvokeActivity`             | Ações a serem executadas no recebimento de uma atividade com o tipo `Invoke`.                  |
| O usuário está digitando      | `Typing`       | `OnTypingActivity`             | Ações a serem executadas no recebimento de uma atividade com o tipo `Typing`.                  |

#### <a name="activity-events-examples"></a>Exemplos de eventos de atividade

##### <a name="onconversationupdateactivity"></a>OnConversationUpdateActivity

O gatilho `OnConversationUpdateActivity` é um dos gatilhos que permitem que você manipule um evento de _atividade recebida_. O gatilho `OnConversationUpdateActivity` só será acionado quando a condição _ActivityTypes.conversationUpdate_ for atendida.

O snippet de código a seguir demonstra como você pode criar um gatilho `OnConversationUpdateActivity`:

```C#
var myDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(new TemplateEngine().AddFile(Path.Combine(".", "myDialog.lg"))),
    Triggers = new List<OnCondition>() {
        new OnConversationUpdateActivity() {
            Actions = new List<Dialog>() {
                new SendActivity("${Welcome-user()}")
            }
        }
    }
};
```

### <a name="message-events"></a>Eventos de mensagem

<!-- Should the Message events section be combined with the Activity events section? -->

Os gatilhos de **evento de mensagem** permitem reagir a qualquer evento de mensagem, como quando uma mensagem é atualizada (`MessageUpdate`) ou excluída (`MessageDeletion`) ou quando alguém reage (`MessageReaction`) a uma mensagem (por exemplo, algumas das reações de mensagens comuns incluem Curtida, Coração, Gargalhada, Surpreso, Triste e Irritado).

Os eventos de mensagem são um tipo de evento de atividade e, como tal, todos eles têm um evento base de `ActivityReceived` e são refinados pelo _tipo de atividade_. A classe base da qual todos os gatilhos de mensagem derivam é `OnActivity`.

| Causa do evento      | activityType      | Nome do gatilho               | Descrição                                                               |
| ---------------- | ----------------- | -------------------------- | ------------------------------------------------------------------------- |
| Mensagem recebida | `Message`         | `OnMessageActivity`        | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReceived`. | <!--Overrides Intent trigger.-->
| Mensagem excluída  | `MessageDeletion` | `OnMessageDeleteActivity`  | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageDelete`.   |
| Reação de mensagem | `MessageReaction` | `OnMessageReactionActivity`| Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReaction`. |
| Mensagem atualizada  | `MessageUpdate`   | `OnMessageUpdateActivity`  | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageUpdate`.   |

<!--TODO P1: Need Message event examples
#### Message event examples

--->

### <a name="custom-events"></a>Eventos personalizados

Você pode emitir seus eventos adicionando a ação [EmitEvent][13] a qualquer gatilho, depois pode manipular esse evento personalizado em qualquer gatilho em qualquer caixa de diálogo em seu bot definindo um gatilho de _evento personalizado_. Um gatilho de evento personalizado é o gatilho `OnDialogEvent` que, na verdade, torna-se um gatilho personalizado quando você define a propriedade `Event` com o mesmo valor que a propriedade `EventName` de EmitEvent.

> [!TIP]
> Você pode permitir que outras caixas de diálogo em seu bot manipulem o evento personalizado definindo a propriedade `BubbleEvent` de EmitEvent como true.

| Causa do evento  | Nome do gatilho    | Classe base    | Descrição                                                                                                        |
| ------------ | --------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| Evento personalizado | `OnDialogEvent` | `OnCondition` | Ações a serem executadas quando um evento personalizado for detectado. Use a ação [Emitir um evento personalizado][13] para gerar um evento personalizado. |

<!--Was: OnCustomEvent-->

#### <a name="custom-event-example"></a>Exemplo de evento personalizado

```csharp
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
    {
        Generator = new TemplateEngineLanguageGenerator(),
        Triggers = new List<OnCondition>()
        {
            new OnUnknownIntent()
            {
                Actions = new List<Dialog>()
                {
                    new TextInput()
                    {
                        Prompt = new ActivityTemplate("What's your name?"),
                        Property = "user.name",
                        AlwaysPrompt = true,
                        OutputFormat = "toLower(this.value)"
                    },
                    new EmitEvent()
                    {
                        EventName = "contoso.custom",
                        EventValue = "=user.name",
                        BubbleEvent = true,
                    },
                    new SendActivity("Your name is ${user.name}"),
                    new SendActivity("And you are ${$userType}")
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",

                // You can use conditions (expression) to examine value of the event as part of the trigger selection process.
                Condition = "turn.dialogEvent.value && (substring(turn.dialogEvent.value, 0, 1) == 'v')",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "VIP"
                    }
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",

                // You can use conditions (expression) to examine value of the event as part of the trigger selection process.
                Condition = "turn.dialogEvent.value && (substring(turn.dialogEvent.value, 0, 1) == 's')",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "Special"
                    }
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "regular customer"
                    }
                }
            }
        }
    };
```

## <a name="additional-information"></a>Informações adicionais

* [Introdução às caixas de diálogo adaptáveis][1]
* [Bibliotecas de caixa de diálogo][5]
* [Ações em caixas de diálogo adaptáveis][2]

[1]:bot-builder-adaptive-dialog-introduction.md
[2]:bot-builder-concept-adaptive-dialog-actions.md
[3]:https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md
[4]:bot-builder-concept-adaptive-expressions.md
[5]:bot-builder-concept-dialog.md
[6]:bot-builder-concept-adaptive-dialog-recognizers.md#intents
[7]:https://www.luis.ai/home
[8]:bot-builder-concept-adaptive-dialog-recognizers.md
[9]:https://aka.ms/luis-prediction-scores
[10]:https://aka.ms/how-bots-work
[11]:bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[12]:bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer
[13]:bot-builder-concept-adaptive-dialog-actions.md#emitevent
[14]:bot-builder-concept-adaptive-dialog-inputs.md
