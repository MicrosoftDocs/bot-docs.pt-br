---
title: Implementar uma habilidade para o Power Virtual Agents | Microsoft Docs
description: Saiba como implementar uma habilidade que pode ser usada no Power Virtual Agents, usando o SDK do Bot Framework.
keywords: habilidades
author: clearab
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/17/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d02eb70351df5a895d3ee73d4800757d1ec374ab
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417377"
---
# <a name="implement-a-skill-for-use-in-power-virtual-agents"></a>Implementar uma habilidade para uso no Power Virtual Agents

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Uma habilidade é um bot que pode ser usado por outro bot. Dessa forma, você pode criar um só bot voltado para o usuário e estendê-lo com uma ou mais habilidades. Saiba mais sobre as habilidades em geral na [Visão geral das habilidades](skills-conceptual.md) e como criá-las em [Implementar uma habilidade](skill-implement-skill.md). Como alternativa, os modelos do Assistente Virtual contêm um conjunto de [habilidades predefinidas](bot-builder-skills-overview.md) que você pode personalizar e implantar em vez de criar uma do zero.

Se você espera que suas habilidades sejam consumidas de um bot de [agentes virtuais de energia](https://powerva.microsoft.com/#/) , há algumas restrições adicionais colocadas em suas habilidades que você precisará considerar.

## <a name="manifest-restrictions"></a>Restrições de manifesto

O Power Virtual Agents impõe restrições no que pode ser declarado no [manifesto de habilidades](./skills-write-manifest-2-1.md).

- Você pode declarar apenas 25 ou menos ações.
- Cada ação é limitada a 25 ou menos entradas ou saídas.
- Você não pode usar o tipo de matriz para entradas ou saídas.

## <a name="same-tenant-restriction"></a>Restrição de mesmo locatário

Para garantir a conformidade e a governança adequada das habilidades personalizadas que estão sendo registradas para uso no Power Virtual Agents, seu bot de habilidades precisa ser um aplicativo registrado no Azure Active Directory. Após a adição de uma habilidade, validamos se a ID do aplicativo da habilidade está no locatário do usuário conectado e se o ponto de extremidade da habilidade corresponde à `Home Page URL` do aplicativo registrado.

Para fazer o registro do bot como uma habilidade no Power Virtual Agents, verifique se, para o bot, a [home page no portal do Azure](/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page#change-the-home-page-in-the-azure-portal) está definida com a URL do manifesto de habilidades do bot.

## <a name="validation-performed-during-registering-a-skill"></a>Validação executada durante o registro de uma habilidade

Quando um usuário final tenta se conectar à sua habilidade em um bot do Power Virtual Agents, primeiro ele precisa [importar a habilidade para o Power Virtual Agents](/power-virtual-agents/advanced-use-skills). Sua habilidade passará por uma série de verificações de validação. Uma falha em uma dessas verificações poderá resultar em uma mensagem de erro, conforme descrito nesta tabela.

| Etapa de validação | Código do erro | Mensagem de erro | Descrição ou mitigação
| :-- | :-- | :-- | :--
| A URL do manifesto é válida | `URL_MALFORMED`, `URL_NOT_HTTPS` | O link não é válido; o link precisa começar com https:// | Insira novamente o link como uma URL segura.
| O manifesto é recuperável | `MANIFEST_FETCH_FAILED` | Encontramos problemas ao obter o manifesto de habilidades. | Verifique se a URL do manifesto é um link para seu manifesto; Tente abrir a URL do manifesto em um navegador da Web. Se a URL renderizar a página em 10 segundos, registre novamente sua habilidade.
| O manifesto é legível | `MANIFEST_TOO_LARGE` | O manifesto é muito grande. | Seu manifesto deve ter 500 KB ou menos.
| O manifesto é legível | `MANIFEST_MALFORMED` | O manifesto é incompatível. | Verifique se o manifesto é um arquivo JSON válido. Verifique se o manifesto contém as propriedades necessárias, como `name` , `msaAppId` e assim por diante. Consulte [restrições de manifesto](#manifest-restrictions) para obter mais informações.
| A habilidade ainda não está registrada | `MANIFEST_ALREADY_IMPORTED` | Esta habilidade já foi adicionada ao bot. | Exclua a habilidade e registre-a novamente.
| Correspondência de domínios de ponto de extremidade de manifesto e página inicial | `MANIFEST_ENDPOINT_ORIGIN_MISMATCH` | Há uma incompatibilidade nos pontos de extremidade da habilidade. | O domínio da URL da home page do aplicativo do Azure AD e o domínio da URL do manifesto devem corresponder. Confira [Restrição de mesmo locatário](#same-tenant-restriction)
| A habilidade está hospedada no locatário do usuário conectado | `APPID_NOT_IN_TENANT` | Para adicionar uma habilidade, primeiro ela precisa ser registrada.| Um administrador global precisa registrar a habilidade na organização do usuário conectado.
| As ações são limitadas | `LIMITS_TOO_MANY_ACTIONS` | A habilidade é limitada a 25 ações.|Há muitas ações de habilidades definidas no manifesto de habilidades. Remova as ações e tente novamente.
| Os parâmetros de entrada da ação são limitados | `LIMITS_TOO_MANY_INPUTS` | As ações são limitadas a 25 entradas.|Há muitos parâmetros de entrada da ação da habilidade. Remova os parâmetros e tente novamente.
| Parâmetros de saída de ação são limitados | `LIMITS_TOO_MANY_OUTPUTS` | As ações são limitadas a 25 saídas.|Há muitos parâmetros de saída da ação da habilidade. Remova o parâmetro e tente novamente.
| A contagem de habilidades é limitada | `LIMITS_TOO_MANY_SKILLS` | O bot pode ter um máximo de 25 habilidades.| Há muitas habilidades adicionadas a um bot. Remova uma habilidade existente e tente novamente.
| O token de segurança é válido | `AADERROR_OTHER` | Parece que algo deu errado.|Pode haver um erro transitório para adquirir um token de segurança e disparar a habilidade. Tente importar a habilidade novamente.
| A habilidade está íntegra | `ENDPOINT_HEALTHCHECK_FAILED`, `HEALTH_PING_FAILED` | Algo deu errado ao verificar sua habilidade. | Os agentes virtuais de energia receberam uma resposta desconhecida ao enviar uma `EndOfConversation` atividade para sua habilidade. Verifique se a habilidade está em execução e se ela responde corretamente.
| A habilidade é autorizada | `ENDPOINT_HEALTHCHECK_UNAUTHORIZED` | Essa habilidade não permite-listar o bot. | Verifique se o bot foi adicionado à lista de permissões da habilidade. Para obter mais informações, consulte os agentes virtuais de energia como [Configurar uma habilidade](/power-virtual-agents/configuration-add-skills#configure-a-skill-for-use-in-power-virtual-agents).
