---
title: Enviar notificações proativas para os usuários – Serviço de Bot
description: Entenda como enviar mensagens de notificação
keywords: mensagem proativa, mensagem de notificação, notificação de bot,
author: jonathanfingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/24/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 61fb04f7f0e65bbc4f1730cac9cacf8ed47af6a8
ms.sourcegitcommit: 5add21ad3daf0ce894612a22b951b98350961720
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420317"
---
# <a name="send-proactive-notifications-to-users"></a>Enviar notificações proativas para os usuários

[!INCLUDE[applies-to](../includes/applies-to.md)]

Normalmente, cada mensagem que um bot envia ao usuário está diretamente relacionada à entrada anterior do usuário.
Em alguns casos, talvez o bot precise enviar ao usuário uma mensagem sem relação direta com o tópico atual da conversa ou com a última mensagem enviada pelo usuário. Esses tipos de mensagens são chamados _mensagens proativas_.

Mensagens proativas podem ser útil em uma variedade de cenários. Por exemplo, se o usuário tiver solicitado ao bot o monitoramento do preço de um produto, se o preço do produto cair 20%, o bot poderá alertar o usuário. Ou, se o bot precisar de um tempo para compilar uma resposta para a pergunta do usuário, ele poderá informar ao usuário sobre o atraso e permitir que a conversa continue enquanto isso. Quando o bot terminar de compilar a resposta para a pergunta, compartilhará essas informações com o usuário.

Ao implementar mensagens proativas em seu bot, não envie várias mensagens proativas em um curto período de tempo. Alguns canais impõem restrições sobre a frequência de envio de mensagens para o usuário de um bot, e desabilitarão o bot se ele violar essas restrições.

Uma mensagem proativa ad hoc é o tipo mais simples de mensagem proativa. O bot apenas injeta a mensagem na conversa sempre que ele é disparado, sem considerar se o usuário está envolvido em um tópico separado de conversa com o bot e não tentará alterar a conversa de modo algum.

Para lidar com notificações de forma mais suave, considere outras maneiras de integrar a notificação no fluxo de conversa, como definir um sinalizador no estado da conversa ou adicionar a notificação a uma fila.

## <a name="prerequisites"></a>Pré-requisitos

