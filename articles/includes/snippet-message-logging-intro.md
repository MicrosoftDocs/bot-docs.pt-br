---
ms.openlocfilehash: 12ead266dea859c84450e08ae12ed98d4952d698
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "70297384"
---
A funcionalidade **middleware** no SDK do Bot Framework permite que o bot intercepte todas as mensagens trocadas entre o usuário e o bot. Para cada mensagem interceptada, você poderá salvar a mensagem em um armazenamento de dados especificado, o que cria um log de conversa, ou inspecionar a mensagem de alguma forma e executar qualquer ação especificada pelo código. 

> [!NOTE]
> O Bot Framework não salva automaticamente os detalhes da conversa, pois fazer isso possivelmente iria capturar informações particulares que usuários e bots não querem compartilhar com participantes externos. Se o bot salva os detalhes de conversa, ele deve informar isso ao usuário e descrever o que será feito com os dados.