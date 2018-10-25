---
title: Visão geral dos diálogos | Microsoft Docs
description: Saiba como usar os diálogos no SDK do Construtor de Bot para .NET para modelar conversas e gerenciar o fluxo da conversa.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 943b206e4991c52f22928d2113977249ff9d9e04
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997573"
---
# <a name="dialogs-in-the-bot-builder-sdk-for-net"></a>Diálogos no SDK do Construtor de Bot para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-dialogs.md)
> - [Node.js](../nodejs/bot-builder-nodejs-dialog-overview.md)

Quando você cria um bot usando o SDK do Construtor de Bot para .NET, é possível usar os diálogos para modelar uma conversa e gerenciar um [fluxo de conversa](../bot-service-design-conversation-flow.md). Cada diálogo é uma abstração que encapsula seu próprio estado em uma classe C# que implementa `IDialog`. Um diálogo pode ser composto por outros diálogos para maximizar a reutilização e o contexto de um diálogo mantém a [pilha de diálogos](../bot-service-design-conversation-flow.md#dialog-stack) que estão ativos na conversa em qualquer momento específico. 

Uma conversa composta por diálogos pode ser transportada entre vários computadores, permitindo dimensionar a implementação do bot. Quando você usa diálogos no SDK do Construtor de Bot para .NET, o estado da conversa (a pilha de diálogos e o estado de cada diálogo na pilha) é automaticamente armazenado na opção escolhida do armazenamento de [dados de estado](bot-builder-dotnet-state.md). Isso permite que o código de serviço do bot não tenha um estado, muito semelhante a um aplicativo Web que não precisa armazenar o estado da sessão na memória do servidor Web. 

## <a name="echo-bot-example"></a>Exemplo do echo bot

Considere este exemplo de echo bot que descreve como alterar o bot criado no tutorial do [Início Rápido](bot-builder-dotnet-quickstart.md) para que ele use diálogos para trocar mensagens com o usuário.

> [!TIP]
> Para acompanhar este exemplo, use as instruções no tutorial do [Início Rápido](bot-builder-dotnet-quickstart.md) para criar um bot e, em seguida, atualize seu arquivo **MessagesController.cs** conforme descrito abaixo.

### <a name="messagescontrollercs"></a>MessagesController.cs 

No SDK do Construtor de Bot para .NET, a biblioteca do [Construtor][builderLibrary] permite implementar diálogos. Para acessar as classes relevantes, importe o namespace `Dialogs`.

