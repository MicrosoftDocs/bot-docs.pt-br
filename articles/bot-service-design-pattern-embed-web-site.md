---
title: Inserir um bot em um site | Microsoft Docs
description: Saiba como criar um bot que será inserido em um site.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 8f50c54c0841db5778c7966e30ec33f89938b376
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296802"
---
# <a name="embed-a-bot-in-a-website"></a>Inserir um bot em um site

Embora seja mais comum ver bots fora de sites, eles também podem ser inseridos em um site. Por exemplo, você pode inserir um [bot de conhecimento](~/bot-service-design-pattern-knowledge-base.md) dentro de um site para permitir que os usuários encontrem informações as quais, de outra forma, poderiam ser difíceis de localizar dentro de estruturas complexas de sites. Ou, você pode inserir um bot dentro de um site de suporte técnico para atuar como o primeiro respondente às solicitações do usuário. O bot poderia resolver de forma independente problemas simples e [repassar](~/bot-service-design-pattern-handoff-human.md) os problemas mais complexos para um agente humano. 

Este artigo explora a integração dos bots com sites, e o processo de usar o mecanismo *backchannel* para facilitar a comunicação privada entre uma página da Web e um bot. 

A Microsoft fornece duas maneiras diferentes de integrar um bot a um site: o controle da Web do Skype e um controle da Web de software livre.

## <a name="skype-web-control"></a>Controle da Web do Skype

O controle da Web do Skype é, essencialmente, um cliente do Skype em um controle habilitado para Web. A autenticação integrada do Skype permite que o bot autentique e reconheça usuários, sem exigir que o desenvolvedor escreva qualquer código personalizado. O Skype reconhece automaticamente Contas da Microsoft usadas em seu cliente da Web. 

Como o controle da Web do Skype age simplesmente como um front-end para o Skype, o cliente do Skype do usuário tem acesso automático a todo o contexto de qualquer conversa facilitada pelo controle da Web. Mesmo após fechar o navegador da Web, o usuário pode continuar a interagir com o bot usando o cliente do Skype. 

## <a name="open-source-web-control"></a>Controle da Web de software livre

O <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de chat na Web de software livre</a> tem base em ReactJS e usa a [API de Linha Direta][directLineAPI] para se comunicar com o Bot Framework. O controle de chat na Web fornece uma tela em branco para implementação do chat na Web, dando a você controle total sobre o comportamento dele e sobre a experiência de usuário que ele oferece. 

O mecanismo de *backchannel* permite que a página da Web que está hospedando o controle, se comunique diretamente com o bot de uma maneira completamente invisível ao usuário. Esse recurso possibilita diversos cenários úteis: 

- A página da Web pode enviar dados relevantes para o bot (por exemplo, localização do GPS).
- A página da Web pode aconselhar o bot sobre ações do usuário (por exemplo, "o usuário acabou de selecionar a Opção A na lista suspensa").
- A página da Web pode enviar ao bot o token de autenticação de um usuário conectado.
- O bot pode enviar os dados relevantes à página da Web (por exemplo, valor atual do portfólio do usuário).
- O bot pode enviar "comandos" à página da Web (por exemplo, mudar cor de fundo).

## <a name="using-the-backchannel-mechanism"></a>Usar mecanismo de backchannel

[!INCLUDE [Introduction to backchannel mechanism](~/includes/snippet-backchannel.md)]

## <a name="sample-code"></a>Exemplo de código

O <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de chat na Web de software livre</a> está disponível por meio do GitHub. Para obter detalhes sobre como você pode implementar o mecanismo de backchannel usando o controle de chat na Web de software livre e o SDK do Construtor de Bot para Node.js, confira [Usar o mecanismo backchannel](~/nodejs/bot-builder-nodejs-backchannel.md).

## <a name="additional-resources"></a>Recursos adicionais

- [API de Linha Direta][directLineAPI]
- [TODO](~/dotnet/bot-builder-dotnet-activities.md)
- [Usar mecanismo de backchannel](~/nodejs/bot-builder-nodejs-backchannel.md)

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle