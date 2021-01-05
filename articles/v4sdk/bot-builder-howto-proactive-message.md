---
title: Enviar notificações proativas para os usuários – Serviço de Bot
description: Saiba como os bots enviam mensagens de notificação. Consulte como recuperar referências de conversa e testar mensagens proativas. Exibir exemplos de código e considerações de design.
keywords: mensagem proativa, mensagem de notificação, notificação de bot,
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 779ca565acd4e2ee71dbe1b569676db8c9fbcb2d
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759015"
---
# <a name="send-proactive-notifications-to-users"></a>Enviar notificações proativas para os usuários

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Normalmente, um bot envia uma mensagem a um usuário diretamente em resposta ao recebimento de uma mensagem do usuário.
Ocasionalmente, um bot pode precisar enviar uma _mensagem proativa_, uma mensagem em resposta ao estímulo não originado do usuário.

Mensagens proativas podem ser útil em uma variedade de cenários. Por exemplo, se o usuário tiver solicitado ao bot o monitoramento do preço de um produto, se o preço do produto cair 20%, o bot poderá alertar o usuário. Ou, se o bot precisar de um tempo para compilar uma resposta para a pergunta do usuário, ele poderá informar ao usuário sobre o atraso e permitir que a conversa continue enquanto isso. Quando o bot terminar de compilar a resposta para a pergunta, compartilhará essas informações com o usuário.

## <a name="requirements"></a>Requisitos

Antes que você possa enviar uma mensagem proativa, seu bot precisa de uma _referência de conversa_. O bot pode recuperar a referência de conversa de qualquer atividade recebida do usuário, mas isso normalmente requer que o usuário interaja com o bot pelo menos uma vez antes que o bot possa enviar uma mensagem proativa.

Muitos canais proíbem um bot de enviar mensagens para um usuário, a menos que o usuário tenha acionado o bot pelo menos uma vez. Alguns canais permitem exceções. Por exemplo, o canal de equipes permite que o bot envie uma mensagem proativa (ou 1-em-1) para indivíduos em uma conversa de grupo já estabelecida que inclua o bot.

Mais informações sobre as mensagens proativas em equipes podem ser encontradas nesses recursos:

