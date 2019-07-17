---
ms.openlocfilehash: 91b2729aa10c8ac1985e62845126296e8141ef77
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230636"
---
> [!IMPORTANT]
> O encadeamento que manipula o giro do bot primário lida com a disposição do objeto de contexto quando isso é feito. **Certifique-se que `await` todas as chamadas de atividade** para que o segmento principal espere na atividade gerada antes de terminar o processamento e a eliminação do contexto de turno. Caso contrário, se uma resposta (incluindo seus manipuladores) demora um tempo significativo e tenta agir no objeto de contexto, ela pode receber um erro de _contexto descartado_.