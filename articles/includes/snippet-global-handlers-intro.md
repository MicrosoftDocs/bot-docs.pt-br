---
ms.openlocfilehash: b5809b6d46cdc09035efb36c3ea58c2ca9dc6c3e
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563624"
---
Normalmente, os usuários tentam acessar algumas funcionalidades em um bot usando palavras-chave como "ajuda", "cancelar" ou "começar novamente". Isso costuma ocorrer no meio de uma conversa, quando o bot está esperando uma resposta diferente. Implementando **manipuladores de mensagens globais**, você pode projetar seu bot para manipular normalmente essas solicitações.
Os manipuladores examinarão na entrada do usuário as palavras-chave especificadas, como "ajuda", "cancelar" ou "começar novamente", e responderão de maneira adequada. 

![como os usuários se comunicam](~/media/designing-bots/capabilities/trigger-actions.png)

> [!NOTE]
> Ao definir a lógica em manipuladores de mensagens globais, você está tornando-a acessível a todos os diálogos. Prompts e diálogos individuais podem ser configurados para ignorar com segurança as palavras-chave.