- O exemplo de **bot de conversa de equipes** em [**C#**](https://aka.ms/cs-teams-conversations-sample), [**JavaScript**](https://aka.ms/js-teams-conversations-sample)ou [**Python**](https://aka.ms/py-teams-conversations-sample).
- Documentação do Microsoft Teams sobre como [enviar mensagens proativas](/microsoftteams/platform/bots/how-to/conversations/send-proactive-messages)

## <a name="prerequisites"></a>Pré-requisitos

- Entenda sobre as [Noções básicas do bot](bot-builder-basics.md).
- Uma cópia do exemplo de **mensagens proativas** em [**C#**](https://aka.ms/proactive-sample-cs), [**JavaScript**](https://aka.ms/proactive-sample-js)ou [**Python**](https://aka.ms/bot-proactive-python-sample-code). O exemplo é usado para explicar sobre mensagens proativas neste artigo.

## <a name="about-the-proactive-sample"></a>Sobre a amostra proativa

Em geral, um bot como um aplicativo tem algumas camadas:

- O aplicativo Web que pode aceitar solicitações HTTP e especificamente dá suporte a um ponto de extremidade de mensagens.
- Um adaptador que lida com a conectividade com os canais.
- Um manipulador para a ativação, normalmente encapsulado em uma classe _bot_ que manipula o raciocínio de conversação para o aplicativo bot.

Em resposta a uma mensagem de entrada do usuário, o aplicativo chama o método de _atividade de processo_ do adaptador, que cria um contexto de ativar e desativar, chama seu pipeline de middleware e, em seguida, chama o manipulador de folheio do bot.

Para iniciar uma mensagem proativa, o aplicativo bot precisa ser capaz de receber entradas adicionais.
A lógica do aplicativo para iniciar uma mensagem proativa está fora do escopo do SDK.
Para este exemplo, um ponto de extremidade de _notificação_ , além de um ponto de extremidade de _mensagens_ padrão, é usado para disparar a ativação proativa.

Em resposta a uma solicitação GET nesse ponto de extremidade notificar, o aplicativo chama o método _continue Conversation_ do adaptador, que se comporta de forma semelhante ao método de _atividade Process_ . O método _continue Conversation_ :

- Usa uma referência de conversa apropriada para o usuário e o método de retorno de chamada a ser usado para a opção proativa.
- Cria uma atividade de evento e transforma o contexto para a opção proativa.
- Chama o pipeline de middleware do adaptador.
- Chama o método de retorno de chamada fornecido.
- O contexto de Turn usa a referência de conversa para enviar todas as mensagens ao usuário.

O exemplo tem um bot, um ponto de extremidade de mensagens e um ponto de extremidade de notificação adicional que é usado para enviar mensagens proativas para o usuário, conforme mostrado na ilustração a seguir.

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

A referência de conversa inclui uma propriedade de _conversa_ que descreve a conversa na qual a atividade existe. A conversa inclui uma propriedade de _usuário_ que lista os usuários que participam da conversa e uma propriedade de _URL de serviço_ que indica onde as respostas à atividade atual podem ser enviadas. Uma referência de conversa válida é necessária para enviar mensagens proativas para os usuários. (Para o canal de equipes, a URL de serviço é mapeada para um servidor regional.)

> [!NOTE]
> Em um cenário real, você manteria as referências de conversa em um banco de dados em vez de usar um objeto na memória.

## <a name="send-proactive-message"></a>Como enviar mensagens proativas

O segundo controlador, o controlador de _notificação_ , é responsável por enviar a mensagem proativa para o usuário. Ele usa as etapas a seguir para gerar uma mensagem proativa.

1. Recupera a referência para a conversa para a qual enviar a mensagem proativa.
1. Chama o método _continue Conversation_ do adaptador, fornecendo a referência de conversa e o delegado de manipulador de ativação a ser usado. (O método continue Conversation gera um contexto de ativação para a conversa referenciada e, em seguida, chama o delegado de manipulador de ativação especificado.)
1. No delegado, o usa o contexto de ativação para enviar a mensagem proativa. Aqui, o delegado é definido no controlador Notify e envia a mensagem proativa para o usuário.

> [!NOTE]
> Embora cada canal deva usar uma URL de serviço estável, a URL pode mudar ao longo do tempo. Para obter mais informações sobre a URL de serviço, consulte as seções [básicas de estrutura de atividade](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#basic-activity-structure) e URL de [serviço](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#service-url) do esquema de atividade do bot Framework.
>
> Se a URL do serviço for alterada, as referências anteriores à conversa não serão mais válidas e as chamadas para _continuar a conversa_ gerarão um erro ou exceção. Nesse caso, o bot precisará adquirir uma nova referência de conversa para o usuário antes de poder enviar mensagens proativas novamente.

# <a name="c"></a>[C#](#tab/csharp)

**Controllers\NotifyController .cs**

Sempre que a página de notificação do bot é solicitada, o controlador de notificação recupera as referências de conversa a partir do dicionário.
Então, o controlador usa os métodos `ContinueConversationAsync` e `BotCallback` para enviar a mensagem proativa.

[!code-csharp[Notify logic](~/../botbuilder-samples/samples/csharp_dotnetcore/16.proactive-messages/Controllers/NotifyController.cs?range=17-54&highlight=20,32-37)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

Sempre que a página `/api/notify` do servidor for solicitada, o servidor recuperará as referências de conversa a partir do dicionário.
Então, o servidor usa o método `continueConversation` para enviar a mensagem proativa.
O parâmetro para `continueConversation` é uma função que serve como manipulador de turno do bot para este turno.

[!code-javascript[Notify logic](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/index.js?range=70-84&highlight=4-8)]

# <a name="python"></a>[Python](#tab/python)

Sempre que a página de notificação do bot for solicitada, o servidor vai recuperar as referências da conversa do dicionário.
Em seguira, o servidor usará o `_send_proactive_message` para enviar a mensagem proativa.

[!code-python[Notify logic](~/../botbuilder-samples/samples/python/16.proactive-messages/app.py?range=98-106&highlight=5-9)]

---

Para enviar uma mensagem proativa, o adaptador requer uma ID do aplicativo para o bot. Em um ambiente de produção, você pode usar a ID do aplicativo do bot. Para testar o bot localmente com o emulador, você pode usar a cadeia de caracteres vazia ("").

## <a name="test-your-bot"></a>Testar seu bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador e conecte-se ao bot.
1. Carregar à página de api/notificação do seu bot. Isso irá gerar uma mensagem proativa no emulador.

## <a name="additional-information"></a>Informações adicionais

Além do exemplo usado neste artigo, exemplos adicionais estão disponíveis no [GitHub](https://github.com/Microsoft/BotBuilder-Samples/).

### <a name="design-considerations"></a>Considerações sobre o design

Ao implementar mensagens proativas em seu bot, não envie várias mensagens proativas em um curto período de tempo. Alguns canais impõem restrições sobre a frequência de envio de mensagens para o usuário de um bot, e desabilitarão o bot se ele violar essas restrições.

Uma mensagem proativa ad hoc é o tipo mais simples de mensagem proativa. O bot apenas injeta a mensagem na conversa sempre que ele é disparado, sem considerar se o usuário está envolvido em um tópico separado de conversa com o bot e não tentará alterar a conversa de modo algum.

Para lidar com notificações de forma mais suave, considere outras maneiras de integrar a notificação no fluxo de conversa, como definir um sinalizador no estado da conversa ou adicionar a notificação a uma fila.

### <a name="about-the-proactive-turn"></a>Sobre a opção proativa

O método _continue Conversation_ usa a referência de conversa e um manipulador de retorno de chamada ativado para:

1. Crie uma vez em que o aplicativo bot possa enviar a mensagem proativa. O adaptador cria uma `event` atividade para essa ativação, com seu nome definido como "ContinueConversation".
1. Envie o por meio do pipeline de middleware do adaptador.
1. Chame o manipulador de retorno de chamada Turn para executar a lógica personalizada.

No exemplo de **mensagens proativas** , o manipulador de retorno de chamada ativado é definido no controlador de notificação e envia a mensagem diretamente para a conversa, sem enviar a atividade proativa por meio do manipulador de folheio normal do bot.
O código de exemplo também não acessa ou atualiza o estado do bot na ativação proativa.

Muitos bots são monitorados e usam o estado para gerenciar uma conversa em várias ativações.
Quando o método continue Conversation cria um contexto de ativação, o ativará o usuário e o estado de conversa corretos associados a ele, e você poderá integrar o proativo à lógica do bot.
Se você precisar que a lógica de bot esteja ciente da mensagem proativa, terá algumas opções para fazer isso. Você poderá:

- Forneça o manipulador de folheio do bot como o manipulador de retorno de chamada de ativação. O bot receberá então a atividade de evento "ContinueConversation".
- Use o manipulador ativar retorno de chamada para adicionar informações ao contexto de ativação primeiro e, em seguida, chame o manipulador de folheio do bot.

Em ambos os casos, você precisará criar sua lógica de bot para manipular o evento proativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implementar o fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md)
