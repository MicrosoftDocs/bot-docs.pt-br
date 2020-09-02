---
title: Conectar um bot ao email do Office 365 – Serviço de Bot
description: Saiba como configurar os bots para enviar e receber mensagens de email conectando-as ao Microsoft 365 email. Consulte como personalizar mensagens.
keywords: O Office 365, canais de bot, email, credenciais de email, portal do azure, email personalizado
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/15/2019
ms.openlocfilehash: 4dcdedbfbadc3f1fc60bd57d9bd3657e38c98255
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362119"
---
# <a name="connect-a-bot-to-office-365-email"></a>Conectar um bot ao email do Office 365

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

O canal de Email dá suporte ao envio de propriedades personalizadas para criar emails mais avançados e personalizado usando a propriedade `channelData`.

[!INCLUDE [Email channelData table](~/includes/snippet-channelData-email.md)]

A mensagem de exemplo a seguir mostra um arquivo JSON que inclui esses `channelData` propriedades.

```json
{
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

::: moniker range="azure-bot-service-3.0"
Para saber mais sobre o uso de `channelData`, consulte o exemplo de [Node.js](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ChannelData) ou a documentação de [.NET](~/dotnet/bot-builder-dotnet-channeldata.md).
::: moniker-end

::: moniker range="azure-bot-service-4.0"
Para obter mais informações sobre o uso de `channelData`, consulte [como implementar funcionalidade específica do canal](~/v4sdk/bot-builder-channeldata.md).
::: moniker-end

## <a name="other-considerations"></a>Outras considerações

Se seu bot não retornar um código de status de HTTP 200 OK dentro de 15 segundos em resposta a uma mensagem de email de entrada, o canal de email tentará reenviar a mensagem e seu bot pode receber a mesma atividade de mensagem de email algumas vezes. Para obter mais informações, confira a seção de [Detalhes de HTTP](v4sdk/bot-builder-basics.md#http-details) em **Como funcionam os bots** e o artigo sobre como [Solucionar problemas de erros de tempo limite](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md).

> [!NOTE]
> Se você estiver usando uma conta do Office 365 com a MFA habilitada, primeiro desabilite a MFA para a conta especificada e, em seguida, você poderá configurar a conta para o canal de email. Caso contrário, a conexão falhará. 

## <a name="additional-resources"></a>Recursos adicionais

<!-- Put whole list in monikers, even though it's just the second item that needs to be different. -->
::: moniker range="azure-bot-service-3.0"
* Conectar um bot aos [canais](~/bot-service-manage-channels.md)
* [Implemente a funcionalidade específica do canal](dotnet/bot-builder-dotnet-channeldata.md) com o SDK do Bot Framework para .NET
* Leia o artigo [referência de canais](bot-service-channels-reference.md) para obter mais informações sobre quais recursos são compatíveis com cada canal
::: moniker-end
::: moniker range="azure-bot-service-4.0"
* Conectar um bot aos [canais](~/bot-service-manage-channels.md)
* [Implemente a funcionalidade específica do canal](~/v4sdk/bot-builder-channeldata.md) com o SDK do Bot Framework para .NET
* Leia o artigo [referência de canais](bot-service-channels-reference.md) para obter mais informações sobre quais recursos são compatíveis com cada canal
::: moniker-end