- Entenda sobre as [Noções básicas do bot](bot-builder-basics.md).
- Uma cópia do exemplo de mensagens proativas em [**C#** ](https://aka.ms/proactive-sample-cs), [**JavaScript**](https://aka.ms/proactive-sample-js) ou [**Python**](https://aka.ms/bot-proactive-python-sample-code). O exemplo é usado para explicar sobre mensagens proativas neste artigo.

## <a name="about-the-proactive-sample"></a>Sobre a amostra proativa

A amostra tem um bot e um controlador adicional que é usado para enviar mensagens proativas para o bot, conforme mostrado na ilustração a seguir.

![bot proativo](media/proactive-sample-bot.png)

## <a name="retrieve-and-store-conversation-reference"></a>Como recuperar e armazenar a referência de conversa

Quando o emulador se conecta ao bot, o bot recebe duas atividades de atualização de conversa. No manipulador de atividade de atualização de conversa do bot, a referência de conversa é recuperada e armazenada em um dicionário, conforme mostrado abaixo.

# <a name="c"></a>[C#](#tab/csharp)

**Bots\ProactiveBot.cs**

[!code-csharp[OnConversationUpdateActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/16.proactive-messages/Bots/ProactiveBot.cs?range=26-37&highlight=3-4,9)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/proactiveBot.js**

[!code-javascript[onConversationUpdateActivity](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/bots/proactiveBot.js?range=13-17&highlight=2)]

[!code-javascript[onConversationUpdateActivity](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/bots/proactiveBot.js?range=41-44&highlight=2-3)]

# <a name="python"></a>[Python](#tab/python)

**bots/proactive_bot.py** [!code-python[on_conversation_update_activity](~/../botbuilder-samples/samples/python/16.proactive-messages/bots/proactive_bot.py?range=14-16&highlight=2)]

[!code-python[on_conversation_update_activity](~/../botbuilder-samples/samples/python/16.proactive-messages/bots/proactive_bot.py?range=35-45)]

---

Observação: Em um cenário real, você manteria as referências de conversa em um banco de dados em vez de usar um objeto na memória.

A referência de conversa tem uma propriedade de _conversa_ propriedade que descreve a conversa na qual existe atividade. A conversa tem uma propriedade de _usuário_ que lista os usuários que participam da conversa, e uma propriedade de _URL do serviço_ que os canais usam para indicar a URL onde as respostas para a atividade atual podem ser enviado. Uma referência de conversa válida é necessária para enviar mensagens proativas para os usuários.

## <a name="send-proactive-message"></a>Como enviar mensagens proativas

O segundo controlador, o controlador _notificar_, é responsável por enviar a mensagem proativa para o bot. Use as etapas a seguir para gerar uma mensagem proativa.

1. Recupere a referência para a conversa à qual você deseja enviar a mensagem proativa.
1. Chame o método _continuar a conversa_ do adaptador, fornecendo a referência da conversa e a o manipulador de turno delegado para ser usado. O método “continuar conversa” gera um contexto de turno para a conversa referenciada, chamando o manipulador de turno delegado especificado.
1. Ao delegar, use o contexto de turno para enviar a mensagem proativa.

# <a name="c"></a>[C#](#tab/csharp)

**Controllers\NotifyController .cs**

Sempre que a página de notificação do bot é solicitada, o controlador de notificação recupera as referências de conversa a partir do dicionário.
Então, o controlador usa os métodos `ContinueConversationAsync` e `BotCallback` para enviar a mensagem proativa.

[!code-csharp[Notify logic](~/../botbuilder-samples/samples/csharp_dotnetcore/16.proactive-messages/Controllers/NotifyController.cs?range=17-62&highlight=28,40-44)]

Para enviar uma mensagem proativa, o adaptador requer uma ID do aplicativo para o bot. Em um ambiente de produção, você pode usar a ID do aplicativo do bot. Em um ambiente de teste local, você pode usar qualquer GUID. Se no momento o bot não estiver atribuído a uma ID do aplicativo, o controlador de notificação gera automaticamente um espaço reservado para ID a ser usado para a chamada.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

Sempre que a página `/api/notify` do servidor for solicitada, o servidor recuperará as referências de conversa a partir do dicionário.
Então, o servidor usa o método `continueConversation` para enviar a mensagem proativa.
O parâmetro para `continueConversation` é uma função que serve como manipulador de turno do bot para este turno.

[!code-javascript[Notify logic](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/index.js?range=68-82&highlight=4-8)]

# <a name="python"></a>[Python](#tab/python)

Sempre que a página de notificação do bot for solicitada, o servidor vai recuperar as referências da conversa do dicionário.
Em seguira, o servidor usará o `_send_proactive_message` para enviar a mensagem proativa.

[!code-python[Notify logic](~/../botbuilder-samples/samples/python/16.proactive-messages/app.py?range=98-106&highlight=5-9)]

---

## <a name="test-your-bot"></a>Testar seu bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador e conecte ao seu bot.
1. Carregar à página de api/notificação do seu bot. Isso gerará uma mensagem proativa no emulador.

## <a name="additional-information"></a>Informações adicionais

Além do exemplo usado neste artigo, amostrar adicionais estão disponíveis em C# e JS no [GitHub](https://github.com/Microsoft/BotBuilder-Samples/).

### <a name="avoiding-401-unauthorized-errors"></a>Como evitar erros 401 "Não Autorizados"

Por padrão, o SDK BotBuilder adiciona um `serviceUrl` à lista de nomes de hosts confiáveis se a solicitação de entrada for autenticada por BotAuthentication. Eles são mantidos em um cache na memória. Se o bot for reiniciado, um usuário que estiver aguardando por uma mensagem proativa não vai recebê-la, a menos que tenha enviado uma mensagem ao bot novamente depois da reinicialização.

Para evitar isso, você deve adicionar manualmente o `serviceUrl` à lista de nomes de host confiáveis, usando:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
MicrosoftAppCredentials.TrustServiceUrl(serviceUrl);
```

Para enviar mensagens proativas, `serviceUrl` é a URL do canal, que o destinatário da mensagem proativa estará usando, e pode ser encontrado no `Activity.ServiceUrl`.

Você deve adicionar o código acima antes do código que envia a mensagem proativa. No [Exemplo de Mensagens Proativas](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages), você deve colocá-lo em `NotifyController.cs` logo antes de `await turnContext.SendActivityAsync("proactive hello");`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
MicrosoftAppCredentials.trustServiceUrl(serviceUrl);
```

Para enviar mensagens proativas, `serviceUrl` é a URL do canal, que o destinatário da mensagem proativa estará usando, e pode ser encontrado no `activity.serviceUrl`.

Você deve adicionar o código acima antes do código que envia a mensagem proativa. No [Exemplo de Mensagens Proativas](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages), você deve colocá-lo em `index.js` logo antes de `await turnContext.sendActivity('proactive hello');`.

# <a name="python"></a>[Python](#tab/python)

```python
MicrosoftAppCredentials.trustServiceUrl(serviceUrl)
```

Para enviar mensagens proativas, `serviceUrl` é a URL do canal, que o destinatário da mensagem proativa estará usando, e pode ser encontrado no `activity.serviceUrl`.

Você deve adicionar o código acima antes do código que envia a mensagem proativa. No [Exemplo de Mensagens Proativas](https://aka.ms/bot-proactive-python-sample-code), adicione-o ao `app.py` antes de enviar a mensagem *proactive hello*.

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implementar o fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md)
