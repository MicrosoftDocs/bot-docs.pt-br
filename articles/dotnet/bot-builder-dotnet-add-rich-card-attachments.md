---
title: Adicionar anexos de cartão avançado a mensagens (C# v3) – Serviço de Bot
description: Saiba como adicionar cartões avançados às mensagens usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e30ac144d4e960672f3d129935a657c42ed1aa6d
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75796509"
---
# <a name="add-rich-card-attachments-to-messages"></a>Adicionar anexos de cartão avançados às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Uma troca de mensagens entre o usuário e o bot pode conter um ou mais cartões avançados renderizados como uma lista ou carrossel. 

A propriedade `Attachments` do objeto <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Atividade</a> contém uma matriz de objetos <a href="https://docs.microsoft.com/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Anexo</a> que representam os cartões avançados e os anexos de mídia da mensagem. 

> [!NOTE]
> Para obter informações sobre como adicionar anexos de mídia às mensagens, consulte [Adicionar anexos de mídia às mensagens](bot-builder-dotnet-add-media-attachments.md).

## <a name="types-of-rich-cards"></a>Tipos de cartões avançados

Atualmente, o Bot Framework dá suporte a oito tipos de cartões avançados: 

| Tipo de cartão | DESCRIÇÃO |
|----|----|
| <a href="/adaptive-cards/get-started/bots">Cartão adaptável</a> | Um cartão personalizável que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Confira [suporte por canal](/adaptive-cards/get-started/bots#channel-status).  |
| [Cartão de animação][animationCard] | Um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| [Cartão de áudio][audioCard] | Um cartão que pode reproduzir um arquivo de áudio. |
| [Cartão Hero][heroCard] | Um cartão que geralmente contém uma única imagem grande, um ou mais botões e um texto. |
| [Cartão em miniatura][thumbnailCard] | Um cartão que geralmente contém uma única imagem em miniatura, um ou mais botões e um texto. |
| [Cartão de recibo][receiptCard] | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| [Cartão de entrada][signinCard] | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |
| [Cartão de vídeo][videoCard] | Um cartão que pode reproduzir vídeos. |

> [!TIP]
> Para exibir vários cartões avançados no formato de lista, defina a propriedade `AttachmentLayout` como "lista". Para exibir vários cartões avançados no formato de carrossel, defina a propriedade `AttachmentLayout` da atividade como "carrossel". Se o canal não der suporte para formato de carrossel, ele exibirá os cartões avançados no formato de lista, mesmo se a propriedade `AttachmentLayout` especificar "carrossel".

## <a name="process-events-within-rich-cards"></a>Processar eventos em cartões avançados

Para processar eventos em cartões avançados, defina objetos `CardAction` para especificar o que deverá acontecer quando o usuário clicar em um botão ou tocar em uma seção do cartão. Cada objeto `CardAction` contém estas propriedades:

| Propriedade | Type | DESCRIÇÃO | 
|----|----|----|
| Type | string | tipo de ação (um dos valores especificados na tabela a seguir) |
| Title | string | título do botão |
| Imagem | string | URL da imagem do botão |
| Valor | string | valor necessário para executar o tipo de ação especificado |

> [!NOTE]
> Botões dentro de Cartões Adaptáveis não são criados usando objetos `CardAction`, mas usando o esquema definido pelos <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>. Consulte [Adicionar um Cartão Adaptável a uma mensagem](#adaptive-card) para obter um exemplo que mostra como adicionar botões a um Cartão Adaptável.

Essa tabela lista os valores válidos para `CardAction.Type` e descreve o conteúdo esperado de `CardAction.Value` para cada tipo:

| CardAction.Type | CardAction.Value | 
|----|----|
| openUrl | URL a ser aberta no navegador interno |
| imBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Essa mensagem (do usuário ao bot) ficará visível a todos os participantes da conversa por meio do aplicativo cliente que hospeda a conversa. |
| postBack | Texto da mensagem para enviar ao bot (do usuário que clicou no botão ou tocou no cartão). Alguns aplicativos cliente podem exibir esse texto no feed de mensagem, onde ficará visível a todos os participantes da conversa. |
| chamada | Destino de uma chamada telefônica neste formato: **tel:123123123123** |
| playAudio | URL do áudio para reprodução |
| playVideo | URL do vídeo para reprodução |
| showImage | URL da imagem a ser exibida |
| downloadFile | URL do arquivo para download |
| signin | URL do fluxo do OAuth a ser iniciado |

## <a name="add-a-hero-card-to-a-message"></a>Adicionar um cartão Hero a uma mensagem

O cartão Hero geralmente contém uma única imagem grande, um ou mais botões e texto. 

Este exemplo de código mostra como criar uma mensagem de resposta que contém três cartões Hero renderizados no formato de carrossel: 

[!code-csharp[Add HeroCard attachment](../includes/code/dotnet-add-attachments.cs#addHeroCardAttachment)]

## <a name="add-a-thumbnail-card-to-a-message"></a>Adicionar um cartão de miniatura a uma mensagem

O cartão de miniatura normalmente contém uma única imagem em miniatura, um ou mais botões e texto. 

Este exemplo de código mostra como criar uma mensagem de resposta que contém dois cartões de miniaturas renderizados no formato de lista: 

[!code-csharp[Add ThumbnailCard attachment](../includes/code/dotnet-add-attachments.cs#addThumbnailCardAttachment)]

## <a name="add-a-receipt-card-to-a-message"></a>Adicionar um cartão de recebimento a uma mensagem

O cartão de recebimento permite que um bot forneça um recibo ao usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. 

Este exemplo de código mostra como criar uma mensagem de resposta que contém um cartão de recebimento: 

[!code-csharp[Add ReceiptCard attachment](../includes/code/dotnet-add-attachments.cs#addReceiptCardAttachment)]

## <a name="add-a-sign-in-card-to-a-message"></a>Adicionar um cartão de entrada a uma mensagem

O cartão de entrada permite que um bot solicite a entrada de um usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. 

Este exemplo de código mostra como criar uma mensagem de resposta que contém um cartão de entrada:

[!code-csharp[Add SignInCard attachment](../includes/code/dotnet-add-attachments.cs#addSignInCardAttachment)]

## <a id="adaptive-card"></a> Adicionar um cartão adaptável a uma mensagem

O Cartão Adaptável pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. Os Cartões Adaptáveis são criados usando o formato JSON especificado em <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>, o que fornece a você controle total sobre o conteúdo e o formato do cartão. 

Para criar um Cartão Adaptável usando o .NET, instale o pacote NuGet `AdaptiveCards`. Em seguida, aproveite as informações dentro do site <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a> para compreender o esquema de Cartão Adaptável, explore os elementos do Cartão Adaptável e veja exemplos de JSON que podem ser usados para criar cartões de composição e complexidade variadas. Além disso, você pode usar o Visualizador Interativo para criar o conteúdo de Cartão Adaptável e visualizar a saída do cartão.

Este exemplo de código mostra como criar uma mensagem que contém um Cartão Adaptável para um lembrete de calendário: 

[!code-csharp[Add Adaptive Card attachment](../includes/code/dotnet-add-attachments.cs#addAdaptiveCardAttachment)]

O cartão resultante contém três blocos de texto, um campo de entrada (lista de opções) e três botões:

![Lembrete de calendário do Cartão Adaptável](../media/adaptive-card-reminder.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Referência de canais](../bot-service-channels-reference.md)
- <a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a>
- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Adicionar anexos de mídia às mensagens](bot-builder-dotnet-add-media-attachments.md)
- <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe de atividade</a>
- <a href="https://docs.microsoft.com/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Classe do anexo</a>

[animationCard]: /dotnet/api/microsoft.bot.connector.animationcard

[audioCard]: /dotnet/api/microsoft.bot.connector.audiocard 

[heroCard]: /dotnet/api/microsoft.bot.connector.herocard 

[thumbnailCard]: /dotnet/api/microsoft.bot.connector.thumbnailcard 

[receiptCard]: /dotnet/api/microsoft.bot.connector.receiptcard 

[signinCard]: /dotnet/api/microsoft.bot.connector.signincard 

[videoCard]: /dotnet/api/microsoft.bot.connector.videocard

[inspector]: ../bot-service-channels-reference.md
