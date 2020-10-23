---
title: Diferenças entre o SDK do NodeJS v3 e v4 – Serviço de Bot
description: Descubra como as versões 3 e 4 do SDK do JavaScript do bot Framework diferem. Saiba mais sobre as alterações no processamento de atividades, no gerenciamento de estado e nas caixas de diálogo.
keywords: migração de bot, caixas de diálogo, estado
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/28/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b6d085c09d77e196c0ee2d31c8bb90876831db66
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417394"
---
# <a name="differences-between-the-v3-and-v4-javascript-sdk"></a>Diferenças entre o SDK do JavaScript v3 e v4

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

A versão 4 do SDK do Bot Framework suporta o mesmo Bot Framework Service que a versão 3. No entanto, a v4 é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle aos desenvolvedores sobre seus bots. Alterações importantes no SDK incluem:

- Introdução a um adaptador de bot
  - Isto faz parte da pilha de processamento de atividade
  - Lida com a autenticação do Bot Framework
  - Gerencia o tráfego de entrada e saída entre um canal e o manipulador de turno do seu bot, encapsulando as chamadas para o Bot Framework Connector
  - Inicializa o contexto para cada turno
  - Para obter mais informações, confira [como os bots funcionam](../bot-builder-basics.md).
- Gerenciamento de estado refatorado
  - Os dados de estado não estão mais automaticamente disponíveis dentro de um bot
  - Agora eles são gerenciados por meio de objetos de gerenciamento e acessadores de propriedade
  - Para obter mais informações, confira [como gerenciar o estado](../bot-builder-concept-state.md)
- Uma nova biblioteca de Diálogos
  - Os diálogos da v3 devem ser reescritos para a nova biblioteca de diálogos
  - Não há mais pontuáveis. Você pode buscar comandos “globais”, antes de passar o controle para seus diálogos. Dependendo de como o seu bot v4 foi criado, pode estar no manipulador de mensagens ou em um diálogo pai. Por exemplo, consulte como [manipular interrupções do usuário](../bot-builder-howto-handle-user-interrupt.md).
  - Para obter mais informações, confira a [biblioteca de diálogos](../bot-builder-concept-dialog.md).

## <a name="activity-processing"></a>Processamento de atividade

Quando você cria o adaptador para o bot, também fornece um delegado manipulador de mensagens que receberá as atividades de entrada enviadas por canais e usuários. O adaptador cria um objeto de contexto do turno para cada atividade recebida. Ele passa o objeto de contexto do turno para o manipulador de turnos do bot e, em seguida, descarta o objeto quando o turno é concluído.

O manipulador de turnos pode receber vários tipos de atividades. Em geral, você encaminhará somente atividades de _mensagem_ para os diálogos contidos no seu bot. Se você derivar seu bot de `ActivityHandler`, o manipulador de turnos do bot encaminhará todas as atividades de mensagem para `OnMessage`. Substitua esse método para adicionar lógica de manipulação de mensagens. Para obter mais informações sobre os tipos de atividade, confira o [esquema de atividade](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md).

### <a name="handling-turns"></a>Manipulação de turnos

Ao manipular uma mensagem, use o contexto do turno para obter informações sobre a atividade de entrada e enviar as atividades para o usuário:

|Atividade |Descrição |
|:---|:---|
| Para obter a atividade de entrada | Obtenha a propriedade `Activity` do contexto do turno. |
| Para criar e enviar uma atividade para o usuário | Chame o método `SendActivity` do contexto do turno.<br/> Para obter mais informações, confira [enviar e receber uma mensagem de texto](../../rest-api/bot-framework-rest-direct-line-1-1-receive-messages.md) e [adicionar mídia às mensagens](../bot-builder-howto-add-media-attachments.md). |

A classe `MessageFactory` fornece alguns métodos auxiliares para atividades de criação e formatação.

### <a name="interruptions"></a>Interrupções

Trate-as no loop de mensagem do bot. Para obter uma descrição de como fazer isso com os diálogos da v4, confira como [tratar interrupções do usuário](../bot-builder-howto-handle-user-interrupt.md).

## <a name="state-management"></a>Gerenciamento de estado

