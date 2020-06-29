---
ms.openlocfilehash: 99bd2b58bafd1c4ece57aad5decc710346f56f86
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "74536362"
---
> [!IMPORTANT]
> O encadeamento que manipula o giro do bot primário lida com a disposição do objeto de contexto quando isso é feito. **Certifique-se de `await` todas as chamadas de atividade** para que o segmento principal espere na atividade gerada antes de terminar o processamento e a eliminação do contexto de turno. Caso contrário, se uma resposta (incluindo seus manipuladores) demora um tempo significativo e tenta agir no objeto de contexto, ela pode receber um erro de _contexto descartado_.