---
title: Eventos e gatilhos em caixas de diálogo adaptáveis – guia de referência
description: Descrevendo os gatilhos predefinidos da caixa de diálogo adaptável
keywords: bot, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 06/09/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6e286a0bdb9d0c5db5f9f1393a57b934c61a5d14
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576408"
---
# <a name="events-and-triggers-in-adaptive-dialogs---reference-guide"></a>Eventos e gatilhos em caixas de diálogo adaptáveis – guia de referência

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Para obter uma introdução a este tópico, consulte o artigo sobre o conceito de [eventos e gatilhos em caixas de diálogo adaptáveis](../v4sdk/bot-builder-concept-adaptive-dialog-triggers.md) .

## <a name="base-trigger"></a>Gatilho base

O gatilho `OnCondition` é o gatilho base do qual todos os gatilhos derivam. Em uma caixa de diálogo adaptável, os gatilhos são definidos como uma lista de objetos `OnCondition`, conforme demonstrado no seguinte exemplo:

```csharp
Triggers = new List<OnCondition>()
{
    ...
}
```

## <a name="recognizer-event-triggers"></a>Gatilhos de evento de reconhecedores

| Causa do evento               | Nome do gatilho  | Evento base    | Descrição                                                       |
| ------------------------- | ------------- | ------------- | ----------------------------------------------------------------- |
| Escolher intenção | `OnChooseIntent` |`ChooseIntent` | Esse gatilho é executado quando uma ambiguidade é detectada entre intenções de vários reconhecedores em um [CrossTrainedRecognizerSet][recognizers-cross-trained-recognizer-set].|
| Intenção reconhecida| `OnIntent` | `RecognizedIntent` | Ações a serem executadas quando a intenção especificada for reconhecida.           |
|Intenção QnAMatch|`OnQnAMatch`| `RecognizedIntent` |Esse gatilho será executado quando o [QnAMakerRecognizer][qna-maker-recognizer] tiver retornado uma intenção de `QnAMatch`. A entidade `@answer` terá a resposta `QnAMaker`.|
|Intenção desconhecida reconhecida| `OnUnknownIntent` | `UnknownIntent` | Ações a serem executadas quando a entrada do usuário não for reconhecida ou nenhuma correspondência for encontrada em nenhum dos gatilhos `OnIntent`. Você também pode usar isso como o seu primeiro gatilho no diálogo raiz no lugar de `OnBeginDialog` para realizar as tarefas necessárias quando o diálogo for iniciado pela primeira vez. |

> [!TIP]
> Use o `OnUnknownIntent` gatilho para capturar e responder quando ocorrer uma tentativa de "nenhum". O uso do `OnIntent` gatilho para lidar com uma intenção "nenhum" pode produzir resultados inesperados.

### <a name="recognizer-event-example"></a>Exemplo do evento Recognizer

Exemplos de gatilhos `OnIntent` e `OnUnknownIntent` são fornecidos abaixo para demonstrar o uso dos gatilhos de reconhecedores.

> [!NOTE]
>
> - O gatilho `OnIntent` permite que você manipule o evento `recognizedIntent`. O `recognizedIntent` evento é gerado por um [reconhecedor][recognizers]. Com exceção do [reconhecedor de QnA Maker][qna-maker-recognizer], todos os reconhecedores internos do SDK do bot Framework emitem esse evento quando identificam com êxito uma _entrada_ do usuário para que o bot possa responder adequadamente.
> - Use o gatilho `OnUnknownIntent` para capturar e responder quando um evento `recognizedIntent` não tiver sido capturado e nem for manipulado por nenhum dos outros gatilhos. Isso significa que qualquer intenção não tratada (incluindo "None") pode causar o gatilho, mas somente se não houver nenhuma ação em execução no momento para a caixa de diálogo.<!--Use the `OnUnknownIntent` trigger to catch and respond when a "none" intent occurs. This is especially helpful to capture and handle cases where your dialog wishes to participate in consultation.-->

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

## <a name="dialog-event-triggers"></a>Gatilhos de evento de diálogo

Os gatilhos de caixa de diálogo tratam eventos específicos da caixa de diálogo relacionados ao _ciclo de vida_ da caixa de diálogo.  Atualmente, há seis gatilhos de caixa de diálogo no SDK do Bot Framework, e todos eles derivam da classe `OnDialogEvent`.

