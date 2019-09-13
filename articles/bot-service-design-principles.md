---
title: Princípios de design de bot | Microsoft Docs
description: Saiba o que compõe um bom bot de conversa e como planejar e projetar bots de acordo com suas necessidades para impressionar os usuários.
keywords: melhores práticas, design de bot
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 27553a88ee4373eacb089eb6358520820509e195
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297592"
---
# <a name="principles-of-bot-design"></a>Princípios de design de bot

O Bot Framework permite aos desenvolvedores criar ótimas experiências de bot que resolvem uma variedade de problemas de negócios. Aprendendo os conceitos descritos nesta seção, você estará equipado para projetar um bot que se alinha com as melhores práticas e aproveita as lições aprendidas até o momento nessa área relativamente nova. 

## <a name="designing-a-bot"></a>Projetando um bot

Se você estiver criando um bot, é seguro pressupor que você esteja esperando que os usuários usem o bot. Também é seguro pressupor que você esteja esperando que os usuários prefiram a experiência do bot em vez de experiências alternativas, como aplicativos, sites, chamadas telefônicas e outros meios de atender a suas necessidades específicas. Em outras palavras, seu bot está competindo pelo tempo dos usuários em relação a coisas como aplicativos e sites. Portanto, como você pode maximizar as chances de que seu bot atingirá a meta final de atrair e manter os usuários? É simplesmente uma questão de priorizar os fatores certos ao projetar o bot.

## <a name="factors-that-do-not-guarantee-a-bots-success"></a>Fatores que não garantem o sucesso de um bot

Ao projetar o bot, lembre-se de que nenhum dos seguintes fatores garante necessariamente o sucesso de um bot: 

- **O grau de “inteligência” do bot**: Na maioria dos casos, é improvável que tornar seu bot mais inteligente garantirá a satisfação dos usuários ou a adoção de sua plataforma. Na realidade, muitos bots têm poucas funcionalidades avançadas de aprendizado de máquina ou idioma natural. Um bot pode incluir essas funcionalidades se ele é necessário para resolver os problemas com os quais ele foi projetado para lidar. No entanto, você não deve pressupor nenhuma correlação entre a inteligência de um bot e a adoção dele por parte do usuário.

- **A quantidade de idioma natural para a qual o bot dá suporte**: Seu bot pode ser excelente em conversas. Ele pode ter um amplo vocabulário e pode até mesmo fazer ótimas piadas. Mas, a menos que ele resolva os problemas que os usuários precisam resolver, essas funcionalidades podem contribuir com muito pouco para tornar seu bot bem-sucedido. Na verdade, alguns bots não têm nenhuma funcionalidade de conversa. E, em muitos casos, isso é perfeitamente normal.

- **Voz**: Nem sempre é verdade que habilitar os bots para fala levará a ótimas experiências do usuário. Muitas vezes, forçar os usuários a usar a voz pode resultar em uma experiência do usuário frustrante. Ao projetar seu bot, sempre considere se a voz é o canal apropriado para o problema específico. O ambiente ficará barulhento? A voz transmitirá as informações que precisam ser compartilhadas com o usuário? 

## <a name="factors-that-do-influence-a-bots-success"></a>Fatores que influenciam o sucesso de um bot

A maioria dos aplicativos ou dos sites bem-sucedidos têm, pelo menos, uma coisa em comum: uma ótima experiência do usuário. Os bots não são diferentes nesse aspecto. Portanto, garantir uma ótima experiência do usuário deve ser sua prioridade número um durante a criação de um bot. Algumas das principais considerações incluem:

- O bot resolve com facilidade o problema do usuário com o número mínimo de etapas?

- O bot resolve o problema do usuário melhor/mais facilmente/mais rápido do que qualquer uma das experiências alternativas?

- O bot é executado em dispositivos e plataformas importantes para o usuário?

- O bot é detectável? Os usuários naturalmente sabem o que fazer quando o estando usando?

Nenhuma dessas perguntas está diretamente relacionada a fatores como o grau de inteligência do bot, o nível de funcionalidade de idioma natural que ele tem, se ele usa o aprendizado de máquina ou qual linguagem de programação foi usada para criá-lo. Os usuários provavelmente não se importarão com nenhuma dessas coisas se o bot resolver o problema para o qual precisam encontrar uma solução e oferecer uma ótima experiência do usuário. Uma ótima experiência do usuário com o bot não exige que os usuários digitem muito, falem muito, repitam algo várias vezes ou expliquem coisas que o bot deve conhecer automaticamente.

> [!TIP]
> Seja qual for o tipo de aplicativo que você está criando (bot, site ou aplicativo), faça da experiência do usuário sua principal prioridade.

O processo de criação de um bot é semelhante ao processo de criação de um aplicativo ou um site. Portanto, as lições aprendidas com várias décadas de criação de interface do usuário e de entrega da experiência do usuário para aplicativos e sites ainda se aplicam quando se trata da criação de bots. 

Sempre que não tiver certeza sobre a abordagem de design ideal para o bot, pare e faça a seguinte pergunta: como você resolveria esse problema em um aplicativo ou um site? Provavelmente, a mesma resposta pode ser aplicada ao design de bot. 

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com alguns princípios básicos de design de bot, saiba mais sobre como criar a experiência do usuário e os padrões comuns no restante desta seção.