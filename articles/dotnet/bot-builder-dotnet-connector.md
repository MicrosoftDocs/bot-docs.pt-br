---
title: Enviar e receber atividades | Microsoft Docs
description: Saiba como trocar informações com um usuário em vários canais usando o serviço do Connector por meio do SDK do Bot Builder para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 2a5c9dcad0d9fd70caaf28ff7ac95830bd47e2d6
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574592"
---
# <a name="send-and-receive-activities"></a>Enviar e receber atividades

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O Bot Framework Connector oferece uma única API REST que permite a um bot se comunicar por diversos canais, como Skype, Email, Slack e mais. Ele facilita a comunicação entre o bot e o usuário por mensagens de retransmissão do bot ao canal e do canal ao bot. 

Este artigo descreve como usar o Connector por meio do SDK do SDK do Bot Builder para .NET para trocar informações entre o usuário e o bot em um canal. 

> [!NOTE]
> Embora seja possível construir um bot usando exclusivamente as técnicas descritas neste artigo, o SDK do Bot Builder fornece recursos adicionais, como [diálogos](bot-builder-dotnet-dialogs.md) e [FormFlow](bot-builder-dotnet-formflow.md), que podem simplificar o processo de gerenciar o estado e o fluxo da conversa, simplificando a incorporação de serviços cognitivos, como reconhecimento vocal.

## <a name="create-a-connector-client"></a>Criar um cliente do conector

A classe [ConnectorClient][ConnectorClient] contém os métodos que um bot usa para comunicar-se com um usuário em um canal. Quando o bot recebe um objeto <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Atividade</a> do Connector, ele deve usar o `ServiceUrl` especificado para a atividade para criar o cliente do conector que usará posteriormente para gerar uma resposta. 

[!code-csharp[Create connector client](../includes/code/dotnet-send-and-receive.cs#createConnectorClient)]

> [!TIP]
> Como o ponto de extremidade do canal pode não ser estável, o bot deve direcionar as comunicações para o ponto de extremidade que o Connector especifica no objeto `Activity`, sempre que possível (em vez de depender de um ponto de extremidade armazenado em cache). 
>
> Se o bot precisar iniciar a conversa, ele poderá usar um ponto de extremidade armazenado em cache para o canal especificado (já que não haverá nenhum objeto `Activity` de entrada nesse cenário), mas o bot deve atualizar os pontos de extremidade armazenados em cache com frequência. 

## <a id="create-reply"></a> Criar uma resposta

O Connector usa um objeto [Atividade](bot-builder-dotnet-activities.md) para circular informações entre o bot e canal (usuário). Cada atividade contém as informações usadas para rotear a mensagem ao destino apropriado juntamente com informações sobre quem criou a mensagem (propriedade `From`), o contexto da mensagem e o destinatário da mensagem (propriedade `Recipient`).

Quando o bot recebe uma atividade do Connector, a propriedade `Recipient` da atividade de entrada especifica a identidade do bot nessa conversa. Como alguns canais (por exemplo, Slack) atribuem ao bot uma nova identidade quando ele é adicionado a uma conversa, o bot deve sempre usar o valor da propriedade `Recipient` da atividade de entrada como o valor da propriedade `From` na resposta.

Embora seja possível criar e inicializar o objeto `Activity` de saída por conta própria do zero, o SDK do Bot Builder proporciona uma maneira mais fácil de criar uma resposta. Usando o método `CreateReply` da atividade de entrada, você simplesmente especifica o texto da mensagem para a resposta, sendo que a atividade de saída é criada com as propriedades `Recipient`, `From` e `Conversation` preenchidas automaticamente.

[!code-csharp[Create reply](../includes/code/dotnet-send-and-receive.cs#createReply)]

## <a name="send-a-reply"></a>Enviar uma resposta

Depois de criar uma resposta, você pode enviá-la chamando o método `ReplyToActivity` do cliente do conector. O Connector fornecerá a resposta usando a semântica de canal apropriada. 

[!code-csharp[Send reply](../includes/code/dotnet-send-and-receive.cs#sendReply)]

> [!TIP]
> Se o bot estiver respondendo a mensagem de um usuário, sempre use o método `ReplyToActivity`.

## <a name="send-a-non-reply-message"></a>Enviar uma mensagem (sem resposta) 

Se o bot fizer parte de uma conversa a, ele pode enviar uma mensagem que não seja uma resposta direta para qualquer mensagem do usuário chamando o método `SendToConversation`. 

[!code-csharp[Send non-reply message](../includes/code/dotnet-send-and-receive.cs#sendNonReplyMessage)]

É possível usar o método `CreateReply` para inicializar a nova mensagem (o que definiria automaticamente as propriedades `Recipient`, `From` e `Conversation` da mensagem). Como alternativa, é possível usar o método `CreateMessageActivity` para criar a nova mensagem e definir todos os valores da propriedade você mesmo.

> [!NOTE]
> O Bot Framework não impõe nenhuma restrição quanto ao número de mensagens que podem ser enviadas por um bot. No entanto, a maioria dos canais impõe uma limitação para impedir que os bots enviem um grande número de mensagens em um período curto. Além disso, se o bot enviar rapidamente várias mensagens sucessivas, o canal nem sempre pode renderizar as mensagens na sequência correta.

## <a name="start-a-conversation"></a>Iniciar uma conversa

Pode haver ocasiões em que o bot precisa iniciar uma conversa com um ou mais usuários. Você pode iniciar uma conversa chamando o método `CreateDirectConversation` (para uma conversa privada com um único usuário) ou o método `CreateConversation` (para uma conversa de grupo com vários usuários) para recuperar um objeto `ConversationAccount`. Em seguida, crie a mensagem e envie-a chamando o método `SendToConversation`. Para usar o método `CreateDirectConversation` ou `CreateConversation`, primeiro é necessário [criar o cliente do conector](#create-a-connector-client) usando a URL do serviço do canal de destino (que você pode recuperar do cache, caso tenha persistido nas mensagens anteriores). 

> [!NOTE]
> Nem todos os canais dão suporte a conversas em grupo. Para determinar se um canal dá suporte a conversas em grupo, consulte a documentação do canal.

Este exemplo de código usa o método `CreateDirectConversation` para criar uma conversa privada com um único usuário.

[!code-csharp[Start private conversation](../includes/code/dotnet-send-and-receive.cs#startPrivateConversation)]

Este exemplo de código usa o método `CreateConversation` para criar uma conversa em grupo com um vários usuários.

[!code-csharp[Start group conversation](../includes/code/dotnet-send-and-receive.cs#startGroupConversation)]

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Builder para .NET</a>
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="/dotnet/api/microsoft.bot.connector.connectorclient" target="_blank">Classe ConnectorClient</a>

[ConnectorClient]: /dotnet/api/microsoft.bot.connector.connectorclient
