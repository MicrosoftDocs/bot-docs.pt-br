---
title: Criar a primeira interação do usuário de um bot – Serviço de Bot
description: Saiba o que engrandece uma primeira experiência do usuário e como projetar os bots para obter êxito.
keywords: primeira impressão, início, linguagem vs menu
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 43fd8d91fdd724762825f04d839990d0ad903082
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456506"
---
# <a name="design-a-bots-first-user-interaction"></a>Projetar a primeira interação do usuário de um bot

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

## <a name="first-impressions-matter"></a>Primeiras impressões importam

A primeira interação entre o usuário e o bot é essencial para a experiência do usuário. Ao projetar o bot, tenha em mente que há mais nessa primeira mensagem do que apenas dizer "olá". Ao compilar um aplicativo, você cria a primeira tela para fornecer importantes indicações de [navegação](bot-service-design-navigation.md). Os usuários devem compreender intuitivamente as coisas, como onde o menu está localizado e como ele funciona, onde procurar ajuda, o que é a política de privacidade e assim por diante. Quando você projeta um bot, a primeira interação do usuário com o bot deve fornecer esse mesmo tipo de informação.

## <a name="language-versus-menus"></a>Linguagem versus menus

Considere os dois designs a seguir:

### <a name="design-1"></a>Design 1

![mensagem de boas-vindas, sem sugestões](./media/bot-service-design-first-interaction/hello1.png)

### <a name="design-2"></a>Design 2

![mensagem de boas-vindas, com sugestões](./media/bot-service-design-first-interaction/hello2.png)

Iniciar o bot com uma pergunta aberta, tal como "Como posso ajudar você?" geralmente não é recomendado. Se o bot tem centenas de coisas diferentes que ele pode fazer, é provável que os usuários não possam adivinhar a maioria deles. Seu bot não informa o que ele pode fazer, portanto, como eles podem possivelmente saber?

Menus fornecem uma solução simples para esse problema. Primeiramente, listando as opções disponíveis, o bot pode transmitir suas capacidades para o usuário. Em segundo lugar, menus isentam o usuário de ter que digitar muito em vez de apenas clicar. Por fim, o uso de menus pode simplificar significativamente os modelos de linguagem natural, restringindo o escopo de entrada que o bot pode receber do usuário.

> [!TIP]
> Os menus são uma ferramenta valiosa ao projetar bots para uma excelente experiência de usuário; não descartá-las como não sendo "inteligentes o suficiente".
> É possível projetar o bot para usar menus ao mesmo tempo em que dá suporte à entrada livre.
> Se um usuário responder ao menu inicial digitando em vez de selecionar uma opção de menu, o bot pode tentar analisar a entrada de texto do usuário.

Como alternativa, você pode fazer perguntas mais pontuais para conduzir o usuário se o bot tiver uma função específica. Por exemplo, se o bot é responsável por receber pedidos de sanduíche, a primeira interação poderia ser "Olá! Estou aqui para anotar seu pedido de sanduíche. Que tipo de pão você gostaria? Temos branco, de trigo ou de centeio". Dessa forma, o usuário sabe o que responder e recebe as indicações de navegação por meio da conversa.

## <a name="other-considerations"></a>Outras considerações

Além de fornecer uma primeira interação intuitiva e navegada com facilidade, um bot bem projetado fornece ao usuário acesso a informações sobre a política de privacidade e termos de uso.

> [!TIP]
> Se o bot coletar dados pessoais do usuário, é importante transmitir essa informação e descrever o que será feito com os dados.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com alguns princípios básicos para projetar a primeira interação entre o usuário e o bot, saiba mais sobre [Projetar o fluxo da conversa](~/bot-service-design-conversation-flow.md).
