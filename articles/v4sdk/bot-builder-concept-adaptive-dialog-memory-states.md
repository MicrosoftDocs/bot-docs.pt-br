---
title: Como gerenciar o estado em diálogos adaptáveis
description: Como gerenciar o estado em diálogos adaptáveis
keywords: bot, Managing state, User scope, Conversation scope, Dialog scope, Settings scope, adaptive dialogs
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/08/2020
ms.openlocfilehash: 98ea298a9e281625f94611e9ea84251c548c82e3
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073905"
---
# <a name="managing-state-in-adaptive-dialogs"></a>Como gerenciar o estado em diálogos adaptáveis

Os termos _com estado_ e _sem estado_ são expressões que descrevem se um aplicativo foi projetado para lembrar um ou mais eventos anteriores em determinada sequência de interações com um usuário (ou qualquer outra atividade). Com estado significa que o aplicativo _acompanha_ o estado das interações, normalmente salvando os valores na memória na forma de propriedades. Sem estado significa que o aplicativo _não_ acompanha o estado das interações, o que significa que não há memória de nenhuma interação anterior e todas as solicitações de entrada precisam conter todas as informações relevantes necessárias para executar a ação solicitada. Considere o _estado_ como o conjunto atual de valores ou do conteúdo do bot, como a ID da conversa ou o nome do usuário ativo.

O SDK do Bot Framework segue os mesmos paradigmas dos aplicativos Web modernos e não gerencia ativamente o estado. No entanto, o SDK do Bot Framework fornece algumas abstrações que facilitam muito a incorporação do gerenciamento de estado ao bot. Esse tópico é abordado detalhadamente no artigo [Como gerenciar o estado][3] do SDK do Bot Framework. Recomendamos que você leia e entenda as informações abordadas nele antes de ler este artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma compreensão geral de [Como funcionam os bots][1].
* Uma compreensão geral dos diálogos adaptáveis. Para obter mais informações, confira [Introdução aos diálogos adaptáveis][2] e [Bibliotecas de diálogos][8].
* Confira o artigo [Como gerenciar o estado][3] do SDK do Bot Framework para obter uma visão geral do gerenciamento de estado.

## <a name="managing-state"></a>Gerenciar estado

Um bot é inerentemente sem estado. Para alguns bots, o bot pode operar sem informações adicionais ou há a garantia de que as informações necessárias estão contidas na mensagem de entrada. Para outros bots, o estado (por exemplo, em que ponto da conversa estamos ou a resposta recebida do usuário) é necessário para que o bot tenha uma conversa útil.

O SDK do Bot Framework define escopos de memória para ajudar os desenvolvedores a armazenar e recuperar os valores na memória do bot, para uso durante o processamento de loops e branches, ao criar mensagens dinâmicas e outros comportamentos no bot.

Isso possibilita que os bots criados com o SDK do Bot Framework executem ações como:

* Passar informações entre diálogos.
* Armazenar perfis e preferências do usuário.
* Lembrar-se de informações entre as sessões como a última consulta de pesquisa ou a última seleção feita pelo usuário.

Os diálogos adaptáveis oferecem um modo de acesso e de gerenciamento de memória e todos os diálogos adaptáveis usam esse modelo, o que significa que todos os componentes que fazem leituras ou gravações na memória têm uma forma consistente de lidar com as informações dentro dos escopos apropriados.

> [!NOTE]
> Todas as propriedades da memória, em todos os escopos da memória, são recipientes de propriedades, o que significa que você pode adicionar propriedades a eles conforme necessário.

Estes são os diferentes escopos de memória disponíveis nos diálogos adaptáveis:

