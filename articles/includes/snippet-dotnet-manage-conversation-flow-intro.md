---
ms.openlocfilehash: 7eed8c8328456bad43f3bdfe0029df09efa062d6
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563504"
---
Este diagrama mostra o fluxo de tela de um aplicativo tradicional em comparação com o fluxo de diálogo de um bot. 

![bot](~/media/designing-bots/core/dialogs-screens.png)

Em um aplicativo tradicional, tudo começa com a **tela principal**.
A **tela principal** invoca a **tela de novo pedido**.
A **tela de novo pedido** permanece no controle até fechar ou invocar outras telas. Se a **tela de novo pedido** fechar, o usuário retornará à **tela principal**.

Em um bot, tudo começa com o **diálogo raiz**. O **diálogo raiz** invoca o **diálogo de novo pedido**. Nesse ponto, o **diálogo de novo pedido** assume o controle da conversa e permanece no controle até fechar ou invocar outros diálogos. Se o **diálogo de novo pedido** fechar, o controle da conversa retornará ao **diálogo raiz**.