> [!TIP]
> Eles não são como manipuladores de eventos de interrupção normais em que as ações do filho continuarão em execução depois que as ações do manipulador forem concluídas. Para todos os eventos abaixo, o bot executará um novo conjunto de ações e terminará a ativação quando essas ações forem concluídas.

| Nome do gatilho     | Evento base   | Descrição                                                                                                                         |
| ---------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| `OnBeginDialog`    | `BeginDialog`  | Ações a serem executadas quando a caixa de diálogo for iniciada. Para uso somente com caixas de diálogo secundárias, não deve ser usado na caixa de diálogo raiz; em caixa de diálogo raiz, use `OnUnknownIntent` para executar atividades de inicialização de caixa de diálogo.|
| `OnCancelDialog`   | `CancelDialog` | Esse evento permite impedir que o diálogo atual seja cancelado devido a um diálogo secundário estar executando uma ação `CancelAllDialogs`. |
| `OnEndOfActions`   | `EndOfActions` | Esse evento ocorrerá assim que todas as ações e eventos de ambiguidade forem processados.                                                        |
| `OnError`          | `Error`        | Ações a serem executadas quando um `Error` evento de caixa de diálogo ocorrer. Esse evento é semelhante ao `OnCancelDialog` que você está impedindo que a caixa de diálogo adaptável que contém esse gatilho seja encerrada, nesse caso, devido a um erro em uma caixa de diálogo filho.|
| `OnRepromptDialog` |`RepromptDialog`| Ações a serem executadas quando o evento `RepromptDialog` ocorrer.                                                                              |
| `OnDialog` | `DialogEvents.VersionChanged` | |

> [!TIP]
> Se você estiver usando a abordagem [declarativa][declarative] para caixas de diálogo adaptáveis, suas caixas de diálogo serão definidas como `.dialog` arquivos que são usados para criar suas caixas de diálogo em tempo de execução. Essas caixas de diálogo também podem ser modificadas em tempo de execução atualizando o `.dialog` arquivo diretamente e manipulando o `resourceExplorer.Changed` evento para recarregar a caixa de diálogo. Você também pode capturar o `DialogEvents.VersionChanged` evento em um [gatilho][triggers] para executar as ações necessárias que podem resultar de uma caixa de diálogo sendo alterada no meio de uma conversa com um usuário. Para obter mais informações, consulte [caixas de diálogo de Recarregamento automático quando o arquivo é alterado][auto-reload-dialogs-when-file-changes] no artigo [usando ativos declarativos em caixas de diálogo adaptáveis][declarative] .

### <a name="dialog-event-example"></a>Exemplo de evento de caixa de diálogo

Este exemplo demonstra como enviar uma mensagem de boas-vindas para o usuário usando o gatilho `OnBeginDialog`.

