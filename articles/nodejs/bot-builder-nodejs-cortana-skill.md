---
title: Criar um bot habilitado para fala com habilidades da Cortana | Microsoft Docs
description: Saiba como criar um bot habilitado para fala com habilidades da Cortana e o SDK do Bot Builder para o Node.js.
author: DeniseMak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: c213c1155b1eef5f5c776ba42a221d95b74f99a5
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42906071"
---
# <a name="build-a-speech-enabled-bot-with-cortana-skills"></a>Criar um bot habilitado para fala com habilidades da Cortana

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-cortana-skill.md)
> - [Node.js](../nodejs/bot-builder-nodejs-cortana-skill.md)

O SDK do Bot Builder para Node.js permite que você crie um bot habilitado para fala conectando-o ao canal da Cortana como uma habilidade da Cortana. Habilidades da Cortana permitem que você forneça funcionalidades por meio da Cortana em resposta à entrada falada de um usuário.

> [!TIP]
> Para obter mais informações sobre o que é uma habilidade e o que ela pode fazer, consulte o [Kit de Habilidades da Cortana][CortanaGetStarted].

Criar uma habilidade da Cortana usando o Bot Framework requer muito pouco conhecimento específico sobre Cortana e consiste principalmente na criação de um bot. Uma das principais diferenças de outros bots que você talvez tenha criado é que a Cortana tem componentes visuais e de áudio. Para o componente visual, a Cortana fornece uma área da tela para renderizar o conteúdo, como cartões, por exemplo. Para o componente de áudio, você fornece texto ou SSML nas mensagens do bot, que são lidas pela Cortana para o usuário, dando uma voz para o bot. 

> [!NOTE]
> A Cortana está disponível em vários dispositivos diferentes. Alguns têm uma tela, enquanto outros, como um alto-falante autônomo, talvez não. Garanta que o bot consiga lidar com ambos os cenários. Confira [Entidades específicas da Cortana][CortanaSpecificEntities] para saber como verificar as informações do dispositivo.

## <a name="adding-speech-to-your-bot"></a>Adicionando fala ao bot

As mensagens faladas do bot são representadas como SSML (Linguagem de Marcação de Sintetização de Voz). O SDK do Bot Builder permite incluir SSML nas respostas do seu bot para controlar o que o bot diz, além do que ele mostra.

### <a name="sessionsay"></a>session.say

Seu bot usa o método **session.say** para falar com o usuário, em vez de **session.send**. Ele inclui parâmetros opcionais para o envio da saída de SSML, assim como anexos, como cartões, por exemplo. 

O método tem este formato:

```session.say(displayText: string, speechText: string, options?: object)```

| Parâmetro | DESCRIÇÃO |
|------|------|
| **displayText** | Uma mensagem de texto para exibir na interface do usuário da Cortana.|
| **speechText** | O texto ou SSML que a Cortana lê para o usuário. |
| **options** | Um objeto [IMessage][IMessage] que pode conter um anexo ou dica de entrada. Dicas de entrada indicam se o bot está aceitando, aguardando ou ignorando a entrada. Anexos de cartão são exibidos na tela da Cortana abaixo das informações de **displayText**.   |

A propriedade **inputHint** ajuda a indicar à Cortana se o seu bot está esperando uma entrada. Se você estiver usando um prompt interno, esse valor é automaticamente definido como **expectingInput** por padrão.


| Valor | DESCRIÇÃO |
|------|------|
| **acceptingInput** | O bot está passivamente pronto para a entrada, mas não está aguardando uma resposta. Cortana aceita a entrada do usuário, se o usuário mantém pressionado o botão de microfone.|
| **expectingInput** | Indica se o bot está ativamente aguardando uma resposta do usuário. Cortana ouve o usuário falar no microfone.  |
| **ignoringInput** | Cortana está ignorando a entrada. Seu bot pode enviar essa dica se ele estiver processando ativamente uma solicitação e ignorará a entrada de usuários até que a solicitação seja concluída.  |


