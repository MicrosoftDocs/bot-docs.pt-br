---
title: Adicionar anexos de mídia às mensagens | Microsoft Docs
description: Saiba como adicionar anexos de mídia às mensagens usando o SDK do Bot Builder para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 8fd9b181676eee24b1e9c64c79663d0d0ac8abfa
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997313"
---
# <a name="add-media-attachments-to-messages"></a>Adicionar anexos de mídia às mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Uma troca de mensagens entre o usuário e o bot pode conter anexos de mídia (por exemplo, imagem, vídeo, áudio e arquivo). A propriedade `Attachments` do objeto <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Atividade</a> contém uma matriz de objetos <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Anexo</a> que representam os anexos de mídia e os cartões avançados na mensagem. 

> [!NOTE]
> [Adicionar cartões avançados às mensagens](bot-builder-dotnet-add-rich-card-attachments.md).

## <a name="add-a-media-attachment"></a>Adicionar um anexo de mídia  

Para adicionar um anexo de mídia a uma mensagem, crie um objeto `Attachment` para a atividade `message` e defina as propriedades `ContentType`, `ContentUrl` e `Name`. 

[!code-csharp[Add media attachment](../includes/code/dotnet-add-attachments.cs#addMediaAttachment)]

Se um anexo for uma imagem, áudio ou vídeo, o serviço Connector comunicará os dados do anexo ao canal de forma a permitir que o [canal](bot-builder-dotnet-channeldata.md) renderize esse anexo na conversa. Se o anexo for um arquivo, a URL do arquivo será renderizada como um hiperlink na conversa.

## <a name="additional-resources"></a>Recursos adicionais

- [Visualizar recursos com o Inspetor de Canal][inspector]
- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Criar mensagens](bot-builder-dotnet-create-messages.md)
- [Adicionar cartões avançados às mensagens](bot-builder-dotnet-add-rich-card-attachments.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe da atividade</a>
- <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Classe do anexo</a>

[inspector]: ../bot-service-channel-inspector.md


