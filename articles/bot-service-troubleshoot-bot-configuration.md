---
title: Solucionar problemas de configuração de bot – Serviço de Bot
description: Como solucionar problemas de configuração em um bot implantado.
keywords: solucionar problemas, configuração, bate-papo, problemas.
author: jonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/20/2020
ms.openlocfilehash: 775ffd3a72f69ad721eacbe31b27ddbe5b1fa5e5
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77519965"
---
# <a name="troubleshoot-bot-configuration-issues"></a>Solucionar problemas de configuração de bot

Um bot pode gerar diferentes tipos de erros, como não ser capaz de responder, gerar erros ou trabalhar em um canal, mas não em outro. A primeira etapa na solução de problemas de um bot é testá-lo no Webchat. Isso permitirá que você determine se o problema é específico ao seu bot (o bot não funciona em nenhum canal) ou a um determinado canal (bot funciona em alguns canais, mas não em outros).

## <a name="test-in-web-chat"></a>Testar no Webchat

1. Abra o recurso de bot no [Portal do Azure](https://portal.azure.com/).
1. Abra o painel **Testar no Webchat**.
1. Envie uma mensagem ao seu bot.

![Testar no Webchat](./media/test-in-webchat.png)

Se o bot não responder com a saída esperada, acesse [Bot não funciona no Webchat](#bot-does-not-work-in-web-chat). Caso contrário, vá para [Bot funciona no Webchat, mas não em outros canais](#bot-works-in-web-chat-but-not-in-other-channels).

## <a name="bot-does-not-work-in-web-chat"></a>Bot não funciona no Webchat

Pode haver vários motivos por que um bot não funciona. Provavelmente, o aplicativo bot está inoperante e não pode receber mensagens ou o bot recebe as mensagens mas não responde. Aqui estão algumas das possíveis causas:

- O bot está inoperante e não pode ser acessado.
- O bot está falhando.
- O ponto de extremidade do bot está incorreto.
- O bot está recebendo suas mensagens com sucesso, mas não consegue responder.

Para ver se o bot está em execução:

1. Abra o painel **Visão geral**.
1. Copie o **ponto de extremidade de mensagens** e cole-o no seu navegador.

Se o ponto de extremidade retornar o erro HTTP 404 ou 405, isso significará que o bot está acessível e que é capaz de responder às mensagens. Para investigar problemas de tempo limite, confira os artigos [tempo limite](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md) ou [falha com um erro HTTP 5xx](bot-service-troubleshoot-500-errors.md).

Se o ponto de extremidade retornar um erro "Este site não pode ser acessado" ou "não é possível acessar esta página", significa que seu bot está desativado e você precisará reimplantá-lo.

## <a name="bot-works-in-web-chat-but-not-in-other-channels"></a>O Bot funciona no Webchat, mas não em outros canais

Se o bot funciona conforme o esperado no Webchat mas falha em algum outro canal, os possíveis motivos são:

- [Solucionar problemas da configuração de bot](#troubleshoot-bot-configuration-issues)
  - [Testar no Webchat](#test-in-web-chat)
  - [O bot não funciona no Webchat](#bot-does-not-work-in-web-chat)
  - [O Bot funciona no Webchat, mas não em outros canais](#bot-works-in-web-chat-but-not-in-other-channels)
    - [Problemas de configuração de canal](#channel-configuration-issues)
    - [Comportamento específico de canal](#channel-specific-behavior)
    - [Interrupção de canal](#channel-outage)
  - [Recursos adicionais](#additional-resources)

### <a name="channel-configuration-issues"></a>Problemas de configuração de canal

É possível que os parâmetros de configuração do canal, como o nome de usuário e a senha do bot, tenham sido definidos de modo incorreto ou alterados externamente. Por exemplo, um bot foi configurado com o canal do Facebook para uma página específica e a página foi posteriormente excluída. A solução mais simples é remover o canal e refazer a configuração do canal.

Os links a seguir fornecem instruções sobre como configurar canais com suporte no Bot Framework:

- [Cortana](bot-service-channel-connect-cortana.md)
- [Linha direta](bot-service-channel-connect-directline.md)
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

A implementação de alguns recursos pode ser diferente por canal. Por exemplo, nem todos os canais dão suporte a cartões adaptáveis. A maioria dos canais é compatível com Ações (botões), mas é renderizada em um modo específico de canal. Se detectar diferenças em como alguns tipos de mensagem funcionam em canais distintos, consulte o artigo de [referência de canais](bot-service-channels-reference.md).

Abaixo estão alguns links adicionais que podem ajudar com os canais individuais:

- [Adicionar bots aos aplicativos do Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-overview)
- [Facebook: Introdução à plataforma do Messenger](https://developers.facebook.com/docs/messenger-platform/introduction)
- [Princípios de design de habilidades da Cortana](https://docs.microsoft.com/cortana/skills/design-principles)
- [Skype para Desenvolvedores](https://dev.skype.com/bots)
- [Slack: Habilitar as interações com os bots](https://api.slack.com/bot-users)

### <a name="channel-outage"></a>Interrupção de canal

Ocasionalmente, alguns canais podem ter uma interrupção de serviço. Geralmente, essas interrupções não duram muito. No entanto, se você suspeitar de uma interrupção, confira o site ou a mídia social do canal.

Outra maneira de determinar se um canal tem uma interrupção é criar um bot de teste (por exemplo, um Bot de Eco simples) e adicionar um canal. Se o bot de teste funciona com alguns canais, mas não com outros, indicaria que o problema não está em seu bot de produção.

## <a name="additional-resources"></a>Recursos adicionais

Configura as instruções sobre como [depurar um bot](bot-service-debug-bot.md) e outros artigos sobre depuração nesta seção.
