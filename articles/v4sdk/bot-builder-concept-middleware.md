---
title: Middleware | Microsoft Docs
description: Entenda a middleware e de seus usos dentro de bot do SDK.
keywords: middleware, pipeline de middleware, curto-circuito, usos de middleware
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/8/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 231ed330faf9ce777a5acc5f4e6272b747a6f7fc
ms.sourcegitcommit: d385ec5fe61c469ab17e6f21b4a0d50e5110d0fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54298273"
---
# <a name="middleware"></a>Middleware

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O middleware é simplesmente uma classe que fica entre o adaptador e sua lógica de bot, adicionada à coleção de middlewares do seu adaptador durante a inicialização. O SDK permite escrever seu próprio middleware ou adicionar o middleware criado por outras pessoas. Toda atividade que entra ou sai do seu bot flui pelo middleware.

O adaptador processa e direciona atividades de entrada através do pipeline de middleware de bot para a lógica do seu bot e, em seguida, recua novamente. O adaptador processa e direciona atividades de entrada através do pipeline de middleware de bot para a lógica do seu bot e, em seguida, recua novamente.

Antes de entrar no middleware, é importante entender [os bots em geral](~/v4sdk/bot-builder-basics.md) e [como eles processam as atividades](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack).

## <a name="uses-for-middleware"></a>Usos para middleware
A pergunta que geralmente surge é: “Quando devo implementar ações como middleware em vez de usar minha lógica normal de bot?” O middleware oferece outras oportunidades para interagir com o fluxo da conversa dos seus usuários antes e depois que cada _tuno_ da conversa é processado. O middleware também permite armazenar e recuperar as informações relacionadas à lógica de processamento adicional de conversas e chamadas quando necessário. Abaixo estão alguns cenários comuns que mostram onde o middleware pode ser útil.

### <a name="looking-at-or-acting-on-every-activity"></a>Olhando ou agindo em todas as atividades
Existem muitas situações que exigem que seu bot faça algo em cada atividade, ou para cada atividade de um certo tipo. Por exemplo, você pode querer registrar cada atividade de mensagem que nosso bot recebe, ou fornecer uma resposta de fallback se o bot não gerou uma resposta neste turno. O middleware é um ótimo lugar para isso, com sua capacidade de agir antes e depois do resto da lógica do bot ser executada.

