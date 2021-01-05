---
title: Conectar um bot ao email do Office 365 – Serviço de Bot
description: Saiba como configurar os bots para enviar e receber mensagens de email conectando-as ao Microsoft 365 email. Consulte como personalizar mensagens.
keywords: O Office 365, canais de bot, email, credenciais de email, portal do azure, email personalizado
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/09/2019
ms.openlocfilehash: 84704e6899337a085e5a93e12898e1f857b5c054
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759214"
---
# <a name="connect-a-bot-to-office-365-email"></a>Conectar um bot ao email do Office 365

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Os bots podem se comunicar com usuários por meio de email do Office 365 além de outros [canais](~/bot-service-manage-channels.md). Quando um bot é configurado para acessar uma conta de email, ele recebe uma mensagem quando um novo email chega. Em seguida, o bot pode responder conforme indicado por sua lógica de negócios. Por exemplo, o bot poderia enviar uma resposta de email confirmando que um email foi recebido com a mensagem "Olá! Agradecemos seu pedido! Começaremos a processá-lo imediatamente."

> [!WARNING]
> É uma violação do [Código de Conduta](https://www.botframework.com/Content/Microsoft-Bot-Framework-Preview-Online-Services-Agreement.htm) do Bot Framework criar "spambots", incluindo bots que enviam email em massa não solicitados ou indesejados.

> [!NOTE]
> Se você está usando o Microsoft Exchange Server, verifique se você habilitou [Descoberta automática](https://docs.microsoft.com/exchange/client-developer/exchange-web-services/autodiscover-for-exchange) antes de configurar o canal de email.

## <a name="configure-email-credentials"></a>Configurar credenciais de email

Você pode conectar um bot ao canal de Email inserindo as credenciais do Office 365 na configuração de canal de Email.
Não há suporte para autenticação federada usando qualquer fornecedor que substitui o AAD.

> [!NOTE]
> Você não deve usar suas próprias contas de email para bots, pois todas as mensagens enviadas para essa conta de email serão encaminhadas para o bot. Isso pode resultar no bot enviando inadequadamente uma resposta para um remetente. Por esse motivo, os bots só devem usar contas de email do O365 dedicadas.

Para adicionar o canal de Email, abra o bot no [Portal do Azure](https://portal.azure.com/), clique na folha **Canais** e clique em **Email**. Insira suas credenciais de email válidas e clique em **Salvar**.

![Inserir credenciais de email](~/media/bot-service-channel-connect-email/bot-service-channel-connect-email-credentials.png)

O canal de Email funciona no momento apenas com o Office 365. No momento, não há suporte para outros serviços de email.

## <a name="customize-emails"></a>Personalizar emails

O canal de email dá suporte ao envio de valores personalizados para criar emails personalizados mais avançados usando a `channelData` Propriedade atividade.
O trecho de código abaixo mostra um exemplo do `channelData` para uma mensagem de email personalizada de entrada, do bot ao usuário.

[!INCLUDE [email channelData json](~/includes/snippet-channelData-email.md)]

Para obter mais informações sobre a `channelData` Propriedade atividade, consulte [criar uma mensagem de email personalizada](v4sdk/bot-builder-channeldata.md#create-a-custom-email-message).

## <a name="other-considerations"></a>Outras considerações

Se seu bot não retornar um código de status de HTTP 200 OK dentro de 15 segundos em resposta a uma mensagem de email de entrada, o canal de email tentará reenviar a mensagem e seu bot pode receber a mesma atividade de mensagem de email algumas vezes. Para obter mais informações, confira a seção de [Detalhes de HTTP](v4sdk/bot-builder-basics.md#http-details) em **Como funcionam os bots** e o artigo sobre como [Solucionar problemas de erros de tempo limite](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md).

> [!NOTE]
> Se você estiver usando uma conta do Office 365 com a MFA habilitada, primeiro desabilite a MFA para a conta especificada e, em seguida, você poderá configurar a conta para o canal de email. Caso contrário, a conexão falhará.

## <a name="additional-resources"></a>Recursos adicionais

- Conectar um bot aos [canais](~/bot-service-manage-channels.md)
- [Implemente a funcionalidade específica do canal](~/v4sdk/bot-builder-channeldata.md) com o SDK do Bot Framework para .NET
- Leia o artigo [referência de canais](bot-service-channels-reference.md) para obter mais informações sobre quais recursos são compatíveis com cada canal