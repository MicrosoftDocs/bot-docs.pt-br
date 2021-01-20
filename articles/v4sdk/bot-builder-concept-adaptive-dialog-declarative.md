---
title: Como usar ativos declarativos em diálogos adaptáveis
description: Como usar ativos declarativos em diálogos adaptáveis
keywords: declarativo, diálogos adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/31/2020
ms.openlocfilehash: 079b0ceb56ecb7d4ced6b07916cdb4de870997bc
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577238"
---
# <a name="using-declarative-assets-in-adaptive-dialogs"></a>Como usar ativos declarativos em diálogos adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo explica os conceitos contidos em ativos declarativos e bots que incorporam diálogos adaptáveis usando a abordagem declarativa.  Os diálogos adaptáveis declarativos consistem em arquivos baseados em JSON que descrevem todos os atributos de diálogos adaptáveis, incluindo os [gatilhos][triggers] e as [ações][actions]. Esses arquivos declarativos são carregados no tempo de execução usando o gerenciador de recursos para criar os diálogos adaptáveis.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [noções básicas sobre bots][concept-basics], da [biblioteca de diálogos][concept-dialogs] e dos [diálogos adaptáveis][concept-adaptive].

## <a name="declarative-files"></a>Arquivos declarativos

Atualmente, os arquivos declarativos consistem em arquivos _.dialog_ que descrevem todos os atributos de um diálogo adaptável e arquivos _.lg_ que consistem em modelos de LG que definem os aspectos de [LG (geração de linguagem)](bot-builder-concept-adaptive-dialog-generators.md) do seu bot.

### <a name="dialog-files"></a>Arquivos .dialog

Arquivos de diálogo adaptáveis que têm a extensão .dialog contêm os seguintes elementos:

