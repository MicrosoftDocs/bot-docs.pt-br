---
title: Manter os dados de usuário | Microsoft Docs
description: Saiba como salvar dados de estado do usuário para o armazenamento no SDK do Construtor de Bot.
keywords: manter os dados de usuário, armazenamento, dados de conversa
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 539e9e1cd772495d849ce106ee7d6a157fc1a9c0
ms.sourcegitcommit: 9a38d76afb0e82fdccc1f36f9b1a65042671e538
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39515076"
---
# <a name="persist-user-data"></a>Manter os dados de usuário

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Quando o bot pede dados entrada aos usuários, é provável que você deseje manter algumas informações no armazenamento de algum formulário. O SDK do Construtor de Bot permite armazenar entradas do usuário usando *armazenamento na memória*, *armazenamento de arquivos* ou armazenamento de banco de dados, como *CosmosDB* ou *SQL*, nos quais os tipos de armazenamento local são usados principalmente para teste ou a criação de protótipos, sendo que os tipos de armazenamento posteriores são melhores para bots de produção.

Este tutorial mostrará como definir o objeto de armazenamento e salvar entradas de usuário no objeto de armazenamento, de modo que ele possa ser mantido. 

> [!NOTE]
> Independentemente do tipo de armazenamento que você optar por usar, o processo para vinculá-lo e manter os dados é o mesmo. Este tutorial usa o `FileStorage` como o armazenamento para manter os dados.
> Para obter mais informações sobre o estado e outros tipos de armazenamento, consulte [Gerenciar o estado do usuário e a conversa](bot-builder-howto-v4-state.md).

## <a name="prequisite"></a>Pré-requisitos 

Este tutorial baseia-se no tutorial [Reservar uma tabela](bot-builder-tutorial-waterfall.md). Nesse tutorial, você cria um bot que pede ao usuário três informações sobre sua reserva em seu restaurante. No entanto, a entrada do usuário não foi mantida. Este tutorial adicionará a persistência de armazenamento de dados a esse bot.

## <a name="add-storage-to-middleware-layer"></a>Adicionar armazenamento a camada de middleware

O SDK V4 do Construtor de Bot manipula o estado e o armazenamento por meio de um middleware de gerenciamento de estado. O middleware fornece uma camada de abstração que permite acessar propriedades usando um repositório de chave-valor simples, independente do tipo de armazenamento subjacente. O gerenciador de estado é responsável por gravar dados no armazenamento e gerenciar simultaneidade, caso o tipo de armazenamento subjacente esteja na memória, armazenamento de arquivos ou armazenamento de tabelas do Azure. Neste tutorial, vamos usar `FileStorage` para manter as entradas do usuário.

O provedor `FileStorage` vem com o pacote `bot-builder`. O exemplo com o qual você iniciou usa o provedor `MemoryStorage`. Que tipo de armazenamento está usando a *memória* volátil do bot, é descartada quando o bot é reiniciado. A biblioteca `FileStorage`, por outro lado, se comporta como um banco de dados. Ou seja, essa biblioteca grava as informações de armazenamento em um arquivo em seu computador local. Você pode especificar onde colocar esse arquivo de armazenamento para que você possa inspecionar mais tarde.

Para usar o `FileStorage`, localize a instrução no seu bot em que o `conversationState` do objeto é definido e atualize-o para criar um `new botbuilder.FileStorage("c:/temp")`. Além disso, você pode definir o local para onde esse arquivo de armazenamento deve ser gravado. Dessa forma, você pode localizá-lo facilmente para inspecionar o conteúdo daquilo que foi mantido.

# <a name="ctabcstab"></a>[C#](#tab/cstab)
```cs
var storage = new FileStorage("c:/temp");

// These two classes are simply Dictionaries to store state
options.Middleware.Add(new ConversationState<MyBot.convoState>(storage));
options.Middleware.Add(new UserState<MyBot.userState>(storage));
```

O SDK do Construtor de Bot fornece três objetos de estado com escopos diferentes que podem ser escolhidos.

| Estado | Escopo | DESCRIÇÃO |
| ---- | ---- | ---- |
| `dc.ActiveDialog.State` | caixa de diálogo | Estado disponível para as etapas da caixa de diálogo em cascata. |
| `ConversationState` | conversa | Estado disponível para a conversa atual. |
| `UserState` | usuário | Estado disponível entre várias conversas. |

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

**app.js**
```javascript
// Storage
const storage = new FileStorage("c:/temp");
const convoState = new ConversationState(storage);
const userState  = new UserState(storage);
adapter.use(new BotStateSet(convoState, userState));
```

O `BotStateSet` pode gerenciar o `ConversationState` e o `UserState` ao mesmo tempo. No momento de salvar os dados do usuário, você pode escolher. O SDK do Construtor de Bot fornece três objetos de estado com escopos diferentes que podem ser escolhidos.

| Estado | Escopo | DESCRIÇÃO |
| ---- | ---- | ---- |
| `dc.activeDialog.state` | caixa de diálogo | Estado disponível para as etapas da caixa de diálogo em cascata. |
| `ConversationState` | conversa | Estado disponível para a conversa atual. |
| `UserState` | usuário | Estado disponível entre várias conversas. |