O exemplo a seguir mostra como a Cortana lê o texto sem formatação ou SSML:

```javascript
// Have Cortana read plain text
session.say('This is the text that Cortana displays', 'This is the text that is spoken by Cortana.');

// Have Cortana read SSML
session.say('This is the text that Cortana displays', '<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">This is the text that is spoken by Cortana.</speak>');
```

Este exemplo mostra como permitir que a Cortana saiba que a entrada do usuário é esperada. O microfone será deixado aberto.
```javascript
// Add an InputHint to let Cortana know to expect user input
session.say('Hi there', 'Hi, what’s your name?', {
    inputHint: builder.InputHint.expectingInput
});
```
<!-- TODO: tip about time limit and batching -->


### <a name="prompts"></a>Prompts

Além de usar o método **session.say()** você também pode passar texto ou SSML para prompts internos usando as opções **speak** e **retrySpeak**.  

```javascript
builder.Prompts.text(session, 'text based prompt', {                                    
    speak: 'Cortana reads this out initially',                                               
    retrySpeak: 'This message is repeated by Cortana after waiting a while for user input',  
    inputHint: builder.InputHint.expectingInput                                              
});
```



<!-- TODO: Link to SSML library -->

Para apresentar ao usuário uma lista de opções, use **Prompts.choice**. A opção **synonyms** permite mais flexibilidade ao reconhecer os enunciados do usuário. A opção **value** é retornada em **results.response.entity**. A opção **action** especifica o rótulo que seu bot exibe para a escolha.

**Prompts.Choice** dá suporte a opções de número ordinal. Isso significa que o usuário pode dizer “primeiro”, “segundo” ou “terceiro” para escolher um item em uma lista. Por exemplo, considerando o seguinte prompt, se o usuário solicitar à Cortana a “segunda opção”, o prompt retornará o valor 8.

```javascript
        var choices = [
            { value: '4', action: { title: '4 Sides' }, synonyms: 'four|for|4 sided|4 sides' },
            { value: '8', action: { title: '8 Sides' }, synonyms: 'eight|ate|8 sided|8 sides' },
            { value: '12', action: { title: '12 Sides' }, synonyms: 'twelve|12 sided|12 sides' },
            { value: '20', action: { title: '20 Sides' }, synonyms: 'twenty|20 sided|20 sides' },
        ];
        builder.Prompts.choice(session, 'choose_sides', choices, { 
            speak: speak(session, 'choose_sides_ssml') // use helper function to format SSML
        });
```

No exemplo anterior, o SSML para a propriedade **speak** do prompt é formatada usando cadeias de caracteres armazenadas em um arquivo de prompts localizado com o seguinte formato. 

```json
{
    "choose_sides": "__Number of Sides__",
    "choose_sides_ssml": [
        "How many sides on the dice?",
        "Pick your poison.",
        "All the standard sizes are supported."
    ]
}
```


Uma função auxiliar, em seguida, cria o elemento raiz necessário de um documento de linguagem SSML. 

```javascript

module.exports.speak = function (template, params, options) {
    options = options || {};
    var output = '<speak xmlns="http://www.w3.org/2001/10/synthesis" ' +
        'version="' + (options.version || '1.0') + '" ' +
        'xml:lang="' + (options.lang || 'en-US') + '">';
    output += module.exports.vsprintf(template, params);
    output += '</speak>';
    return output;
}
```

