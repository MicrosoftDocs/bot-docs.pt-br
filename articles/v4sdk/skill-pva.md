---
title: Implementar uma habilidade para o Power Virtual Agents | Microsoft Docs
description: Saiba como implementar uma habilidade que pode ser usada no Power Virtual Agents, usando o SDK do Bot Framework.
keywords: habilidades
author: clearab
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/15/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: bec4f891c8870ecadd2e04e82d5ff33e6f73a8aa
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566646"
---
# <a name="implement-a-skill-for-use-in-power-virtual-agents"></a>Implementar uma habilidade para uso no Power Virtual Agents

[!INCLUDE[applies-to](../includes/applies-to.md)]

Uma habilidade é um bot que pode ser usado por outro bot. Dessa forma, você pode criar um só bot voltado para o usuário e estendê-lo com uma ou mais habilidades. Saiba mais sobre as habilidades em geral na [Visão geral das habilidades](skills-conceptual.md) e como criá-las em [Implementar uma habilidade](skill-implement-skill.md). Como alternativa, os modelos do Assistente Virtual contêm um conjunto de [habilidades predefinidas](bot-builder-skills-overview.md) que você pode personalizar e implantar em vez de criar uma do zero.

Se você espera que as suas habilidades sejam consumidas em um bot do [Power Virtual Agents](https://powerva.microsoft.com/#/), há algumas restrições adicionais impostas às habilidades que você precisará considerar.

## <a name="manifest-restrictions"></a>Restrições de manifesto

O Power Virtual Agents impõe restrições no que pode ser declarado no [manifesto de habilidades](./skills-write-manifest-2-1.md).

- Você pode declarar apenas 25 ou menos ações.
- Cada ação é limitada a 25 ou menos entradas ou saídas.
- Você não pode usar o tipo de matriz para entradas ou saídas.
- No momento, há suporte apenas para o idioma inglês (localidade 'en')

## <a name="same-tenant-restriction"></a>Restrição de mesmo locatário

Para garantir a conformidade e a governança adequada das habilidades personalizadas que estão sendo registradas para uso no Power Virtual Agents, seu bot de habilidades precisa ser um aplicativo registrado no Azure Active Directory. Após a adição de uma habilidade, validamos se a ID do aplicativo da habilidade está no locatário do usuário conectado e se o ponto de extremidade da habilidade corresponde à `Home Page URL` do aplicativo registrado.

Para fazer o registro do bot como uma habilidade no Power Virtual Agents, verifique se, para o bot, a [home page no portal do Azure](/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page#change-the-home-page-in-the-azure-portal) está definida com a URL do manifesto de habilidades do bot.

## <a name="validation-performed-during-registering-a-skill"></a>Validação executada durante o registro de uma habilidade

Quando um usuário final tenta se conectar à sua habilidade em um bot do Power Virtual Agents, primeiro ele precisa [importar a habilidade para o Power Virtual Agents](/power-virtual-agents/advanced-use-skills). Sua habilidade passará por uma série de verificações de validação. Uma falha em uma dessas verificações poderá resultar em uma mensagem de erro, conforme descrito nesta tabela.

Etapa de validação|Mensagem de erro|Descrição ou mitigação
|---|---|---
|Validar URL do manifesto de habilidades|O link não é válido; o link precisa começar com https:// | Insira novamente o link como uma URL segura. |
|Validar se o manifesto de habilidades pode ser recuperado|Encontramos problemas ao obter o manifesto de habilidades.| Confirme se a URL do manifesto é um link para o manifesto e pode ser baixada como um arquivo .json.|
|Validar se o manifesto de habilidades pode ser lido|O manifesto é muito grande; o manifesto é incompatível.| Corrija os erros sintáticos no manifesto. Confira [Restrições de manifesto](#manifest-restrictions) |
|Validar se a habilidade foi registrada anteriormente|Esta habilidade já foi adicionada ao bot.|O usuário final já adicionou sua habilidade ao PVA. |
|Validar a origem do ponto de extremidade do manifesto de habilidade|Há uma incompatibilidade nos pontos de extremidade da habilidade.|O domínio da URL da home page do aplicativo do AAD e o domínio da URL do manifesto precisam ser correspondentes. Confira [Restrição de mesmo locatário](#same-tenant-restriction)|
|Validar se a habilidade está hospedada no locatário do usuário conectado|Para adicionar uma habilidade, primeiro ela precisa ser registrada.| Um administrador global precisa registrar a habilidade na organização do usuário conectado. |
|Validar as ações da habilidade|A habilidade é limitada a 25 ações.|Há muitas ações de habilidades definidas no manifesto de habilidades. Remova as ações e tente novamente. |
|Validar parâmetros de entrada da ação da habilidade|As ações são limitadas a 25 entradas.|Há muitos parâmetros de entrada da ação da habilidade. Remova os parâmetros e tente novamente. |
|Validar os parâmetros de saída da ação da habilidade|As ações são limitadas a 25 saídas.|Há muitos parâmetros de saída da ação da habilidade. Remova o parâmetro e tente novamente. |
|Validar a contagem de habilidades|O bot pode ter um máximo de 25 habilidades.| Há muitas habilidades adicionadas a um bot. Remova uma habilidade existente e tente novamente. |
|Validar o idioma da ação da habilidade|Atualmente, há suporte apenas para o inglês nas habilidades.| A habilidade tem ações com localidades sem suporte. Só há suporte para habilidades com ações em inglês (localidades `en-`). |
|Validar a configuração de aplicativo do AAD |A habilidade precisa ser registrada como multilocatário.| Verifique se o aplicativo do AAD está marcado como multilocatário. Confira [Converter o aplicativo em multilocatário](/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant#update-registration-to-be-multi-tenant) |
|Validar o token de segurança |Parece que algo deu errado.|Pode haver um erro transitório para adquirir um token de segurança e disparar a habilidade. Tente importar a habilidade novamente.|
|Validar a integridade da habilidade|Algo deu errado ao verificar sua habilidade.|O PVA recebeu uma resposta desconhecida ao enviar uma atividade `EndOfConversation` para a sua habilidade. Verifique se a habilidade está em execução e se ela responde corretamente.|
