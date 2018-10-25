---
title: Estado do diálogo | Microsoft Docs
description: Descreve como o estado funciona dentro do SDK do Bot Builder.
keywords: estado, estado do bot, estado da conversa, estado do usuário
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 9/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4da715650eb1c3e7b284aaa47aa5ce4ac7280f4c
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998753"
---
# <a name="dialog-state"></a>Estado do diálogo

Os diálogos são uma abordagem usada para implementar lógica de conversa em vários turnos e, por isso, são um exemplo de um recurso do SDK que se baseia em um estado persistente. 

Um bot baseado em diálogo normalmente contém uma coleção DialogSet como variável de membro em sua implementação de bot. O DialogSet é criado com um identificador para um objeto chamado Acessador. 

O Acessador é o conceito central no modelo de estado do SDK. Um Acessador implementa a interface IStatePropertyAccessor, o que significa que ele deve fornecer implementações para os métodos Get, Set e Delete. Para criar um Acessador, você deve dar um nome de propriedade a ele. 

O código que usa o Acessador pode chamar os métodos Get, Set e Delete sabendo que eles farão referência a uma propriedade com aquele nome. Quando um componente de nível superior exigir certa persistência de estado, ele deverá usar o Acessador. Dessa forma, vários cenários poderão trazer suas implementações de armazenamento de estado e fazer uso do mesmo código de nível superior. Por exemplo, os diálogos fazem uso do Acessador e essa é sua única maneira de acessar o estado persistente.

![estado do diálogo](media/bot-builder-dialog-state.png)

Quando o OnTurn do bot é chamado, o bot inicializa o subsistema do diálogo chamando CreateContext no DialogSet. A criação de um DialogContext requer o estado e, portanto, o DialogSet usa seu Acessador para obter o JSON do estado do diálogo pertinente. O SDK fornece uma implementação do Acessador na forma da classe BotState. Os aplicativos que pretendem aproveitar a implementação do estado podem colocar BotState como subclasse e, assim, herdar uma implementação do Acessador. Há duas subclasses de BotState incluídas no SDK:

- UserState
- ConversationState

UserState e ConversationState usam chaves para o armazenamento subjacente. A chave é passada para o armazenamento físico. Logicamente, a chave atua como um namespace para a propriedade chamada pelo Acessador. A implementação de BotState usa internamente a atividade de entrada em TurnContext para gerar dinamicamente a chave de armazenamento que ele usa.

- O UserState cria uma chave usando a ID do Canal e a ID De. Por exemplo, _{Activity.ChannelId}/conversations/{Activity.From.Id}#DialogState_
- O ConversationState cria uma chave usando a ID do Canal e a ID da Conversa. Por exemplo, _{Activity.ChannelId}/users/{Activity.Conversation.Id}#YourPropertyName_

Um aplicativo precisa fornecer um Acessador, e a associação apropriada ao nome da propriedade e à chave de armazenamento criada dinamicamente acontece nos bastidores. A implementação do BotState do Acessador inclui algumas otimizações: 

- A primeira otimização é um carregamento lento e armazenado em cache. Uma carga na implementação de armazenamento real é adiada até que o primeiro Get seja chamado para o Acessador, e o resultado dessa carga é mantido em um cache. Uma referência a esse cache é adicionada a TurnContext, usando uma chave fornecida pelo BotState correspondente. Portanto, o cache correspondente a UserState será mantido em um campo chamado "UserState" e o cache correspondente a ConversationState será mantido em um campo chamado "ConversationState". As chamadas para os diversos objetos do Acessador são entregues ao TurnContext para que ele possa buscar o cache apropriado.

- A segunda otimização é que a classe BotState contém a lógica para determinar se alguma alteração foi feita no estado. Ela só executa uma operação de salvamento real no armazenamento subjacente em caso de alterações feitas.

- A terceira otimização na implementação do BotState está em uma classe chamada BotStateSet. O BotStateSet é uma coleção de objetos BotState que delega uma chamada em seu método SaveChanges para todos os membros da coleção em paralelo.

É relevante que a chamada SaveChanges no BotState não seja parte da interface IStatePropertyAccessor. O motivo é que SaveChanges é uma otimização específica da implementação BotState e não um aspecto principal do modelo. Especificamente, códigos como os Diálogos não tem nada sobre BotState, quanto mais sobre SaveChanges. Na verdade, o código de Diálogos só está acoplado ao Acessador. A intenção é que o método SaveChanges seja chamado fora do sistema do diálogo apenas após sua conclusão. Por exemplo, conforme mostrado no diagrama, ele pode ser chamado de dentro do método OnTurn do bot.

É importante observar que a implementação do BotState traz uma semântica específica. Mais especificamente, ela dá suporte a um comportamento de "última gravação prevalece", no qual a última gravação será marcada em cima do estado gravado anteriormente. Isso pode funcionar bem para muitos aplicativos, mas tem implicações, especialmente em cenários de expansão em que pode haver algum nível de simultaneidade. Se essa for uma preocupação, a resposta é implementar seu próprio Acessador e transmiti-lo para o Diálogo. Há muitas abordagens alternativas, por exemplo, uma solução pode optar por utilizar a condição de eTag popular em serviços de armazenamento em nuvem, como o Armazenamento do Azure. Nesse caso, a solução provavelmente implementaria outras partes importantes. Por exemplo, ela pode armazenar atividades de saída em buffer e enviá-las apenas após uma operação de salvamento bem-sucedida. O ponto importante a observar é que esse comportamento não é o comportamento da implementação de BotState, mas algo que um aplicativo forneceria e conectaria no nível do Acessador.

A implementação de BotState em si tem um modelo de armazenamento conectável. Isso segue um padrão simples de carregar/salvar, e o SDK fornece duas implementações alternativas para a produção. Uma para o Armazenamento do Azure e outra para o CosmosDB, além de uma implementação na memória para fins de teste. No entanto, é importante observar aqui a semântica de "última gravação prevalece", que é determinada pela implementação de BotState.

## <a name="handling-state-in-middleware"></a>Tratando do estado em middleware
O diagrama acima mostra uma chamada para SaveAllChanges que acontece no final do OnTurn do bot. Veja aqui o mesmo diagrama com foco na chamada.

![problemas de middleware de estado](media/bot-builder-dialog-state-problem.png)

O problema com essa abordagem é que nenhuma atualização de estado feita de um middleware personalizado que acontece depois que o método OnTurn do bot retorna é salva no armazenamento durável. A solução é mover a chamada a SaveAllChanges para depois que o middleware personalizado for concluído adicionando AutoSaveChangesMiddleware no final da pilha de middleware. A execução é mostrada abaixo.

![solução de middleware de estado](media/bot-builder-dialog-state-solution.png)

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais detalhes, consulte o SDK do Bot Builder no GitHub [[C#](https://github.com/Microsoft/BotBuilder-dotnet) | [JavaScript](https://github.com/Microsoft/BotBuilder-js)].
