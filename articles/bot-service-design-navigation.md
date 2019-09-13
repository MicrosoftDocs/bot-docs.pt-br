---
title: Design da navegação do bot | Microsoft Docs
description: Saiba como criar uma boa estrutura de navegação para o bot e como evitar os erros mais comuns de design de navegação.
keywords: navegação, visão geral, bot teimoso, bot sem noção, bot misterioso, bot óbvio, bot que não esquece
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 7d1bfe669ebafa7e4a2104f384265760720d663c
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298118"
---
# <a name="design-bot-navigation"></a>Design da navegação do bot

Os usuários podem navegar em sites que usam trilhas, aplicativos que usam menus e navegadores que usam botões como **avançar** e **voltar**. No entanto, nenhuma dessas técnicas de navegação bem-estabelecidas atende totalmente os requisitos de navegação dentro de um bot. Conforme discutido [anteriormente](~/bot-service-design-conversation-flow.md#handle-interruptions), os usuários geralmente interagem com os bots de maneira não linear, dificultando o design da navegação do bot que propicia consistentemente uma excelente experiência de usuário. 

Considere os dilemas a seguir:

- Como você pode garantir que um usuário não se perca em uma conversa com um bot? 
- Um usuário pode navegar "de volta" em uma conversa com um bot? 
- Como um usuário navega até o "menu principal" durante uma conversa com um bot? 
- Como um usuário "cancela" uma operação durante uma conversa com um bot? 

As especificações de design da navegação do seu bot dependerão principalmente dos recursos e da funcionalidade do seu bot. Independentemente do tipo de bot que você está desenvolvendo, você deverá evitar as armadilhas comuns de interfaces de conversação mal-projetadas. Este artigo descreve essas armadilhas em termos de cinco personalidades: "bot teimoso", "bot sem noção", "bot misterioso", "bot óbvio" e "bot que não esquece". 

> [!TIP]
> Mitigar cada tipo dessas personalidades para o bot geralmente pode ser feito pelo [manuseio correto das interrupções do usuário](v4sdk/bot-builder-howto-handle-user-interrupt.md).

## <a name="the-stubborn-bot"></a>O "bot teimoso"

O bot teimoso insiste na manutenção do curso atual da conversa, mesmo quando o usuário tentar conduzir as coisas em outra direção. 

Considere este cenário: 

![bot](~/media/bot-service-design-navigation/stubborn-bot-new.png)

Os usuários geralmente mudam de ideia, decidem cancelar ou, às vezes, querem começar tudo de novo. 

> [!TIP]
> <b>Certo</b>: Projete seu bot para considerar que um usuário pode tentar alterar o curso da conversa a qualquer momento. 
>
> <b>Errado</b>: Projete seu bot para ignorar a entrada do usuário e continuar repetindo a mesma pergunta em um loop indefinido. 

Há vários métodos de evitar esta armadilha, mas talvez a maneira mais fácil de impedir que um bot faça a mesma pergunta indefinidamente é especificar um número máximo de tentativas de repetição para cada pergunta. Se ele for criado dessa maneira, o bot não estará fazendo nada "inteligente" para entender a entrada do usuário e responder adequadamente, mas pelo menos evitará a mesma pergunta em um loop infinito. 

## <a name="the-clueless-bot"></a>O "bot sem noção"

O bot sem noção responde coisas sem sentido quando não entende uma tentativa do usuário de acessar determinadas funcionalidades. Um usuário pode tentar comandos de palavra-chave comuns, como "ajuda" ou "cancelar" com expectativas razoáveis de que o bot responda adequadamente.

Considere este cenário: 

![bot](~/media/bot-service-design-navigation/clueless-bot.png)

Embora você possa ficar tentado a projetar cada diálogo dentro do seu bot para ouvir e responder adequadamente a determinadas palavras-chave, essa abordagem não é recomendável. 

> [!TIP]
> <b>Certo</b>: Implemente um [middleware](v4sdk/bot-builder-create-middleware.md) que examinará a entrada do usuário em busca das palavras-chave especificadas (por exemplo, "ajuda", "cancelar" ou "começar novamente" etc.) e responderá de acordo. 
> 
> <b>Errado</b>: Projete cada diálogo para examinar a entrada do usuário em busca de uma lista de palavras-chave. 

Ao definir a lógica em seu **middleware**, você estará tornando-o acessível para cada interação com o usuário. Usando essa abordagem, crie caixas de diálogo individuais e avisos para ignorar com segurança as palavras-chave, se isso for necessário.

## <a name="the-mysterious-bot"></a>O "bot misterioso"

O bot misterioso não reconhece imediatamente a entrada do usuário de nenhuma maneira. 

Considere este cenário: 

![bot](~/media/bot-service-design-navigation/mysterious-bot.png)

Em alguns casos, essa situação pode ser uma indicação de que o bot está tendo uma interrupção. No entanto, pode ser que o bot esteja apenas ocupado processando a entrada do usuário e ainda não tenha terminado a compilação de sua resposta. 

> [!TIP]
> <b>Certo</b>: Projete seu bot para reconhecer imediatamente a entrada do usuário, mesmo nos casos em que o bot pode levar algum tempo para compilar a resposta. 
> 
> <b>Errado</b>: Projete seu bot para adiar a confirmação da entrada do usuário até que o bot termine de compilar a resposta.

Ao confirmar imediatamente a entrada do usuário, você elimina qualquer possibilidade de confusão sobre o estado do bot. Se sua resposta levar muito tempo para compilar, envie uma mensagem de "digitando" para indicar que seu bot está funcionando e, em seguida, acompanhe com uma [mensagem proativa](v4sdk/bot-builder-howto-proactive-message.md)

## <a name="the-captain-obvious-bot"></a>O "bot óbvio"

O bot óbvio fornece informações não solicitadas que são completamente óbvias e, portanto, inúteis para o usuário. 

Considere este cenário:

![bot](~/media/bot-service-design-navigation/captainobvious-bot.png)

> [!TIP]
> <b>Certo</b>: Projete seu bot para fornecer informações que serão úteis para o usuário. 
> 
> <b>Errado</b>: Projete seu bot para fornecer informações não solicitadas que provavelmente não serão úteis para o usuário.

Ao projetar seu bot para fornecer informações úteis, você estará aumentando as chances de o usuário interagir com ele.

## <a name="the-bot-that-cant-forget"></a>O "bot que não esquece"

O bot que não esquece integra de maneira inadequada informações de conversas anteriores na atual. 

Considere este cenário:

![bot](~/media/bot-service-design-navigation/rememberall-bot.png)

> [!TIP]
> <b>Certo</b>: Projete seu bot para manter o tópico atual da conversa, a menos ou até que o usuário expresse um desejo de rever um tópico anterior. 
> 
> <b>Errado</b>: Projete seu bot para intercalar informações de conversas passadas quando isso não for relevante para a conversa atual.

Quando você mantém o tópico atual da conversa, consegue reduzir o potencial de confusão e frustrações, além de aumentar as chances de o usuário continuar a se envolver com seu bot.

## <a name="next-steps"></a>Próximas etapas

Ao criar o seu bot para evitar essas armadilhas comuns de interfaces de conversação mal-projetadas, você estará ajudará a garantir uma excelente experiência de usuário. 

Em seguida, saiba mais sobre os [Elementos da experiência do usuário](~/bot-service-design-user-experience.md) que os bots utilizam normalmente para interagir com os usuários. 
