---
title: Migrar um bot JavaScript v3 existente para um novo projeto v4 – Serviço de Bot
description: Pegamos um bot JavaScript v3 existente e o migramos para o SDK V4 usando um novo projeto.
keywords: JavaScript, migração de bot, diálogos, bot v3
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 95912fe2d490abf405b54c994002a65493bf4f33
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416881"
---
# <a name="migrate-a-javascript-v3-bot-to-a-v4-bot"></a>Migrar um bot v3 do Javascript para um bot v4

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Neste artigo, portaremos o bot [core-MultiDialogs-v3](https://aka.ms/v3-js-core-multidialog-migration-sample) JavaScript do SDK v3 para um novo bot JavaScript v4.
Essa conversão é dividida entre estes estágios:

1. Criar o novo projeto e adicionar dependências.
1. Atualizar o ponto de entrada e definir constantes.
1. Criar os diálogos, reimplementando-os com o SDK v4.
1. Atualizar o código do bot para executar os diálogos.
1. Portar o arquivo utilitário **store.js**.

No final deste processo, teremos um bot v4 funcionando. Uma cópia do bot convertido também está no repositório de exemplos, [core-MultiDialogs-v4](https://aka.ms/v4-js-core-multidialog-migration-sample).

O SDK v4 do Bot Framework é baseado na mesma API REST subjacente que o SDK v3. No entanto, a v4 do SDK é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle aos desenvolvedores sobre seus bots. Alterações importantes no SDK incluem:

- O estado é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade.
- Mudamos nossa maneira de lidar com turnos, ou seja, como o bot recebe e responde uma atividade de entrada do canal do usuário.
- A v4 não usa um objeto `session`. Em vez disso, ela tem um objeto _turn context_ que contém informações sobre a atividade de entrada e pode ser usado para enviar de volta uma atividade de resposta.
- Uma nova biblioteca de diálogos que é muito diferente daquela da v3. Você precisará converter os diálogos antigos no novo sistema de diálogo, usando os diálogos do componente e em cascata.

<!-- TODO
For more information about specific changes, see [differences between the v3 and v4 JavaScript SDK](???.md).
-->

> [!NOTE]
> Como parte da migração, também limpamos alguns dos códigos, mas apenas destacaremos as alterações feitas na lógica da v3 como parte do processo de migração.

## <a name="prerequisites"></a>Pré-requisitos

- Node.js
- Visual Studio Code
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)

## <a name="about-this-bot"></a>Sobre este bot

O bot que estamos migrando demonstra o uso de vários diálogos para gerenciar o fluxo da conversa. O bot pode pesquisar informações de voo ou hotel.

- O diálogo principal pergunta ao usuário o tipo de informação que está procurando.
- O diálogo de hotel solicita ao usuário parâmetros de pesquisa e, em seguida, executa uma pesquisa fictícia.
- O diálogo de voo gera um erro que o bot captura e trata normalmente.

## <a name="create-and-open-a-new-v4-bot-project"></a>Criar e abrir um novo projeto de bot v4

1. Você precisará de um projeto da v4 para portar o código do bot. Para criar um bot localmente, confira [Criar um bot com o SDK para JavaScript do Bot Framework](../../javascript/bot-builder-javascript-quickstart.md).

    > [!TIP]
    > Você também pode criar um projeto no Azure, confira [Criar um bot com o Serviço de Bot do Azure](../../bot-service-quickstart.md).
    > No entanto, esses dois métodos resultam em uma pequena diferença nos arquivos de suporte. O projeto da v4 deste artigo foi criado como um projeto local.

1. Em seguida, abra o projeto no Visual Studio Code.

## <a name="update-the-packagejson-file"></a>Atualizar o arquivo package.json

1. Adicione uma dependência do pacote **botbuilder-dialogs** inserindo `npm i botbuilder-dialogs` na janela de terminal do Visual Studio Code.

1. Edite **./package.json** e atualize `name`, `version`, `description` e outras propriedades conforme desejar.

## <a name="update-the-v4-app-entry-point"></a>Atualizar o ponto de entrada de aplicativo da v4

O modelo da v4 cria um arquivo **index.js** para o ponto de entrada de aplicativo e um arquivo **bot.js** para a lógica específica do bot. Nas etapas posteriores, renomearemos o arquivo **bot.js** para **bots/reservationBot.js** e adicionaremos uma classe para cada diálogo.

Edite **./index.js**, que é o ponto de entrada do nosso aplicativo bot. Ele conterá partes do arquivo **app.js** da v3 que configura o servidor HTTP.

1. Além de `BotFrameworkAdapter`, importe `MemoryStorage` e `ConversationState` do pacote **botbuilder**. Importe também o bot e os módulos do diálogo principal. (Criaremos eles logo, mas precisamos citá-los aqui.)

    ```javascript
    // Import required bot services.
    // See https://aka.ms/bot-services to learn more about the different parts of a bot.
    const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');

    // This bot's main dialog.
    const { MainDialog } = require('./dialogs/main')
    const { ReservationBot } = require('./bots/reservationBot');
    ```

1. Defina um manipulador `onTurnError` para o adaptador.

    ```javascript
    // Catch-all for errors.
    adapter.onTurnError = async (context, error) => {
        const errorMsg = error.message ? error.message : `Oops. Something went wrong!`;
        // This check writes out errors to console log .vs. app insights.
        console.error(`\n [onTurnError]: ${ error }`);
        // Clear out state
        await conversationState.delete(context);
        // Send a message to the user
        await context.sendActivity(errorMsg);
    };
    ```

    Na v4, usamos um _adaptador de bot_ para rotear as atividades de entrada até o bot. O adaptador nos permite capturar e responder aos erros antes de concluir um turno. Aqui, limpamos o estado da conversa, se ocorrer um erro de aplicativo, o que redefinirá todos os diálogos e impedirá o bot de permanecer em um estado corrompido de conversa.

1. Substitua o código de modelo de criação do bot por esse.

    ```javascript
    // Define state store for your bot.
    const memoryStorage = new MemoryStorage();

    // Create conversation state with in-memory storage provider.
    const conversationState = new ConversationState(memoryStorage);

    // Create the base dialog and bot
    const dialog = new MainDialog();
    const reservationBot = new ReservationBot(conversationState, dialog);
    ```

    A camada de armazenamento na memória agora é fornecida pela classe `MemoryStorage` e podemos criar explicitamente um objeto de gerenciamento de estado da conversa.

    O código de definição do diálogo foi movido para uma classe `MainDialog`, que definiremos daqui a pouco. Também migraremos o código de definição do bot para uma classe `ReservationBot`.

1. Por fim, atualizaremos o manipulador de solicitação do servidor para usar o adaptador nas atividades de roteamento para o bot.

    ```javascript
    // Listen for incoming requests.
    server.post('/api/messages', (req, res) => {
        adapter.processActivity(req, res, async (context) => {
            // Route incoming activities to the bot.
            await reservationBot.run(context);
        });
    });
    ```

    Na v4, nosso bot deriva de `ActivityHandler`, que define o método `run` para receber uma atividade para um turno.

## <a name="add-a-constants-file"></a>Adicionar um arquivo de constantes

Crie um arquivo **./const.js** para armazenar identificadores do nosso bot.

```javascript
module.exports = {
    MAIN_DIALOG: 'mainDialog',
    INITIAL_PROMPT: 'initialPrompt',
    HOTELS_DIALOG: 'hotelsDialog',
    INITIAL_HOTEL_PROMPT: 'initialHotelPrompt',
    CHECKIN_DATETIME_PROMPT: 'checkinTimePrompt',
    HOW_MANY_NIGHTS_PROMPT: 'howManyNightsPrompt',
    FLIGHTS_DIALOG: 'flightsDialog',
};
```

Na v4, são atribuídas IDs ao diálogo e aos objetos de prompt e os diálogos e prompts são invocados pela ID.

## <a name="create-new-dialog-files"></a>Criar novos arquivos de diálogo

Crie estes arquivos:

| Nome do arquivo | Descrição |
|:---|:---|
| **./dialogs/flights.js** | Ele conterá a lógica migrada do diálogo `hotels`. |
| **./dialogs/hotels.js** | Ele conterá a lógica migrada do diálogo `flights`. |
| **./dialogs/main.js** | Ele conterá a lógica migrada do nosso bot e substituirá o diálogo _raiz_. |

Não migramos o diálogo de suporte. Para obter um exemplo de como implementar um diálogo de ajuda na v4, confira [Manipular interrupções do usuário](../bot-builder-howto-handle-user-interrupt.md?tabs=javascript).

### <a name="implement-the-main-dialog"></a>Implementar o diálogo principal

Na v3, todos os bots eram criados sobre um sistema de diálogos. Na v4, a lógica do bot e a lógica do diálogo agora são separadas. Pegamos o _diálogo raiz_ no bot da v3 e criamos uma classe `MainDialog` para substituí-lo.

Edite **./dialogs/main.js**.

1. Importe as classes e constantes que precisamos para o diálogo.

    ```javascript
    const { DialogSet, DialogTurnStatus, ComponentDialog, WaterfallDialog,
        ChoicePrompt } = require('botbuilder-dialogs');
    const { FlightDialog } = require('./flights');
    const { HotelsDialog } = require('./hotels');
    const { MAIN_DIALOG,
        INITIAL_PROMPT,
        HOTELS_DIALOG,
        FLIGHTS_DIALOG
    } = require('../const');
    ```

1. Defina e exporte a classe `MainDialog`.

    ```javascript
    const initialId = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor() {
            super(MAIN_DIALOG);

            // Create a dialog set for the bot. It requires a DialogState accessor, with which
            // to retrieve the dialog state from the turn context.
            this.addDialog(new ChoicePrompt(INITIAL_PROMPT, this.validateNumberOfAttempts.bind(this)));
            this.addDialog(new FlightDialog(FLIGHTS_DIALOG));

            // Define the steps of the base waterfall dialog and add it to the set.
            this.addDialog(new WaterfallDialog(initialId, [
                this.promptForBaseChoice.bind(this),
                this.respondToBaseChoice.bind(this)
            ]));

            // Define the steps of the hotels waterfall dialog and add it to the set.
            this.addDialog(new HotelsDialog(HOTELS_DIALOG));

            this.initialDialogId = initialId;
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

    Ela declara os outros diálogos e prompts aos quais o diálogo principal faz referência direta.

    - O diálogo em cascata principal que contém as etapas deste diálogo. Quando o diálogo de componente é iniciado, ele começa seu _diálogo inicial_.
    - O prompt de escolha que usaremos para perguntar ao usuário qual tarefa ele deseja executar. Criamos o prompt de escolha com um validador.
    - Os dois diálogos filho, voos e hotéis.

1. Adicione um método `run` auxiliar à classe.

    ```javascript
    /**
     * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
     * If no dialog is active, it will start the default dialog.
     * @param {*} turnContext
     * @param {*} accessor
     */
    async run(turnContext, accessor) {
        const dialogSet = new DialogSet(accessor);
        dialogSet.add(this);

        const dialogContext = await dialogSet.createContext(turnContext);
        const results = await dialogContext.continueDialog();
        if (results.status === DialogTurnStatus.empty) {
            await dialogContext.beginDialog(this.id);
        }
    }
    ```

    Na v4, um bot interage com o sistema de diálogos criando um contexto de caixa de diálogo primeiro, e, em seguida, chamando `continueDialog`. Se houver um diálogo ativo, o controle será passado a ele. Caso contrário, essa chamada simplesmente retornará. Um resultado de `empty` indica que nenhum diálogo estava ativo e, portanto, aqui, iniciamos o diálogo principal novamente.

    Para a propriedade de estado do diálogo, o parâmetro `accessor` passa no acessador. Estado da _pilha do diálogo_ é armazenado nessa propriedade. Para obter mais informações sobre como o estado e os diálogos funcionam na v4, confira [Gerenciamento de estado](../bot-builder-concept-state.md) e [Biblioteca de diálogos](../bot-builder-concept-dialog.md), respectivamente.

1. Para a classe, adicione as etapas de cascata do diálogo principal e o validador do prompt de escolha.

    ```javascript
    async promptForBaseChoice(stepContext) {
        return await stepContext.prompt(
            INITIAL_PROMPT, {
                prompt: 'Are you looking for a flight or a hotel?',
                choices: ['Hotel', 'Flight'],
                retryPrompt: 'Not a valid option'
            }
        );
    }

    async respondToBaseChoice(stepContext) {
        // Retrieve the user input.
        const answer = stepContext.result.value;
        if (!answer) {
            // exhausted attempts and no selection, start over
            await stepContext.context.sendActivity('Not a valid option. We\'ll restart the dialog ' +
                'so you can try again!');
            return await stepContext.endDialog();
        }
        if (answer === 'Hotel') {
            return await stepContext.beginDialog(HOTELS_DIALOG);
        }
        if (answer === 'Flight') {
            return await stepContext.beginDialog(FLIGHTS_DIALOG);
        }
        return await stepContext.endDialog();
    }

    async validateNumberOfAttempts(promptContext) {
        if (promptContext.attemptCount > 3) {
            // cancel everything
            await promptContext.context.sendActivity('Oops! Too many attempts :( But don\'t worry, I\'m ' +
                'handling that exception and you can try again!');
            return await promptContext.context.endDialog();
        }

        if (!promptContext.recognized.succeeded) {
            await promptContext.context.sendActivity(promptContext.options.retryPrompt);
            return false;
        }
        return true;
    }
    ```

    A primeira etapa da cascata pede para o usuário fazer uma escolha, iniciando o prompt de escolha, que também é um diálogo. A segunda etapa da cascata consome o resultado do prompt de escolha. Ela inicia um diálogo filho (se uma escolha foi criada) ou encerra o diálogo principal (se o usuário não fez uma escolha).

    O prompt de escolha retornará a escolha do usuário, se ele fizer uma opção válida, ou solicitará novamente que o usuário faça a escolha. O validador verifica o número de vezes seguidas que o aviso foi feito para o usuário e permite que o prompt falhe após 3 tentativas sem êxito, devolvendo o controle ao diálogo em cascata principal.

### <a name="implement-the-flights-dialog"></a>Implementar o diálogo de voos

No bot da v3, o diálogo de voos era um stub que demonstrava como o bot trata um erro de conversa. Aqui, fazemos o mesmo.

Edite **./dialogs/flights.js**.

```javascript
const { ComponentDialog, WaterfallDialog } = require('botbuilder-dialogs');

const initialId = 'flightsWaterfallDialog';

class FlightDialog extends ComponentDialog {
    constructor(id) {
        super(id);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = initialId;

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(initialId, [
            async () => {
                throw new Error('Flights Dialog is not implemented and is instead ' +
                    'being used to show Bot error handling');
            }
        ]));
    }
}

