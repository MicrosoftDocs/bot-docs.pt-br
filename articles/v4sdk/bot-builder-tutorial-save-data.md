---
title: Tutorial – salve os dados de estado do usuário | Microsoft Docs
description: Saiba como salvar dados de estado do usuário no SDK do construtor de Bot.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/23/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 86f70fd66f1bc2261339cbe0590061913b51ddbc
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904434"
---
# <a name="save-user-state-data"></a>Salvar dados de estado do usuário

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Quando o bot está pedindo aos usuários informações, é provável que você queira persistir algumas das informações no armazenamento de alguma forma. O Bot Builder SDK permite que você armazene entradas do usuário usando *armazenamento na memória*, *armazenamento de arquivos*, armazenamento do banco de dados como *CosmosDB* ou *SQL* . 

Este tutorial mostrará como definir seu objeto de armazenamento na camada de middleware e como salvar o usuário no objeto de armazenamento para que ele possa persistir.

## <a name="prequisite"></a>Pré-requisitos 

Este tutorial é baseado no tutorial [ Gerenciar um fluxo de conversação com cascata ](bot-builder-tutorial-waterfall.md).

## <a name="add-storage-to-middleware-layer"></a>Adicionar armazenamento à camada de middleware


## <a name="save-user-input-to-storage"></a>Salvar a entrada do usuário para o armazenamento

Para gerenciar a conversação de reserva de tabela, você precisará definir uma caixa de diálogo **waterfall** com quatro etapas. Nesta conversa, você também usará um `DatetimePrompt` e `NumberPrompt` adicional ao `TextPrompt`.

O `reserveTable` caixa de diálogo terá esta aparência:

```javascript
// Reserve a table:
// Help the user to reserve a table

var reservationInfo = {
    dateTime: '',
    partySize: '',
    reserveName: ''
}

dialogs.add('reserveTable', [
    async function(dc, args, next){
        await dc.context.sendActivity("Welcome to the reservation service.");
        reservationInfo = {}; // Clears any previous data
        await dc.prompt('dateTimePrompt', "Please provide a reservation date and time.");
    },
    async function(dc, result){
        reservationInfo.dateTime = result[0].value;

        // Ask for next info
        await dc.prompt('partySizePrompt', "How many people are in your party?");
    },
    async function(dc, result){
        reservationInfo.partySize = result;

        // Ask for next info
        await dc.prompt('textPrompt', "Who's name will this be under?");
    },
    async function(dc, result){
        reservationInfo.reserveName = result;

        // Confirm reservation
        var msg = `Reservation confirmed. Reservation details: 
            <br/>Date/Time: ${reservationInfo.dateTime} 
            <br/>Party size: ${reservationInfo.partySize} 
            <br/>Reservation name: ${reservationInfo.reserveName}`;
        await dc.context.sendActivity(msg);
        await dc.end();
    }
]);

```

Agora você está pronto para enganchar isso na lógica do bot.

## <a name="start-the-dialog"></a>Iniciar a caixa de diálogo

Modificar seu bot `processActivity()` método para ter esta aparência:

```javascript
// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            // State will store all of your information 
            const state = conversationState.get(context);
            const dc = dialogs.createContext(context, state);

            if(context.activity.text.match(/hi/ig)){
                await dc.begin('greeting');
            }
            if(context.activity.text.match(/reserve table/ig)){
                await dc.begin('reserveTable');
            }
            else{
                // Continue executing the "current" dialog, if any.
                await dc.continue();
            }
        }
    });
});
```

No momento da execução, sempre que o usuário enviar a mensagem que contém a string `reserve table`, o bot iniciará a conversa `reserveTable`.

## <a name="next-steps"></a>Próximas etapas

??? 

> [!div class="nextstepaction"]
> [Salvar dados de estado do usuário](bot-builder-tutorial-save-data.md)
