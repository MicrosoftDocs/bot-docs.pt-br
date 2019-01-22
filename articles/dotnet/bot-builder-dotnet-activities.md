---
title: Visão geral das atividades | Microsoft Docs
description: Saiba mais sobre os diferentes tipos de atividade disponíveis no SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 076e460f393c5db524cfade81e5c007484fe2cca
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225661"
---
# <a name="activities-overview"></a>Visão geral das atividades

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Activity concept overview](../includes/snippet-dotnet-concept-activity.md)]

## <a name="activity-types-in-the-bot-framework-sdk-for-net"></a>Tipos de atividade no SDK do Bot Framework para .NET

Os tipos de atividade a seguir têm suporte do SDK do Bot Framework para .NET.

| Activity.Type | Interface | DESCRIÇÃO |
|------|------|------|
| [message](#message) | IMessageActivity | Representa uma comunicação entre o usuário e o bot. |
| [conversationUpdate](#conversationupdate) | IConversationUpdateActivity | Indica que o bot foi adicionado a uma conversa, outros membros foram adicionados ou removidos da conversa ou houve alteração de metadados da conversa. |
| [contactRelationUpdate](#contactrelationupdate) | IContactRelationUpdateActivity | Indica que o bot foi adicionado ou removido da lista de contatos do usuário. |
| [typing](#typing) | ITypingActivity | Indica que o usuário ou o bot na outra extremidade da conversa está compilando uma resposta. | 
| [deleteUserData](#deleteuserdata) | n/d | Indica para um bot que um usuário solicitou ao bot a exclusão de quaisquer dados de usuário que ele possa ter armazenado. |
| [endOfConversation](#endofconversation) | IEndOfConversationActivity | Indica o final de uma conversa. |
| [event](#event) | IEventActivity | Representa uma comunicação enviada a um bot que não é visível para o usuário. |
| [invoke](#invoke) | IInvokeActivity | Representa uma comunicação enviada a um bot para solicitar a execução de uma operação específica. Esse tipo de atividade é reservado para uso interno do Microsoft Bot Framework. |
| [messageReaction](#messagereaction) | IMessageReactionActivity | Indica que um usuário reagiu a uma atividade existente. Por exemplo, um usuário clica no botão "Curtir" em uma mensagem. |

## <a name="message"></a>Message

Seu bot enviará atividades de **mensagem** para comunicar informações aos usuários e receber atividades de **mensagem** dos usuários. Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem conter conteúdo mais elaborado, como [texto a ser falado](bot-builder-dotnet-text-to-speech.md), [ações sugeridas](bot-builder-dotnet-add-suggested-actions.md), [anexos de mídia](bot-builder-dotnet-add-media-attachments.md), [cartões avançados](bot-builder-dotnet-add-rich-card-attachments.md) e [dados específicos do canal](bot-builder-dotnet-channeldata.md). Para saber mais sobre as propriedades de mensagem mais usadas, consulte [Criar mensagens](bot-builder-dotnet-create-messages.md).

## <a name="conversationupdate"></a>conversationUpdate

Um bot receberá uma atividade **conversationUpdate** sempre que tiver sido adicionado a uma conversa, outros membros tiverem sido adicionados ou removidos de uma conversa ou se houver alteração nos metadados da conversa. 

Se os membros foram adicionados à conversa, a propriedade `MembersAdded` da atividade conterá uma matriz de objetos `ChannelAccount` para identificar os novos membros. 

Para determinar se o seu bot foi adicionado à conversa (ou seja, é um dos novos membros), avalie se o valor `Recipient.Id` da atividade (ou seja, a ID do seu bot) corresponde à propriedade `Id` de qualquer uma das contas na matriz `MembersAdded`.

Se os membros tiverem sido removidos da conversa, a propriedade `MembersRemoved` conterá uma matriz de objetos `ChannelAccount` para identificar os membros removidos. 

> [!TIP]
> Se o seu bot receber uma atividade **conversationUpdate** indicando que um usuário entrou para a conversa, você poderá optar por fazer com que ele responda, enviando uma mensagem de boas-vinda para esse usuário. 

## <a name="contactrelationupdate"></a>contactRelationUpdate

Um bot receberá uma atividade de **contactRelationUpdate** sempre que for adicionado ou removido da lista de contatos de um usuário. O valor da propriedade `Action` da atividade (adicionar | remover) indica se o bot foi adicionado ou removido da lista de contatos do usuário.

## <a name="typing"></a>digitação

A bot recebe uma atividade **digitação** para indicar que o usuário está digitando uma resposta. Um bot pode enviar uma atividade **digitação** para indicar ao usuário que está trabalhando para atender uma solicitação ou compilar uma resposta. 

## <a name="deleteuserdata"></a>deleteUserData

Um bot recebe uma atividade **deleteUserData** quando um usuário solicita a exclusão de quaisquer dados que o bot tenha persistido anteriormente para o usuário. Se o bot receber esse tipo de atividade, ele deverá excluir quaisquer PII (informações de identificação pessoal) que armazenou anteriormente para o usuário que fez a solicitação.

## <a name="endofconversation"></a>endOfConversation 

Um bot recebe uma atividade **endOfConversation** para indicar que o usuário encerrou a conversa. Um bot pode enviar uma atividade **endOfConversation** para indicar ao usuário que a conversa está encerrando. 

## <a name="event"></a>evento

Seu bot pode receber uma atividade **event** de um processo ou serviço externo que deseja comunicar informações ao seu bot, sem que essas informações fiquem visíveis aos usuários. O remetente de uma atividade **event** normalmente não espera que o bot confirme o recebimento de qualquer forma.

## <a name="invoke"></a>invoke

Seu bot pode receber uma atividade **invoke** que representa uma solicitação para executar uma operação específica. O remetente de uma atividade **invoke** normalmente espera que o bot confirme o recebimento por meio de resposta HTTP. Esse tipo de atividade é reservado para uso interno do Microsoft Bot Framework.

## <a name="messagereaction"></a>messageReaction

Alguns canais enviarão atividades **messageReaction** ao seu bot quando um usuário reagir a uma atividade existente. Por exemplo, um usuário clica no botão "Curtir" em uma mensagem. A propriedade **ReplyToId** indicará a qual atividade o usuário reagiu.

A atividade **messageReaction** pode corresponder a qualquer quantidade de **messageReactionTypes** definida pelo canal. Por exemplo, "Like" ou "PlusOne" como tipos de reação enviados por um canal. 

## <a name="additional-resources"></a>Recursos adicionais

- [Enviar e receber atividades](bot-builder-dotnet-connector.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