* [Escopo do usuário](#user-scope)
* [Escopo da conversa](#conversation-scope)
* [Escopo do diálogo](#dialog-scope)
  * [Subescopos do diálogo](#dialog-sub-scopes)
* [Escopo do turno](#turn-scope)
  * [Subescopos do turno](#turn-sub-scopes)
* [Escopo das configurações](#settings-scope)
* [Este escopo](#this-scope)
* [Escopo da classe](#class-scope)
* [Notações abreviadas da memória](#memory-short-hand-notations)

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

O exemplo a seguir demonstra como você poderá usar o escopo da conversa para coletar a entrada do usuário, criando um objeto `PropertyAssignment` para uso na [ação][7] `SetProperties`, obtendo o valor do escopo da conversa.

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

Todas as ações de gatilho em um diálogo adaptável têm os próprios subescopos e são acessadas pelo nome, por exemplo, a ação `Foreach` é acessada como `dialog.Foreach`. Por padrão, o índice e o valor são definidos no escopo `dialog.foreach`, que pode ser acessado como `dialog.Foreach.index` e `dialog.Foreach.value`. Leia mais sobre a ação `Foreach` em [Ações em diálogos adaptáveis][6].

## <a name="turn-scope"></a>Escopo do turno

O escopo do turno contém dados _não persistentes_ que têm o escopo definido apenas para o turno atual. O escopo do turno fornece um local para compartilhar dados durante o tempo de vida do turno atual.  

### <a name="this-scope-example"></a>Exemplo de Este escopo

    turn.bookingConfirmation
    turn.activityProcessed

### <a name="turn-sub-scopes"></a>Subescopos do turno

#### <a name="turnactivity"></a>turn.activity

Cada [atividade][5] de entrada no bot está disponível por meio do escopo `turn.activity`.

Por exemplo, você pode ter algo como isso definido no arquivo .lg para responder a um usuário que inseriu um valor inválido quando a idade dele foi solicitada:

```.lg
Sorry, I do not understand '${turn.activity.text}'. ${GetAge()}
```

Ou então, definir os valores da propriedade no código-fonte dos diálogos:

```c#
ItemsProperty = "turn.activity.membersAdded"
```

#### <a name="turnrecognized"></a>turn.recognized

Todas as intenções e entidades retornadas de um [reconhecedor][4] em qualquer turno especificado são definidas automaticamente no escopo `turn.recognized` e permanecem disponíveis até que o próximo turno ocorra. o escopo `turn.recognized` tem três propriedades:

* `turn.recognized.intents.xxx`: uma lista das principais intenções classificadas pelo reconhecedor para esse turno.
* `turn.recognized.entities.xxx`: uma lista das entidades reconhecidas nesse turno.
* `turn.recognized.score`: A _pontuação de confiança_ da principal intenção de pontuação desse turno.

Por exemplo, um aplicativo de reservas de voo pode ter uma intenção de reserva de voo com uma entidade para os destinos de partida e outra para os destinos de chegada. O exemplo abaixo demonstra como capturar o valor de destino de partida antes que o turno seja encerrado.

```c#
Value = "=turn.recognized.entities.fromCity.location"
```

Há outra maneira de fazer isso usando uma [notação abreviada de memória](#memory-short-hand-notations)

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

## <a name="memory-short-hand-notations"></a>Notações abreviadas da memória

Há poucas notações abreviadas compatíveis para acessar os escopos da memória específicos.

| Símbolo | Uso           | Expansão                             | Observações                                                                                                                   |
|--------|-----------------|---------------------------------------|------------------------------------------------------------------------------------------------------------------------ |
| $      | `$userName`     | `dialog.userName`                     | Notação abreviada que representa o escopo do diálogo.                                                                   |
| #      | `#intentName`   | `turn.recognized.intents.intentName`  | Abreviação usada para indicar uma intenção nomeada retornada pelo reconhecedor.                                                    |
| @      | `@entityName`   | `turn.recognized.entities.entityName` | `@entityName` retorna o primeiro e o _único_ primeiro valor encontrado para a entidade, independentemente da cardinalidade do valor. |
| @@     | `@@entityName`  | `turn.recognized.entities.entityName` | O `@@entityName` retornará o valor real da entidade, preservando a cardinalidade do valor.                          |
| %      | `%propertyName` | `class.propertyName`                  | Usado para fazer referência a propriedades de instância (por exemplo, `MaxTurnCount`, `DefaultValue` etc.).                                         |

[1]:bot-builder-basics.md
[2]:bot-builder-adaptive-dialog-introduction.md
[3]:bot-builder-concept-state.md
[4]:bot-builder-concept-adaptive-dialog-recognizers.md
[5]:https://github.com/microsoft/botbuilder/blob/master/specs/botframework-activity/botframework-activity.md
[6]:bot-builder-concept-adaptive-dialog-actions.md#foreach
[7]:bot-builder-concept-adaptive-dialog-actions.md#setproperties
[8]:bot-builder-concept-dialog.md