Na v3, você pode armazenar dados de conversa no Serviço de Estado do Bot, parte do conjunto maior de serviços fornecidos pelo Bot Framework. Porém, esse serviço foi desativado em 31 de março de 2018. Da v4 em diante, as considerações de design para o gerenciamento de estado são iguais às de qualquer aplicativo Web e há várias opções disponíveis. O armazenamento do estado na memória e no mesmo processo normalmente é a maneira mais fácil; entretanto, nos aplicativos de produção é preciso armazená-lo mais permanentemente; por exemplo, em um banco de dados SQL ou NoSQL ou como blobs.

A v4 não usa as propriedades `UserData`, `ConversationData` e `PrivateConversationData` e o recipiente de dados para gerenciar o estado.
O estado agora é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade conforme descrito em [gerenciar o estado](../bot-builder-concept-state.md).

A v4 define as classes `UserState`, `ConversationState` e `PrivateConversationState` que gerenciam dados de estado para o bot. Você precisa criar um acessador de propriedade de estado para cada propriedade que você deseja manter, em vez de apenas ler e gravar em um recipiente de dados predefinido.

### <a name="setting-up-state"></a>Configurar o estado

O estado deve ser configurado no arquivo de ponto de entrada do aplicativo, comumente 'index.js' ou 'app.js' em aplicativos do NodeJS. 