[!code-csharp[Using statement](../includes/code/dotnet-dialogs.cs#usingStatement)]

Em seguida, adicione essa classe `EchoDialog` ao arquivo **MessagesController.cs** para representar a conversa. 

[!code-csharp[EchoDialog class](../includes/code/dotnet-dialogs.cs#echobot1)]

Em seguida, conecte a classe `EchoDialog` ao método `Post` chamando o método `Conversation.SendAsync`.

[!code-csharp[Post method](../includes/code/dotnet-dialogs.cs#echobot2)]

### <a name="implementation-details"></a>Detalhes de implementação 

O método `Post` está marcado como `async` porque o Construtor de Bot usa os recursos do C# para tratar da comunicação assíncrona. Ele retorna um objeto `Task` que representa a tarefa responsável pelo envio de respostas à mensagem passada. Se não houver uma exceção, o `Task` que é retornado pelo método conterá as informações da exceção. 

O método `Conversation.SendAsync` é a chave para implementar diálogos com o SDK do Construtor de Bot para .NET. Ele segue o <a href="https://en.wikipedia.org/wiki/Dependency_inversion_principle" target="_blank">princípio da inversão de dependências</a> e executa as seguintes etapas:

1. Instancia os componentes necessários  
2. Desserializa o estado da conversa (a pilha de diálogos e o estado de cada diálogo na pilha) de `IBotDataStore`
3. Retoma o processo de conversa a partir do ponto onde o bot o suspendeu e aguarda uma mensagem
4. Envia as respostas
5. Serializa o estado da conversa atualizada e salva-o novamente em `IBotDataStore`

Quando a conversa é iniciada pela primeira vez, o diálogo não tem um estado, assim `Conversation.SendAsync` cria `EchoDialog` e chama seu método `StartAsync`. O método `StartAsync` chama `IDialogContext.Wait` com o representante de continuação para especificar o método que deverá ser chamado quando uma nova mensagem for recebida (`MessageReceivedAsync`). 

O método `MessageReceivedAsync` aguarda uma mensagem, posta uma resposta e aguarda pela próxima mensagem. Sempre que `IDialogContext.Wait` é chamado, o bot entra em um estado suspenso e pode ser reiniciado em qualquer computador que recebe a mensagem. 

Um bot criado usando os exemplos de código acima responderá a cada mensagem que o usuário envia simplesmente ecoando a mensagem do usuário prefixada com o texto "Você disse:". Como o bot é criado usando diálogos, ele pode evoluir para oferecer suporte a conversas mais complexas sem a necessidade de gerenciamento explícito do estado.

## <a name="echo-bot-with-state-example"></a>Exemplo de echo bot com estado

Este próximo exemplo é baseado no exemplo acima porém, contém a capacidade de rastrear o estado do diálogo. Quando a classe `EchoDialog` é atualizada, como mostrado no exemplo de código abaixo, o bot responderá a cada mensagem que o usuário envia ecoando a mensagem do usuário prefixada com um número (`count`) seguido pelo texto 'Você disse: '. O bot continuará a incrementar `count` a cada resposta até que o usuário opte por redefinir a contagem.

### <a name="messagescontrollercs"></a>MessagesController.cs 

[!code-csharp[EchoDialog class](../includes/code/dotnet-dialogs.cs#echobot3)]

### <a name="implementation-details"></a>Detalhes de implementação

Como no primeiro exemplo, o método `MessageReceivedAsync` é chamado quando uma nova mensagem é recebida. No entanto, desta vez, o método `MessageReceivedAsync` avalia a mensagem do usuário antes de responder. Se a mensagem do usuário for "redefinir", o prompt interno `PromptDialog.Confirm` gerará um subdiálogo que solicitará ao usuário a confirmação da redefinição da contagem. O subdiálogo possui seu próprio estado particular que não interfere no estado do diálogo pai. Quando o usuário responde à solicitação, o resultado do subdiálogo é passado para o método `AfterResetAsync`, que envia uma mensagem para o usuário para indicar se a contagem foi redefinida ou não e, em seguida, chama `IDialogContext.Wait` com uma continuação de volta para `MessageReceivedAsync` na próxima mensagem.

## <a name="dialog-context"></a>Contexto do diálogo

A interface `IDialogContext` que é passada para cada método de diálogo fornece acesso aos serviços exigidos pelo diálogo para salvar o estado e se comunicar com o canal. A interface `IDialogContext` é formada por três interfaces: [Internals.IBotData][iBotData], [Internals.IBotToUser][iBotToUser] e [Internals.IDialogStack][iDialogStack]. 

### <a name="internalsibotdata"></a>Internals.IBotData

A interface `Internals.IBotData` fornece acesso aos dados de estado por usuário, por conversa e por conversa privada que são mantidos pelo Connector. Os dados de estado por usuário são úteis para armazenar dados do usuário que não estão relacionados a uma conversa específica. Já os dados por conversa são úteis para armazenar dados gerais sobre uma conversa e os dados de conversas privadas são úteis para armazenar dados do usuário relacionados a uma conversa específica. 

### <a name="internalsibottouser"></a>Internals.IBotToUser

`Internals.IBotToUser` fornece métodos para enviar uma mensagem do bot para o usuário. As mensagens podem ser enviadas de acordo com a resposta à chamada do método da API da Web ou diretamente, usando o [cliente do Connector](bot-builder-dotnet-connector.md#create-a-connector-client). Enviar e receber mensagens pelo contexto do diálogo garante que o estado `Internals.IBotData` passe pelo Connector.

### <a name="internalsidialogstack"></a>Internals.IDialogStack

`Internals.IDialogStack` fornece métodos para gerenciar a [pilha de diálogos](../bot-service-design-conversation-flow.md#dialog-stack). Na maioria das vezes, a pilha de diálogos será automaticamente gerenciada por você. No entanto, poderá haver casos em que você desejará gerenciar explicitamente a pilha. Por exemplo, você pode querer chamar um diálogo filho e adicioná-lo à parte superior da pilha de diálogos, marcar o diálogo atual como concluído (removendo-o da pilha de diálogos e retornando o resultado no diálogo anterior da pilha), suspender o diálogo atual até que uma mensagem do usuário chegue ou até redefinir totalmente a pilha de diálogos.

## <a name="serialization"></a>Serialização

A pilha de diálogos e o estado de todos os diálogos ativos são serializados para [IBotDataBag][iBotDataBag] por usuário, por conversa. O blob serializado é mantido nas mensagens que o bot envia e recebe do [Connector](bot-builder-dotnet-concepts.md#connector). Para ser serializada, uma classe `Dialog` deverá incluir o atributo `[Serializable]`. Todas as implementações `IDialog` na biblioteca do [Construtor][builderLibrary] estão marcadas como serializáveis. 

Os [métodos de Cadeia](#dialog-chains) oferecem uma interface fluente para diálogos que podem ser usados na sintaxe da consulta LINQ. O formulário compilado da sintaxe da consulta LINQ geralmente usa métodos anônimos. Se esses métodos anônimos não referenciarem o ambiente de variáveis locais, eles não terão estado e serão serializáveis trivialmente. No entanto, se o método anônimo capturar qualquer variável local no ambiente, o objeto de fechamento resultante (gerado pelo compilador) não será marcado como serializável. Nessa situação, o Construtor de Bot lançará um `ClosureCaptureException` para identificar o problema.

Para usar a reflexão para serializar classes que não estão marcadas como serializáveis, a biblioteca do Construtor inclui um substituto de serialização baseado em reflexão que você pode usar para registrar-se com [Autofac][autofac].

[!code-csharp[Serialization](../includes/code/dotnet-dialogs.cs#serialization)]

## <a id="dialog-chains"></a> Cadeias de diálogos

Embora você possa gerenciar explicitamente a pilha de diálogos ativos usando `IDialogStack.Call<R>` e `IDialogStack.Done<R>`, também é possível gerenciar implicitamente a pilha de diálogos ativos usando esses métodos de [Cadeia][chain].


|           Método            |  Tipo   |                                 Observações                                  |
|-----------------------------|---------|------------------------------------------------------------------------|
|     Chain.Select<T, R>      |  LINQ   |           Dá suporte a "select" e "let" na sintaxe da consulta LINQ.            |
|  Chain.SelectMany<T, C, R>  |  LINQ   |            Dá suporte sucessivo a "from" na sintaxe da consulta LINQ.            |
|       Chain.Where<T>        |  LINQ   |                 Dá suporte a "where" na sintaxe da consulta LINQ.                 |
|        Chain.From<T>        | Cadeias  |                Cria uma nova instância de um diálogo.                |
|       Chain.Return<T>       | Cadeias  |                Retorna um valor constante na cadeia.                |
|         Chain.Do<T>         | Cadeias  |               Permite efeitos colaterais dentro na cadeia.                |
|  Chain.ContinueWith<T, R>   | Cadeias  |                      Encadeamento simples de diálogos.                       |
|       Chain.Unwrap<T>       | Cadeias  |                  Desencapsula um diálogo aninhado em um diálogo.                   |
| Chain.DefaultIfException<T> | Cadeias  | Absorve uma exceção do resultado anterior e retorna default(T). |
|        Chain.Loop<T>        | Branch  |                   Executa um loop de toda a cadeia de diálogos.                   |
|        Chain.Fold<T>        | Branch  |   Dobra resultados de uma enumeração de diálogos em um único resultado.   |
|     Chain.Switch<T, R>      | Branch  |            Dá suporte à ramificação em cadeias de diálogos diferentes.            |
|     Chain.PostToUser<T>     | Mensagem |                      Posta uma mensagem para o usuário.                      |
|     Chain.WaitToBot<T>      | Mensagem |                    Aguarda uma mensagem para o bot.                     |
|    Chain.PostToChain<T>     | Mensagem |              Inicia uma cadeia com uma mensagem do usuário.              |

### <a name="examples"></a>Exemplos 

A sintaxe da consulta LINQ usa o método `Chain.Select<T, R>`.

[!code-csharp[Chain.Select](../includes/code/dotnet-dialogs.cs#chain1)]

Ou o método `Chain.SelectMany<T, C, R>`.

[!code-csharp[Chain.SelectMany](../includes/code/dotnet-dialogs.cs#chain2)]

Os métodos `Chain.PostToUser<T>` e `Chain.WaitToBot<T>` postam mensagens do bot para o usuário e vice-versa.

[!code-csharp[Chain.PostToUser](../includes/code/dotnet-dialogs.cs#chain3)]

O método `Chain.Switch<T, R>` ramifica o fluxo do diálogo da conversa.

[!code-csharp[Chain.Switch](../includes/code/dotnet-dialogs.cs#chain4)]

Se `Chain.Switch<T, R>` retornar um `IDialog<IDialog<T>>` aninhado, então, o `IDialog<T>` interno poderá ser desempacotado com `Chain.Unwrap<T>`. Isso permite a ramificação de conversas para diferentes caminhos de diálogos encadeados, possivelmente de comprimento desigual. Este exemplo mostra um exemplo mais completo de diálogos de ramificação gravados no estilo da cadeia fluente com gerenciamento de pilha implícito.

[!code-csharp[Chain.Switch](../includes/code/dotnet-dialogs.cs#chain5)]

## <a name="next-steps"></a>Próximas etapas

Os diálogos gerenciam o fluxo de conversas entre um bot e um usuário. Um diálogo define como interagir com um usuário. Um bot pode usar muitos diálogos organizados em pilhas para orientar a conversa com o usuário. Na próxima seção, veja como a pilha de diálogos aumenta e diminui à medida que você cria e descarta diálogos na pilha.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo de conversas com diálogos](bot-builder-dotnet-manage-conversation-flow.md)


[builderLibrary]: /dotnet/api/microsoft.bot.builder.dialogs

[iBotData]: /dotnet/api/microsoft.bot.builder.dialogs.internals.ibotdata

[iBotToUser]: /dotnet/api/microsoft.bot.builder.dialogs.internals.ibottouser

[iDialogStack]: /dotnet/api/microsoft.bot.builder.dialogs.internals.idialogstack

[iBotDataBag]: /dotnet/api/microsoft.bot.builder.dialogs.ibotdatabag

[autofac]: /dotnet/api/microsoft.bot.builder.autofac.base

[chain]: /dotnet/api/microsoft.bot.builder.dialogs.chain
