---
title: Criar uma habilidade da Cortana usando Node.js | Microsoft Docs
description: Aprenda os conceitos básicos para criar uma habilidade da Cortana no SDK do Bot Builder para Node.js.
keywords: Bot Framework, habilidade da Cortana, fala, Node.js, Bot Builder, SDK, conceitos fundamentais, conceitos básicos
author: DeniseMak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 38fa3811c079f07f847fbfb0fad1b9ed9f695c51
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42905842"
---
# <a name="key-concepts-for-building-a-bot-for-cortana-skills-using-nodejs"></a>Os conceitos fundamentais para criar um bot de habilidades da Cortana usando Node.js
 
[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!NOTE]
> Este artigo é de conteúdo preliminar e será atualizado.

Este artigo apresenta os conceitos fundamentais para criar uma habilidade da Cortana no SDK do Bot Builder para Node.js. 

## <a name="what-is-a-cortana-skill"></a>O que é uma habilidade da Cortana?
Uma habilidade da Cortana é um bot que você pode chamar usando um cliente da Cortana, como aquele que está integrado ao Windows 10. O usuário inicia o bot dizendo algumas palavras-chave ou frases associadas ao bot. É possível usar o Portal do Bot Framework para configurar quais palavras-chave são usadas para iniciar o bot. 

Cortana pode ser imaginada como um canal habilitado para fala que pode enviar e receber mensagens de voz além da conversa textual. Um bot que é publicado como uma habilidade da Cortana deve ser projetado tanto para fala como para texto. O Bot Framework fornece métodos para especificar SSML (Linguagem de Marcação de Sintetização de Voz) para definir as mensagens faladas enviadas pelo bot.

## <a name="acknowledge-user-utterances"></a>Reconhecer enunciados de usuário 

<!-- Establishing conversational understanding -->
<!-- Placeholder: In this section, describe how you have to write your speech to sound natural -->


Ao criar um bot habilitado para fala, você deve tentar estabelecer o consenso e o entendimento mútuo na conversa. O bot deve "fundamentar" os enunciados do usuário, indicando que o usuário foi ouvido e compreendido.

Usuários ficam confusos se um sistema não fundamentar os enunciados do usuário. Por exemplo, a conversa a seguir pode ser um pouco confusa quando o bot pergunta: "o que vem depois?":

```
Agent: Did you want to review some more of your profile?

User: No.

Agent: What's next?
```

Se o bot adiciona um "Ok" como confirmação, é mais amigável para o usuário:

```
Agent: Did you want to review some more of your profile?

User: No.

Agent: **Okay**, what's next?
```


Graus de fundamentação, do mais fraco ao mais forte:
1. Atenção contínua
2. Próxima contribuição relevante
3. Confirmação: Resposta mínima ou continuador discursivo: "sim", "uh-hum", "ok", "beleza"
4. Demonstrar: indicar compreensão por reformulação, conclusão.
5. Exibir: repetir tudo ou parte.

#### <a name="acknowledgement-and-next-relevant-contribution"></a>Confirmação e próxima contribuição relevante
Usuário: ... Eu preciso viajar em maio.
Agente: **E**, qual dia em maio você queria viajar?
Usuário: OK, eu preciso estar lá do dia 12 ao dia 15?
Agente: **E**, você está voando para qual cidade?

#### <a name="grounding-by-demonstration"></a>Fundamentação por demonstração
Usuário: ... Eu preciso viajar em maio.
Agente: E, em **qual dia** de maio você queria viajar?
Usuário: OK, eu preciso estar lá do dia 12 ao dia 15?
Agente: **E**, você está voando para qual cidade?


### <a name="closure"></a>Fechamento

O bot que está executando uma ação deve apresentar evidência do desempenho bem-sucedido.
Também é importante indicar falha ou compreensão. 
* Fechamento sem fala: se você apertar um botão de elevador, a luz do botão acende.
Processo de duas etapas:
* Apresentação 
* Aceitação


### <a name="differences-in-content-presentation"></a>Diferenças na apresentação do conteúdo
Ao projetar o bot habilitado para fala, tenha em mente que a que o diálogo falado geralmente não é idêntico às mensagens textuais que o bot envia.
<!-- If there are differences in what the bot will say, in the text vs the speak fields of a prompt or in a waterfall, for example, discuss them here.

## Speech

You bot uses the **session.say** method to speak to the user. The speak method has three overloads:
* If you pass only one parameter to **session.say**, it can be a text parameter.
* If you pass two parameters to **session.say**, it can take text and SSML.
* If you pass three parameters, the third parameter takes an options structure that specifies all the options you can pass to build an **IMessage** object.

```javascript
var bot = new builder.UniversalBot(connector, function (session) {
    session.say("Hello... I'm a decision making bot.'.", 
        ssml.speak("Hello. I can help you answer all of life's tough questions."));
    session.replaceDialog('rootMenu');
});

```
## Speech in messages

The **IMessage** object provides a **speak** property for SSML. It can be used to play a .wav file.

The **inputHint** property helps indicate to Cortana whether your bot is expecting input. If you're using a built-in prompt, this value is automatically set to the default of **expectingInput**.

The **inputHint** property can take the following values: 
* **expectingInput**: Indicates that the bot is actively expecting a response from the user. Cortana listens for the user to speak into the microphone.
* **acceptingInput**: Indicates that the bot is passively ready for input but is not waiting on a response. Cortana accepts input from the user if the user holds down the microphone button.
* **ignoringInput**: Cortana is ignoring input. Your bot may send this hint if it is actively processing a request and will ignore input from users until the request is complete.

Prompts can take a `speak:` or `retrySpeak` option.

```javascript
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```

Prompts.number has *ordinal support*, meaning that you can say "the last", "the first", "the next-to-last" to choose an item in a list.




## Using synonyms

<!-- Axl Rose example -->     
```javascript   
         var choices = [
            { 
                value: 'flipCoinDialog',
                action: { title: "Flip A Coin" },
                synonyms: 'toss coin|flip quarter|toss quarter'
            },
            {
                value: 'rollDiceDialog',
                action: { title: "Roll Dice" },
                synonyms: 'roll die|shoot dice|shoot die'
            },
            {
                value: 'magicBallDialog',
                action: { title: "Magic 8-Ball" },
                synonyms: 'shake ball'
            },
            {
                value: 'quit',
                action: { title: "Quit" },
                synonyms: 'exit|stop|end'
            }
        ];
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```


## <a name="configuring-your-bot"></a>Configurar o bot

## <a name="prompts"></a>Solicitações


## <a name="additional-resources"></a>Recursos adicionais

[CortanaGetstarted]: /cortana/getstarted
[SSMLRef]: https://msdn.microsoft.com/en-us/library/hh378377(v=office.14).aspx
