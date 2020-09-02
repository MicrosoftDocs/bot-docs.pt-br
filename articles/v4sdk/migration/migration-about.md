---
title: Diferenças entre o SDK v3 e v4 – Serviço de Bot
description: Descubra como as versões 3 e 4 do SDK do bot Framework são diferentes. Saiba mais sobre as alterações no processamento de atividades, gerenciamento de estado, caixas de diálogo e suporte ASP.NET Core.
keywords: migração de bot, formflow, diálogos, estado
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2574082067a5687f442c110227a7defb7f540670
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362219"
---
# <a name="differences-between-the-v3-and-v4-net-sdk"></a>Diferenças entre o SDK do .NET v3 e v4

A versão 4 do SDK do Bot Framework suporta o mesmo Bot Framework Service que a versão 3. No entanto, a v4 é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle aos desenvolvedores sobre seus bots. Alterações importantes no SDK incluem:

- Introdução de um adaptador de bot. O adaptador é parte da pilha de processamento de atividade.
  - O adaptador manipula a autenticação do Bot Framework.
  - O adaptador gerencia o tráfego de entrada e saída entre um canal e o manipulador de turno do seu bot, encapsulando as chamadas para o Bot Framework Connector.
  - O adaptador inicializa o contexto para cada turno.
  - Para obter mais detalhes, confira [como funcionam os bots][about-bots].
- Gerenciamento de estado refatorado.
  - Os dados de estado não estão mais automaticamente disponíveis dentro de um bot.
  - O estado agora é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade.
  - Para obter mais detalhes, confira [gerenciar o estado][about-state].
- Uma nova biblioteca de Diálogos.
  - Os diálogos da v3 precisarão ser reescritos para a nova biblioteca de diálogos.
  - Não há mais pontuáveis. Você pode buscar comandos “globais”, antes de passar o controle para seus diálogos. Dependendo de como o seu bot v4 foi criado, pode estar no manipulador de mensagens ou em um diálogo pai. Por exemplo, consulte como [manipular interrupções do usuário][interruptions].
  - Para obter mais detalhes, confira [biblioteca de diálogos][about-dialogs].
