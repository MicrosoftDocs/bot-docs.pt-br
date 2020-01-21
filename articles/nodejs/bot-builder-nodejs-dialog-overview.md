---
title: Visão geral das caixas de diálogo (JS v3) – Serviço de Bot
description: Saiba como usar os diálogos no SDK do Bot Framework para Node.js para modelar conversas e gerenciar o fluxo da conversa.
author: DucVo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: be983482273eaaa5be79f7c4100dcd4741f62469
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790918"
---
# <a name="dialogs-in-the-bot-framework-sdk-for-nodejs"></a>Diálogos no SDK do Bot Framework para Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-dialogs.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-overview.md)

Os diálogos no SDK do Bot Framework para Node.js permitem modelar conversas e gerenciar um fluxo de conversa. Um bot se comunica com um usuário por meio de uma conversa. As conversas são organizadas em diálogos. Os diálogos podem conter etapas em cascata e solicitações. À medida que o usuário interagir com o bot, o bot irá iniciar, parar e alternar entre vários diálogos em resposta às mensagens do usuário. Entender como funcionam os diálogos é fundamental para o desenvolver e criar com sucesso grandes bots. 

Este artigo apresenta os conceitos de diálogo. Depois de ler este artigo, siga os links na seção [Próximas etapas](#next-steps) para se aprofundar nesses conceitos.

## <a name="conversations-through-dialogs"></a>Conversas por diálogos

O SDK do Bot Framework para Node.js define uma conversa como a comunicação entre um bot e um usuário por meio de um ou mais diálogos. Um diálogo, no seu nível mais básico, é um módulo reutilizável que realiza uma operação ou coleta informações de um usuário. Você pode encapsular a lógica complexa do seu bot no código de diálogo reutilizável.

Uma conversa pode ser estruturada e alterada de muitas formas:

- Ela pode ser originada do seu [diálogo padrão](#default-dialog).
- Ela pode ser redirecionada de um diálogo para outro.
- Ela pode ser retomada.
- Ela pode seguir um padrão em [cascata](bot-builder-nodejs-dialog-waterfall.md), que orienta o usuário por uma série de etapas, ou [fazer](bot-builder-nodejs-dialog-prompt.md) uma série de perguntas ao usuário.
- Ela pode usar [ações](bot-builder-nodejs-dialog-actions.md) que escutam palavras ou frases que disparam um diálogo diferente. 

Pense em uma conversa como o pai dos diálogos. Dessa forma, uma conversa contém uma *pilha de diálogos* e mantém o seu próprio conjunto de dados de estado; especificamente, o `conversationData` e o `privateConversationData`. Um diálogo, por outro lado, mantém o `dialogData`. Para saber mais sobre os dados do estado em [Gerenciar dados de estado](bot-builder-nodejs-state.md).

## <a name="dialog-stack"></a>Pilha de diálogos

Um bot interage com um usuário por meio de uma série de diálogos que são mantidos na pilha de diálogos. Os diálogos são inseridos e removidos da pilha no decorrer de uma conversa. A pilha funciona como uma pilha UEPS normal; ou seja, o último diálogo adicionado será o primeiro a ser concluído. Após a conclusão de um diálogo, o controle é retornado ao diálogo anterior na pilha.

Quando uma conversa de bot for iniciada pela primeira vez ou quando ela for encerrada, a pilha de diálogos ficará vazia. Neste ponto, quando o usuário enviar uma mensagem para o bot, o bot responderá com o *diálogo padrão*.

## <a name="default-dialog"></a>Diálogo padrão

Antes da versão 3.5 do Bot Framework, um diálogo *raiz* era definido adicionando um diálogo chamado `/`, que levava a convenções de nomenclatura similares às de URLs. Essas conversões de nomenclatura não eram apropriadas para diálogos de nomenclatura. 

> [!NOTE]
> A partir da versão 3.5 do Bot Framework, o *diálogo padrão* passou a ser registrado como o segundo parâmetro no construtor de [`UniversalBot`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html#constructor).  

O snippet de código a seguir mostra como definir o diálogo padrão ao criar o objeto `UniversalBot`.

```javascript
var bot = new builder.UniversalBot(connector, [
    //...Default dialog waterfall steps...
    ]);
```

O diálogo padrão é executado sempre que a pilha de diálogos está vazia e nenhum outro diálogo é [disparado](bot-builder-nodejs-dialog-actions.md) pelo LUIS ou outro [reconhecedor](bot-builder-nodejs-recognize-intent-messages.md). Como o diálogo padrão é a primeira resposta do bot ao usuário, ele deve fornecer algumas informações de contexto ao usuário, como uma lista de comandos disponíveis ou uma visão geral do que o bot pode fazer.

## <a name="dialog-handlers"></a>Manipuladores de diálogo

O manipulador de diálogo gerencia o fluxo de uma conversa. Para percorrer uma conversa, o manipulador de diálogo direciona o fluxo iniciando e encerrando os diálogos. 

## <a name="starting-and-ending-dialogs"></a>Iniciar e encerrar diálogos

Para iniciar um novo diálogo (e enviá-lo para a pilha), use [`session.beginDialog()`](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#begindialog). Para encerrar um diálogo (e removê-lo da pilha, retornando o controle para o diálogo de chamada), use [`session.endDialog()`](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#enddialog) ou [`session.endDialogWithResult()`](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#enddialogwithresult). 

## <a name="using-waterfalls-and-prompts"></a>Usando cascatas e prompts

A [Cascata](bot-builder-nodejs-dialog-waterfall.md) é uma maneira simples de modelar e gerenciar o fluxo da conversa. Uma cascata contém uma sequência de etapas. Em cada etapa, você pode concluir uma ação em nome do usuário ou [solicitar](bot-builder-nodejs-dialog-prompt.md) informações ao usuário.

Uma cascata é implementada usando um diálogo composto por um conjunto de funções. Cada função define uma etapa da cascata. O exemplo de código a seguir mostra uma conversa simples que usa uma cascata com duas etapas para solicitar o nome do usuário e saudá-lo pelo nome.

```javascript
// Ask the user for their name and greet them by name.
bot.dialog('greetings', [
    function (session) {
        builder.Prompts.text(session, 'Hi! What is your name?');
    },
    function (session, results) {
        session.endDialog(`Hello ${results.response}!`);
    }
]);
```

Quando um bot chegar ao fim da cascata sem encerrar o diálogo, a próxima mensagem do usuário reiniciará esse diálogo da primeira etapa da cascata. Isso pode gerar frustração, dando aos usuários a sensação de que eles estão presos em um loop. Para evitar esta situação, quando uma conversa ou diálogo chegar ao fim, a melhor prática é chamar explicitamente `endDialog`, `endDialogWithResult` ou `endConversation`.

## <a name="next-steps"></a>Próximas etapas

Para se aprofundar nos diálogos, é importante entender como o padrão de cascata funciona e como usá-lo para guiar os usuários por um processo.

> [!div class="nextstepaction"]
> [Definir etapas de conversa com cascatas](bot-builder-nodejs-dialog-waterfall.md)