---
 

## <a name="persist-state"></a>Manter o estado

Para o bot, você pode escrever para qualquer um desses locais de três estados, dependendo o que você está salvando e quanto tempo ela precisa ser mantida.  

# <a name="ctabcstab"></a>[C#](#tab/cstab)

O *middleware do gerenciador de estado* manipula o estado de gravação para você no fim de cada volta. Portanto, tudo que você precisa fazer em seu bot é atribuir os dados ao objeto de estado de sua escolha. Neste exemplo, usamos o `dc.ActiveDialog.State` para acompanhar a entrada do usuário em nossa reserva. Dessa forma, em vez de salvar o entrada do usuário em uma variável global, você pode armazená-la em um escopo de objeto de estado temporário na caixa de diálogo. Este objeto existe somente enquanto a caixa de diálogo está ativa. Se você quiser mantê-la por mais tempo, é necessário transferi-la para um dos outros objetos de estado. Nesse caso, podemos atribuir a reserva `msg` ao estado da conversa na última etapa da cascata.

```cs
dialogs.Add("reserveTable", new WaterfallStep[]
{
    async (dc, args, next) =>
    {
        // Prompt for the guest's name.
        await dc.Context.SendActivity("Welcome to the reservation service.");

        dc.ActiveDialog.State = new Dictionary<string, object>();

        await dc.Prompt("dateTimePrompt", "Please provide a reservation date and time.");
    },
    async(dc, args, next) =>
    {
        var dateTimeResult = ((DateTimeResult)args).Resolution.First();

        dc.ActiveDialog.State["date"] = Convert.ToDateTime(dateTimeResult.Value);
        
        // Ask for next info
        await dc.Prompt("partySizePrompt", "How many people are in your party?");

    },
    async(dc, args, next) =>
    {
        dc.ActiveDialog.State["partySize"] = (int)args["Value"];

        // Ask for next info
        await dc.Prompt("textPrompt", "Who's name will this be under?");
    },
    async(dc, args, next) =>
    {
        dc.ActiveDialog.State["name"] = args["Text"];
        string msg = "Reservation confirmed. Reservation details - " +
        $"\nDate/Time: {dc.ActiveDialog.State["date"].ToString()} " +
        $"\nParty size: {dc.ActiveDialog.State["partySize"].ToString()} " +
        $"\nReservation name: {dc.ActiveDialog.State["name"]}";

        var convo = ConversationState<convoState>.Get(dc.Context);

        // In production, you may want to store something more helpful
        convo[$"{dc.ActiveDialog.State["name"]} reservation"] = msg;

        await dc.Context.SendActivity(msg);
        await dc.End();
    }
});
```

# <a name="javascripttabjstab"></a>[JavaScript](#tab/jstab)

O *middleware do gerenciador de estado* manipula o estado de gravação no arquivo para você no fim de cada volta. Portanto, tudo que você precisa fazer em seu bot é atribuir os dados ao objeto de estado de sua escolha. Neste exemplo, usamos o `dc.activeDialog.state` para acompanhar a entrada do usuário em um objeto `reservervationInfo`. Dessa forma, em vez de salvar o entrada do usuário em uma variável global, você pode armazená-la em um escopo de objeto de estado temporário na caixa de diálogo. Como este objeto existe somente enquanto a caixa de diálogo está ativa, se você quiser mantê-la por mais tempo, é necessário transferi-la para um dos outros objetos de estado. Nesse caso, podemos atribuir a `reservationInfo` ao estado `convo` na última etapa da cascata.

```javascript
// Reserve a table:
// Help the user to reserve a table

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");

        dc.activeDialog.state.reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Who's name will this be under?");
    },
    async function(dc, result){
        dc.activeDialog.state.reservationInfo.reserveName = result;
        
        // Persist data
        var convo = conversationState.get(dc.context);; // conversationState.get(dc.context);
        convo.reservationInfo = dc.activeDialog.state.reservationInfo;

        // Confirm reservation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${dc.activeDialog.state.reservationInfo.dateTime} 
            <br/>Party size: ${dc.activeDialog.state.reservationInfo.partySize} 
            <br/>Reservation name: ${dc.activeDialog.state.reservationInfo.reserveName}`;
            
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);
```

---

Agora, você está pronto para conectar isso à lógica do bot.

## <a name="start-the-dialog"></a>Iniciar a caixa de diálogo

Não é necessário fazer nenhuma alteração de código aqui. Basta executar o bot e enviar a mensagem para iniciar a conversa `reserveTable`.

## <a name="check-file-storage-content"></a>Verifique o conteúdo do armazenamento de arquivo

Depois de executar o bot e verificar a conversa `reserveTable`, localize as informações salvas em um arquivo no local especificado (por exemplo: "C: temp"). O nome do arquivo é precedido com "conversa!" ou "usuário!". Ele pode ajudar a classificar os arquivos por data, para que você possa encontrar o mais recente de forma mais fácil.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como salvar as entradas do usuário, vamos dar uma olhada em que tipo de entrada você pode pedir ao usuário usando a biblioteca de prompts.

> [!div class="nextstepaction"]
> [Solicitar entrada ao usuário](~/v4sdk/bot-builder-prompts.md)
