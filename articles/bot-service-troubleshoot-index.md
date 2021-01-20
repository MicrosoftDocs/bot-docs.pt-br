---
title: Serviço TroubleshootIndex-bot do bot Framework
description: Índice do TroubleshootIndex bot Framework.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/15/2020
ms.openlocfilehash: 7a967df67e7bb5e4c095bb41f5b48c405294c0e3
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576878"
---
# <a name="troubleshoot-index"></a>Solucionar problemas de índice

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

<!-- Attention writers!! When you create a new FAQ, please add the related link to the proper section below. -->

A seguir estão algumas perguntas comuns que abordam os problemas que você pode enfrentar.
Caso você não encontre a resposta que está procurando, poderá postar suas perguntas em [Stack Overflow](https://stackoverflow.com/questions/tagged/botframework) usando a `botframework` marca. Se você for um novo usuário, visite a [Stack Overflow centro de ajuda](https://stackoverflow.com/help/how-to-ask).

## <a name="troubleshoot-general"></a>Solucionar problemas gerais

- [Como é possível solucionar problemas com o bot?](bot-service-troubleshoot-general-problems.md#how-can-i-troubleshoot-issues-with-my-bot)
- [Como é possível solucionar problemas de autenticação?](bot-service-troubleshoot-general-problems.md#how-can-i-troubleshoot-authentication-issues)
- [Estou usando o SDK do bot Framework para .NET. Como posso solucionar problemas com meu bot?](bot-service-troubleshoot-general-problems.md#im-using-the-bot-framework-sdk-for-net-how-can-i-troubleshoot-issues-with-my-bot)
- [Como fazer testar a conectividade de rede entre bots e um canal?](bot-service-troubleshoot-general-problems.md#how-do-i-test-network-connectivity-between-bots-and-a-channel)
- [O que causa um erro com o código de status HTTP 429 "Muitas solicitações"?](bot-service-troubleshoot-general-problems.md#what-causes-an-error-with-http-status-code-429-too-many-requests)
- [Por que as mensagens do meu bot não estão sendo recebidas pelo usuário?](bot-service-troubleshoot-general-problems.md#why-arent-my-bot-messages-getting-received-by-the-user)
- [Como é possível executar tarefas em segundo plano no ASP.NET?](bot-service-troubleshoot-general-problems.md#how-can-i-run-background-tasks-in-aspnet)
- [Meu bot está lento para responder à primeira mensagem que recebe. Como posso torná-lo mais rápido?](bot-service-troubleshoot-general-problems.md#my-bot-is-slow-to-respond-to-the-first-message-it-receives-how-can-i-make-it-faster)
- [Como é possível garantir a ordem de entrega da mensagem?](bot-service-troubleshoot-general-problems.md#how-can-i-guarantee-message-delivery-order)
- [Por que partes do texto da mensagem estão sendo removidas?](bot-service-troubleshoot-general-problems.md#why-are-parts-of-my-message-text-being-dropped)
- [Como dar suporte a vários bots no mesmo ponto de extremidade de serviço de bot?](bot-service-troubleshoot-general-problems.md#how-can-i-support-multiple-bots-at-the-same-bot-service-endpoint)
- [Como os identificadores funcionam no Bot Framework?](bot-service-troubleshoot-general-problems.md#how-do-identifiers-work-in-the-bot-framework)
- [Como é possível obter acesso à ID de usuário?](bot-service-troubleshoot-general-problems.md#how-can-i-get-access-to-the-user-id)
- [Por que meus nomes de usuário do Facebook não estão mais sendo exibidos?](bot-service-troubleshoot-general-problems.md#why-are-my-facebook-user-names-not-showing-anymore)
- [Por que meu bot no Kik está respondendo "Desculpe, não posso falar agora"?](bot-service-troubleshoot-general-problems.md#why-is-my-kik-bot-replying-im-sorry-i-cant-talk-right-now)
- [Como é possível usar serviços autenticados do meu bot?](bot-service-troubleshoot-general-problems.md#how-can-i-use-authenticated-services-from-my-bot)
- [Como é possível limitar o acesso ao bot para uma lista pré-determinada de usuários?](bot-service-troubleshoot-general-problems.md#how-can-i-limit-access-to-my-bot-to-a-pre-determined-list-of-users)
- [Por que minha conversa com Direct Line 1.1 inicia novamente após cada mensagem?](bot-service-troubleshoot-general-problems.md#why-does-my-direct-line-11-conversation-start-over-after-every-message)
- [O que faz com que o serviço Direct Line 3.0 responda com o código de status HTTP 502 "Gateway Incorreto"?](bot-service-troubleshoot-general-problems.md#what-causes-the-direct-line-30-service-to-respond-with-http-status-code-502-bad-gateway)
- [Por que recebo uma exceção Authorization_RequestDenied ao criar um bot?](bot-service-troubleshoot-general-problems.md#why-do-i-get-an-authorization_requestdenied-exception-when-creating-a-bot)
- [Por que não consigo migrar o bot?](bot-service-troubleshoot-general-problems.md#why-cant-i-migrate-my-bot)
- [Onde é possível obter mais ajuda?](bot-service-troubleshoot-general-problems.md#where-can-i-get-more-help)

## <a name="configuration"></a>Configuração

- [Testar no Webchat](bot-service-troubleshoot-bot-configuration.md#test-in-web-chat)
- [O bot não funciona no Webchat](bot-service-troubleshoot-bot-configuration.md#bot-does-not-work-in-web-chat)
- [O Bot funciona no Webchat, mas não em outros canais](bot-service-troubleshoot-bot-configuration.md#bot-works-in-web-chat-but-not-in-other-channels)
    - [Problemas de configuração de canal](bot-service-troubleshoot-bot-configuration.md#channel-configuration-issues)
    - [Comportamento específico de canal](bot-service-troubleshoot-bot-configuration.md#channel-specific-behavior)
    - [Interrupção de canal](bot-service-troubleshoot-bot-configuration.md#channel-outage)

## <a name="http-500-errors"></a>Erros HTTP 500

- [Habilitar Application Insights em ASP.NET](bot-service-troubleshoot-500-errors.md#enable-application-insights-on-aspnet)
- [Habilitar o Application Insights no Node.js](bot-service-troubleshoot-500-errors.md#enable-application-insights-on-nodejs)
- [Consultar exceções](bot-service-troubleshoot-500-errors.md#query-for-exceptions)
- [Sem eventos do Application Insights](bot-service-troubleshoot-500-errors.md#no-application-insights-events)

## <a name="authentication"></a>Autenticação

- [ID do aplicativo e senha](bot-service-troubleshoot-authentication-problems.md#app-id-and-password)
- [Etapa 1: desabilitar a segurança e testar no localhost](bot-service-troubleshoot-authentication-problems.md#step-1-disable-security-and-test-on-localhost)
- [Etapa 2: verificar a ID do aplicativo e a senha do bot](bot-service-troubleshoot-authentication-problems.md#step-2)
- [Etapa 3: habilitar a segurança e o teste no localhost](bot-service-troubleshoot-authentication-problems.md#step-3-enable-security-and-test-on-localhost-)
- [Etapa 4: testar o bot na nuvem](bot-service-troubleshoot-authentication-problems.md#step-4-test-your-bot-in-the-cloud)
