---
ms.openlocfilehash: c6abff25dcccce4c18ad0083ef24b69495222c9f
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "70297350"
---
Normalmente, cada mensagem que um bot envia ao usuário está diretamente relacionada à entrada anterior do usuário.
Em alguns casos, talvez o bot precise enviar ao usuário uma mensagem sem relação direta com o tópico atual da conversa ou com a última mensagem enviada pelo usuário. Esses tipos de mensagens são chamados **mensagens proativas**.

Mensagens proativas podem ser útil em uma variedade de cenários.
Se um bot definir um timer ou um lembrete, ele terá que notificar o usuário quando chegar a hora.
Ou, se um bot receber uma notificação de um sistema externo, talvez precise informá-la imediatamente ao usuário.
Por exemplo, se o usuário tiver solicitado ao bot o monitoramento do preço de um produto, se o preço do produto cair 20%, o bot poderá alertar o usuário. Ou, se o bot precisar de um tempo para compilar uma resposta para a pergunta do usuário, ele poderá informar ao usuário sobre o atraso e permitir que a conversa continue enquanto isso. Quando o bot terminar de compilar a resposta para a pergunta, compartilhará essas informações com o usuário.

Ao implementar mensagens proativas em seu bot:

- *Não* envie várias mensagens proativas em um período curto. Alguns canais impõem restrições sobre a frequência de envio de mensagens para o usuário de um bot, e desabilitarão o bot se ele violar essas restrições.
- *Não* envie mensagens proativas para usuários que ainda não interagiram com o bot ou que pediram contato com o bot por outros meios, como email ou SMS.

Mensagens proativas podem gerar um comportamento inesperado. Considere este cenário.

![como os usuários falam](~/media/designing-bots/capabilities/proactive1.png)

Neste exemplo, o usuário solicitou anteriormente ao bot o monitoramento dos preços de um hotel em Las Vegas.
O bot iniciou uma tarefa de monitoramento em segundo plano, que permaneceu em execução continuamente nos últimos dias.
Na conversa, o usuário está reservando uma viagem para Londres quando a tarefa em segundo plano dispara uma mensagem de notificação sobre um desconto para o hotel em Las Vegas. O bot insere essas informações na conversa, tornando a experiência do usuário um pouco confusa.

Como o bot deveria ter tratado essa situação?

- Aguardar a conclusão da reserva atual e, então, entregar a notificação. Essa abordagem causaria menos perturbações, mas o atraso na comunicação da informação poderia fazer com que o usuário perdesse a oferta do hotel em Las Vegas.
- Cancelar o fluxo de reserva atual e entregar a notificação imediatamente. Essa abordagem entrega a informação no momento certo, mas provavelmente causaria uma frustração ao usuário, forçando-o a reiniciar as reservas.
- Interromper a reserva atual, mudar claramente o tópico da conversa para o hotel em Las Vegas até que o usuário responda e, em seguida, voltar à reserva em andamento e continuar de onde parou. Essa abordagem pode parecer a melhor opção, mas é complexa tanto para o desenvolvedor do bot quanto para o usuário.

O mais comum é seu bot usar uma combinação de **mensagens proativas ad hoc** e outras técnicas para lidar com situações como essa.