1. Inicialize um ou mais objetos que implementam a interface `Storage` fornecida pelo botbuilder-core. Isso representa o repositório de backup para os dados do seu bot.
    O SDK v4 fornece algumas [camadas de armazenamento](../bot-builder-concept-state.md#storage-layer).
    Você também pode implementar sua própria camada, para se conectar a outro tipo de repositório.
1. Em seguida, crie e registre os objetos de [gerenciamento de estado](../bot-builder-concept-state.md#state-management) conforme necessário.
    Você tem os mesmos escopos disponíveis que tinha na v3 e pode criar outros se precisar.
1. Por fim, crie e registre os [acessadores de propriedade de estado](../bot-builder-concept-state.md#state-property-accessors) para as propriedades de que seu bot precisar.
    Dentro de um objeto de gerenciamento de estado, cada acessador de propriedade precisa de um nome exclusivo.

### <a name="using-state"></a>Usando o estado

Use os acessadores de propriedade de estado para obter e atualizar suas propriedades e use os objetos de gerenciamento de estado para gravar todas as alterações no armazenamento. Com o entendimento de que você deve levar em conta problemas de simultaneidade, aqui está como realizar algumas tarefas comuns.

| Tarefa|Descrição |
|:---|:---|
| Para criar um acessador de propriedade de estado | Chame o método `createProperty` de seu objeto `BotState`. <br/>`BotState` é a classe base abstrata para a conversa, conversa privada e estado do usuário. |
| Para obter o valor atual de uma propriedade | Chame `StatePropertyAccessor.get(TurnContext)`.<br/>Se nenhum valor tiver sido definido, o parâmetro de fábrica padrão será usado para gerar um valor. |
| Para manter as alterações de estado para o armazenamento | Chame `BotState.saveChanges(TurnContext, boolean)` para qualquer um dos objetos de gerenciamento de estado no qual o estado foi alterado antes de sair do manipulador de turnos. |

### <a name="managing-concurrency"></a>Gerenciando simultaneidade

Talvez o seu bot precise gerenciar a simultaneidade de estado. Para obter mais informações, confira a seção [salvar estado](../bot-builder-concept-state.md#saving-state) em **Gerenciamento de estado** e a seção [gerenciar a simultaneidade usando eTags](../bot-builder-howto-v4-storage.md#manage-concurrency-using-etags) em **Gravar diretamente no armazenamento**.

## <a name="dialogs-library"></a>Biblioteca de diálogos

Aqui estão algumas das principais alterações nos diálogos:

- A biblioteca de diálogo agora é um pacote npm separado: **botbuilder-dialogs**.
- O estado do diálogo é gerenciado por meio de acessador de propriedade e classe de estado `DialogState`.
  - A propriedade de estado do diálogo agora é persistida entre turnos, em vez do próprio objeto de diálogo.
- Dentro de um turno, você cria um contexto de diálogo para um _conjunto de diálogos_.
  - Esse contexto de diálogo encapsula a pilha de diálogo. Essas informações são persistidas dentro da propriedade de estado do diálogo.
- Ambas as versões contêm uma classe `Dialog` abstrata, no entanto, na v3, ela estende a classe 'ActionSet' enquanto que, na v4, estende 'Object'.

### <a name="defining-dialogs"></a>Definindo diálogos

A v3 fornecia uma maneira flexível de implementar diálogos usando a classe `Dialog`, mas você precisava implementar seu próprio código dos recursos, como validação. Na v4, agora há classes de prompt que validarão automaticamente a entrada do usuário para você, a restringirá a um tipo específico (ex.: um inteiro) e avisará o usuário novamente, até que ele forneça uma entrada válida. De maneira geral, o desenvolvedor terá menos código para escrever.

Agora você tem algumas opções de como definir os diálogos:

|Tipo de Caixa de Diálogo| Descrição |
|:---|:---|
| Um componente de diálogo, derivado da classe `ComponentDialog` | Permite que você encapsule o código do diálogo sem conflitos de nomenclatura com os contextos externos. Confira [reutilizar os diálogos](../bot-builder-concept-dialog.md).|
| Um diálogo em cascata, uma instância da classe `WaterfallDialog` | Projetado para funcionar bem com os diálogos de prompt, que solicitam e validam os vários tipos de entrada do usuário. A cascata automatiza a maior parte do processo para você, mas impõe uma determinada forma ao código do diálogo; confira [fluxo de conversa sequencial](../bot-builder-dialog-manage-conversation-flow.md). |
| Um diálogo personalizado, derivado da classe abstrata `Dialog` | Isso lhe dá mais flexibilidade em como os diálogos se comportam, mas também requer que você saiba mais sobre como a pilha de diálogo é implementada. |

Quando você cria um diálogo em cascata, define as etapas do diálogo no construtor. A ordem das etapas executadas segue exatamente como se você a tivesse declarado e, automaticamente, faz com que avance, uma após a outra.

Você também pode criar fluxos de controle complexos usando vários diálogos; confira [fluxo avançado de conversa](../bot-builder-dialog-manage-complex-conversation-flow.md).

Para acessar um diálogo, você precisa colocar uma instância em um _conjunto de diálogos_ e, em seguida, gerar um _contexto de diálogo_ para esse conjunto. Você precisa fornecer um acessador de propriedade de estado de diálogo ao criar um conjunto de diálogos. Isso permite que a estrutura persista o estado do diálogo entre um turno e outro. [Gerenciando o estado](../bot-builder-concept-state.md) descreve como o estado é gerenciado na v4.

### <a name="using-dialogs"></a>Usar os diálogos

Aqui está uma lista de operações comuns na v3 e como realizá-las dentro de um diálogo em cascata. Observe que para cada etapa de um diálogo em cascata espera-se que um valor `DialogTurnResult` seja retornado. Caso contrário, a cascata pode terminar cedo.

| Operação | v3 | v4 |
|:---|:---|:---|
| Manipula o início do diálogo | chame `session.beginDialog`, passando a ID do diálogo | chamar `DialogContext.beginDialog` |
| Enviar uma atividade | Chame `session.send`. | Chame `TurnContext.sendActivity`.<br/>Use a propriedade `Context` do contexto da etapa para obter o contexto do turno (`step.context.sendActivity`).  |
| Aguarde a resposta do usuário | chame um prompt de dentro da etapa de cascata, por exemplo: `builder.Prompts.text(session, 'Please enter your destination')`. Recupere a resposta na próxima etapa. | Faça o return await de `TurnContext.prompt` para iniciar um diálogo de prompt. Em seguida, recupere o resultado na etapa seguinte da cascata. |
| Manipula a continuação do diálogo | Automático | Adiciona etapas a um diálogo em cascata ou implementar `Dialog.continueDialog` |
| Sinalize o final do processamento até a mensagem seguinte do usuário | Chame `session.endDialog`. | Retorne `Dialog.EndOfTurn`. |
| Iniciar um diálogo filho | Chame `session.beginDialog`. | Faça o return await para o método `beginDialog` do contexto da etapa.<br/>Se o diálogo filho retornar um valor, esse valor estará disponível na etapa seguinte da cascata por meio da propriedade `Result` do contexto da etapa. |
| Substituir o diálogo atual por um novo diálogo | Chame `session.replaceDialog`. | Faça o return await de `ITurnContext.replaceDialog`. |
| Sinaliza que o diálogo atual foi concluído | Chame `session.endDialog`. | faça o return await para o método `endDialog` do contexto da etapa. |
| Falha de um diálogo. | Chame `session.pruneDialogStack`. | Lance uma exceção para ser capturada em outro nível do bot, encerre a etapa com um status de `Cancelled` ou chame `cancelAllDialogs` o contexto da etapa ou do diálogo. |

Outras observações sobre o código da v4:

- As várias classes derivadas de `Prompt` na v4 implementam prompts de usuário como caixas de diálogos separados, em duas etapas. Veja como [implementar fluxo de conversa sequencial][sequential-flow].
- Use `DialogSet.createContext` para criar um contexto de diálogo para o turno atual.
- Use a propriedade `DialogContext.context` para obter o contexto de turno atual de dentro de um diálogo.
- As etapas de cascata têm um parâmetro `WaterfallStepContext`, que deriva de `DialogContext`.
- Todas as classes concretas de diálogo e de prompt derivam da classe `Dialog` abstrata.
- Você atribui uma ID ao criar um diálogo do componente. Cada diálogo presente em um conjunto de diálogos precisa ser atribuído a uma ID exclusiva dentro desse conjunto.

### <a name="passing-state-between-and-within-dialogs"></a>Transmitir o estado entre e dentro de diálogos

A seção [estado da caixa](../bot-builder-concept-dialog.md#dialog-state) de diálogo do artigo da biblioteca de caixas de **diálogo** e as seções Propriedades do [contexto](../bot-builder-concept-waterfall-dialogs.md#waterfall-step-context-properties) e uso de [caixas de diálogo](../bot-builder-concept-waterfall-dialogs.md#using-dialogs) do **componente sobre componentes e caixas** de diálogo de cascata descrevem como gerenciar o estado da caixa de diálogo no v4.

### <a name="get-user-response"></a>Obter resposta do usuário

Para obter a atividade do usuário em um turno, use o contexto do turno.

Para fazer uma solicitação ao usuário e receber o seu respectivo resultado:

- Adicione uma instância de solicitação apropriada ao seu conjunto de diálogos.
- Chame o prompt de uma etapa em um diálogo em cascata.
- Recupere o resultado da propriedade `Result` do contexto da etapa na etapa a seguir.

## <a name="additional-resources"></a>Recursos adicionais

Confira os seguintes recursos para obter mais detalhes e informações de contexto.

| Tópico | Descrição |
| :--- | :--- |
|[Migre um bot do SDK do JavaScript v3 para v4](https://docs.microsoft.com/azure/bot-service/migration/conversion-javascript?view=azure-bot-service-4.0)| Como realizar a portabilidade de um bot JavaScript v3 para v4|
| [Novidades no Bot Framework](https://docs.microsoft.com/azure/bot-service/what-is-new?view=azure-bot-service-4.0) | Aprimoramentos e recursos principais do Bot Framework e do Serviço de Bot do Azure|
|[Como funcionam os bots](../bot-builder-basics.md)|O mecanismo interno de um bot|
|[Gerenciar estado](../bot-builder-concept-state.md)|Abstrações para facilitar o gerenciamento de estado|
|[Biblioteca de caixas de diálogo](../bot-builder-concept-dialog.md)| Conceitos centrais para gerenciar uma conversa|
|[Enviar e receber mensagens de texto](../bot-builder-howto-send-messages.md)|Principal maneira de um bot comunicar-se com usuários|
|[Enviar Mídia](../bot-builder-howto-add-media-attachments.md)|Anexos de mídia, como imagens, vídeo, áudio e arquivos| 
|[Fluxo de conversa sequencial](../bot-builder-dialog-manage-conversation-flow.md)| Questionamento como o principal meio de um bot interagir com os usuários|
|[Salvar dados de usuário e de conversa](../bot-builder-howto-v4-state.md)|Como acompanhar uma conversa sem estado|
|[Fluxo Complexo](../bot-builder-dialog-manage-complex-conversation-flow.md)|Gerenciar fluxos de conversas complexos |
|[Reutilizar Caixas de Diálogo](../bot-builder-compositcontrol.md)|Criar caixas de diálogo independentes para lidar com cenários específicos|
|[Interrupções](../bot-builder-howto-handle-user-interrupt.md)| Lidar com interrupções para criar um bot robusto|
|[Esquema da Atividade](https://aka.ms/botSpecs-activitySchema)|Esquema para seres humanos e software automatizado|
