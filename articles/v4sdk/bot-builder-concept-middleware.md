---
title: Middleware | Microsoft Docs
description: Entenda a middleware e de seus usos dentro de bot do SDK.
keywords: middleware, pipeline de middleware, curto-circuito, usos de middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/24/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9986ac7d46acfa94694456d653b91dd66c1f55f0
ms.sourcegitcommit: f95702d27abbd242c902eeb218d55a72df56ce56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39297537"
---
# <a name="middleware"></a>Middleware

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

O middleware é simplesmente uma classe que fica entre o adaptador e sua lógica de bot, adicionada à coleção de middlewares do seu adaptador durante a inicialização. O SDK permite que você escrever seu próprio middleware ou adicionar componentes reutilizáveis de middleware criados por outras pessoas. O que você pode fazer no middleware? Apenas sobre qualquer coisa ... Toda atividade que entra ou sai do seu bot flui pelo middleware.

O adaptador processa e direciona atividades de entrada através do pipeline de middleware de bot para a lógica do seu bot e, em seguida, recua novamente. O adaptador processa e direciona atividades de entrada através do pipeline de middleware de bot para a lógica do seu bot e, em seguida, recua novamente.

Antes de entrar no middleware, é importante entender [os bots em geral](~/v4sdk/bot-builder-basics.md) e [como eles processam as atividades](~/v4sdk/bot-builder-concept-activity-processing.md).

## <a name="uses-for-middleware"></a>Usos para middleware

A pergunta que muitas vezes surge: o que deve acontecer no middleware versus sua lógica bot? O middleware é muito flexível, então, por fim, que fica a seu critério. Vamos cobrir alguns bons usos para middleware.

### <a name="looking-at-or-acting-on-every-activity"></a>Olhando ou agindo em todas as atividades

Existem muitas situações que exigem que nosso bot faça algo em cada atividade, ou para cada atividade de um certo tipo. Por exemplo, podemos querer registrar cada atividade de mensagem que nosso bot recebe, ou fornecer uma resposta de fallback se o bot não gerou uma resposta neste turno. O middleware é um ótimo lugar para isso, com sua capacidade de agir antes e depois do resto da lógica do bot ser executada.

### <a name="modifying-or-enhancing-the-turn-context"></a>Modificando ou aprimorando o contexto de turno

Certas conversas podem ser muito mais proveitosas se o bot tiver mais informações do que as fornecidas na atividade. O middleware neste caso poderia examinar as informações de estado da conversação até o momento, consultar uma fonte de dados externa e anexá-las ao objeto de contexto antes de passar a execução para a lógica do bot.
Por exemplo, o middleware poderia identificar os detalhes da conversa, como o ID e o estado da conversa, e consultar um serviço de diretório para obter informações. O middleware poderia adicionar o objeto de usuário recebido dessa consulta externa ao objeto de contexto e transmiti-lo, fornecendo mais dados sobre o usuário e permitindo que o bot manipulasse melhor a solicitação.

Middleware pode cair em ambos os usos acima, ou em outro uso completamente; tudo depende de como você deseja que seu bot seja estruturado e o que seu bot está tentando alcançar.
Middleware pode estabelecer ou persistir o estado, reagir às solicitações de entrada ou curto-circuito do pipeline.
Alguns candidatos para o middleware incluem:

- **armazenamento ou persistência**: use o middleware para armazenar ou persistir valores após um turno ou atualize algum valor com base no que aconteceu naquele turno.
- **tratamento de erro ou caindo em desgraça**: se uma exceção for lançada, o middleware pode enviar uma mensagem de usuário amigável para essa exceção.
- **translation**: Esse middleware pode detectar e converter mensagens recebidas e configurar mensagens de saída para serem traduzidas de volta para o idioma de entrada detectado.

Você pode definir seu próprio middleware, ou o SDK define um middleware que representa componentes independentes de aplicativo, como:

- Oriente o middleware que persiste e restaura informações de estado no objeto de contexto. O SDK fornece o middleware de estado do usuário e a conversa.
- Middleware de tradução que pode reconhecer o idioma de entrada e traduzir para outro idioma, como aquele que seu aplicativo entende.
- Log de middleware que pode registrar atividades de entrada e saída.

