---
title: Criar mensagens com a API do Conector de Bot – Serviço de Bot
description: Familiarize-se com as mensagens que os bots usam para se comunicar com os usuários. Saiba mais sobre as propriedades usadas para formatar texto, anexar arquivos e especificar outro comportamento.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 7e4eafd75d6d60b78906a33d537adec3eadf7dab
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824536"
---
# <a name="create-messages-with-the-bot-connector-api"></a>Criar mensagens com a API do conector de bot

Seu bot enviará objetos [Atividade][] do tipo **message** para comunicar informações aos usuários e, da mesma forma, também receberá atividades **message** dos usuários. Algumas mensagens podem simplesmente consistir em texto sem formatação, enquanto outras podem conter conteúdo mais elaborado, como [texto a ser falado](bot-framework-rest-connector-text-to-speech.md), [ações sugeridas](bot-framework-rest-connector-add-suggested-actions.md), [anexos de mídia](bot-framework-rest-connector-add-media-attachments.md), [cartões avançados](bot-framework-rest-connector-add-rich-cards.md) e [dados específicos do canal](bot-framework-rest-connector-channeldata.md). Este artigo descreve algumas das propriedades de mensagem comumente usadas.

## <a name="message-text-and-formatting"></a>Texto e formatação da mensagem

O texto da mensagem pode ser formatado usando **texto sem formatação**, **markdown** ou **xml**. O formato padrão da propriedade `textFormat` é **markdown**, e interpreta o texto usando padrões de formatação Markdown. O nível de suporte ao formato de texto varia entre os canais. 

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

A propriedade `textFormat` do objeto [Atividade][] pode ser usada para especificar o formato do texto. Por exemplo, para criar uma mensagem básica que contém apenas texto sem formatação, defina a propriedade `textFormat` do objeto `Activity` como **sem formatação**, defina a propriedade `text` do conteúdo da mensagem e defina a propriedade `locale` da localidade do remetente. 

## <a name="attachments"></a>Anexos

A propriedade `attachments` do objeto [Atividade][] pode ser usada para enviar anexos de mídia simples (imagem, áudio, vídeo, arquivo) e cartões avançados. Para obter detalhes, confira [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md) e [Adicionar cartões avançados às mensagens](bot-framework-rest-connector-add-rich-cards.md).

## <a name="entities"></a>Entidades

A propriedade `entities` do objeto [Atividade][] é uma matriz de objetos <a href="http://schema.org/" target="_blank">schema.org</a> abertos que permite a troca de metadados contextuais comuns entre o canal e o bot.

### <a name="mention-entities"></a>Entidades de menção

Vários canais dão suporte à capacidade de um usuário ou bot "mencionar" alguém dentro do contexto de uma conversa. Para mencionar um usuário em uma mensagem, preencha a propriedade `entities` da mensagem com um objeto [Mention][]. 

### <a name="place-entities"></a>Entidades Place

Para transmitir <a href="https://schema.org/Place" target="_blank">informações relacionadas à localização</a> dentro de uma mensagem, preencha a propriedade `entities` da mensagem com o objeto [Local][]. 

## <a name="channel-data"></a>Dados do canal

A propriedade `channelData` de um objeto [Atividade][] pode ser usada para implementar a funcionalidade específica do canal. Para ver detalhes, confira [Implementar a funcionalidade específica do canal](bot-framework-rest-connector-channeldata.md).

## <a name="text-to-speech"></a>Texto em fala

A propriedade `speak` do objeto [Atividade][] pode ser usada para especificar o texto a ser falado pelo seu bot em um canal habilitado para fala e a propriedade `inputHint` do objeto `Activity` pode ser usada para influenciar o estado do microfone do cliente. Para obter detalhes, consulte [Adicionar fala às mensagens](bot-framework-rest-connector-text-to-speech.md) e [Adicionar dicas de entrada às mensagens](bot-framework-rest-connector-add-input-hints.md).

## <a name="suggested-actions"></a>Ações sugeridas

A propriedade `suggestedActions` de um objeto [Atividade][] pode ser usada para apresentar os botões nos quais o usuário pode tocar para fornecer entradas. Ao contrário de botões exibidos em cartões avançados (que permanecem visíveis e acessíveis ao usuário, mesmo após serem tocados), os botões que aparecem no painel de ações sugeridas desaparecerão após a seleção do usuário. Para ver detalhes, confira [Adicionar ações sugeridas a mensagens](bot-framework-rest-connector-add-suggested-actions.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Referência de canais][ChannelInspector]
- [Visão geral das atividades](https://aka.ms/botSpecs-activitySchema)
- [Enviar e receber mensagens](bot-framework-rest-connector-send-and-receive-messages.md)
- [Adicionar anexos de mídia às mensagens](bot-framework-rest-connector-add-media-attachments.md)
- [Adicionar cartões ricos mensagens](bot-framework-rest-connector-add-rich-cards.md)
- [Adicionar fala a mensagens](bot-framework-rest-connector-text-to-speech.md)
- [Adicionar dicas de entrada às mensagens](bot-framework-rest-connector-add-input-hints.md)
- [Adicionar ações sugeridas a mensagens](bot-framework-rest-connector-add-suggested-actions.md)
- [Implementar a funcionalidade específica do canal](bot-framework-rest-connector-channeldata.md)

[ChannelInspector]: ../bot-service-channels-reference.md
[textFormating]: ../bot-service-channel-inspector.md#text-formatting

[Atividade]: bot-framework-rest-connector-api-reference.md#activity-object
[Mention]: bot-framework-rest-connector-api-reference.md#mention-object
[Local]: bot-framework-rest-connector-api-reference.md#place-object
