---
title: Realizar chamadas de áudio com o Skype | Microsoft Docs
description: Saiba como realizar chamadas de áudio com o Skype usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 621be0d3fe785cfdd51e9bd5c864b9bc4f60d8ad
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464432"
---
# <a name="conduct-audio-calls-with-skype"></a>Realizar chamadas de áudio com o Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to conducting audio calls](../includes/snippet-audio-call-intro.md)]

A arquitetura de um bot que dá suporte a chamadas de áudio é muito semelhante à de um bot típico. Os exemplos de código a seguir mostram como habilitar o suporte para chamadas de áudio por meio do Skype com o SDK do Bot Framework para .NET. 

## <a name="enable-support-for-audio-calls"></a>Habilitar suporte para chamadas de áudio

Para habilitar um bot a dar suporte a chamadas de áudio, defina `CallingController`.

```cs
[BotAuthentication]
[RoutePrefix("api/calling")]
public class CallingController : ApiController
{
    public CallingController() : base()
    {
        CallingConversation.RegisterCallingBot(callingBotService => new IVRBot(callingBotService));
    }

    [Route("callback")]
    public async Task<HttpResponseMessage> ProcessCallingEventAsync()
    {
        return await CallingConversation.SendAsync(this.Request, CallRequestType.CallingEvent);
    }

    [Route("call")]
    public async Task<HttpResponseMessage> ProcessIncomingCallAsync()
    {
        return await CallingConversation.SendAsync(this.Request, CallRequestType.IncomingCall);
    }
}
```

> [!NOTE]
> Além de `CallingController`, que dá suporte a chamadas de áudio, um bot também pode conter `MessagesController` para dar suporte a mensagens. Fornecer as duas opções permite aos usuários interagir com o bot da maneira que preferirem. <!-- docs on MessagesController are where? -->

## <a name="answer-the-call"></a>Responder à chamada

A tarefa `ProcessIncomingCallAsync` será executada sempre que um usuário inicia uma chamada para esse bot pelo Skype.
O construtor registra a classe `IVRBot`, que tem um manipulador predefinido para `incomingCallEvent`.

A primeira ação do fluxo de trabalho deve determinar se o bot responde ou rejeita a chamada de entrada. Esse fluxo de trabalho instrui o bot a responder a chamada de entrada e, em seguida, reproduzir uma mensagem de boas-vindas. 

```cs
private Task OnIncomingCallReceived(IncomingCallEvent incomingCallEvent)
{
    this.callStateMap[incomingCallEvent.IncomingCall.Id] = new CallState(incomingCallEvent.IncomingCall.Participants);

    incomingCallEvent.ResultingWorkflow.Actions = new List<ActionBase>
    {
        new Answer { OperationId = Guid.NewGuid().ToString() },
        GetPromptForText(WelcomeMessage)
    };

    return Task.FromResult(true);
}
```

## <a name="after-the-bot-answers"></a>Após as respostas do bot

Se o bot responde à chamada, as ações subsequentes especificadas no fluxo de trabalho instruirão a **Plataforma de Bot do Skype para Chamadas** para executar prompt, gravar áudio, reconhecer fala ou coletar os dígitos de um teclado de discagem. A ação final do fluxo de trabalho pode ser encerrar a chamada. 

Este exemplo de código define um manipulador que configura um menu ao término da mensagem de boas-vindas.

```cs
private Task OnPlayPromptCompleted(PlayPromptOutcomeEvent playPromptOutcomeEvent)
{
    var callState = this.callStateMap[playPromptOutcomeEvent.ConversationResult.Id];
    SetupInitialMenu(playPromptOutcomeEvent.ResultingWorkflow);
    return Task.FromResult(true);
}
```

O método `CreateIvrOptions` define o menu que será apresentado ao usuário.

```cs
private static Recognize CreateIvrOptions(string textToBeRead, int numberOfOptions, bool includeBack)
{
    if (numberOfOptions > 9)
    {
        throw new Exception("too many options specified");
    }

    var choices = new List<RecognitionOption>();

    for (int i = 1; i <= numberOfOptions; i++)
    {
        choices.Add(new RecognitionOption { Name = Convert.ToString(i), DtmfVariation = (char)('0' + i) });
    }

    if (includeBack)
    {
        choices.Add(new RecognitionOption { Name = "#", DtmfVariation = '#' });
    }

    var recognize = new Recognize
    {
        OperationId = Guid.NewGuid().ToString(),
        PlayPrompt = GetPromptForText(textToBeRead),
        BargeInAllowed = true,
        Choices = choices
    };

    return recognize;
}
```

A classe `RecognitionOption` define a resposta dada, bem como a variação de DTMF (Dual-Tone Multi-Frequency) correspondente. DTMF permite que o usuário responda digitando os dígitos correspondentes no teclado em vez de falar.

O método `OnRecognizeCompleted` processa a seleção do usuário e o parâmetro de entrada `recognizeOutcomeEvent` contém o valor da seleção do usuário.

```cs
private Task OnRecognizeCompleted(RecognizeOutcomeEvent recognizeOutcomeEvent)
{
    var callState = this.callStateMap[recognizeOutcomeEvent.ConversationResult.Id];
    ProcessMainMenuSelection(recognizeOutcomeEvent, callState);
    return Task.FromResult(true);
}
```

## <a name="support-natural-language"></a>Suporte para linguagem natural
O bot também pode ser desenvolvido para dar suporte a respostas de idioma natural. O **API de Fala do Bing** permite que o bot a reconheça palavras na resposta falada do usuário.

```cs
private async Task OnRecordCompleted(RecordOutcomeEvent recordOutcomeEvent)
{
    recordOutcomeEvent.ResultingWorkflow.Actions = new List<ActionBase>
    {
        GetPromptForText(EndingMessage),
        new Hangup { OperationId = Guid.NewGuid().ToString() }
    };

    // Convert the audio to text
    if (recordOutcomeEvent.RecordOutcome.Outcome == Outcome.Success)
    {
        var record = await recordOutcomeEvent.RecordedContent;
        string text = await this.GetTextFromAudioAsync(record);

        var callState = this.callStateMap[recordOutcomeEvent.ConversationResult.Id];

        await this.SendSTTResultToUser("We detected the following audio: " + text, callState.Participants);
    }

    recordOutcomeEvent.ResultingWorkflow.Links = null;
    this.callStateMap.Remove(recordOutcomeEvent.ConversationResult.Id);
}
```

## <a name="sample-code"></a>Exemplo de código

Veja um exemplo completo que mostra como dar suporte a chamadas de áudio com o Skype usando o SDK do Bot Framework para .NET em <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/skype-CallingBot" target="_blank">Exemplo do Bot de Chamada do Skype</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/skype-CallingBot" target="_blank">Exemplo do Bot de Chamada do Skype (GitHub)</a>