### <a name="modifying-or-enhancing-the-turn-context"></a>Modificando ou aprimorando o contexto de turno
Certas conversas podem ser muito mais proveitosas se o bot tiver mais informações do que as fornecidas na atividade. O middleware neste caso poderia examinar as informações de estado da conversa até o momento, consultar uma fonte de dados externa e anexá-las ao objeto de [contexto de turno](~/v4sdk/bot-builder-basics.md#defining-a-turn) antes de passar a execução para a lógica do bot. 

O SDK define o middleware de registros de log que pode registrar as atividades de entrada e saída, mas você também pode definir seu próprio middleware.

## <a name="the-bot-middleware-pipeline"></a>O pipeline de middleware de bot
Para cada atividade, o adaptador chama o middleware na ordem na qual você o adicionou. O adaptador passa o objeto de contexto para o turno e uma _próxima_ delegado e o middleware chama o delegado para transmitir o controle para o próximo middleware no pipeline. Middleware também tem a oportunidade de fazer coisas após o _próxima_ delegado retorna antes de concluir o método. Você pode pensar nisso como cada objeto de middleware tem a primeira e última chance de agir em relação aos objetos de middleware que o seguem no pipeline.

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

As primeiras coisas em seu pipeline de middleware devem ser aquelas que cuidam das tarefas de nível mais baixo que são usadas todas as vezes. Exemplos incluem o registro em log, tratamento de exceções e a tradução. Ordená-los pode variar dependendo de suas necessidades, como se você deseja que a mensagem recebida seja traduzida primeiro, antes que mensagens sejam armazenadas, ou se o armazenamento de mensagens deve ocorrer primeiro, o que pode significar que as mensagens de exceção não seriam traduzidas.

As últimas coisas em seu pipeline de middleware devem ser middleware específico de bot, que é o middleware que você implementa para fazer algum processamento em cada mensagem enviada ao seu bot. Se seu middleware usa informações de estado ou outras informações definidas no contexto do bot, inclua-as no pipeline do middleware após o middleware que modifica o estado ou o contexto.

## <a name="short-circuiting"></a>Curto-circuito
Uma ideia importante em torno do middleware e dos manipuladores de respostas é o _curto-circuito_. Se a execução deve continuar através das camadas que o seguem, o middleware (ou um manipulador de resposta) é necessário para passar a execução chamando o _próximo_ delegado.  Se o próximo delegado não é chamado dentro desse middleware (ou manipulador de resposta), o pipeline associado entra em curto-circuito e as camadas subsequentes não serão executadas. Isso significa que toda lógica de bot e qualquer middleware mais tarde no pipeline são ignorados. Há uma diferença sutil entre seu middleware e seu manipulador de resposta fazendo com que um turno entre em curto-circuito.

Quando um middleware fizer com que um turno entre em curto-circuito, o manipulador de turno do bot não será chamado, mas todo o código de middleware executado antes desse ponto no pipeline ainda será executado até a conclusão. 

Para manipuladores de eventos não chamando _próxima_ significa que o evento for cancelado, o que é um resultado significativamente diferente de middleware, ignorando a lógica. Por não processar o resto do evento, o adaptador nunca o envia.

> [!TIP]
> Se você fizer com que um evento de resposta entre em curto-circuito, como `SendActivities`, tenha certeza de esse seja o comportamento pretendido. Caso contrário, ele pode resultar em bugs difíceis de corrigir.

## <a name="response-event-handlers"></a>Manipuladores de eventos de resposta
Além da lógica de middleware aplicativo, os manipuladores de resposta (às vezes também chamados de manipuladores de eventos ou manipuladores de eventos de atividade) podem ser adicionados ao objeto de contexto. Esses manipuladores são chamados quando a resposta associada acontece no objeto de contexto atual, antes de executar a resposta real. Esses manipuladores são úteis quando você sabe que deseja fazer algo, antes ou depois do evento real, para cada atividade desse tipo para o restante da resposta atual.

> [!WARNING]
> Tenha o cuidado para não chamar um método de resposta de atividade de dentro de seu respectivo manipulador de eventos de resposta, por exemplo, chamando o método de atividade de envio de dentro de um manipulador de atividades de envio. Isso pode gerar um loop infinito.

Lembre-se: cada nova atividade obtém um novo thread para executar. Quando o encadeamento para processar a atividade é criado, a lista de manipuladores dessa atividade é copiada para esse novo encadeamento. Nenhum manipulador adicionado após esse ponto será executado para esse evento de atividade específico.
Os manipuladores registrados em um objeto de contexto são tratados como o adaptador gerencia o pipeline do middleware. Ou seja, os manipuladores são chamados na ordem em que são adicionados e chamar o próximo representante passa o controle para o próximo manipulador de eventos registrado. Se um manipulador não chamar o próximo representante, nenhum manipulador de eventos subsequente será chamado, o evento entrará em curto-circuito e o adaptador não enviará a resposta para o canal.

## <a name="handling-state-in-middleware"></a>Tratando do estado em middleware

Um método comum para salvar o estado é chamar o método salvar alterações no final do manipulador de turno. Aqui está um diagrama com foco na chamada.

![problemas de middleware de estado](media/bot-builder-dialog-state-problem.png)

O problema com essa abordagem é que as atualizações de estado feitas de um middleware personalizado que ocorrem depois que o manipulador de turnos do bot retornou não serão salvas no armazenamento durável. A solução é mover a chamada para o método salvar alterações após o middleware personalizado ser concluído, adicionando uma instância do middleware _salvar alterações automaticamente_ no início da pilha de middleware ou pelo menos antes de qualquer middleware que possa atualizar o estado. A execução é mostrada abaixo.

![solução de middleware de estado](media/bot-builder-dialog-state-solution.png)

Adicione os objetos de gerenciamento de estado que devem ser atualizados para um objeto de _conjunto de estado do bot_ e, em seguida, use-os ao criar seu middleware de salvamento automático de alterações.


## <a name="additional-resources"></a>Recursos adicionais
Você pode dar uma olhada no middleware do agente de transcrição, conforme implementado no SDK do Bot Framework [[C#](https://github.com/Microsoft/botbuilder-dotnet/blob/master/libraries/Microsoft.Bot.Builder/TranscriptLoggerMiddleware.cs) | [JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-core/src/transcriptLogger.ts)].
