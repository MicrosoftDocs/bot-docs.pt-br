---
title: Inserir um bot em um site – Serviço de Bot
description: Saiba como inserir bots em sites usando um controle da Web. Veja como o mecanismo de backchannel facilita a comunicação privada entre as páginas da Web e os bots.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: bcc2f2455e0677ac1349761c3b6f5f0736211da5
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415091"
---
# <a name="embed-a-bot-in-a-website"></a>Inserir um bot em um site

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Embora seja mais comum ver bots fora de sites, eles também podem ser inseridos em um site. Por exemplo, você pode inserir um [bot de conhecimento](~/bot-service-design-pattern-knowledge-base.md) dentro de um site para permitir que os usuários encontrem informações as quais, de outra forma, poderiam ser difíceis de localizar dentro de estruturas complexas de sites. Ou, você pode inserir um bot dentro de um site de suporte técnico para atuar como o primeiro respondente às solicitações do usuário. O bot poderia resolver de forma independente problemas simples e [repassar](~/bot-service-design-pattern-handoff-human.md) os problemas mais complexos para um agente humano. 

Este artigo explora a integração dos bots com sites, e o processo de usar o mecanismo *backchannel* para facilitar a comunicação privada entre uma página da Web e um bot. 

A Microsoft fornece duas maneiras diferentes de integrar um bot a um site: o controle da Web do Skype e um controle da Web de software livre.

## <a name="skype-web-control"></a>Controle da Web do Skype

>[!NOTE]
> Desde 31 de outubro de 2019, o canal do Skype não aceita mais novas solicitações de publicação de bot. Isso significa que você pode continuar a desenvolver bots usando o canal do Skype, mas o bot será limitado a 100 usuários. Você não poderá publicar o bot para um público-alvo maior. Os bots atuais do Skype continuarão a ser executados sem interrupção. Leia mais sobre [por que alguns recursos não estão mais disponíveis no Skype](https://support.skype.com/faq/fa12091/why-are-some-features-not-available-in-skype-anymore).


O [controle da Web do Skype](https://aka.ms/bot-skype-web-control) é, essencialmente, um cliente do Skype em um controle habilitado para Web. A autenticação integrada do Skype permite que o bot autentique e reconheça usuários, sem exigir que o desenvolvedor escreva qualquer código personalizado. O Skype reconhece automaticamente Contas da Microsoft usadas em seu cliente da Web. 

Como o controle da Web do Skype age simplesmente como um front-end para o Skype, o cliente do Skype do usuário tem acesso automático a todo o contexto de qualquer conversa facilitada pelo controle da Web. Mesmo após fechar o navegador da Web, o usuário pode continuar a interagir com o bot usando o cliente do Skype. 

## <a name="open-source-web-control"></a>Controle da Web de software livre

O <a href="https://aka.ms/BotFramework-WebChat" target="_blank">controle de Webchat de software livre</a> tem base em ReactJS e usa a [Direct Line API][directLineAPI] para se comunicar com o Bot Framework. O controle de chat na Web fornece uma tela em branco para implementação do chat na Web, dando a você controle total sobre o comportamento dele e sobre a experiência de usuário que ele oferece. 

O mecanismo de *backchannel* permite que a página da Web que está hospedando o controle, se comunique diretamente com o bot de uma maneira completamente invisível ao usuário. Esse recurso possibilita diversos cenários úteis: 

- A página da Web pode enviar dados relevantes para o bot (por exemplo, localização do GPS).
- A página da Web pode aconselhar o bot sobre ações do usuário (por exemplo, "o usuário acabou de selecionar a Opção A na lista suspensa").
- A página da Web pode enviar ao bot o token de autenticação de um usuário conectado.
- O bot pode enviar os dados relevantes à página da Web (por exemplo, valor atual do portfólio do usuário).
- O bot pode enviar "comandos" à página da Web (por exemplo, mudar cor de fundo).

## <a name="using-the-backchannel-mechanism"></a>Usar mecanismo de backchannel

[!INCLUDE [Introduction to backchannel mechanism](~/includes/snippet-backchannel.md)]

## <a name="sample-code"></a>Código de exemplo

O <a href="https://aka.ms/BotFramework-WebChat" target="_blank">controle de chat na Web de software livre</a> está disponível por meio do GitHub. Para obter detalhes sobre como você pode implementar o mecanismo de backchannel usando o controle de chat na Web de software livre e o SDK do Bot Framework para Node.js, consulte [Usar o mecanismo backchannel](~/nodejs/bot-builder-nodejs-backchannel.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Direct Line API][directLineAPI]
- [Controle de chat da Web de software livre](https://github.com/Microsoft/BotFramework-WebChat)
- [Usar mecanismo de backchannel](~/nodejs/bot-builder-nodejs-backchannel.md)

[directLineAPI]: https://docs.botframework.com/restapi/directline3/#navtitle
