---
title: Gerenciando o estado em caixas de diálogo adaptáveis – guia de referência
description: Descrevendo escopos de memória em caixas de diálogo adaptáveis
keywords: bot, estado de gerenciamento, escopos de memória, escopo do usuário, escopo da conversa, escopo da caixa de diálogo, escopo das configurações, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 06/12/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4320a8e11ebf0e0d07d59ed95b91b5c3002fec21
ms.sourcegitcommit: 757ddf4f3fd3557ad9746dfa83ca742d54ac4763
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86876356"
---
# <a name="managing-state-in-adaptive-dialogs---reference-guide"></a>Gerenciando o estado em caixas de diálogo adaptáveis – guia de referência

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Este artigo fornece detalhes técnicos que irão ajudá-lo a trabalhar com escopos de memória em caixas de diálogo adaptáveis. Para obter uma introdução aos escopos de memória e gerenciar o estado em caixas de diálogo adaptáveis, veja o artigo [Gerenciamento de estado no conceito de caixas de diálogo adaptáveis][managing-state] .

<!--
Memory scopes available in adaptive dialogs:

* [User scope](#user-scope)
* [Conversation scope](#conversation-scope)
* [Dialog scope](#dialog-scope)
  * [Dialog sub-scopes](#dialog-sub-scopes)
* [Turn scope](#turn-scope)
  * [Turn sub-scopes](#turn-sub-scopes)
* [Settings scope](#settings-scope)
* [This scope](#this-scope)
* [Class scope](#class-scope)
-->

> [!TIP]
> Todos os caminhos de propriedades não diferenciam maiúsculas de minúsculas. Por exemplo, `user.name` é o mesmo que `user.Name`. Além disso, se você não tiver uma propriedade chamada `user.name` e criar uma propriedade chamada `user.name.first`, o objeto `user.name` será criado automaticamente para você.

## <a name="user-scope"></a>Escopo do usuário

O escopo do usuário são dados persistentes no escopo da ID do usuário com o qual você está conversando.  

Exemplos:

    user.name
    user.address.city

## <a name="conversation-scope"></a>Escopo da conversa

O escopo da conversa são dados persistentes no escopo da ID da conversa que você está tendo.  

Exemplos:

    conversation.hasAccepted
    conversation.dateStarted
    conversation.lastMaleReference
    conversation.lastFemaleReference
    conversation.lastLocationReference

No exemplo a seguir demonstra como você pode usar o escopo da conversa para coletar entrada do usuário, criando um novo `PropertyAssignment` objeto para uso na `SetProperties` [Action] [SetProperties-Action], obtendo o valor do escopo da conversa.

```csharp
new PropertyAssignment()
{
    Property = "conversation.flightBooking.departureCity",
    Value = "=turn.recognized.entities.fromCity.location"
},
```

## <a name="dialog-scope"></a>Escopo do diálogo

O escopo do diálogo persiste os dados durante a vida útil do diálogo associado, fornecendo espaço de memória para cada diálogo ter uma escrituração persistente interna. O escopo do diálogo é limpo quando o diálogo associado é encerrado.

Exemplos abreviados de escopo do diálogo:

```markdown
The shorthand for `dialog.orderStarted`:

    $orderStarted

The shorthand for `dialog.shoppingCart`:

    $shoppingCart
```

Todas as opções passadas para `BeginDialog` ao criar um diálogo adaptável tornam-se propriedades desse diálogo e podem ser acessadas desde que estejam no escopo. Você acessa essas propriedades pelo nome: dialog.\<propertyName>. Por exemplo, se o chamador passou {a: '1', b: '2'}, elas serão definidas como dialog.a e dialog.b.

### <a name="dialog-sub-scopes"></a>Subescopos do diálogo

Todas as ações de gatilho em um diálogo adaptável têm os próprios subescopos e são acessadas pelo nome, por exemplo, a ação `Foreach` é acessada como `dialog.Foreach`. Por padrão, o índice e o valor são definidos no escopo `dialog.foreach`, que pode ser acessado como `dialog.Foreach.index` e `dialog.Foreach.value`. Você pode ler mais sobre a [`Foreach`][foreach-action] ação no tópico **ações em caixas de diálogo adaptáveis** .

## <a name="turn-scope"></a>Escopo do turno

O escopo do turno contém dados _não persistentes_ que têm o escopo definido apenas para o turno atual. O escopo do turno fornece um local para compartilhar dados durante o tempo de vida do turno atual.  

### <a name="this-scope-example"></a>Exemplo de Este escopo

    turn.bookingConfirmation
    turn.activityProcessed

### <a name="turn-sub-scopes"></a>Subescopos do turno

#### <a name="turnactivity"></a>turn.activity

Cada [atividade][botframework-activity] de entrada no bot está disponível por meio do escopo `turn.activity`.

Por exemplo, você pode ter algo como isso definido no arquivo .lg para responder a um usuário que inseriu um valor inválido quando a idade dele foi solicitada:

```.lg
Sorry, I do not understand '${turn.activity.text}'. ${GetAge()}
```

Ou então, definir os valores da propriedade no código-fonte dos diálogos:

```c#
ItemsProperty = "turn.activity.membersAdded"
```

#### <a name="turnrecognized"></a>turn.recognized

Todas as intenções e entidades retornadas de um [reconhecedor][recognizers] em qualquer turno especificado são definidas automaticamente no escopo `turn.recognized` e permanecem disponíveis até que o próximo turno ocorra. o escopo `turn.recognized` tem três propriedades:

* `turn.recognized.intents.xxx`: uma lista das principais intenções classificadas pelo reconhecedor para esse turno.
* `turn.recognized.entities.xxx`: uma lista das entidades reconhecidas nesse turno.
* `turn.recognized.score`: A _pontuação de confiança_ da principal intenção de pontuação desse turno.

Por exemplo, um aplicativo de reservas de voo pode ter uma intenção de reserva de voo com uma entidade para os destinos de partida e outra para os destinos de chegada. O exemplo abaixo demonstra como capturar o valor de destino de partida antes que o turno seja encerrado.

```c#
Value = "=turn.recognized.entities.fromCity.location"
```

Há outra maneira de fazer isso usando a [notação de memória curta](../v4sdk/bot-builder-concept-adaptive-dialog-memory-states.md#memory-short-hand-notations).

```c#
// Value is a property containing an expression. @entityName is shorthand to refer to the value of
// the entity recognized. @fromCity.location is same as turn.recognized.entities.fromCity.location
Value = "=@fromCity.location"
```

#### <a name="turndialogevent"></a>turn.dialogEvent

`turn.dialogEvent` contém o conteúdo de um evento gerado pelo sistema ou pelo código. Você pode acessar as informações contidas no conteúdo acessando escopo turn.dialogEvent.\<eventName\>.value.

#### <a name="turnlastresult"></a>turn.lastResult

 Acesse os resultados do último diálogo que foi chamado por meio do escopo `turn.lastResult`.

#### <a name="turnactivityprocessed"></a>turn.activityProcessed

`turn.activityProcessed`, uma propriedade booliana que, se definida, significa que `turnContext.activity` foi consumida por algum componente no sistema.

#### <a name="turninterrupted"></a>turn.interrupted

`turn.interrupted`, uma propriedade booliana; `true` indica que ocorreu uma interrupção.

## <a name="settings-scope"></a>Escopo das configurações

Isso representa qualquer configuração disponibilizada para o bot por meio do sistema de definição das configurações específicas da plataforma. Por exemplo, se você estiver desenvolvendo o seu bot usando o C#, essas configurações serão exibidas no arquivo appsettings.json; se estiver desenvolvendo o bot usando o JavaScript, essas configurações serão exibidas no arquivo .env ou no arquivo config.py no desenvolvimento com o Python. Além disso, algumas configurações estão contidas nas configurações de ambiente dinâmico no Azure e todas estão disponíveis no escopo das configurações.

### <a name="settings-scope-example"></a>Exemplo de escopo das configurações
<!--TODO P2: rewrite this with language tabs for C#/JS. -->
Este é um exemplo de um arquivo appsettings.json que contém definições de configuração para o bot:

```json
{
    "MicrosoftAppId": "<yourMicrosoftAppId>",
    "MicrosoftAppPassword": "<yourMicrosoftAppPassword>",
    "QnAMaker": {
        "knowledgebaseId": "<yourQnAKnowledgebaseId>",
        "hostname": "https://<YourHostName>.azurewebsites.net/qnamaker",
        "endpointKey": "yourEndpointKey"
    }
}
```

Este é um exemplo de como se referir às definições de configuração armazenadas no arquivo appsettings.json usando o escopo de memória de configurações:

```csharp
var recognizer = new QnAMakerRecognizer()
{
    HostName = settings.QnAMaker.hostname,
    EndpointKey = settings.QnAMaker:endpointKey,
    KnowledgeBaseId = settings.QnAMaker:knowledgebaseId,
};
```

## <a name="this-scope"></a>Este escopo

O escopo `this` pertence ao recipiente de propriedades da ação ativa. Isso é útil para ações de entrada, pois o tipo de vida normalmente ultrapassa um só turno da conversa.

* `this.value` contém o valor reconhecido atual para a entrada.
* `this.turnCount` contém o número de vezes que as informações ausentes foram solicitadas para essa entrada.

Este exemplo mostra um uso comum em uma classe de inicialização de bots:

### <a name="this-scope-example"></a>Exemplo de Este escopo

```csharp
new TextInput()
{
    property = "user.name",
    prompt = new ActivityTemplate("what is your name?"),
    defaultValue = "Human",
    defaultValueResponse = new ActivityTemplate("Sorry, I still am not getting it after ${this.turnCount} attempts. For now, I'm setting your name to '${class.defaultValue}' for now. You can always say 'My name is <your name>' to re-introduce yourself to me.")
}
```

## <a name="class-scope"></a>Escopo da classe

Isso contém as propriedades de instância do diálogo ativo. referencie esse escopo da seguinte maneira: `${class.<propertyName>}`.

### <a name="class-scope-example"></a>Exemplo de escopo da classe

```csharp
new TextInput()
{
  Property = "user.age"
  Prompt = new ActivityTemplate("What is your age?"),
  DefaultValue = "30",
  DefaultValueResponse = new ActivityTemplate("Sorry, I'm not getting it in spite of you trying ${this.turnCount} number of times. \n I'm going with ${class.defaultValue} for now.")
}
```

## <a name="additional-information"></a>Informações adicionais

* Para obter uma introdução ao gerenciamento de estado em caixas de diálogo adaptáveis, veja o artigo [Gerenciamento de estado no conceito de caixas de diálogo adaptáveis][managing-state] .
* [Notação curta de memória](../v4sdk/bot-builder-concept-adaptive-dialog-memory-states.md#memory-short-hand-notations).

[managing-state]: ../v4sdk/bot-builder-concept-adaptive-dialog-memory-states.md
[foreach-action]: ./adaptive-dialog-prebuilt-actions.md#foreach
[botframework-activity]: https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md
[recognizers]: ../v4sdk/bot-builder-concept-adaptive-dialog-recognizers.md