```cs
var welcomeDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
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

## <a name="activity-event-triggers"></a>Gatilhos de evento de atividade

Os gatilhos de atividade permitem associar ações a qualquer atividade de entrada do cliente, como quando um novo usuário ingressa e o bot inicia uma nova conversa. Informações adicionais sobre atividades podem ser encontradas no [esquema de Atividade do Bot Framework][botframework-activity].

Todos os eventos de atividade têm um evento base de `ActivityReceived` e são refinados pelo seu _tipo de atividade_. A classe base da qual todos os gatilhos de atividade derivam é `OnActivity`.

| Causa do evento         | activityType   | Nome do gatilho                   | Descrição                                                                       |
| ------------------- | -------------- | ------------------------------ | --------------------------------------------------------------------------------- |
| Saudação            | `ConversationUpdate` | `OnConversationUpdateActivity` | Ações a serem executadas no recebimento de uma `conversationUpdate` atividade, quando o bot ou um usuário ingressar ou sair de uma conversa. |
| Conversa encerrada  | `EndOfConversation` | `OnEndOfConversationActivity`  | Ações a serem executadas no recebimento de uma `endOfConversation` atividade.  |
| Evento recebido      | `Event`        | `OnEventActivity`              | Ações a serem executadas no recebimento de uma `event` atividade.                   |
| Transferir para um humano   | `Handoff`      | `OnHandoffActivity`            | Ações a serem executadas no recebimento de uma `handOff` atividade.   |
| Conversa invocada| `Invoke`       | `OnInvokeActivity`             | Ações a serem executadas no recebimento de uma `invoke` atividade.                  |
| O usuário está digitando      | `Typing`       | `OnTypingActivity`             | Ações a serem executadas no recebimento de uma `typing` atividade.                  |

### <a name="activity-event-example"></a>Exemplo de evento de atividade

#### <a name="onconversationupdateactivity"></a>OnConversationUpdateActivity

O `OnConversationUpdateActivity` gatilho permite que você manipule um evento de _atividade recebida_ . O `OnConversationUpdateActivity` gatilho só será acionado quando a atividade recebida for uma `conversationUpdate` atividade.

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

## <a name="message-event-triggers"></a>Gatilhos de evento de mensagem

<!-- Should the Message events section be combined with the Activity events section? -->

Os gatilhos de evento de mensagem permitem reagir a qualquer evento de mensagem, como quando uma mensagem é atualizada (`MessageUpdate`) ou excluída (`MessageDeletion`) ou quando alguém reage (`MessageReaction`) a uma mensagem (por exemplo, algumas das reações de mensagens comuns incluem Curtida, Coração, Gargalhada, Surpreso, Triste e Irritado).

Os eventos de mensagem são um tipo de evento de atividade e, como tal, todos eles têm um evento base de `ActivityReceived` e são refinados pelo _tipo de atividade_. A classe base da qual todos os gatilhos de mensagem derivam é `OnActivity`.

| Causa do evento      | activityType      | Nome do gatilho               | Descrição                                                               |
| ---------------- | ----------------- | -------------------------- | ------------------------------------------------------------------------- |
| Mensagem recebida | `Message`         | `OnMessageActivity`        | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReceived`. | <!--Overrides Intent trigger.-->
| Mensagem excluída  | `MessageDeletion` | `OnMessageDeleteActivity`  | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageDelete`.   |
| Reação de mensagem | `MessageReaction` | `OnMessageReactionActivity`| Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReaction`. |
| Mensagem atualizada  | `MessageUpdate`   | `OnMessageUpdateActivity`  | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageUpdate`.   |

<!--TODO P1: Need Message event examples
### Message event examples

--->

## <a name="custom-event-trigger"></a>Gatilho de evento personalizado

Você pode emitir seus eventos adicionando a ação [EmitEvent][emitevent] a qualquer gatilho, depois pode manipular esse evento personalizado em qualquer gatilho em qualquer caixa de diálogo em seu bot definindo um gatilho de _evento personalizado_. Um gatilho de evento personalizado é o gatilho `OnDialogEvent` que, na verdade, torna-se um gatilho personalizado quando você define a propriedade `Event` com o mesmo valor que a propriedade `EventName` de EmitEvent.

> [!TIP]
> Você pode permitir que outras caixas de diálogo em seu bot manipulem o evento personalizado definindo a propriedade `BubbleEvent` de EmitEvent como true.

| Causa do evento  | Nome do gatilho    | Classe base    | Descrição                                                                                                              |
| ------------ | --------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Evento personalizado | `OnDialogEvent` | `OnCondition` | Ações a serem executadas quando um evento personalizado for detectado. Use [emitir uma ação de evento personalizado][emitevent] para gerar um evento personalizado. |

<!--Was: OnCustomEvent-->

### <a name="custom-event-example"></a>Exemplo de evento personalizado

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

- [Introdução às caixas de diálogo adaptáveis][introduction]
- [Biblioteca de caixas de diálogo][bot-builder-concept-dialog]
- [Ações em caixas de diálogo adaptáveis][actions]

[introduction]:../v4sdk/bot-builder-adaptive-dialog-introduction.md
[actions]:../v4sdk/bot-builder-concept-adaptive-dialog-actions.md
[triggers]:../v4sdk/bot-builder-concept-adaptive-dialog-triggers.md
[inputs]:../v4sdk/bot-builder-concept-adaptive-dialog-inputs.md
[recognizers]:../v4sdk/bot-builder-concept-adaptive-dialog-recognizers.md

[declarative]:../v4sdk/bot-builder-concept-adaptive-dialog-declarative.md
[auto-reload-dialogs-when-file-changes]:../v4sdk/bot-builder-concept-adaptive-dialog-declarative.md#auto-reload-dialogs-when-file-changes

[bot-builder-concept-dialog]:../v4sdk/bot-builder-concept-dialog.md

[recognizers-cross-trained-recognizer-set]:../v4sdk/bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[qna-maker-recognizer]:adaptive-dialog-prebuilt-recognizers.md#qna-maker-recognizer
[emitevent]:adaptive-dialog-prebuilt-actions.md#emitevent

[botframework-activity]:https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md
