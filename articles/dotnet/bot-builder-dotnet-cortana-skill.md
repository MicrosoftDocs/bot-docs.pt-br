---
title: Criar uma habilidade da Cortana usando o .NET – Serviço de Bot
description: Aprenda os conceitos básicos da criação de uma habilidade da Cortana no SDK do Bot Framework para .NET.
keywords: Bot Framework, habilidade da Cortana, fala, .NET, SDK, principais conceitos, conceitos básicos
author: DeniseMak
manager: kamrani
ms.author: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ecdbf3e573c091893d0f2da8d2c7acb0c6348810
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75796680"
---
# <a name="build-a-speech-enabled-bot-with-cortana-skills"></a>Criar um bot habilitado para fala com habilidades da Cortana

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-cortana-skill.md)
> - [Node.js](../nodejs/bot-builder-nodejs-cortana-skill.md)


O SDK do Bot Framework para .NET permite que você crie um bot habilitado para fala conectando-o ao canal da Cortana como uma habilidade da Cortana. 


> [!TIP]
> Para obter mais informações sobre o que é uma habilidade e o que ela pode fazer, confira [O Kit de Habilidades da Cortana][CortanaGetStarted].

A criação de uma habilidade da Cortana usando o Bot Framework exige muito pouco conhecimento específico sobre a Cortana e consiste principalmente na criação de um bot. Uma das principais prováveis diferenças de outros bots que talvez você tenha criado no passado é que a Cortana tem um componente visual e de áudio. Para o componente visual, a Cortana fornece uma área da tela para renderizar o conteúdo, como cartões. Para o componente de áudio, você fornece texto ou SSML nas mensagens do bot, que são lidas pela Cortana para o usuário, dando uma voz para o bot. 

> [!NOTE]
> A Cortana está disponível em vários dispositivos diferentes. Alguns têm uma tela, enquanto outros, como um alto-falante autônomo, talvez não. Garanta que o bot consiga lidar com ambos os cenários. Confira [Entidades específicas da Cortana][CortanaSpecificEntities] para saber como verificar as informações do dispositivo.

## <a name="adding-speech-to-your-bot"></a>Adicionando fala ao bot

As mensagens faladas do bot são representadas como SSML (Linguagem de Marcação de Sintetização de Voz). O SDK do Bot Framework permite incluir SSML nas respostas do bot para controlar o que o bot diz, além do que ele mostra.  Você também pode controlar o estado do microfone da Cortana, especificando se o bot está aceitando, esperando ou ignorando a entrada do usuário.

Defina a propriedade `Speak` do objeto `IMessageActivity` para especificar uma mensagem a ser dita pela Cortana. Se você especificar um texto sem formatação, a Cortana determinará como as palavras serão pronunciadas. 

```cs
Activity reply = activity.CreateReply("This is the text that Cortana displays."); 
reply.Speak = "This is the text that Cortana will say.";
```

