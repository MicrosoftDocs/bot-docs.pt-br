---
ms.openlocfilehash: 12ead266dea859c84450e08ae12ed98d4952d698
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54226011"
---
A funcionalidade **middleware** no SDK do Bot Framework permite que o bot intercepte todas as mensagens trocadas entre o usuário e o bot. Para cada mensagem interceptada, você poderá salvar a mensagem em um armazenamento de dados especificado, o que cria um log de conversa, ou inspecionar a mensagem de alguma forma e executar qualquer ação especificada pelo código. 

> [!NOTE]
> O Bot Framework não salva automaticamente os detalhes da conversa, pois fazer isso possivelmente iria capturar informações particulares que usuários e bots não querem compartilhar com participantes externos. Se o bot salva os detalhes de conversa, ele deve informar isso ao usuário e descrever o que será feito com os dados.