- Suporte para ASP.NET Core.
  - Os modelos para criar novos bots C# são direcionados à estrutura do ASP.NET Core.
  - Você ainda pode usar o ASP.NET para seus bots, mas nosso foco para v4 é oferecer suporte à estrutura do ASP.NET Core.
  - Para obter mais informações sobre essa estrutura, confira [introdução ao ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

## <a name="activity-processing"></a>Processamento de atividade

Quando você cria o adaptador para o bot, também fornece um delegado manipulador de mensagens que receberá as atividades de entrada enviadas por canais e usuários. O adaptador cria um objeto de contexto do turno para cada atividade recebida. Ele passa o objeto de contexto do turno para o manipulador de turnos do bot e, em seguida, descarta o objeto quando o turno é concluído.

O manipulador de turnos pode receber vários tipos de atividades. Em geral, você encaminhará somente atividades de _mensagem_ para os diálogos contidos no seu bot. Se você derivar seu bot de `ActivityHandler`, o manipulador de turnos do bot encaminhará todas as atividades de mensagem para `OnMessageActivityAsync`. Substitua esse método para adicionar lógica de manipulação de mensagens. Para obter informações detalhadas sobre os tipos de atividade, confira o [esquema de atividade][].

### <a name="handling-turns"></a>Manipulação de turnos

Ao manipular uma mensagem, use o contexto do turno para obter informações sobre a atividade de entrada e enviar as atividades para o usuário:

| | |
|-|-|
| Para obter a atividade de entrada | Obtenha a propriedade `Activity` do contexto do turno. |
| Para criar e enviar uma atividade para o usuário | Chame o método `SendActivityAsync` do contexto do turno.<br/>Para obter mais informações, confira [enviar e receber uma mensagem de texto][send-messages] e [adicionar mídia às mensagens][send-media]. |

A classe `MessageFactory` fornece alguns métodos auxiliares para atividades de criação e formatação.

### <a name="scorables-is-gone"></a>Não existem mais classificações

Trate-as no loop de mensagem do bot. Para obter uma descrição de como fazer isso com os diálogos da v4, confira como [tratar interrupções do usuário][interruptions].

Árvores de expedição classificáveis combináveis e diálogos de cadeia combináveis, como _exceção padrão_, também não existem mais. Uma maneira de reproduzir essa funcionalidade é implementá-la dentro do manipulador de turno do seu bot.

## <a name="state-management"></a>Gerenciamento de estado

Na v3, você pode armazenar dados de conversa no Serviço de Estado do Bot, parte do conjunto maior de serviços fornecidos pelo Bot Framework. Porém, o serviço foi desativado em 31 de março de 2018. A partir da v4, as considerações de design sobre gerenciamento do estado é igual às de qualquer aplicativo Web e há várias opções disponíveis. O armazenamento do estado na memória e no mesmo processo normalmente é a maneira mais fácil; entretanto, nos aplicativos de produção é preciso armazená-lo mais permanentemente; por exemplo, em um banco de dados SQL ou NoSQL ou como blobs.

A v4 não usa as propriedades `UserData`, `ConversationData` e `PrivateConversationData` e o recipiente de dados para gerenciar o estado.
O estado agora é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade conforme descrito em [gerenciar o estado][about-state].

A v4 define as classes `UserState`, `ConversationState` e `PrivateConversationState` que gerenciam dados de estado para o bot. Você precisa criar um acessador de propriedade de estado para cada propriedade que você deseja manter, em vez de apenas ler e gravar em um recipiente de dados predefinido.

### <a name="setting-up-state"></a>Configurar o estado

O estado deve ser configurado como singletons sempre que possível, no **Startup.cs** para o .NET Core ou no **Global.asax.cs** para o .NET Framework.

1. Inicialize um ou mais dos objetos `IStorage`. Isso representa o repositório de backup para os dados do seu bot.
    O SDK v4 fornece algumas [camadas de armazenamento](../bot-builder-concept-state.md#storage-layer).
    Você também pode implementar sua própria camada, para se conectar a outro tipo de repositório.
1. Em seguida, crie e registre os objetos de [gerenciamento de estado](../bot-builder-concept-state.md#state-management) conforme necessário.
    Você tem os mesmos escopos disponíveis que tinha na v3 e pode criar outros se precisar.
1. Em seguida, crie e registre os [acessadores de propriedade de estado](../bot-builder-concept-state.md#state-property-accessors) para as propriedades que seu bot precisar.
    Dentro de um objeto de gerenciamento de estado, cada acessador de propriedade precisa de um nome exclusivo.

### <a name="using-state"></a>Usando o estado

Você pode usar a injeção de dependência para acessá-lo sempre que seu bot é criado.
(No ASP.NET, uma nova instância do seu controlador de mensagem ou bot é criada para cada turno.) Use os acessadores de propriedade de estado para obter e atualizar suas propriedades e use os objetos de gerenciamento de estado para gravar todas as alterações no armazenamento. Com o entendimento de que você deve levar em conta problemas de simultaneidade, aqui está como realizar algumas tarefas comuns.

| | |
|-|-|
| Para criar um acessador de propriedade de estado | Chame `BotState.CreateProperty<T>`.<br/>`BotState` é a classe base abstrata para a conversa, conversa privada e estado do usuário. |
| Para obter o valor atual de uma propriedade | Chame `IStatePropertyAccessor<T>.GetAsync`.<br/>Se nenhum valor tiver sido definido, o parâmetro de fábrica padrão será usado para gerar um valor. |
| Para atualizar o valor atual, armazenado em cache, de uma propriedade | Chame `IStatePropertyAccessor<T>.SetAsync`.<br/>Isso só atualiza o cache e não a camada de armazenamento de backup. |
| Para manter as alterações de estado para o armazenamento | Chame `BotState.SaveChangesAsync` para qualquer um dos objetos de gerenciamento de estado no qual o estado foi alterado antes de sair do manipulador de turnos. |

### <a name="managing-concurrency"></a>Gerenciando simultaneidade

Talvez o seu bot precise gerenciar a simultaneidade de estado. Para obter mais informações, confira a seção [salvar estado](../bot-builder-concept-state.md#saving-state) em **Gerenciamento de estado** e a seção [gerenciar a simultaneidade usando eTags](../bot-builder-howto-v4-storage.md#manage-concurrency-using-etags) em **Gravar diretamente no armazenamento**.

## <a name="dialogs-library"></a>Biblioteca de diálogos

Aqui estão algumas das principais alterações nos diálogos:

- A biblioteca de diálogos agora é um pacote NuGet separado: **Microsoft.Bot.Builder.Dialogs**.
- As classes de diálogo não precisam mais ser serializáveis. O estado do diálogo é gerenciado por meio de um acessador de propriedade de estado de `DialogState`.
  - A propriedade de estado do diálogo agora é persistida entre turnos, em vez do próprio objeto de diálogo.
- A interface `IDialogContext` foi substituída pela classe `DialogContext`. Dentro de um turno, você cria um contexto de diálogo para um _conjunto de diálogos_.
  - Esse contexto de diálogo encapsula a pilha de diálogo (o antigo registro de ativação). Essas informações são persistidas dentro da propriedade de estado do diálogo.
- A interface `IDialog` foi substituída pela classe abstrata `Dialog`.

### <a name="defining-dialogs"></a>Definindo diálogos

A v3 fornecia uma maneira flexível de implementar diálogos usando a interface `IDialog`, mas você tinha de implementar seu próprio código dos recursos, como validação. Na v4, agora há classes de prompt que validarão automaticamente a entrada do usuário para você, a restringirá a um tipo específico (ex.: um inteiro) e avisará o usuário novamente, até que ele forneça uma entrada válida. De maneira geral, o desenvolvedor terá menos código para escrever.

Agora você tem algumas opções de como definir os diálogos:

| | |
|:--|:--|
| Um componente de diálogo, derivado da classe `ComponentDialog` | Permite que você encapsule o código do diálogo sem conflitos de nomenclatura com os contextos externos. Confira [reutilizar os diálogos][reuse-dialogs]. |
| Um diálogo em cascata, uma instância da classe `WaterfallDialog` | Projetado para funcionar bem com os diálogos de prompt, que solicitam e validam os vários tipos de entrada do usuário. A cascata automatiza a maior parte do processo para você, mas impõe uma determinada forma ao código do diálogo; confira [fluxo de conversa sequencial][sequential-flow]. |
| Um diálogo personalizado, derivado da classe abstrata `Dialog` | Isso lhe dá mais flexibilidade em como os diálogos se comportam, mas também requer que você saiba mais sobre como a pilha de diálogo é implementada. |

Na v3, você utilizava `FormFlow` para executar um determinado número de etapas de uma tarefa. Na v4, o diálogo em cascata substitui FormFlow. Quando você cria um diálogo em cascata, define as etapas do diálogo no construtor. A ordem das etapas executadas segue exatamente como se você a tivesse declarado e, automaticamente, faz com que avance, uma após a outra.

Você também pode criar fluxos de controle complexos usando vários diálogos; confira [fluxo avançado de conversa][complex-flow].

Para acessar um diálogo, você precisa colocar uma instância em um _conjunto de diálogos_ e, em seguida, gerar um _contexto de diálogo_ para esse conjunto. Você precisa fornecer um acessador de propriedade de estado de diálogo ao criar um conjunto de diálogos. Isso permite que a estrutura persista o estado do diálogo entre um turno e outro. [Gerenciando o estado][about-state] descreve como o estado é gerenciado na v4.

### <a name="using-dialogs"></a>Usar os diálogos

Aqui está uma lista de operações comuns na v3 e como realizá-las dentro de um diálogo em cascata. Observe que para cada etapa de um diálogo em cascata espera-se que um valor `DialogTurnResult` seja retornado. Caso contrário, a cascata pode terminar cedo.

| Operação | v3 | v4 |
|:---|:---|:---|
| Manipula o início do diálogo | Implementa `IDialog.StartAsync` | Torne essa a primeira etapa de um diálogo em cascata. |
| Enviar uma atividade | Chame `IDialogContext.PostAsync`. | Chame `ITurnContext.SendActivityAsync`.<br/>Use a propriedade `Context` do contexto da etapa para obter o contexto do turno.  |
| Aguarde a resposta do usuário | Use um parâmetro `IAwaitable<IMessageActivity>` e chame `IDialogContext.Wait` | Faça o return await de `ITurnContext.PromptAsync` para iniciar um diálogo de prompt. Em seguida, recupere o resultado na etapa seguinte da cascata. |
| Manipula a continuação do diálogo | Chame `IDialogContext.Wait`. | Adiciona etapas a um diálogo em cascata ou implementar `Dialog.ContinueDialogAsync` |
| Sinalize o final do processamento até a mensagem seguinte do usuário | Chame `IDialogContext.Wait`. | Retorne `Dialog.EndOfTurn`. |
| Iniciar um diálogo filho | Chame `IDialogContext.Call`. | Faça o return await para o método `BeginDialogAsync` do contexto da etapa.<br/>Se o diálogo filho retornar um valor, esse valor estará disponível na etapa seguinte da cascata por meio da propriedade `Result` do contexto da etapa. |
| Substituir o diálogo atual por um novo diálogo | Chame `IDialogContext.Forward`. | Faça o return await de `ITurnContext.ReplaceDialogAsync`. |
| Sinaliza que o diálogo atual foi concluído | Chame `IDialogContext.Done`. | faça o return await para o método `EndDialogAsync` do contexto da etapa. |
| Falha de um diálogo. | Chame `IDialogContext.Fail`. | Lance uma exceção para ser capturada em outro nível do bot, encerre a etapa com um status de `Cancelled` ou chame `CancelAllDialogsAsync` o contexto da etapa ou do diálogo.<br/>Observe que na v4, as exceções dentro de uma caixa de diálogo são propagadas ao longo da pilha C#, em vez da pilha do diálogo. |

Outras observações sobre o código da v4:

- As várias classes derivadas de `Prompt` na v4 implementam prompts de usuário como caixas de diálogos separados, em duas etapas. Confira como [implementar um fluxo de conversa sequencial][sequential-flow].
- Use `DialogSet.CreateContextAsync` para criar um contexto de diálogo para o turno atual.
- Use a propriedade `DialogContext.Context` para obter o contexto de turno atual de dentro de um diálogo.
- As etapas de cascata têm um parâmetro `WaterfallStepContext`, que deriva de `DialogContext`.
- Todas as classes concretas de diálogo e de prompt derivam da classe `Dialog` abstrata.
- Você atribui uma ID ao criar um diálogo do componente. Cada diálogo presente em um conjunto de diálogos precisa ser atribuído a uma ID exclusiva dentro desse conjunto.

### <a name="passing-state-between-and-within-dialogs"></a>Transmitir o estado entre e dentro de diálogos

A seção [estado da caixa](../bot-builder-concept-dialog.md#dialog-state) de diálogo do artigo da biblioteca de caixas de **diálogo** e as seções Propriedades do [contexto](../bot-builder-concept-waterfall-dialogs.md#waterfall-step-context-properties) e uso de [caixas de diálogo](../bot-builder-concept-waterfall-dialogs.md#using-dialogs) do **componente sobre componentes e caixas** de diálogo de cascata descrevem como gerenciar o estado da caixa de diálogo no v4.

### <a name="iawaitable-is-gone"></a>IAwaitable não existe mais

Para obter a atividade do usuário em um turno, use o contexto do turno.

Para fazer uma solicitação ao usuário e receber o seu respectivo resultado:

- Adicione uma instância de solicitação apropriada ao seu conjunto de diálogos.
- Chame o prompt de uma etapa em um diálogo em cascata.
- Recupere o resultado da propriedade `Result` do contexto da etapa na etapa a seguir.

### <a name="formflow"></a>Formflow

Na v3, o Formflow fazia parte do SDK de C#, mas não do SDK do JavaScript. Ele não faz parte do SDK da v4, mas existe uma versão da comunidade para C#.

| Nome do pacote NuGet | Repositório GitHub da comunidade |
|-|-|
| Bot.Builder.Community.Dialogs.Formflow | [BotBuilderCommunity/botbuilder-community-dotnet/libraries/Bot.Builder.Community.Dialogs.FormFlow](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/master/libraries/Bot.Builder.Community.Dialogs.FormFlow) |

## <a name="additional-resources"></a>Recursos adicionais

- [Migrar um bot do SDK do .NET v3 para v4](conversion-framework.md)

<!-- -->

[about-bots]: ../bot-builder-basics.md
[about-state]: ../bot-builder-concept-state.md
[about-dialogs]: ../bot-builder-concept-dialog.md

[send-messages]: ../bot-builder-howto-send-messages.md
[send-media]: ../bot-builder-howto-add-media-attachments.md

[sequential-flow]: ../bot-builder-dialog-manage-conversation-flow.md
[complex-flow]: ../bot-builder-dialog-manage-complex-conversation-flow.md
[reuse-dialogs]: ../bot-builder-compositcontrol.md
[interruptions]: ../bot-builder-howto-handle-user-interrupt.md

[esquema de atividade]: https://aka.ms/botSpecs-activitySchema