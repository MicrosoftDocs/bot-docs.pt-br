---
redirect_url: /bot-framework/bot-builder-howto-v4-state
ms.openlocfilehash: e5da105e32ae748383d376f90afd9aebbf4c7aa5
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708112"
---
<a name="--"></a><!--
---
título: Estado e armazenamento | Microsoft Docs descrição: Descreve como o gerenciador de estado, estado de conversa e estado de usuário estão no SDK do Bot Builder.
palavras-chave: LUIS, estado da conversa, estado do usuário, o armazenamento, gerenciar estado autor: DeniseMak ms.author: v-demak manager: kamrani ms.topic: article ms.prod: bot-framework ms.date: 15/02/2018 monikerRange: 'azure-bot-service-4.0'
---

# <a name="state-and-storage"></a>Estado e armazenamento
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma chave para obter bom design de bot é acompanhar o contexto de uma conversa para que o bot reconheça aspectos como as respostas às perguntas anteriores.
Dependendo no que o bot for utilizado, talvez seja necessário até mesmo controlar as informações de estado ou repositório por mais tempo que o tempo de vida da conversa.
Um *estado* do bot é uma informação que o bot reconhece para responder apropriadamente às mensagens recebidas. O SDK do Bot Builder fornece classes para armazenar e recuperar dados de estado como um objeto associado a um usuário ou a uma conversa.

* As **propriedades de conversa** ajudam o bot a acompanhar a conversa atual que o bot está tendo com o usuário. Se o bot precisar concluir uma sequência de etapas ou alternar entre tópicos de conversa, será possível usar as propriedades de conversa para gerenciar etapas em uma sequência ou acompanhar o tópico atual. Como as propriedades de conversa refletem o estado da conversa atual, geralmente são apagadas no fim de uma conversa quando o bot recebe uma atividade _fim da conversa_.
* As **propriedades do usuário** podem ser usadas para muitas finalidades como determinar onde a conversa anterior do usuário foi interrompida ou simplesmente cumprimentando um usuário habitual pelo nome. Se você armazenar as preferências de um usuário, poderá usar essas informações para personalizar a conversa no próximo chat. Por exemplo, é possível alertar o usuário sobre um artigo de notícias relacionado a um tópico de interesse ou alertar um usuário quando um compromisso estiver disponível. Será necessário apagá-las se o bot receber uma atividade _excluir dados de usuário_.

É possível usar [Armazenamento](bot-builder-howto-v4-storage.md) para ler de e gravar para armazenamento persistente. Isso permite que o bot execute ações como atualizar recursos compartilhados, gravar RSVPs ou votos, ou ler dados meteorológicos históricos. Da mesma forma que um aplicativo usa armazenamento para atingir os objetivos, o bot pode fazer isso dentro da conversa com o usuário.

<!-- 
*Conversation state* pertains to the current conversation that the user is having with your bot. When the conversation ends, your bot deletes this data.

You can also store *user state* that persists after a conversation ends. For example, if you store a user's preferences, you can use that information to customize the conversation the next time you chat. For example, you might alert the user to a news article about a topic that interests her, or alert a user when an appointment becomes available. 
-->

<!-- You should generally avoid saving state using a global variable or function closures.
Doing so will create issues when you want to scale out your bot. Instead, use the conversation state and user state middleware that the BotBuilder SDK provides --> 

<!--
## Types of underlying storage

The SDK provides bot state manager middleware to persist conversation and user state. State can be accessed using the bot's context. This state manager can use Azure Table Storage, file storage, or memory storage as the underlying data storage. You can also create your own storage components for your bot.

Bots built using Azure Table Storage can be designed to be stateless and scalable across multiple compute nodes.

> [!NOTE] 
> File and memory storage won't scale across nodes.

## Writing directly to storage

You can also use the Bot Builder SDK to read and write data directly to storage, without using middleware or without using the bot context. This can be appropriate to data that your bot uses, that comes from a source outside your bot's conversation flow.

For example, let's say your bot allows the user to ask for the weather report, and your bot retrieves the weather report for a specified date, by reading it from an external database. The content of the weather database isn't dependent on user information or the conversation context, so you could just read it directly from storage instead of using the state manager.  See [How to write directly to storage](bot-builder-howto-v4-storage.md) for an example.

## Next steps

Next, lets get into how activities are processed, in depth, and how we respond to them.

> [!div class="nextstepaction"]
> [Activity Processing](bot-builder-concept-activity-processing.md)

## Additional resources

- [How to save state](bot-builder-howto-v4-state.md)
- [How to write directly to storage](bot-builder-howto-v4-storage.md)

-->
