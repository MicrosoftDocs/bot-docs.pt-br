---
ms.openlocfilehash: 0b991c438c0006d1fb4bafa90982f73f4a18be77
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92438152"
---
O Bot Builder Framework permite que o bot armazene e recupere dados de estado associados a um usuário, uma conversa ou um usuário específico no contexto de uma conversa específica. Os dados de estado podem ser usados para muitas finalidades, como determinar o ponto em que a conversa parou ou apenas saudar um usuário já registrado pelo nome. Se você armazenar as preferências de um usuário, poderá usar essas informações para personalizar a conversa no próximo chat. Por exemplo, é possível alertar o usuário sobre um artigo de notícias relacionado a um tópico de interesse ou alertar um usuário quando um compromisso estiver disponível. 

Para fins de teste e criação de protótipo, use o armazenamento de dados em memória do Bot Builder Framework. Para bots de produção, você pode implementar seu próprio adaptador de armazenamento ou usar uma das Extensões do Azure. As Extensões do Azure permitem que você armazene dados de estado do bot no Armazenamento de Tabelas, no CosmosDB ou no SQL. Este artigo mostrará como usar o adaptador de armazenamento em memória para armazenar os dados de estado do bot. 

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e poderá ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o adaptador de armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção.