> [!TIP]
> Você pode encontrar um módulo de utilitário pequeno (ssml.js) para criar as respostas do seu bot baseadas em SSML na [habilidade de exemplo de Lançador](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill).
> Também há várias bibliotecas SSML úteis disponíveis por meio de [npm](https://www.npmjs.com/search?q=ssml) que facilita a criação de um SSML bem formatado.

## <a name="display-cards-in-cortana"></a>Exibir cartões na Cortana

Além das respostas faladas, a Cortana também pode exibir anexos de cartão. Cortana suporta os seguintes cartões com formatação:
* [HeroCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.herocard.html)
* [ReceiptCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.receiptcard.html)
* [ThumbnailCard](https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.thumbnailcard.html)

Consulte [Práticas recomendadas de design de cartão][CardDesign] para ver como é a aparência desses cartões dentro da Cortana. Para obter um exemplo de como adicionar um cartão com formatação para um bot, consulte [Enviar cartões com formatação](bot-builder-nodejs-send-rich-cards.md). 

O código a seguir demonstra como adicionar as propriedades **speak** e **inputHint** a uma mensagem que contém um cartão Hero.

```javascript 

bot.dialog('HelpDialog', function (session) {
    var card = new builder.HeroCard(session)
        .title('help_title')
        .buttons([
            builder.CardAction.imBack(session, 'roll some dice', 'Roll Dice'),
            builder.CardAction.imBack(session, 'play yahtzee', 'Play Yahtzee')
        ]);
    var msg = new builder.Message(session)
        .speak(speak(session, 'I\'m roller, the dice rolling bot. You can say \'roll some dice\''))
        .addAttachment(card)
        .inputHint(builder.InputHint.acceptingInput); // Tell Cortana to accept input
    session.send(msg).endDialog();
}).triggerAction({ matches: /help/i });


/** This helper function builds the required root element of a Speech Synthesis Markup Language (SSML) document. */
module.exports.speak = function (template, params, options) {
    options = options || {};
    var output = '<speak xmlns="http://www.w3.org/2001/10/synthesis" ' +
        'version="' + (options.version || '1.0') + '" ' +
        'xml:lang="' + (options.lang || 'en-US') + '">';
    output += module.exports.vsprintf(template, params);
    output += '</speak>';
    return output;
}

```
## <a name="sample-rollerskill"></a>Exemplo: RollerSkill
O código das seções a seguir foi obtido de uma habilidade da Cortana de exemplo para lançamento de um dado. Baixe o código completo do bot no [repositório BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-RollerSkill).

Invoque a habilidade dizendo seu [nome de invocação][InvocationNameGuidelines] para a Cortana. Para a habilidade do lançador, depois que você [adicionar o bot ao canal da Cortana][CortanaChannel] e registrá-lo como uma habilidade da Cortana, invoque-o solicitando à Cortana para "Solicitar o Lançador" ou "Solicitar que o Lançador lance o dado".

### <a name="explore-the-code"></a>Explore o código

O exemplo de RollerSkill começa abrindo um cartão com alguns botões para informar ao usuário quais opções estão disponíveis para eles.

```javascript
/**
 *   Create your bot with a default message handler that receive messages from the user.
 * - This function is be called anytime the user's utterance isn't
 *   recognized by the other handlers in the bot.
 */
var bot = new builder.UniversalBot(connector, function (session) {
    // Just redirect to our 'HelpDialog'.
    session.replaceDialog('HelpDialog');
});

//...

bot.dialog('HelpDialog', function (session) {
    var card = new builder.HeroCard(session)
        .title('help_title')
        .buttons([
            builder.CardAction.imBack(session, 'roll some dice', 'Roll Dice'),
            builder.CardAction.imBack(session, 'play craps', 'Play Craps')
        ]);
    var msg = new builder.Message(session)
        .speak(speak(session, 'help_ssml'))
        .addAttachment(card)
        .inputHint(builder.InputHint.acceptingInput);
    session.send(msg).endDialog();
}).triggerAction({ matches: /help/i });
```

### <a name="prompt-the-user-for-input"></a>Solicitar entrada ao usuário

A caixa de diálogo a seguir configura um jogo personalizado para o bot jogar.  Ele pergunta ao usuário quantos lados ele quer que o dado tenha e, em seguida, quantos devem ser lançados. Depois que ele criar a estrutura de jogo ele passará para uma caixa de diálogo “PlayGameDialog” separada.

Para iniciar a caixa de diálogo, o manipulador **triggerAction()** nessa caixa de diálogo permite que um usuário diga algo como “Eu gostaria lançar alguns dados”. Ele usa uma expressão regular para corresponder à entrada do usuário, mas você poderia usar facilmente uma [intenção LUIS](./bot-builder-nodejs-recognize-intent-luis.md). 


```javascript


bot.dialog('CreateGameDialog', [
    function (session) {
        // Initialize game structure.
        // - dialogData gives us temporary storage of this data in between
        //   turns with the user.
        var game = session.dialogData.game = { 
            type: 'custom', 
            sides: null, 
            count: null,
            turns: 0
        };

        var choices = [
            { value: '4', action: { title: '4 Sides' }, synonyms: 'four|for|4 sided|4 sides' },
            { value: '6', action: { title: '6 Sides' }, synonyms: 'six|sex|6 sided|6 sides' },
            { value: '8', action: { title: '8 Sides' }, synonyms: 'eight|8 sided|8 sides' },
            { value: '10', action: { title: '10 Sides' }, synonyms: 'ten|10 sided|10 sides' },
            { value: '12', action: { title: '12 Sides' }, synonyms: 'twelve|12 sided|12 sides' },
            { value: '20', action: { title: '20 Sides' }, synonyms: 'twenty|20 sided|20 sides' },
        ];
        builder.Prompts.choice(session, 'choose_sides', choices, { 
            speak: speak(session, 'choose_sides_ssml') 
        });
    },
    function (session, results) {
        // Store users input
        // - The response comes back as a find result with index & entity value matched.
        var game = session.dialogData.game;
        game.sides = Number(results.response.entity);

        /**
         * Ask for number of dice.
         */
        var prompt = session.gettext('choose_count', game.sides);
        builder.Prompts.number(session, prompt, {
            speak: speak(session, 'choose_count_ssml'),
            minValue: 1,
            maxValue: 100,
            integerOnly: true
        });
    },
    function (session, results) {
        // Store users input
        // - The response is already a number.
        var game = session.dialogData.game;
        game.count = results.response;

        /**
         * Play the game we just created.
         * 
         * replaceDialog() ends the current dialog and start a new
         * one in its place. We can pass arguments to dialogs so we'll pass the
         * 'PlayGameDialog' the game we created.
         */
        session.replaceDialog('PlayGameDialog', { game: game });
    }
]).triggerAction({ matches: [
    /(roll|role|throw|shoot).*(dice|die|dye|bones)/i,
    /new game/i
 ]});
```

### <a name="render-results"></a>Renderizar resultados

 Essa caixa de diálogo é nosso loop principal do jogo. O bot armazena a estrutura de jogo em **session.conversationData** para quando o usuário dizer “lançar novamente” podemos simplesmente lançar novamente o mesmo conjunto de dados.

```javascript

bot.dialog('PlayGameDialog', function (session, args) {
    // Get current or new game structure.
    var game = args.game || session.conversationData.game;
    if (game) {
        // Generate rolls
        var total = 0;
        var rolls = [];
        for (var i = 0; i < game.count; i++) {
            var roll = Math.floor(Math.random() * game.sides) + 1;
            if (roll > game.sides) {
                // Accounts for 1 in a million chance random() generated a 1.0
                roll = game.sides;
            }
            total += roll;
            rolls.push(roll);
        }

        // Format roll results
        var results = '';
        var multiLine = rolls.length > 5;
        for (var i = 0; i < rolls.length; i++) {
            if (i > 0) {
                results += ' . ';
            }
            results += rolls[i];
        }

        // Render results using a card
        var card = new builder.HeroCard(session)
            .subtitle(game.count > 1 ? 'card_subtitle_plural' : 'card_subtitle_singular', game)
            .buttons([
                builder.CardAction.imBack(session, 'roll again', 'Roll Again'),
                builder.CardAction.imBack(session, 'new game', 'New Game')
            ]);
        if (multiLine) {
            //card.title('card_title').text('\n\n' + results + '\n\n');
            card.text(results);
        } else {
            card.title(results);
        }
        var msg = new builder.Message(session).addAttachment(card);

        // Determine bots reaction for speech purposes
        var reaction = 'normal';
        var min = game.count;
        var max = game.count * game.sides;
        var score = total/max;
        if (score == 1.0) {
            reaction = 'best';
        } else if (score == 0) {
            reaction = 'worst';
        } else if (score <= 0.3) {
            reaction = 'bad';
        } else if (score >= 0.8) {
            reaction = 'good';
        }

        // Check for special craps rolls
        if (game.type == 'craps') {
            switch (total) {
                case 2:
                case 3:
                case 12:
                    reaction = 'craps_lose';
                    break;
                case 7:
                    reaction = 'craps_seven';
                    break;
                case 11:
                    reaction = 'craps_eleven';
                    break;
                default:
                    reaction = 'craps_retry';
                    break;
            }
        }

        // Build up spoken response
        var spoken = '';
        if (game.turn == 0) {
            spoken += session.gettext('start_' + game.type + '_game_ssml') + ' ';
        } 
        spoken += session.gettext(reaction + '_roll_reaction_ssml');
        msg.speak(ssml.speak(spoken));

        // Increment number of turns and store game to roll again
        game.turn++;
        session.conversationData.game = game;

        /**
         * Send card and bot's reaction to user. 
         */

        msg.inputHint(builder.InputHint.acceptingInput);
        session.send(msg).endDialog();
    } else {
        // User started session with "roll again" so let's just send them to
        // the 'CreateGameDialog'
        session.replaceDialog('CreateGameDialog');
    }
}).triggerAction({ matches: /(roll|role|throw|shoot) again/i });
```

## <a name="next-steps"></a>Próximas etapas
Se você tiver um bot em execução localmente ou implantado na nuvem, você pode invocá-lo da Cortana. Consulte [Testar uma habilidade Cortana](../bot-service-debug-cortana-skill.md) para as etapas necessárias para testar sua Habilidade da Cortana.


## <a name="additional-resources"></a>Recursos adicionais
* [O Kit de Habilidades da Cortana][CortanaGetStarted]
* [Adicionar fala a mensagens](bot-builder-nodejs-text-to-speech.md)
* [Referência de SSML][SSMLRef]
* [Melhores práticas de design de voz para a Cortana][VoiceDesign]
* [Melhores práticas de design de cartão para a Cortana][CardDesign]
* [Centro de Desenvolvimento da Cortana][CortanaDevCenter]
* [Testar e depurar as melhores práticas para a Cortana][Cortana-TestBestPractice]


[CortanaGetStarted]: /cortana/getstarted
[BFPortal]: https://dev.botframework.com/


[SSMLRef]: https://aka.ms/cortana-ssml
[IMessage]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage.html
[Send]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session#send
[CortanaDevCenter]: https://developer.microsoft.com/en-us/cortana

[CortanaSpecificEntities]: https://aka.ms/lgvcto
[CortanaAuth]: https://aka.ms/vsdqcj

[InvocationNameGuidelines]: https://aka.ms/cortana-invocation-guidelines
[VoiceDesign]: https://aka.ms/cortana-design-voice
[CardDesign]: https://aka.ms/cortana-design-card
[Cortana-Debug]: https://aka.ms/cortana-enable-debug
[Cortana-Publish]: https://aka.ms/cortana-publish


[CortanaTry]: https://aka.ms/try-cortana-bot
[CortanaChannel]: https://aka.ms/bot-cortana-channel
[Cortana-TestBestPractice]: https://aka.ms/cortana-test-best-practice
