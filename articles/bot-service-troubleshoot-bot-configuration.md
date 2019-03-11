---
title: Solucionar problemas de configuração de bot | Microsoft Docs
description: Como solucionar problemas de configuração em um bot implantado.
keywords: solucionar problemas, configuração, bate-papo, problemas.
author: jonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 2/26/2019
ms.openlocfilehash: 53cb392dea5ee0e355451e7455a16512f22ebbaa
ms.sourcegitcommit: cf3786c6e092adec5409d852849927dc1428e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57224844"
---
# <a name="troubleshoot-bot-configuration-issues"></a>Solucionar problemas de configuração de bot

A primeira etapa na solução de problemas de um bot é testá-lo no Webchat. Isso permitirá que você determine se o problema é específico ao seu bot (o bot não funciona em nenhum canal) ou a um determinado canal (bot funciona em alguns canais, mas não em outros).

## <a name="test-in-web-chat"></a>Testar no Webchat

1. Abra o recurso de bot no [Portal do Azure](http://portal.azure.com/).
1. Abra o painel **Testar no Webchat**.
1. Envie uma mensagem ao seu bot.

![Testar no Webchat](./media/test-in-webchat.png)

Se o bot não responder com a saída esperada, acesse [Bot não funciona no Webchat](#bot-does-not-work-in-web-chat). Caso contrário, vá para [Bot funciona no Webchat, mas não em outros canais](#bot-works-in-web-chat-but-not-in-other-channels).

## <a name="bot-does-not-work-in-web-chat"></a>Bot não funciona no Webchat

Pode haver vários motivos por que um bot não funciona. Provavelmente, o aplicativo bot está inoperante e não pode receber mensagens ou o bot recebe as mensagens mas não responde.

Para ver se o bot está em execução:

1. Abra o painel **Visão geral**.
1. Copie o **ponto de extremidade de mensagens** e cole-o no seu navegador.

Se o ponto de extremidade retornar o erro HTTP 405, isso significa que o bot está acessível e o bot é capaz de responder às mensagens. Você deve investigar se seu bot [expira](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md) ou [falha com um erro HTTP 5xx](bot-service-troubleshoot-500-errors.md).

Se o ponto de extremidade retornar um erro "Este site não pode ser acessado" ou "não é possível acessar esta página", significa que seu bot está desativado e você precisará reimplantá-lo.

## <a name="bot-works-in-web-chat-but-not-in-other-channels"></a>O Bot funciona no Webchat, mas não em outros canais

Se o bot funciona conforme o esperado no Webchat mas falha em algum outro canal, os possíveis motivos são:

- [Problemas de configuração de canal](#channel-configuration-issues)
- [Comportamento específico de canal](#channel-specific-behavior)
- [Interrupção de canal](#channel-outage)

### <a name="channel-configuration-issues"></a>Problemas de configuração de canal

É possível que os parâmetros de configuração de canal foram definidos incorretamente ou foram alterados externamente. Por exemplo, um bot configurou o canal do Facebook para uma página específica e a página foi posteriormente excluída. A solução mais simples é remover o canal e refazer a configuração do canal.

Os links a seguir fornecem instruções sobre como configurar canais com suporte no Bot Framework:

- [Cortana](bot-service-channel-connect-cortana.md)
- [DirectLine](bot-service-channel-connect-directline.md)
- [Email](bot-service-channel-connect-email.md)
- [Facebook](bot-service-channel-connect-facebook.md)
- [GroupMe](bot-service-channel-connect-groupme.md)
- [Kik](bot-service-channel-connect-kik.md)
- [Equipes da Microsoft](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-overview)
- [Skype](bot-service-channel-connect-skype.md)
- [Skype for Business](bot-service-channel-connect-skypeforbusiness.md)
- [Margem de atraso](bot-service-channel-connect-slack.md)
- [Telegram](bot-service-channel-connect-telegram.md)
- [Twilio](bot-service-channel-connect-twilio.md)

### <a name="channel-specific-behavior"></a>Comportamento específico de canal

A implementação de alguns recursos pode ser diferente por canal. Por exemplo, nem todos os canais dão suporte a cartões adaptáveis. A maioria dos canais oferecem suporte a Botões, mas eles são renderizados em um modo específico de canal. Se você ver diferenças em como alguns tipos de mensagem funcionam em canais diferentes, consulte o [Channel Inspector](https://docs.botframework.com/channel-inspector/channels/Skype).

Abaixo estão alguns links adicionais que podem ajudar com os canais individuais:

- [Adicionar bots aos aplicativos do Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-overview)
- [Facebook: Introdução à plataforma do Messenger](https://developers.facebook.com/docs/messenger-platform/introduction)
- [Princípios de design de habilidades da Cortana](https://docs.microsoft.com/cortana/skills/design-principles)
- [Skype para Desenvolvedores](https://dev.skype.com/bots)
- [Slack: Habilitar as interações com os bots](https://api.slack.com/bot-users)

### <a name="channel-outage"></a>Interrupção de canal

Ocasionalmente, alguns canais podem ter uma interrupção de serviço. Geralmente, essas interrupções não duram muito. No entanto, se você suspeitar de uma interrupção, consulte um site da web ou mídia social do canal.

Outra maneira de determinar se um canal tem uma interrupção é criar um bot de teste (por exemplo, um Bot de Eco simples) e adicionar um canal. Se o bot de teste funciona com alguns canais, mas não com outros, indicaria que o problema não está em seu bot de produção.

## <a name="additional-resources"></a>Recursos adicionais

Configura as instruções sobre como [depurar um bot](bot-service-debug-bot.md) e outros artigos sobre depuração nesta seção.