exports.FlightDialog = FlightDialog;
```

### <a name="implement-the-hotels-dialog"></a>Implementar o diálogo de hotéis

Mantemos o mesmo fluxo geral do diálogo de hotel: solicitar um destino, uma data, o número de diárias e, em seguida, mostrar ao usuário uma lista de opções que corresponde à pesquisa.

Edite **./dialogs/hotels.js**.

1. Importe as classes e constantes que precisaremos para o diálogo.

    ```javascript
    const { ComponentDialog, WaterfallDialog, TextPrompt, DateTimePrompt } = require('botbuilder-dialogs');
    const { AttachmentLayoutTypes, CardFactory } = require('botbuilder');
    const store = require('../store');
    const {
        INITIAL_HOTEL_PROMPT,
        CHECKIN_DATETIME_PROMPT,
        HOW_MANY_NIGHTS_PROMPT
    } = require('../const');
    ```

1. Defina e exporte a classe `HotelsDialog`.

    ```javascript
    const initialId = 'hotelsWaterfallDialog';

    class HotelsDialog extends ComponentDialog {
        constructor(id) {
            super(id);

            // ID of the child dialog that should be started anytime the component is started.
            this.initialDialogId = initialId;

            // Register dialogs
            this.addDialog(new TextPrompt(INITIAL_HOTEL_PROMPT));
            this.addDialog(new DateTimePrompt(CHECKIN_DATETIME_PROMPT));
            this.addDialog(new TextPrompt(HOW_MANY_NIGHTS_PROMPT));

            // Define the conversation flow using a waterfall model.
            this.addDialog(new WaterfallDialog(initialId, [
                this.destinationPromptStep.bind(this),
                this.destinationSearchStep.bind(this),
                this.checkinPromptStep.bind(this),
                this.checkinTimeSetStep.bind(this),
                this.stayDurationPromptStep.bind(this),
                this.stayDurationSetStep.bind(this),
                this.hotelSearchStep.bind(this)
            ]));
        }
    }

    exports.HotelsDialog = HotelsDialog;
    ```

1. Para a classe, adicione duas funções auxiliares que usaremos nas etapas do diálogo.

    ```javascript
    addDays(startDate, days) {
        const date = new Date(startDate);
        date.setDate(date.getDate() + days);
        return date;
    };

    createHotelHeroCard(hotel) {
        return CardFactory.heroCard(
            hotel.name,
            `${hotel.rating} stars. ${hotel.numberOfReviews} reviews. From ${hotel.priceStarting} per night.`,
            CardFactory.images([hotel.image]),
            CardFactory.actions([
                {
                    type: 'openUrl',
                    title: 'More details',
                    value: `https://www.bing.com/search?q=hotels+in+${encodeURIComponent(hotel.location)}`
                }
            ])
        );
    }
    ```

    O `createHotelHeroCard` cria um cartão Hero com informações sobre um hotel.

1. Para a classe, adicione as etapas de cascata usadas no diálogo.

    ```javascript
    async destinationPromptStep(stepContext) {
        await stepContext.context.sendActivity('Welcome to the Hotels finder!');
        return await stepContext.prompt(
            INITIAL_HOTEL_PROMPT, {
                prompt: 'Please enter your destination'
            }
        );
    }

    async destinationSearchStep(stepContext) {
        const destination = stepContext.result;
        stepContext.values.destination = destination;
        await stepContext.context.sendActivity(`Looking for hotels in ${destination}`);
        return stepContext.next();
    }

    async checkinPromptStep(stepContext) {
        return await stepContext.prompt(
            CHECKIN_DATETIME_PROMPT, {
                prompt: 'When do you want to check in?'
            }
        );
    }

    async checkinTimeSetStep(stepContext) {
        const checkinTime = stepContext.result[0].value;
        stepContext.values.checkinTime = checkinTime;
        return stepContext.next();
    }

    async stayDurationPromptStep(stepContext) {
        return await stepContext.prompt(
            HOW_MANY_NIGHTS_PROMPT, {
                prompt: 'How many nights do you want to stay?'
            }
        );
    }

    async stayDurationSetStep(stepContext) {
        const numberOfNights = stepContext.result;
        stepContext.values.numberOfNights = parseInt(numberOfNights);
        return stepContext.next();
    }

    async hotelSearchStep(stepContext) {
        const destination = stepContext.values.destination;
        const checkIn = new Date(stepContext.values.checkinTime);
        const checkOut = this.addDays(checkIn, stepContext.values.numberOfNights);

        await stepContext.context.sendActivity(`Ok. Searching for Hotels in ${destination} from 
            ${checkIn.toDateString()} to ${checkOut.toDateString()}...`);
        const hotels = await store.searchHotels(destination, checkIn, checkOut);
        await stepContext.context.sendActivity(`I found in total ${hotels.length} hotels for your dates:`);

        const hotelHeroCards = hotels.map(this.createHotelHeroCard);

        await stepContext.context.sendActivity({
            attachments: hotelHeroCards,
            attachmentLayout: AttachmentLayoutTypes.Carousel
        });

        return await stepContext.endDialog();
    }
    ```

    Migramos as etapas do diálogo de hotéis da v3 para as etapas de cascata do nosso diálogo de hotéis da v4.

## <a name="update-the-bot"></a>Atualizar o bot

Na v4, os bots podem reagir a atividades fora do sistema de diálogos. A classe `ActivityHandler` define manipuladores de tipos de atividades comuns, para facilitar o gerenciamento do código.

Renomeie **./bot.js** para **./bots/reservationBot.js** e edite-o.

1. O arquivo já importa o `ActivityHandler`, que fornece uma implementação básica de um bot.

    ```javascript
    const { ActivityHandler } = require('botbuilder');
    ```

1. Renomeie a classe para `ReservationBot`.

    ```javascript
    class ReservationBot extends ActivityHandler {
        // ...
    }

    module.exports.ReservationBot = ReservationBot;
    ```

1. Atualize a assinatura do construtor, para aceitar os objetos que estamos recebendo.

    ```javascript
    /**
     *
     * @param {ConversationState} conversationState
     * @param {Dialog} dialog
     * @param {any} logger object for logging events, defaults to console if none is provided
    */
    constructor(conversationState, dialog, logger) {
        super();
        // ...
    }
    ```

1. No construtor, adicione verificações de parâmetro nulo e defina propriedades do construtor de classe.

    ```javascript
    if (!conversationState) throw new Error('[DialogBot]: Missing parameter. conversationState is required');
    if (!dialog) throw new Error('[DialogBot]: Missing parameter. dialog is required');
    if (!logger) {
        logger = console;
        logger.log('[DialogBot]: logger not passed in, defaulting to console');
    }

    this.conversationState = conversationState;
    this.dialog = dialog;
    this.logger = logger;
    this.dialogState = this.conversationState.createProperty('DialogState');
    ```

    É onde criamos o acessador de propriedade de estado do diálogo, que armazenará o estado da pilha de diálogo.

1. No construtor, atualize o manipulador `onMessage` e adicione um manipulador `onDialog`.

    ```javascript
    this.onMessage(async (context, next) => {
        this.logger.log('Running dialog with Message Activity.');

        // Run the Dialog with the new message Activity.
        await this.dialog.run(context, this.dialogState);

        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });

    this.onDialog(async (context, next) => {
        // Save any state changes. The load happened during the execution of the Dialog.
        await this.conversationState.saveChanges(context, false);

        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });
    ```

    O `ActivityHandler` roteia atividades de mensagem até `onMessage`. Esse bot processa todas as entradas do usuário via diálogos.

    O `ActivityHandler` chama `onDialog` no final do turno, antes de devolver o controle ao adaptador. Precisamos salvar explicitamente o estado, antes da saída do turno. Caso contrário, as alterações de estado não serão salvas e o diálogo não será executado corretamente.

1. Por fim, atualize o manipulador `onMembersAdded` no construtor.

    ```javascript
    this.onMembersAdded(async (context, next) => {
        const membersAdded = context.activity.membersAdded;
        for (let cnt = 0; cnt < membersAdded.length; ++cnt) {
            if (membersAdded[cnt].id !== context.activity.recipient.id) {
                await context.sendActivity('Hello and welcome to Contoso help desk bot.');
            }
        }
        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });
    ```

    O `ActivityHandler` chama `onMembersAdded` quando recebe uma atividade de atualização de conversa que indica que outros participantes além do bot foram adicionados à conversa. Atualizamos esse método para enviar uma mensagem de saudação quando um usuário ingressa na conversa.

## <a name="create-the-store-file"></a>Criar o arquivo de armazenamento

Criar o arquivo **./store.js**, usado pelo diálogo de hotéis. `searchHotels` é uma função de pesquisa de hotéis fictícia, igual àquela do bot da v3.

```javascript
module.exports = {
    searchHotels: destination => {
        return new Promise(resolve => {

            // Filling the hotels results manually just for demo purposes
            const hotels = [];
            for (let i = 1; i <= 5; i++) {
                hotels.push({
                    name: `${destination} Hotel ${i}`,
                    location: destination,
                    rating: Math.ceil(Math.random() * 5),
                    numberOfReviews: Math.floor(Math.random() * 5000) + 1,
                    priceStarting: Math.floor(Math.random() * 450) + 80,
                    image: `https://placeholdit.imgix.net/~text?txtsize=35&txt=Hotel${i}&w=500&h=260`
                });
            }

            hotels.sort((a, b) => a.priceStarting - b.priceStarting);

            // complete promise with a timer to simulate async response
            setTimeout(() => { resolve(hotels); }, 1000);
        });
    }
};
```

## <a name="test-the-bot-in-the-emulator"></a>Testar o bot no emulador

Neste ponto, devemos ser capazes de executar o bot localmente e anexá-lo ao emulador.

1. Execute o exemplo localmente em seu computador.
    Se você iniciar uma sessão de depuração no Visual Studio Code, as informações de log serão enviadas ao console de depuração enquanto você testa o bot.
1. Inicie o emulador e conecte ao bot.
1. Envie mensagens para testar os diálogos principal, de voo e de hotéis.

## <a name="additional-resources"></a>Recursos adicionais

Tópicos conceituais da v4:

- [Como funcionam os bots](../bot-builder-basics.md)
- [Gerenciar estado](../bot-builder-concept-state.md)
- [Biblioteca de caixas de diálogo](../bot-builder-concept-dialog.md)

Tópicos de instruções da v4:

- [Enviar e receber mensagens de texto](../bot-builder-howto-send-messages.md)
- [Salvar dados de usuário e de conversa](../bot-builder-howto-v4-state.md)
- [Implementar o fluxo de conversa sequencial](../bot-builder-dialog-manage-conversation-flow.md)
- [Depurar com o emulador](../../bot-service-debug-emulator.md)
- [Adicionar telemetria ao seu bot](../bot-builder-telemetry.md)
