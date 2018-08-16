---
title: Visão geral das atividades  | Microsoft Docs
description: Saiba mais sobre os diferentes tipos de atividades disponíveis no serviço do Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: cf8da2240df7edbb6ea8c858829e71089b7e72cb
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297412"
---
# <a name="activities-overview"></a>Visão geral das atividades

O serviço Bot Connector troca informações entre o bot e o canal (usuário) passando um objeto [Atividade][Activity]. O tipo de atividade mais comum é **mensagem**, mas há outros tipos de atividade que podem ser usados para comunicar diversos tipos de informações a um bot ou canal. 

## <a name="activity-types-in-the-bot-connector-service"></a>Tipos de atividade no serviço do Bot Connector

Há suporte para os seguintes tipos de atividade pelo Bot Connector.

| Tipo de atividade | DESCRIÇÃO |
|------|------|------|
| Message | Representa uma comunicação entre o bot e o usuário. |
| conversationUpdate | Indica que o bot foi adicionado a uma conversa, outros membros foram adicionados ou removidos da conversa ou houve alteração de metadados da conversa. |
| contactRelationUpdate | Indica que o bot foi adicionado ou removido da lista de contatos do usuário. |
| digitação | Indica que o usuário ou o bot na outra extremidade da conversa está compilando uma resposta. | 
| ping | Representa uma tentativa de determinar se o ponto de extremidade do bot está acessível. | 
| deleteUserData | Indica para um bot que um usuário solicitou ao bot a exclusão de quaisquer dados de usuário que ele possa ter armazenado. |
| endOfConversation | Indica o final de uma conversa. |

## <a name="message"></a>Message

Seu bot enviará atividades de **mensagem** para comunicar informações aos usuários e receber atividades de **mensagem** dos usuários. Algumas mensagens podem ser texto sem formatação, enquanto outras podem conter conteúdo mais avançado, como [anexos de mídia](bot-framework-rest-connector-add-media-attachments.md), [botões e cartões](bot-framework-rest-connector-add-rich-cards.md) ou [dados específicos do canal](bot-framework-rest-connector-channeldata.md). Para obter informações sobre propriedades de mensagens comumente usadas, consulte [Criar mensagens](bot-framework-rest-connector-create-messages.md). Para obter informações sobre como enviar e receber mensagens, consulte [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md). 

## <a name="conversationupdate"></a>conversationUpdate

Um bot receberá uma atividade **conversationUpdate** sempre que for adicionado a uma conversa, outros membros forem adicionados ou removidos de uma conversa ou os metadados da conversa forem alterados. 

Se membros forem adicionados à conversa, a propriedade `addedMembers` da atividade identificará os novos membros. Se membros forem removidos da conversa, a propriedade `removedMembers` identificará os membros removidos. 

> [!TIP]
> Se o bot receber uma atividade **conversationUpdate** indicando que um usuário ingressou na conversa, você poderá responder enviando uma mensagem de boas-vindas a esse usuário. 

## <a name="contactrelationupdate"></a>contactRelationUpdate

Um bot receberá uma atividade de **contactRelationUpdate** sempre que for adicionado ou removido da lista de contatos de um usuário. O valor da propriedade `action` da atividade (adicionar | remover) indica se o bot foi adicionado ou removido da lista de contatos do usuário.

## <a name="typing"></a>digitação

A bot recebe uma atividade **digitação** para indicar que o usuário está digitando uma resposta. Um bot pode enviar uma atividade **digitação** para indicar ao usuário que está trabalhando para atender uma solicitação ou compilar uma resposta. 

## <a name="ping"></a>ping

Um bot recebe uma atividade **ping** para determinar se o seu ponto de extremidade é acessível. O bot deve responder com o código de status HTTP 200 (OK), 403 (Proibido) ou 401 (Não autorizado).

## <a name="deleteuserdata"></a>deleteUserData

Um bot recebe uma atividade **deleteUserData** quando um usuário solicita a exclusão de quaisquer dados que o bot tenha persistido anteriormente para o usuário. Se o bot receber esse tipo de atividade, ele deverá excluir quaisquer PII (informações de identificação pessoal) que armazenou anteriormente para o usuário que fez a solicitação.

## <a name="endofconversation"></a>endOfConversation 

Um bot recebe uma atividade **endOfConversation** para indicar que o usuário encerrou a conversa. Um bot pode enviar uma atividade **endOfConversation** para indicar ao usuário que a conversa está encerrando. 

## <a name="additional-resources"></a>Recursos adicionais

- [Criar mensagens](bot-framework-rest-connector-create-messages.md)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object