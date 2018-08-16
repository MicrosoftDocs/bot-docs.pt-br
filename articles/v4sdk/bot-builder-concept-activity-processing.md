---
title: Processamento de atividade | Microsoft Docs
description: Entenda o processamento de atividades no bot SDK.
keywords: adaptador de bot, middleware personalizado, curto-circuito, fallback, manipuladores de eventos
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/22/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8e08ac4721ee78bbd5d13dac09d9e505d3a1b134
ms.sourcegitcommit: f95702d27abbd242c902eeb218d55a72df56ce56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39297539"
---
# <a name="activity-processing"></a>Processamento de atividade

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

O bot e o usuário trocam informações por meio de atividades. Cada atividade recebida pelo seu aplicativo bot é passada para um adaptador de bot, que transmite informações de atividade para sua lógica de bot e, finalmente, envia qualquer resposta para o usuário.

[!INCLUDE [Define a turn](~/includes/snippet-definition-turn.md)]

> [!IMPORTANT]
> Atividades, particularmente aquelas que [geramos](#generating-responses) durante um giro de bot, são tratadas de forma assíncrona. É uma parte necessária da construção de um bot; Se você precisar revisar como tudo isso funciona, confira [async for .NET](https://docs.microsoft.com/en-us/dotnet/csharp/async) ou [async para JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function), dependendo da sua opção de idioma.

## <a name="the-bot-adapter"></a>O adaptador de bot

Um bot é orientado pelo seu **adaptador**, que pode ser pensado como o Maestro para seu bot. O adaptador é responsável pela autenticação, pelo roteamento da comunicação de entrada e saída e assim por diante. O adaptador difere com base em seu ambiente (o adaptador funciona internamente de maneira diferente localmente em relação ao Azure), mas em cada instância ele atinge o mesmo objetivo. Na maioria das situações, não trabalhamos diretamente com o adaptador, como ao criar um bot a partir de um modelo, mas é bom saber que ele está lá e o que ele faz.

O adaptador de bot encapsula processos de autenticação e envia atividades para e recebe atividades do Serviço de Conector de Bot. Quando seu bot recebe uma atividade, o adaptador encerra tudo sobre essa atividade, cria um [objeto de contexto](#turn-context) para o turn, passa para a lógica do aplicativo do bot e envia respostas geradas pelo seu bot de volta ao canal do usuário.

## <a name="authentication"></a>Autenticação

O adaptador autentica cada atividade recebida que o aplicativo recebe, usando informações da atividade e o cabeçalho `Authentication` da solicitação REST.

O adaptador usa um objeto conector e as credenciais do aplicativo para autenticar as atividades de saída para o usuário.

A autenticação do Serviço Conector Bot usa tokens JWT (Token Web JSON) `Bearer` e **ID de aplicativo da Microsoft**e **senha de aplicativo da Microsoft** criados pelo Azure quando você cria um serviço de bot ou registra seu robô. Seu aplicativo precisará dessas credenciais no momento da inicialização, para permitir que o adaptador autentique o tráfego.

> [!NOTE]
> Se você estiver executando ou testando seu bot localmente, por exemplo, usando o Bot Framework Emulator, você pode fazê-lo sem configurar o adaptador para autenticar o tráfego para e do seu bot.

## <a name="turn-context"></a>Ativar o contexto

Quando um adaptador recebe uma atividade, ele gera um **objeto de contexto**, que fornece informações sobre a atividade recebida, o remetente e o destinatário, o canal, a conversação e outros dados necessários para processar a atividade. O adaptador passa esse objeto de contexto para o bot. O objeto de contexto persiste durante a duração de um turno e fornece informações sobre o seguinte:

* Conversa - identifica a conversa e inclui informações sobre o bot e o usuário participando da conversa.
* Atividade - As solicitações e respostas em uma conversa são todos os tipos de atividades. Esse contexto fornece informações sobre a atividade de entrada, incluindo informações de roteamento, informações sobre o canal, a conversa, o remetente e o destinatário.
* Estado - Propriedades usadas para rastrear seu [estado](~/v4sdk/bot-builder-storage-concept.md), por exemplo, onde ele está em uma conversa com um usuário, informações sobre esse usuário ou outras informações de lógica de negócios.
* Informações personalizadas - Se você estender seu bot implementando o middleware ou dentro da sua lógica de bot, poderá disponibilizar informações adicionais em cada turno.

O objeto de contexto também pode ser usado para enviar uma resposta ao usuário e obter uma referência ao adaptador para criar uma nova conversa ou continuar uma existente.

> [!NOTE]
> Seu aplicativo e o adaptador manipularão solicitações de forma assíncrona; no entanto, sua lógica de negócios não precisa ser orientada por solicitação-resposta.

## <a name="middleware"></a>Middleware

Você pode adicionar middleware para o adaptador. Middleware são camadas de plug-ins adicionados ao seu bot e à sua lógica do bot core. Para obter mais informações detalhadas sobre o middleware, consulte o independentemente [middleware artigo](~/v4sdk/bot-builder-concept-middleware.md).

O middleware e a lógica do bot usam o objeto de contexto para recuperar informações sobre a atividade e aja de forma adequada. O middleware e o bot também podem atualizar ou adicionar informações ao objeto de contexto, como controlar o estado de um turno, uma conversa ou outro escopo. O SDK fornece um _middleware de estado_ que você pode usar para adicionar persistência de estado ao seu bot.

## <a name="generating-responses"></a>Geração de respostas

O objeto de contexto fornece métodos de resposta de atividade para permitir que o código responda a uma atividade:

* Os métodos _send activity_ e _send activities_ enviam uma ou mais atividades para a conversa.
* Se compatível com o canal, o _atividade de atualização_ método atualiza uma atividade em uma conversa.
* Se compatível com o canal, o método _excluir atividade_ remove uma atividade da conversa.

Cada método de resposta é executado em um processo assíncrono. Quando é chamado, o método de resposta da atividade clona a lista de manipuladores de eventos associada antes de começar a chamar os manipuladores, o que significa que conterá todos os manipuladores adicionados até este ponto, mas não conterá nada adicionado após o início do processo.

Isso também significa que a ordem de suas respostas não é garantida, especialmente quando uma tarefa é mais complexa que outra. Se seu bot puder gerar várias respostas para uma atividade de entrada, certifique-se de que elas façam sentido na ordem em que forem recebidas pelo usuário.

> [!IMPORTANT]
> O encadeamento que manipula o giro do bot primário lida com a disposição do objeto de contexto quando isso é feito. Se uma resposta (incluindo seus manipuladores) demorar um tempo significativo e tentar agir no objeto de contexto, ele poderá receber um erro `Context was disposed`. **Certifique-se que `await` todas as chamadas de atividade** para que o segmento principal espere na atividade gerada antes de terminar o processamento e a eliminação do contexto de turno.

## <a name="response-event-handlers"></a>Manipuladores de eventos de resposta

Além da lógica de bot e middleware, os manipuladores de resposta (às vezes também chamados de manipuladores de eventos ou manipuladores de eventos) podem ser adicionados ao objeto de contexto. Esses manipuladores são chamados quando a resposta associada acontece no objeto de contexto atual, antes de executar a resposta real. Esses manipuladores são úteis quando você sabe que deseja fazer algo, antes ou depois do evento real, para cada atividade desse tipo para o restante da resposta atual.

> [!WARNING]
> Tenha o cuidado de não chamar um método de resposta de atividade a partir de seu respectivo manipulador de eventos de resposta, por exemplo, chamando o método de atividade de envio de dentro de um manipulador _na atividade de envio_. Isso pode gerar um loop infinito.

Cada nova atividade obtém um novo thread para executar em. Quando o encadeamento para processar a atividade é criado, a lista de manipuladores dessa atividade é copiada para esse novo encadeamento. Nenhum manipulador adicionado após esse ponto será executado para esse evento de atividade específico.

O adaptador gerencia os manipuladores registrados em um objeto de contexto de maneira muito semelhante a como ele gerencia o [pipeline de middleware](~/v4sdk/bot-builder-concept-middleware.md#the-bot-middleware-pipeline).

Ou seja, os manipuladores são chamados na ordem em que são adicionados e chamar o _próximo_ delegado passa o controle para o próximo manipulador de eventos registrado. Se um manipulador não chamar o próximo delegado, o adaptador não chamará nenhum dos manipuladores de eventos subsequentes; o evento em [curto circuito](~/v4sdk/bot-builder-concept-middleware.md#short-circuiting) e o adaptador não envia a resposta para o canal.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com alguns dos principais conceitos de um bot, vamos nos aprofundar nos detalhes de como um bot pode enviar mensagens proativas.

> [!div class="nextstepaction"]
> [Middleware](~/v4sdk/bot-builder-concept-middleware.md)