## <a name="the-bot-middleware-pipeline"></a>O pipeline de middleware de bot

Para cada atividade, o adaptador chama o middleware na ordem **na qual você o adicionou**. O adaptador passa o objeto de contexto para o turno e uma _próxima_ delegado e o middleware chama o delegado para transmitir o controle para o próximo middleware no pipeline. Middleware também tem a oportunidade de fazer coisas após o _próxima_ delegado retorna antes de concluir o método. Você pode pensar nisso como cada objeto de middleware tem a primeira e última chance de agir em relação aos objetos de middleware que o seguem no pipeline.

Por exemplo: 

- manipulador de turno do objeto do 1º middleware executa o código antes de chamar _próxima_.
  - O manipulador de turnos do segundo objeto de middleware executa o código antes de chamar no _próximo_.
    - Manipulador de turno do bot é executado e retorna.
  - O manipulador de turnos do segundo objeto de middleware executa qualquer código restante antes de retornar.
- O manipulador de turnos do primeiro objeto de middleware executa qualquer código restante antes de retornar.

Se o middleware não chamar o próximo delegado, o adaptador não chamará nenhum dos manipuladores de middleware ou bot subsequentes e os curtos circuitos do pipeline.

Depois que o pipeline de middleware do bot for concluído, o turno terminará e o contexto do turn ficará fora do escopo.

O middleware ou o bot podem gerar respostas e registrar manipuladores de eventos de resposta, mas lembre-se de que as respostas são tratadas em processos separados.

## <a name="order-of-middleware"></a>Ordem de middleware

Como a ordem na qual o middleware é adicionado determina a ordem na qual o middleware processa uma atividade, é importante decidir a sequência que o middleware deve ser adicionado.

> [!NOTE]
> Isto serve para dar a você um padrão comum que funciona para a maioria dos bots, mas lembre-se de considerar como cada parte do middleware irá interagir com os outros para a sua situação.

As primeiras coisas em seu pipeline de middleware devem ser aquelas que cuidam das tarefas de nível mais baixo que são usadas todas as vezes. Exemplos incluem o registro em log, tratamento de exceções, gerenciamento de estado e conversão. Ordená-los pode variar dependendo de suas necessidades, como se você deseja que a mensagem recebida seja traduzida primeiro, antes que qualquer exceção possa ser tratada ou se o tratamento de exceção deve ser o primeiro, o que pode significar que as mensagens de exceção não seriam traduzidas.

As últimas coisas em seu pipeline de middleware devem ser middleware específico de bot, que é o middleware que você implementa para fazer algum processamento em cada mensagem enviada ao seu bot. Se seu middleware usa informações de estado ou outras informações definidas no contexto do bot, inclua-as no pipeline do middleware após o middleware que modifica o estado ou o contexto.

## <a name="short-circuiting"></a>Curto-circuito

Uma ideia importante em torno de middleware (e [manipuladores de eventos](~/v4sdk/bot-builder-concept-activity-processing.md#response-event-handlers)) é _curto-circuito_. Se a execução deve continuar através das camadas que o seguem, o middleware (ou um manipulador) é necessário para passar a execução chamando o _próximo_ delegado.  Se o próximo delegado não é chamado dentro desse middleware (ou manipulador), o pipeline atual será curto-circuito e camadas subsequentes não serão executadas. Isso significa que toda lógica de bot e qualquer middleware mais tarde no pipeline são ignorados.

Para manipuladores de eventos não chamando _próxima_ significa que o evento for cancelado, o que é um resultado significativamente diferente de middleware, ignorando a lógica. Por não processar o resto do evento, o adaptador nunca o envia.

> [!TIP]
> Se você causar um curto-circuito em um evento, como `SendActivities`, verifique se é o comportamento desejado. Caso contrário, ele pode resultar em bugs muito confusos.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com alguns dos principais conceitos de um bot, vamos nos aprofundar nos detalhes de como um bot pode enviar mensagens proativas.

> [!div class="nextstepaction"]
> [Proativo de mensagens](~/v4sdk/bot-builder-proactive-messages.md)