Caso deseje obter mais controle sobre a intensidade, o tom e a ênfase, formate a propriedade `Speak` como [SSML (Linguagem de Marcação de Sintetização de Voz)](http://www.w3.org/TR/speech-synthesis/).  

O seguinte exemplo de código especifica que a palavra "texto" deve ser falada com uma quantidade moderada de ênfase:
```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "<speak version=\"1.0\" xmlns=\"http://www.w3.org/2001/10/synthesis\" xml:lang=\"en-US\">This is the <emphasis level=\"moderate\">text</emphasis> that will be spoken.</speak>";
```


A propriedade **InputHint** ajuda a indicar à Cortana se o bot está esperando uma entrada. O valor padrão é **ExpectingInput** para um prompt e **AcceptingInput** para outros tipos de respostas.


| Valor | DESCRIÇÃO |
|------|------|
| **AcceptingInput** | O bot está passivamente pronto para a entrada, mas não está aguardando uma resposta. A Cortana aceita a entrada do usuário se o usuário mantém pressionado o botão de microfone.|
| **ExpectingInput** | Indica se o bot está ativamente esperando uma resposta do usuário. A Cortana ouve o usuário falar no microfone.  |
| **IgnoringInput** | A Cortana está ignorando a entrada. O bot poderá enviar essa dica se estiver processando ativamente uma solicitação e ignorará a entrada dos usuários até que a solicitação seja concluída.  |

<!-- TODO: tip about time limit and batching -->

Este exemplo mostra como informar a Cortana de que a entrada do usuário é esperada. O microfone será deixado aberto.
```cs
// Add an InputHint to let Cortana know to expect user input
Activity reply = activity.CreateReply("This is the text that will be displayed."); 
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.ExpectingInput;
```



## <a name="display-cards-in-cortana"></a>Exibir cartões na Cortana

Além das respostas faladas, a Cortana também pode exibir anexos de cartão. A Cortana dá suporte aos seguintes cartões avançados:

| Tipo de cartão | DESCRIÇÃO |
|----|----|
| [HeroCard][heroCard] | Um cartão que geralmente contém uma única imagem grande, um ou mais botões e um texto. |
| [ThumbnailCard][thumbnailCard] | Um cartão que geralmente contém uma única imagem em miniatura, um ou mais botões e um texto. |
| [ReceiptCard][receiptCard] | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| [SignInCard][signinCard] | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |


Confira [Melhores práticas de design de cartão][CardDesign] para ver a aparência desses cartões na Cortana. Para obter um exemplo de como usar um cartão avançado em um bot, confira [Adicionar anexos de cartão avançado às mensagens](bot-builder-dotnet-add-rich-card-attachments.md). 

<!--
The following code demonstrates how to add the `Speak` and `InputHint` properties to a message containing a `HeroCard`.
-->


## <a name="sample-rollerskill"></a>Exemplo: RollerSkill
O código das seções a seguir foi obtido de uma habilidade da Cortana de exemplo para lançamento de um dado. Baixe o código completo do bot no [repositório BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples/).

Invoque a habilidade dizendo seu [nome de invocação][InvocationNameGuidelines] para a Cortana. Para a habilidade do lançador, depois que você [adicionar o bot ao canal da Cortana][CortanaChannel] e registrá-lo como uma habilidade da Cortana, invoque-o solicitando à Cortana para "Solicitar o Lançador" ou "Solicitar que o Lançador lance o dado".

### <a name="explore-the-code"></a>Explore o código



Para invocar as diálogos apropriados, os manipuladores de atividades são definidos em `RootDispatchDialog.cs` para usar expressões regulares, a fim de corresponderem à entrada do usuário. Por exemplo, o manipulador no exemplo a seguir é disparado se o usuário diz algo como "Gostaria de lançar um dado". Os sinônimos são incluídos na expressão regular para que enunciados semelhantes dispararem o diálogo.
```cs
        [RegexPattern("(roll|role|throw|shoot).*(dice|die|dye|bones)")]
        [RegexPattern("new game")]
        [ScorableGroup(1)]
        public async Task NewGame(IDialogContext context, IActivity activity)
        {
            context.Call(new CreateGameDialog(), AfterGameCreated);
        }
```

O diálogo `CreateGameDialog` configura um jogo personalizado para o bot jogar. Ele usa um `PromptDialog` para perguntar ao usuário quantos lados ele deseja que o dado tenha e, em seguida, quantos dados devem ser lançados. Observe que o objeto `PromptOptions` usado para inicializar o prompt contém uma propriedade `speak` para a versão falada do prompt.

```cs
    [Serializable]
    public class CreateGameDialog : IDialog<GameData>
    {
        public async Task StartAsync(IDialogContext context)
        {
            context.UserData.SetValue<GameData>(Utils.GameDataKey, new GameData());

            var descriptions = new List<string>() { "4 Sides", "6 Sides", "8 Sides", "10 Sides", "12 Sides", "20 Sides" };
            var choices = new Dictionary<string, IReadOnlyList<string>>()
             {
                { "4", new List<string> { "four", "for", "4 sided", "4 sides" } },
                { "6", new List<string> { "six", "sex", "6 sided", "6 sides" } },
                { "8", new List<string> { "eight", "8 sided", "8 sides" } },
                { "10", new List<string> { "ten", "10 sided", "10 sides" } },
                { "12", new List<string> { "twelve", "12 sided", "12 sides" } },
                { "20", new List<string> { "twenty", "20 sided", "20 sides" } }
            };

            var promptOptions = new PromptOptions<string>(
                Resources.ChooseSides,
                choices: choices,
                descriptions: descriptions,
                speak: SSMLHelper.Speak(Utils.RandomPick(Resources.ChooseSidesSSML))); // spoken prompt

            PromptDialog.Choice(context, this.DiceChoiceReceivedAsync, promptOptions);
        }

        private async Task DiceChoiceReceivedAsync(IDialogContext context, IAwaitable<string> result)
        {
            GameData game;
            if (context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out game))
            {
                int sides;
                if (int.TryParse(await result, out sides))
                {
                    game.Sides = sides;
                    context.UserData.SetValue<GameData>(Utils.GameDataKey, game);
                }

                var promptText = string.Format(Resources.ChooseCount, sides);

                var promptOption = new PromptOptions<long>(promptText, choices: null, speak: SSMLHelper.Speak(Utils.RandomPick(Resources.ChooseCountSSML)));

                var prompt = new PromptDialog.PromptInt64(promptOption);
                context.Call<long>(prompt, this.DiceNumberReceivedAsync);
            }
        }

        private async Task DiceNumberReceivedAsync(IDialogContext context, IAwaitable<long> result)
        {
            GameData game;
            if (context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out game))
            {
                game.Count = await result;
                context.UserData.SetValue<GameData>(Utils.GameDataKey, game);
            }

            context.Done(game);
        }
    }
```

O `PlayGameDialog` renderiza os resultados exibindo-os em um `HeroCard` e criando uma mensagem falada para dizê-la usando o método `Speak`.

```cs
   [Serializable]
    public class PlayGameDialog : IDialog<object>
    {
        private const string RollAgainOptionValue = "roll again";

        private const string NewGameOptionValue = "new game";

        private GameData gameData;

        public PlayGameDialog(GameData gameData)
        {
            this.gameData = gameData;
        }

        public async Task StartAsync(IDialogContext context)
        {
            if (this.gameData == null)
            {
                if (!context.UserData.TryGetValue<GameData>(Utils.GameDataKey, out this.gameData))
                {
                    // User started session with "roll again" so let's just send them to
                    // the 'CreateGameDialog'
                    context.Done<object>(null);
                }
            }

            int total = 0;
            var randomGenerator = new Random();
            var rolls = new List<int>();

            // Generate Rolls
            for (int i = 0; i < this.gameData.Count; i++)
            {
                var roll = randomGenerator.Next(1, this.gameData.Sides);
                total += roll;
                rolls.Add(roll);
            }

            // Format rolls results
            var result = string.Join(" . ", rolls.ToArray());
            bool multiLine = rolls.Count > 5;

            var card = new HeroCard()
            {
                Subtitle = string.Format(
                    this.gameData.Count > 1 ? Resources.CardSubtitlePlural : Resources.CardSubtitleSingular,
                    this.gameData.Count,
                    this.gameData.Sides),
                Buttons = new List<CardAction>()
                {
                    new CardAction(ActionTypes.ImBack, "Roll Again", value: RollAgainOptionValue),
                    new CardAction(ActionTypes.ImBack, "New Game", value: NewGameOptionValue)
                }
            };

            if (multiLine)
            {
                card.Text = result;
            }
            else
            {
                card.Title = result;
            }

            var message = context.MakeMessage();
            message.Attachments = new List<Attachment>()
            {
                card.ToAttachment()
            };

            // Determine bots reaction for speech purposes
            string reaction = "normal";

            var min = this.gameData.Count;
            var max = this.gameData.Count * this.gameData.Sides;
            var score = total / max;
            if (score == 1)
            {
                reaction = "Best";
            }
            else if (score == 0)
            {
                reaction = "Worst";
            }
            else if (score <= 0.3)
            {
                reaction = "Bad";
            }
            else if (score >= 0.8)
            {
                reaction = "Good";
            }

            // Check for special craps rolls
            if (this.gameData.Type == "Craps")
            {
                switch (total)
                {
                    case 2:
                    case 3:
                    case 12:
                        reaction = "CrapsLose";
                        break;
                    case 7:
                        reaction = "CrapsSeven";
                        break;
                    case 11:
                        reaction = "CrapsEleven";
                        break;
                    default:
                        reaction = "CrapsRetry";
                        break;
                }
            }

            // Build up spoken response
            var spoken = string.Empty;
            if (this.gameData.Turns == 0)
            {
                spoken += Utils.RandomPick(Resources.ResourceManager.GetString($"Start{this.gameData.Type}GameSSML"));
            }

            spoken += Utils.RandomPick(Resources.ResourceManager.GetString($"{reaction}RollReactionSSML"));

            message.Speak = SSMLHelper.Speak(spoken);

            // Increment number of turns and store game to roll again
            this.gameData.Turns++;
            context.UserData.SetValue<GameData>(Utils.GameDataKey, this.gameData);

            // Send card and bots reaction to user.
            message.InputHint = InputHints.AcceptingInput;
            await context.PostAsync(message);

            context.Done<object>(null);
        }
    }
```
## <a name="next-steps"></a>Próximas etapas

Se o bot está em execução localmente ou foi implantado na nuvem, você pode invocá-lo por meio da Cortana. Confira [Testar uma habilidade da Cortana](../bot-service-debug-cortana-skill.md) para obter as etapas necessárias para experimentar a habilidade da Cortana.


## <a name="additional-resources"></a>Recursos adicionais
* [O Kit de Habilidades da Cortana][CortanaGetStarted]
* [Adicionar fala a mensagens](bot-builder-dotnet-text-to-speech.md)
* [Referência de SSML][SSMLRef]
* [Melhores práticas de design de voz para a Cortana][VoiceDesign]
* [Melhores práticas de design de cartão para a Cortana][CardDesign]
* [Centro de Desenvolvimento da Cortana][CortanaDevCenter]
* [Como testar e depurar as melhores práticas para a Cortana][Cortana-TestBestPractice]
* <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[CortanaGetStarted]: /cortana/getstarted
[BFPortal]: https://dev.botframework.com/

[SSMLRef]: https://aka.ms/cortana-ssml
[CortanaDevCenter]: https://developer.microsoft.com/cortana

[CortanaSpecificEntities]: https://aka.ms/cortana-channel-data
[CortanaAuth]: https://aka.ms/add-auth-cortana-skill

[InvocationNameGuidelines]: https://aka.ms/cortana-invocation-guidelines  
[VoiceDesign]: https://aka.ms/cortana-design-voice
[CardDesign]: https://aka.ms/cortana-design-card
[Cortana-Debug]: https://aka.ms/cortana-enable-debug
[Cortana-Publish]: https://aka.ms/cortana-publish


[CortanaChannel]: https://aka.ms/bot-cortana-channel
[Cortana-TestBestPractice]: https://aka.ms/cortana-test-best-practice

[heroCard]: /dotnet/api/microsoft.bot.connector.herocard

[thumbnailCard]: /dotnet/api/microsoft.bot.connector.thumbnailcard 

[receiptCard]: /dotnet/api/microsoft.bot.connector.receiptcard 

[signinCard]: /dotnet/api/microsoft.bot.connector.signincard 


