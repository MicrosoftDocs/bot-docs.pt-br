---
title: Enviar e receber mensagem de texto – Serviço de Bot
description: Saiba mais sobre como enviar e receber mensagens de texto dentro do SDK do Bot Framework.
keywords: enviar mensagens, atividades de mensagem, mensagem de texto simples, mensagem, mensagem de texto, receber mensagens
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9dc5bfeab8bc56e81888be5e9463be167fcd2b18
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77071804"
---
# <a name="send-and-receive-text-message"></a>Enviar e receber mensagens de texto

[!INCLUDE[applies-to](../includes/applies-to.md)]

A principal maneira que o bot se comunicará com usuários e, da mesma forma, receberá comunicação, é por meio de atividades de **mensagem**. Algumas mensagens simplesmente podem conter texto sem formatação, enquanto outras podem ter conteúdo mais elaborado, como cartões ou anexos. O manipulador do turno do bot recebe mensagens do usuário e, a partir daí, é possível enviar respostas ao usuário. O objeto de contexto do turno fornece métodos para enviar mensagens de volta ao usuário. Este artigo descreve como enviar mensagens de texto simples.

O markdown tem suporte na maioria dos campos de texto, mas o suporte pode variar por canal.

Para um bot em execução que envia e recebe mensagens, siga os guias de início rápido na parte superior do sumário ou confira o [artigo sobre o funcionamento de bots](bot-builder-basics.md#bot-structure), que também fornece links para exemplos simples disponíveis para execução por conta própria.

## <a name="send-a-text-message"></a>Enviar uma mensagem de texto

Para enviar uma mensagem de texto simples, especifique a cadeia de caracteres que você deseja enviar como a atividade:

# <a name="c"></a>[C#](#tab/csharp)

Nos manipuladores de atividade do bot, use o método `SendActivityAsync` de mudança de contexto do objeto para enviar uma resposta de mensagem individual. Também é possível usar o método `SendActivitiesAsync` do objeto para enviar várias respostas ao mesmo tempo.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nos manipuladores de atividade do bot, use o método `sendActivity` de mudança de contexto do objeto para enviar uma resposta de mensagem individual. Também é possível usar o método `sendActivities` do objeto para enviar várias respostas ao mesmo tempo.

```javascript
await context.sendActivity("Welcome!");
```

# <a name="python"></a>[Python](#tab/python)

Nos manipuladores de atividade do bot, use o método `send_activity` de mudança de contexto do objeto para enviar uma resposta de mensagem individual.

```python
await turn_context.send_activity("Welcome!")
```

---
## <a name="receive-a-text-message"></a>Receber uma mensagem de texto

Para receber uma mensagem de texto simples, use a propriedade de *texto* do objeto de *atividade*. 

# <a name="c"></a>[C#](#tab/csharp)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem.

```javascript
let text = turnContext.activity.text;
```

# <a name="python"></a>[Python](#tab/python)

Nos manipulares de atividade do bot, use o código a seguir para receber uma mensagem.

```python
response = context.activity.text
```

---

## <a name="send-a-typing-indicator"></a>Enviar um indicador de digitação
Os usuários esperam uma resposta em tempo hábil às suas mensagens. Se o seu bot executar alguma tarefa de longa duração, como chamar um servidor ou executar uma consulta sem dar ao usuário alguma indicação de que o bot ouviu, o usuário pode ficar impaciente e enviar mensagens adicionais ou simplesmente assumir que o bot está quebrado.

Os canais de bot de Webchat e Direct Line são compatíveis com o envio de uma indicação de digitação para mostrar ao usuário que a mensagem foi recebida e está sendo processada. Lembre-se de que o bot precisa deixar o ciclo terminar em 15 segundos ou o serviço Conector atingirá o tempo limite. Para processos mais longos, leia mais sobre como enviar [mensagens proativas](bot-builder-howto-proactive-message.md). 

O exemplo a seguir demonstra como enviar uma indicação de digitação.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    if (string.Equals(turnContext.Activity.Text, "wait", System.StringComparison.InvariantCultureIgnoreCase))
    {
        await turnContext.SendActivitiesAsync(
            new Activity[] {
                new Activity { Type = ActivityTypes.Typing },
                new Activity { Type = "delay", Value= 3000 },
                MessageFactory.Text("Finished typing", "Finished typing"),
            },
            cancellationToken);
    }
    else
    {
        var replyText = $"Echo: {turnContext.Activity.Text}. Say 'wait' to watch me type.";
        await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
this.onMessage(async (context, next) => {
    if (context.activity.text === 'wait') {
        await context.sendActivities([
            { type: ActivityTypes.Typing },
            { type: 'delay', value: 3000 },
            { type: ActivityTypes.Message, text: 'Finished typing' }
        ]);
    } else {
        await context.sendActivity(`You said '${ context.activity.text }'. Say "wait" to watch me type.`);
    }
    await next();
});
```

# <a name="python"></a>[Python](#tab/python)

```python
async def on_message_activity(self, turn_context: TurnContext):
    if turn_context.activity.text == "wait":
        return await turn_context.send_activities([
            Activity(
                type=ActivityTypes.typing
            ),
            Activity(
                type="delay",
                value=3000
            ),
            Activity(
                type=ActivityTypes.message,
                text="Finished Typing"
            )
        ])
    else:
        return await turn_context.send_activity(
            f"You said {turn_context.activity.text}.  Say 'wait' to watch me type."
        )
```

---

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações sobre o processamento de atividade, confira [processamento de atividade](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack).
- Para obter mais informações sobre a formatação, confira a [seção de atividades de mensagem](https://aka.ms/botSpecs-activitySchema#message-activity) do esquema de Atividade do Bot Framework.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar mídia às mensagens](./bot-builder-howto-add-media-attachments.md)
