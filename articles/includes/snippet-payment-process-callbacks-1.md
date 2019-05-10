---
ms.openlocfilehash: f09d0a7b81e3cfa69fd42356faf27f79e3bc038c
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563698"
---
Ao receber uma Atualização de Endereço de Envio ou um retorno de chamada de Atualização de Opção de Envio, seu bot receberá o estado atual dos detalhes de pagamento do cliente na propriedade `Activity.Value`.
Como um comerciante, você deve tratar esses retornos de chamada como estáticos, considerando os detalhes de pagamento de entrada, você irá calcular alguns detalhes de pagamento de saída e falhará se o estado fornecido pelo cliente de entrada for inválido por algum motivo. 
Se o bot determinar que certas informações são válidas como estão, bastará enviar o código de status HTTP `200 OK` junto com os detalhes de pagamento não modificados. Como alternativa, o bot pode enviar o código de status HTTP `200 OK` juntamente com detalhes de pagamento atualizados que devem ser aplicados antes que o pedido possa ser processado. Em alguns casos, seu bot pode determinar que as informações atualizadas são inválidas e que o pedido não pode ser processado como está. Por exemplo, o endereço de envio do usuário pode especificar um país para o qual o fornecedor do produto não envia. Nesse caso, o bot pode enviar o código de status HTTP `200 OK` e uma mensagem ao preencher a propriedade de erro do objeto de detalhes de pagamento. O envio de qualquer código de status HTTP no intervalo `400` ou `500` resultará em um erro genérico para o cliente.