- O valor `$schema` contém um URI que aponta para o Esquema que descreve o formato deste arquivo declarativo. Esse esquema é um esquema de componente do Bot Framework, que segue o [rascunho 7](http://json-schema.org/specification-links.html#draft-7) do vocabulário do esquema JSON. Esse arquivo de esquema permite que o [IntelliSense][intellisense] funcione para os seus elementos declarativos. Para obter informações sobre como criar esse arquivo, consulte a seção no [comando Merge](#the-merge-command) abaixo. O nome do arquivo de esquema pode ser qualquer nome de arquivo válido, mas normalmente é chamado de **app.schema**.
- O campo `$kind` identifica o tipo de componente descrito nesse arquivo. Para um diálogo adaptável, `$kind` precisa ser `Microsoft.AdaptiveDialog`. Em subobjetos, `$kind` identifica um gatilho ou uma ação que faz parte da caixa de diálogo. Esse campo é correlacionado com o atributo de classe `[JsonProperty("$kind")]` que é associado a cada classe no SDK do Bot Framework que foi projetada para funcionar usando a abordagem declarativa.
- O valor `recognizer` contém um [tipo de reconhecedor][recognizer-types] e uma matriz de uma ou mais [intenções][intents] e, opcionalmente, uma matriz de uma ou mais [entidades][entity].
- O valor `generator` contém um link para o arquivo .lg associado ao diálogo adaptável que esse arquivo .dialog define.
- O valor `triggers` contém uma matriz de um ou mais [gatilhos](bot-builder-concept-adaptive-dialog-triggers.md). O tipo de gatilho é declarado usando a palavra-chave `$kind`. Cada gatilho contém uma matriz de uma ou mais ações.
- O valor `actions` contém uma matriz de uma ou mais [ações](bot-builder-concept-adaptive-dialog-actions.md), cada ação pode ter propriedades associadas a ela.

Um exemplo de um arquivo .dialog simples:

```json
{
  "$schema": "../app.schema",
  "$kind": "Microsoft.AdaptiveDialog",
  "generator": "multiTurnPrompt.lg",
  "recognizer": {
      "$kind": "Microsoft.RegexRecognizer",
      "intents": [
          {
              "intent": "CancelIntent",
              "pattern": "(?i)cancel"
          }
      ]
  },
  "triggers": [
    {
      "$kind": "Microsoft.OnUnknownIntent",
      "actions": [
        {
          "$kind": "Microsoft.SendActivity",
          "activity":  "You said '${turn.activity.text}'"
        }
      ]
    }
  ]
}
```

> [!NOTE]
> O arquivo `$schema` é o que permite que o IntelliSense funcione. Nenhum aviso ou erro ocorrerá se a palavra-chave `$schema` estiver ausente ou o valor `$schema` fizer referência a um arquivo de esquema que não pode ser encontrado, e tudo, exceto o IntelliSense, ainda funcionará conforme o esperado.

Os elementos do arquivo .dialog definido:

> [!div class="mx-imgBorder"]
> ![Criar o diálogo Get Weather](./media/adaptive-dialogs/dotdialogfile.png)

Para obter informações sobre como os `.dialog` arquivos podem ser gerados automaticamente usando o comando da CLI `luis:build` ao criar um aplicativo Luis, consulte [a seção arquivo de diálogo][the-dialog-file-luis] do artigo **implantar recursos do Luis usando o comando bot Framework Luis CLI** .

Para obter informações sobre como os `.dialog` arquivos podem ser gerados automaticamente usando o comando da CLI `qnamaker:build` ao criar uma base de dados de conhecimento QnA Maker, consulte [a seção arquivo de diálogo][the-dialog-file-qnamaker] da base de dados de **conhecimento implantar QnA Maker usando o artigo comandos da CLI do bot Framework qnamaker** .

### <a name="lg-files"></a>Arquivos .lg

Os arquivos declarativos de diálogo adaptáveis que têm a extensão .lg são descritos detalhadamente no artigo [Formato de arquivo .lg][lg-file-format].

## <a name="the-resource-explorer"></a>O gerenciador de recursos

O gerenciador de recursos fornece as ferramentas necessárias para importar arquivos de diálogo adaptáveis declarativos para o bot e usá-los como se fossem diálogos adaptáveis definidos diretamente no código-fonte do bot.

Com o gerenciador de recursos, você pode criar objetos de recurso que contêm todas as informações relevantes sobre os arquivos declarativos necessários para criar diálogos adaptáveis no tempo de execução. Isso é feito usando o carregador de tipos do gerenciador de recursos que importa arquivos com as extensões .dialog e .lg.

### <a name="the-resource-object"></a>O objeto de recurso

O método _get resource_ do gerenciador de recursos lê o arquivo declarativo em um objeto de recurso.  O objeto de recurso contém as informações sobre o arquivo declarativo e pode ser usado por qualquer processo que precise fazer referência a ele, como o carregador de tipo.

```csharp
var resource = this.resourceExplorer.GetResource("main.dialog");
```

### <a name="the-type-loader"></a>O carregador de tipo

Depois que o método do Gerenciador de recursos `_get resource_` lê o arquivo declarativo em um objeto de recurso, o `_load type_method` converte o recurso em um `AdaptiveDialog` objeto. O objeto `AdaptiveDialog` pode ser usado da mesma forma que qualquer outro diálogo adaptável não declarativo é usado ao criar um gerenciador de diálogos.

```csharp
dialogManager = new DialogManager(resourceExplorer.LoadType<AdaptiveDialog>(resource));
```

### <a name="auto-reload-dialogs-when-file-changes"></a>Recarregar diálogos automaticamente quando o arquivo for alterado

Sempre que um arquivo declarativo for alterado quando o bot estiver em execução, um evento _alterado_ será acionado. Você pode capturar esse evento e recarregar os seus arquivos declarativos. Dessa forma, quando qualquer o diálogo adaptável precisar ser atualizado, você não precisará atualizar o seu código e recompilar o código-fonte ou reiniciar o bot. Isso pode ser especialmente útil em um ambiente de produção.

<!--This example could be improved-->
```csharp
// auto reload the root dialog when it changes
this.resourceExplorer.Changed += (e, resources) =>
{
    if (resources.Any(resource => resource.Id == "main.dialog"))
    {
        Task.Run(() => this.LoadRootDialogAsync());
    }
};

private void LoadRootDialogAsync()
{
    var resource = this.resourceExplorer.GetResource("main.dialog");
    dialogManager = new DialogManager(resourceExplorer.LoadType<AdaptiveDialog>(resource));
}
```

### <a name="the-versionchanged-event"></a>O `VersionChanged` evento

Quando seus ativos declarativos forem recarregados para acomodar as alterações feitas em um `.dialog` arquivo, talvez seja necessário reavaliar o estado de quaisquer conversas atuais para considerar qualquer alteração na lógica da caixa de diálogo. As alterações na sua lógica podem ser tão simples quanto limpar sua pilha de conversa e reiniciar. Uma lógica mais sofisticada permitiria que você faça coisas como reiniciar uma caixa de diálogo mantendo os dados que você tem, permitindo que você pegue novas propriedades e ou caminhos que não existiam antes.

O `DialogEvents.VersionChanged` evento é capturado usando o `OnDialogEvent` gatilho.

<!--This example could be improved-->
```csharp
Triggers = new List<OnCondition>()
{
    new OnDialogEvent(DialogEvents.VersionChanged)
    {
        Actions = new List<Dialog>()
        {
            new SendActivity("The VersionChanged event fired.")
        }
    }
}
```

## <a name="declarative-assets"></a>Ativos declarativos

O SDK do Bot Framework tem vários ativos declarativos disponíveis, cada um deles será listado abaixo. Esses ativos podem ser usados nos seus arquivos .dialog como o valor `$kind`.

### <a name="triggers"></a>Gatilhos

Esta seção contém todos os [gatilhos](bot-builder-concept-adaptive-dialog-triggers.md), agrupados por tipo:

#### <a name="base-trigger"></a>Gatilho base

| `$kind` valor          | Nome do Gatilho | O que este gatilho faz                                              |
| ---------------------- | ------------ | ------------------------------------------------------------------- |
|`Microsoft.OnCondition` | `OnCondition`| O gatilho `OnCondition` é o gatilho base do qual todos os gatilhos derivam. Ao definir gatilhos em um diálogo adaptável, eles são definidos como uma lista de gatilhos `OnCondition`. |

#### <a name="recognizer-event-triggers"></a>Gatilhos de evento de reconhecedores

| `$kind` valor            | Nome do gatilho     | Descrição                                                                                                                                                   |
| ------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|`Microsoft.OnChooseIntent`| `OnChooseIntent` | Esse gatilho é executado quando uma ambiguidade é detectada entre intenções de vários reconhecedores em um [CrossTrainedRecognizerSet][cross-trained-recognizer-set].|
|`Microsoft.OnIntent`      | `OnIntent`       | Ações a serem executadas quando a intenção especificada for reconhecida.                                                                                                       |
|`Microsoft.OnQnAMatch`    | `OnQnAMatch`     | Esse gatilho será executado quando o [QnAMakerRecognizer][qna-maker-recognizer] tiver retornado uma intenção de `QnAMatch`. A entidade @answer terá a resposta `QnAMaker`. |
|`Microsoft.OnUnknownIntent`|`OnUnknownIntent`| Ações a serem executadas quando a entrada do usuário não for reconhecida ou nenhuma correspondência for encontrada em nenhum dos gatilhos `OnIntent`.                                                    |

#### <a name="dialog-event-triggers"></a>Gatilhos de evento de diálogo

| `$kind` valor            | Nome do gatilho     | Descrição                                                                                                                           |
| ------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------  |
|`Microsoft.OnBeginDialog`  | `OnBeginDialog`  | Ações a serem executadas quando a caixa de diálogo for iniciada. Para uso somente com diálogos secundários.                                                         |
|`Microsoft.OnCancelDialog` | `OnCancelDialog` | Esse evento permite impedir que a caixa de diálogo atual seja cancelada devido a uma caixa de diálogo secundária estar executando uma ação CancelAllDialogs.  |
|`Microsoft.OnEndOfActions` | `OnEndOfActions` | Esse evento ocorrerá assim que todas as ações e eventos de ambiguidade forem processados.                                                         |
|`Microsoft.OnError`        | `OnError`        | Ação a ser executada quando ocorrer um evento do diálogo `Error`. Esse evento é semelhante a `OnCancelDialog`, pois você está impedindo que a caixa de diálogo atual termine, neste caso devido a um erro em uma caixa de diálogo secundária.|
|`Microsoft.OnRepromptDialog` |`OnRepromptDialog`| Ações a serem executadas quando o evento `RepromptDialog` ocorrer.                                                                             |

> [!IMPORTANT]
> Não use o gatilho `OnBeginDialog` na caixa de diálogo raiz, pois isso pode causar problemas. Em vez disso, você pode usar o gatilho `OnUnknownIntent`, que será acionado quando a caixa de diálogo raiz for executada.

> [!TIP]
> A maioria das caixas de diálogo secundárias inclui um gatilho `OnBeginDialog` que responde ao evento `BeginDialog`. Esse gatilho é acionado automaticamente quando a caixa de diálogo é iniciada, o que pode permitir que o bot responda imediatamente com uma mensagem de boas-vindas ou um [prompt de entrada do usuário](bot-builder-concept-adaptive-dialog-inputs.md).

##### <a name="dialog-event-trigger-example-using-declarative"></a>Exemplo de gatilho de evento do diálogo usando declarativo

```json
{
    "$schema": "../app.schema",
    "$kind": "Microsoft.AdaptiveDialog",
    "triggers": [
        {
            "$kind": "Microsoft.OnBeginDialog",
            "actions": [
                {
                    "$kind": "Microsoft.SendActivity",
                    "activity": "Hello world!"
                }
            ]
        }
    ]
}
```

#### <a name="activity-event-triggers"></a>Gatilhos de evento de atividade

Os gatilhos de atividade permitem associar ações a qualquer atividade de entrada do cliente, como quando um novo usuário ingressa e o bot inicia uma nova conversa. Informações adicionais sobre atividades podem ser encontradas no [esquema de Atividade do Bot Framework][botframework-activity].

| `$kind` valor                          | Nome do gatilho                  | Descrição                                                                 |
| -------------------------------------- | ----------------------------- | --------------------------------------------------------------------------- |
|`Microsoft.OnConversationUpdateActivity`| `OnConversationUpdateActivity`| Manipule os eventos acionados quando um usuário inicia uma nova conversa com o bot. |
|`Microsoft.OnEndOfConversationActivity` | `OnEndOfConversationActivity` | Ações a serem executadas no recebimento de uma atividade com o tipo `EndOfConversation`. |
|`Microsoft.OnEventActivity`             | `OnEventActivity`             | Ações a serem executadas no recebimento de uma atividade com o tipo `Event`.             |
|`Microsoft.OnHandoffActivity`           | `OnHandoffActivity`           | Ações a serem executadas no recebimento de uma atividade com o tipo `HandOff`.           |
|`Microsoft.OnInvokeActivity`            | `OnInvokeActivity`            | Ações a serem executadas no recebimento de uma atividade com o tipo `Invoke`.            |
|`Microsoft.OnTypingActivity`            | `OnTypingActivity`            | Ações a serem executadas no recebimento de uma atividade com o tipo `Typing`.            |

#### <a name="message-event-triggers"></a>Gatilhos de evento de mensagem

Os gatilhos de **evento de mensagem** permitem reagir a qualquer evento de mensagem, como quando uma mensagem é atualizada (`MessageUpdate`) ou excluída (`MessageDeletion`) ou quando alguém reage (`MessageReaction`) a uma mensagem (por exemplo, algumas das reações de mensagens comuns incluem Curtida, Coração, Gargalhada, Surpreso, Triste e Irritado).

Os eventos de mensagem são um tipo de evento de atividade e, como tal, todos eles têm um evento base de `ActivityReceived` e são refinados por `ActivityType`. A classe base da qual todos os gatilhos de mensagem derivam é `OnActivity`.

| `$kind` valor                        |  Nome do gatilho               | Descrição                                                               |
| ------------------------------------ |  -------------------------- | ------------------------------------------------------------------------- |
|`Microsoft.OnMessageActivity`         | `OnMessageActivity`         | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReceived`. |
|`Microsoft.OnMessageDeleteActivity`   | `OnMessageDeleteActivity`   | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageDelete`.   |
|`Microsoft.OnMessageReactionActivity` | `OnMessageReactionActivity` | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageReaction`. |
|`Microsoft.OnMessageUpdateActivity`   | `OnMessageUpdateActivity`   | Ações a serem executadas no recebimento de uma atividade com o tipo `MessageUpdate`.   |

#### <a name="custom-event-triggers"></a>Gatilhos de evento personalizados

Você pode emitir seus eventos adicionando a ação [EmitEvent][emitevent] a qualquer gatilho, depois pode manipular esse evento personalizado em qualquer gatilho em qualquer caixa de diálogo em seu bot definindo um gatilho de _evento personalizado_. Um gatilho de evento personalizado é um tipo de gatilho `OnDialogEvent`, no qual a propriedade de _evento_ tem o mesmo valor que a propriedade de _nome de evento_ do evento de emissão.

> [!TIP]
> Você pode permitir que outros diálogos no seu bot manipulem o evento personalizado definindo a propriedade de _evento de bolha_ do evento de emissão como true.

| `$kind` valor | Nome do gatilho  | Descrição                                                                                                                               |
| ----------------------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Microsoft.OnDialogEvent | OnDialogEvent | Ações a serem executadas quando um evento personalizado for detectado. Use a ação [Emitir um evento personalizado][emitevent] para gerar um evento personalizado. |

### <a name="actions"></a>Ações

Esta seção contém todas as [ações](bot-builder-concept-adaptive-dialog-actions.md), agrupadas por tipo:

#### <a name="send-a-response"></a>Enviar uma resposta

| `$kind` valor            | Nome da ação                   | O que essa ação faz                                             |
| ------------------------ | ----------------------------- | ----------------------------------------------------------------- |
| `Microsoft.SendActivity` | [SendActivity][send-activity] | Permite que você envie qualquer tipo de atividade, como responder a usuários.|

#### <a name="requesting-user-input"></a>Solicitar entrada do usuário

| `$kind` valor              | Classe de entrada                      | Descrição                                          | Retornos                                      |
| -------------------------- | -------------------------------- | ---------------------------------------------------- | -------------------------------------------- |
|`Microsoft.AttachmentInput` |[AttachmentInput][attachmentinput]| Usado para solicitar/habilitar um usuário para **carregar um arquivo**.  | Uma coleção de objetos de anexos.          |
|`Microsoft.ChoiceInput`     | [ChoiceInput][choiceinput]       | Usado para solicitar uma escolha entre um **conjunto de opções**. | O valor ou o índice da seleção.         |
|`Microsoft.ConfirmInput`    | [ConfirmInput][confirminput]     | Usado para solicitar uma **confirmação** ao usuário.    | Um valor booliano.                             |
|`Microsoft.DateTimeInput`   | [DateTimeInput][datetimeinput]   | Usado para solicitar aos usuários uma **data ou hora**.   | Uma coleção de objetos de data e hora.           |
|`Microsoft.InputDialog`     | [InputDialog][inputdialog]       | Essa é a classe base da qual derivam todas as classes de entrada. Ela define todas as propriedades compartilhadas. |
|`Microsoft.NumberInput`     | [NumberInput][numberinput]       | Usado para solicitar um **número** aos usuários.             | Um valor numérico.                             |
|`Microsoft.OAuthInput`      | [OAuthInput][oauthinput]         | Usado para permitir que os usuários **entrem em um site seguro**.| Uma resposta do token.                        |
|`Microsoft.TextInput`       | [TextInput][textinput]           | Usado para solicitar aos usuários uma **palavra ou frase**.   | Uma cadeia de caracteres.                                    |

#### <a name="create-a-condition"></a>Criar uma condição

| Atividade a realizar | `$kind` valor             | Nome da ação                | O que essa ação faz                                                                               |
| ---------------------- | ------------------------- | -------------------------- | --------------------------------------------------------------------------------------------------- |
| Ramificação: if/else        |`Microsoft.IfCondition`    |[IfCondition][ifcondition]| Usada para criar instruções If e If-Else usadas para executar o código somente se uma condição for verdadeira.  |
| Ramificação: Switch (várias opções) |`Microsoft.SwitchCondition` | [SwitchCondition][switchcondition] | Usada para criar um menu com várias opções.                                           |
| Loop: para cada item    |`Microsoft.Foreach`        | [ForEach][foreach]         | Executa um loop por meio de um conjunto de valores armazenados em uma matriz.                                                    |
| Loop: para cada página (vários itens) |`Microsoft.ForeachPage` | [ForEachPage][foreachpage] | Executa um loop por meio de um grande conjunto de valores armazenados em uma página de uma matriz por vez.                |
| Sair de um loop            |`Microsoft.BreakLoop`      | [BreakLoop][break-loop]    | Interrompe um loop.                                                                                |
| Continuar um loop        |`Microsoft.ContinueLoop`   | [ContinueLoop][continue-loop] | Continua o loop.                                                                               |
| Ir para uma ação diferente|`Microsoft.GotoAction`     | [GotoAction][goto-action]   | Passa imediatamente para a ação especificada e continua a execução. Determinado por `actionId`.        |

#### <a name="dialog-management"></a>Gerenciamento de caixa de diálogo

| `$kind` valor           | Nome da ação                        | O que essa ação faz                                                                                                   |
| ----------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
|`Microsoft.BeginDialog`  | [BeginDialog][begindialog]         | Começa a executar outra caixa de diálogo. Quando essa caixa de diálogo for concluída, a execução do gatilho atual será retomada.           |
|`Microsoft.CancelDialog` | `CancelDialog`                     | Cancela a caixa de diálogo ativa. Use quando desejar que a caixa de diálogo seja fechada imediatamente, mesmo que isso signifique parar o processo intermediário.  |
|`Microsoft.CancelAllDialogs`| [CancelAllDialogs][cancelalldialogs]| Cancela todas as caixas de diálogo ativas, incluindo qualquer caixa de diálogo pai ativa. Use caso você queira remover todas as caixas de diálogo da pilha; é possível limpar a pilha de caixas de diálogo chamando o método cancelar todas as caixas de diálogo no contexto da caixa de diálogo. Emite o evento `CancelAllDialogs`.|
|`Microsoft.EndDialog`    | [EndDialog][enddialog]            | Encerra a caixa de diálogo ativa.  Use quando desejar que a caixa de diálogo seja concluída e retorne os resultados antes de ser encerrada. Emite o evento `EndDialog`.|
|`Microsoft.EndTurn`      | [EndTurn][endturn]                | Encerra o turno atual da conversa sem encerrar a caixa de diálogo.                                                         |
|`Microsoft.RepeatDialog` | [RepeatDialog][repeatdialog]      | Usado para reiniciar a caixa de diálogo pai.                                                                                       |
|`Microsoft.ReplaceDialog`| [ReplaceDialog][replacedialog]    | Substitui a caixa de diálogo atual por uma nova                                                                            |
|`Microsoft.UpdateActivity`| [UpdateActivity][update-activity]| Permite atualizar uma atividade que foi enviada.                                                                    |
|`Microsoft.DeleteActivity` | [DeleteActivity][delete-activity]| Permite excluir uma atividade que foi enviada.                                                                        |
|`Microsoft.GetActivityMembers` | [GetActivityMembers][get-activity-members]| Permite obter uma lista de membros da atividade e salvá-la em uma propriedade na [memória][memory-states].        |
|`Microsoft.GetConversationMembers`| [GetConversationMembers][get-conversation-members] | Permite obter uma lista de membros da conversa e salvá-la em uma propriedade na [memória][memory-states].|
|`Microsoft.EditActions`  | [EditActions][editactions] | Permite editar imediatamente a sequência de ação atual com base na entrada do usuário. Especialmente útil ao lidar com [interrupções][interruptions]. |

#### <a name="manage-properties"></a>Gerenciar propriedades

|  `$kind` valor              | Nome da ação                          | O que essa ação faz                                                     |
| --------------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
|`Microsoft.EditArray`        | [EditArray][editarray]               | Permite executar operações de edição em uma matriz.                  |
|`Microsoft.DeleteProperty`   | [DeleteProperty][deleteproperty]     | Permite remover uma propriedade da [memória][memory-states].                  |
|`Microsoft.DeleteProperties` | [DeleteProperties][deleteproperties] | Permite excluir mais de uma propriedade em uma única ação.     |
|`Microsoft.SetProperty`      | [SetProperty][setproperty]           | Permite definir o valor de uma propriedade na [memória][memory-states].               |
|`Microsoft.SetProperties`    | [SetProperties][setproperties]       | Permite inicializar uma ou mais propriedades em uma única ação. |

#### <a name="access-external-resources"></a>Acessar recursos externos

| `$kind` valor          | Nome da ação                 | O que essa ação faz                                                                                      |
| ---------------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------- |
|`Microsoft.BeginSkill`  | [BeginSkill][beginskill]    | Use a caixa de diálogo adaptável de skill para executar um skill.                                                              |
|`Microsoft.HttpRequest` | [HttpRequest][httprequest]  | Permite fazer solicitações HTTP para qualquer ponto de extremidade.                                                         |
|`Microsoft.EmitEvent`   | [EmitEvent][emitevent]      | Permite gerar um evento personalizado para o qual o bot pode responder usando um [gatilho personalizado][custom-trigger]. |
|`Microsoft.SignOutUser` | [SignOutUser][sign-out-user]| Permite desconectar o usuário atualmente conectado.                                                      |
<!-- NOTE: codeaction is not available via declarative. -->

#### <a name="debugging-options"></a>Opções de depuração

| `$kind` valor           | Nome da ação                     | O que essa ação faz                                                      |
| ----------------------- | ------------------------------- | -------------------------------------------------------------------------- |
|`Microsoft.LogAction`    | [LogAction][log-action]        | Grava no console e, opcionalmente, envia a mensagem como uma atividade de rastreamento. |
|`Microsoft.TraceActivity`| [TraceActivity][traceactivity] | Envia uma atividade de rastreamento com qualquer payload que você especificar.                   |

[log-action]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#log-action
[traceactivity]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#traceactivity
[emitevent]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#emitevent
[sign-out-user]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#sign-out-user
[editactions]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#editactions

### <a name="recognizers"></a>Reconhecedores

O SDK do bot Framework fornece mais de uma meia dúzia de reconhecedores diferentes, você especifica qual reconhecedor usar no arquivo. dialog, por exemplo:

```json
{
    "$schema": "../app.schema",
    "$kind": "Microsoft.AdaptiveDialog",
    "recognizer": {
        "$kind": "Microsoft.RegexRecognizer",
        "intents": [
            {
                "intent": "JokeIntent",
                "pattern": "(?i)joke"
            },
            {
                "intent": "FortuneTellerIntent",
                "pattern": "(?i)fortune|future"
            },
            {
                "intent": "CancelIntent",
                "pattern": "(?i)cancel"
            }
        ]
    },
    ...
}
```

Os diálogos adaptáveis dão suporte aos seguintes reconhecedores:

- [RegexRecognizer](bot-builder-concept-adaptive-dialog-recognizers.md#regexrecognizer)
- [Reconhecedor do LUIS](bot-builder-concept-adaptive-dialog-recognizers.md#luis-recognizer)
- [Reconhecedor do QnA Maker](bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer)
- [Reconhecedor de vários idiomas](bot-builder-concept-adaptive-dialog-recognizers.md#multi-language-recognizer)
- [Conjunto de reconhecedor CrossTrained](bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set)
- [RecognizerSet](bot-builder-concept-adaptive-dialog-recognizers.md#recognizer-set)

### <a name="generators"></a>Geradores

O valor [generator][generator] contém um link para o arquivo .lg associado ao diálogo adaptável que esse arquivo .dialog define. Por exemplo:

```json
{
    "$schema": "../app.schema",
    "$kind": "Microsoft.AdaptiveDialog",
    "generator": "MyBotGeneratorFile.lg",
    ...
}
```

## <a name="the-bot-framework-command-line-interface"></a>A interface do bot Framework Command-Line

Vários comandos do New [bot framework Command-Line interface (BF CLI)][bf-cli] foram adicionados com o lançamento de caixas de diálogo adaptáveis no SDK do bot Framework. Isso inclui dois comandos relacionados à caixa de diálogo para trabalhar com `.dialog` `.schema` arquivos e que são muito úteis ao usar a abordagem declarativa para o desenvolvimento de caixa de diálogo adaptável.

O novo grupo de CLI `dialog` tem os dois comandos a seguir: `dialog:merge` e `dialog:verify` .

### <a name="the-merge-command"></a>O comando Merge

O arquivo de esquema raiz contém os esquemas de todos os componentes consumidos pelo bot. Cada consumidor de arquivos declarativos, incluindo o [Composer][composer], precisa de um arquivo de esquema.

O `dialog:merge` comando é usado para gerar o arquivo de esquema do projeto. Você precisará executar esse comando sempre que adicionar um novo pacote ou criar ou modificar seus próprios componentes.

Isso cria um arquivo chamado **app. Schema** no diretório atual, a menos que especificado de outra forma usando a `-o` opção. Esse arquivo é referenciado pela `"$schema` palavra-chave em cada um dos `.dialog` arquivos em seu projeto.

> [!NOTE]
>
> Um arquivo app. Schema válido é necessário para ferramentas de _preenchimento inteligente de código_ , como o [IntelliSense][intelliSense] para trabalhar com qualquer um dos ativos declarativos.

Para usar o comando Merge, digite o seguinte no prompt de comando, enquanto no diretório raiz do seu projeto:

```cli
bf dialog:merge <filename.csproj>
```

Para obter informações adicionais sobre como usar esse comando, consulte a [caixa de diálogo BF: mesclar][bf-dialogmerge-patterns] na CLI do BF Luis Leiame.

Para obter um exemplo, consulte [criando o arquivo de esquema][creating-the-schema-file] no artigo _criar um bot usando caixas de diálogo adaptáveis declarativas_ .

<!--
> [!TIP]
>
> For users of C#: NuGet does not deal well with content files, so all declarative `.dialog`, .lu, .lg, and .qna files will be copied into `generated/<package>` so you can easily include them in your project output.
-->

### <a name="the-verify-command"></a>O comando VERIFY

O `dialog:verify` comando verificará os `.dialog` arquivos para verificar se eles são compatíveis com o esquema.

Para usar o `verify` comando, digite o seguinte no prompt de comando, enquanto no diretório raiz do seu projeto:

```cli
bf dialog:verify <filename.csproj>
```

Para obter informações adicionais sobre como usar esse comando, consulte a [caixa de diálogo BF: verificar][bf-dialogverify-patterns] na CLI do BF Luis Leiame.

> [!NOTE]
>
> O [Composer][composer] cria um arquivo mesclado `.schema` e `.dialog` arquivos válidos; no entanto, o comando VERIFY pode ser muito útil se você estiver criando esses arquivos manualmente.

### <a name="install-the-bot-framework-cli"></a>Instalar a CLI do bot Framework

[!INCLUDE [Install the Bot Framework CLI](../includes/install-bf-cli.md)]

### <a name="relevant-information"></a>Informações relevantes

- [Comandos da caixa de diálogo][dialog-commands]

## <a name="additional-information"></a>Informações adicionais

- Como [Criar um bot usando diálogos adaptáveis declarativos](bot-builder-dialogs-declarative.md)

<!-- Footnote-style links -->
[declarative-ref]: ../adaptive-dialog/adaptive-dialog-prebuilt-declarative.md
[triggers]: bot-builder-concept-adaptive-dialog-triggers.md
[actions]: bot-builder-concept-adaptive-dialog-actions.md
[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md
[concept-adaptive]: bot-builder-adaptive-dialog-introduction.md
[lg-file-format]: ../file-format/bot-builder-lg-file-format.md
[creating-the-schema-file]: bot-builder-dialogs-declarative.md#creating-the-schema-file

<!-- Declarative files section  -->
[bf-cli]: /azure/bot-service/bf-cli-overview
[bf-cli-install]: /azure/bot-service/bf-cli-overview#installation
[intellisense]: /visualstudio/ide/using-intellisense
[recognizer-types]: bot-builder-concept-adaptive-dialog-recognizers.md#recognizer-types
[intents]: bot-builder-concept-adaptive-dialog-recognizers.md#intents
[entity]: bot-builder-concept-adaptive-dialog-recognizers.md#entities
[generator]: bot-builder-concept-adaptive-dialog-generators.md

<!-- (Triggers) Recognizer event triggers  -->
[cross-trained-recognizer-set]:bot-builder-concept-adaptive-dialog-recognizers.md#cross-trained-recognizer-set
[qna-maker-recognizer]:bot-builder-concept-adaptive-dialog-recognizers.md#qna-maker-recognizer
[botframework-activity]: https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md

<!-- (Actions) Input  -->
[attachmentinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#attachmentinput
[choiceinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#choiceinput
[confirminput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#confirminput
[datetimeinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#datetimeinput
[inputdialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#inputdialog
[numberinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#numberinput
[oauthinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#oauthinput
[textinput]: ../adaptive-dialog/adaptive-dialog-prebuilt-inputs.md#textinput
[send-activity]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#send-activity

<!--  (Actions) Create a condition -->
[ifcondition]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#ifcondition
[switchcondition]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#switchcondition
[foreach]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#foreach
[foreachpage]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#foreachpage
[break-loop]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#break-loop
[continue-loop]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#continue-loop
[goto-action]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#goto-action

<!--  (Actions) Dialog management -->
[begindialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#begindialog
[cancelalldialogs]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#cancelalldialogs
[enddialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#enddialog
[endturn]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#endturn
[repeatdialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#repeatdialog
[replacedialog]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#replacedialog
[update-activity]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#update-activity
[delete-activity]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#delete-activity
[get-activity-members]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#get-activity-members
[get-conversation-members]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#get-conversation-members
[editactions]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#editactions
[memory-states]:bot-builder-concept-adaptive-dialog-memory-states.md
[interruptions]: bot-builder-concept-adaptive-dialog-interruptions.md

<!--  (Actions) Manage properties -->
[editarray]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#editarray
[deleteproperty]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#deleteproperty
[deleteproperties]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#deleteproperties
[setproperty]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#setproperty
[setproperties]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#setproperties

<!--  (Actions) Access external resources -->
[beginskill]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#beginskill
[httprequest]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#httprequest
[emitevent]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#emitevent
[sign-out-user]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#sign-out-user
[custom-trigger]: ../adaptive-dialog/adaptive-dialog-prebuilt-triggers.md#custom-event-trigger
<!--[codeaction]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#codeaction-->

<!--  (Actions) Debugging options -->
[log-action]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#log-action
[traceactivity]: ../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#traceactivity

[intelliSense]: /visualstudio/ide/using-intellisense
[composer]: https://docs.microsoft.com/composer

[dialog-commands]: https://aka.ms/botframework-cli#bf-dialog
[bf-dialogverify-patterns]: https://aka.ms/botframework-cli#bf-dialogverify-patterns
[bf-dialogmerge-patterns]: https://aka.ms/botframework-cli#bf-dialogmerge-patterns

[the-dialog-file-luis]: bot-builder-howto-bf-cli-deploy-luis.md#the-dialog-file
[the-dialog-file-qnamaker]: bot-builder-howto-bf-cli-deploy-qna.md#the-dialog-file
