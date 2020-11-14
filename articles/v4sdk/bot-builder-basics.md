---
title: Como funcionam os bots – Serviço de Bot
description: Familiarize-se com o SDK do bot Framework. Entenda como os bots se comunicam com os usuários. Saiba mais sobre atividades, canais, solicitações HTTP POST e outros tópicos.
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 09/24/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 707e1b25741b5b113431d977bcdd5fb7b7a5c5ad
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94599878"
---
# <a name="how-bots-work"></a>Como funcionam os bots

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões ou imagens) ou fala. O serviço de bot do Azure é uma plataforma de nuvem. Ele hospeda bots e os disponibiliza para os canais.

O serviço bot Framework, que é um componente do serviço de bot do Azure, envia informações entre o aplicativo de bot do usuário conectado (como o Facebook ou a margem de atraso, e assim por diante, que chamamos de *canal* ) e o bot. Cada canal pode incluir informações adicionais nas atividades que enviam. Antes de criar bots, é importante entender como um bot usa objetos de atividade para se comunicar com seus usuários. Vamos primeiro dar uma olhada nas atividades que são trocadas quando executamos um bot de eco simples.

> [!div class="mx-imgBorder"]
> ![diagrama de atividade](./media/bot-builder-activity.png)

Dois tipos de atividade ilustrados aqui são: *atualização de conversa* e *mensagem*.

O serviço Bot Framework pode enviar uma atualização de conversa quando um participante ingressa na conversa. Por exemplo, ao iniciar uma conversa com o Bot Framework Emulator, você verá duas atividades de atualização de conversa (uma para o usuário que participa da conversa e outra para o bot que participa da conversa). Para distinguir essas atividades de atualização de conversa, verifique quem está incluído na propriedade *Members Added* da atividade.

A atividade de mensagem contém informações de conversa entre as partes. Em um exemplo de bot de eco, as atividades de mensagem transmitem texto simples e o canal renderiza esse texto. Como alternativa, a atividade de mensagem pode conter o texto a ser falado, as ações sugeridas ou os cartões a serem exibidos.

Neste exemplo, o bot criou e enviou uma atividade de mensagem em resposta à atividade de mensagem de entrada que ele recebeu. No entanto, um bot pode responder de outras maneiras a uma atividade de mensagem recebida; Não é incomum que um bot responda a uma atividade de atualização de conversa enviando um texto de boas-vindas em uma atividade de mensagem. Mais informações podem ser encontradas em como receber [um usuário de boas-vindas](bot-builder-welcome-user.md).

## <a name="the-bot-framework-sdk"></a>O SDK do bot Framework

O SDK do bot Framework permite que você crie bots que podem ser hospedados no serviço de bot do Azure. O serviço define uma API REST e um protocolo de atividade para como o bot e os canais ou usuários podem interagir. O SDK se baseia nessa API REST e fornece uma abstração do serviço para que você possa se concentrar na lógica de conversação. Embora você não precise entender o serviço REST para usar o SDK, a compreensão de alguns de seus recursos pode ser útil.

Bots são aplicativos que têm uma interface de conversação. Eles podem ser usados para transferir simples tarefas repetitivas, como fazer a reserva para um jantar ou coletar informações de perfil, para sistemas automatizados que não exigem uma intervenção humana direta. Os usuários conversam com um bot usando texto, cartões interativos e fala. Uma interação de bot pode ser uma pergunta e resposta rápidas, ou pode ser uma conversa sofisticada que fornece acesso aos serviços de forma inteligente.

> [!NOTE]
> O suporte para os recursos fornecidos pelo SDK e a API REST varia de acordo com o canal.
> Você pode testar o bot usando o emulador do bot Framework, mas também deve testar todos os recursos do bot em cada canal no qual você pretende disponibilizar o bot.

As interações envolvem a troca de _atividades_ , que são manipuladas por _ativações_.

### <a name="activities"></a>Atividades

Cada interação entre o usuário (ou um canal) e o bot é representada como uma *atividade*.
O esquema de [atividade](https://aka.ms/botSpecs-activitySchema) do bot Framework define as atividades que podem ser trocadas entre um usuário ou canal e um bot. As atividades podem representar texto humano ou fala, notificações de aplicativo para aplicativo, reações a outras mensagens e assim por diante.

<a id="defining-a-turn"></a>

### <a name="turns"></a>Ativa

Em uma conversa, as pessoas geralmente falam uma de cada vez, alternando a vez de falar. Com um bot, geralmente ele reage à entrada do usuário. Dentro do SDK do Bot Framework, um _turno_ é a atividade de entrada do usuário para o bot e qualquer atividade que o bot envia de volta para o usuário como uma resposta imediata. Você pode considerar uma rodada como o processamento associado ao bot que recebe uma determinada atividade.

Por exemplo, um usuário pode pedir a um bot para executar uma determinada tarefa. O bot pode responder com uma pergunta para obter mais informações sobre a tarefa. nesse ponto, essa rodada termina. Na próxima vez, o bot recebe uma nova mensagem do usuário que pode conter a resposta à pergunta do bot ou pode representar uma alteração de assunto ou uma solicitação para ignorar a solicitação inicial para executar a tarefa.

## <a name="bot-application-structure"></a>Estrutura do aplicativo bot

<!-- JF-Open question:
    The application structure section is long.
    Should it be moved into a separate article?
-->

O SDK define uma classe _bot_ que manipula o raciocínio de conversação para o aplicativo bot. A classe bot:

- Reconhece e interpreta a entrada do usuário.
- Motivos sobre a entrada e executa tarefas relevantes.
- Gera respostas sobre o que o bot está fazendo ou fez.

O SDK também define uma classe de _adaptador_ que lida com a conectividade com os canais. O adaptador:

- Fornece um método para lidar com solicitações de métodos e para gerar solicitações para o canal do usuário.
- Inclui um pipeline de middleware, que inclui o processamento de ativação fora do manipulador de folheio de bot.
- Chama o manipulador de folheio do bot e captura os erros não manipulados de outra forma no manipulador de ativação.

Além disso, os bots geralmente precisam recuperar e armazenar o estado cada vez.
Isso é tratado por meio de classes de _armazenamento_ , _estado de bot_ e _acessador de propriedade_ .
O SDK não fornece armazenamento interno, mas fornece abstrações para armazenamento e algumas implementações de uma camada de armazenamento.
O tópico [Gerenciamento de estado](bot-builder-concept-state.md) descreve esses recursos de estado e armazenamento.

> [!div class="mx-imgBorder"]
> ![Um bot tem elementos de conectividade e raciocínio e uma abstração para o estado](../media/architecture/how-bots-work.png)

O SDK não exige que você use uma camada de aplicativo específica para enviar e receber solicitações da Web.
O bot Framework tem modelos e amostras para ASP.NET (C#), restify (JavaScript) e aiohttp (Python).
No entanto, você pode optar por usar uma camada de aplicativo diferente para seu aplicativo.

Ao criar um bot usando o SDK, você fornece o código para receber o tráfego HTTP e encaminhá-lo ao adaptador. A estrutura de bot fornece alguns modelos e exemplos que você pode usar para desenvolver seus próprios bots.

### <a name="bot-logic"></a>Lógica do bot

O objeto bot contém o raciocínio ou a lógica de conversação para uma rodada e expõe um _manipulador de ativação_ , que é o método que pode aceitar as atividades de entrada do adaptador de bot.

O SDK fornece alguns paradigmas diferentes para gerenciar a lógica de bot.

- Os _manipuladores de atividade_ fornecem um modelo controlado por eventos no qual os tipos de atividade de entrada e subtipos são os eventos. Isso pode ser bom para bots com interações limitadas e curtas com o usuário.
  - Use um [manipulador de atividade](bot-activity-handler-concept.md) e implemente manipuladores para cada tipo de atividade ou subtipo que o bot reconhecerá e reaja.
  - Use um [manipulador de atividades de equipes](bot-builder-basics-teams.md) para criar bots que podem se conectar ao canal de equipes. (O canal de equipes requer que o bot lide com algum comportamento específico de canal.)
- A [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) fornece um modelo baseado em estado para gerenciar uma conversa de execução longa com o usuário.
  - Use um manipulador de atividade e uma _caixa de diálogo de componente_ para conversas amplamente sequenciais.
    Consulte [sobre o componente e caixas de diálogo de cascata](bot-builder-concept-waterfall-dialogs.md) para obter mais informações.
  - Use um _Gerenciador de caixas de diálogo_ e uma _caixa de diálogo adaptável_ para o fluxo de conversa flexível que pode lidar com uma maior variedade de interação do usuário. <!-- Your bot class can forward activities to the dialog manager directly or pass them through an activity handler first. -->
    Consulte a [introdução a caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-introduction.md) para obter mais informações.
- Implemente sua própria classe bot e forneça sua própria lógica para lidar com cada vez. Veja como [criar seus próprios prompts para coletar a entrada do usuário](bot-builder-primitive-prompts.md) para obter um exemplo de como isso poderia ser.

### <a name="the-bot-adapter"></a>O adaptador de bot

O adaptador tem um método de _atividade de processo_ para iniciar uma rodada.

- Ele pega o corpo da solicitação (a carga da solicitação, traduzida para uma atividade) e o cabeçalho da solicitação como argumentos.
- Ele verifica se o cabeçalho de autenticação é válido.
- Ele cria um objeto de _contexto_ para a rodada.
- Ele executa isso por meio de seu pipeline de _middleware_ .
- Ele envia a atividade para o manipulador de folheio do objeto bot.

O adaptador também:

- Formata e envia atividades de resposta. Essas respostas normalmente são mensagens para o usuário, mas também podem incluir informações a serem consumidas pelo canal do usuário diretamente.
- Superfícies outros métodos fornecidos pela API REST do conector do bot, como _Atualizar mensagem_ e _Excluir mensagem_.
- Captura erros ou exceções que, de outra forma, não foram capturadas para o desligamento.

#### <a name="the-turn-context"></a>O contexto de ativação

O objeto *contexto de turno* fornece informações sobre a atividade, como o remetente e o receptor, o canal e outros dados necessários para processar a atividade. Ele também permite a adição de informações durante o turno em várias camadas do bot.

O contexto de turno é uma das abstrações mais importantes no SDK. Ele não apenas transporta a atividade de entrada para todos os componentes de middleware e a lógica do aplicativo, mas também fornece o mecanismo no qual os componentes de middleware e a lógica de bot podem enviar atividades de saída.

#### <a name="middleware"></a>Middleware

O Middleware é muito parecido com qualquer outro middleware de mensagens, compreendendo um conjunto linear de componentes que são executados em ordem, dando a cada um a chance de operar na atividade. O estágio final do pipeline de middleware é um retorno de chamada para o manipulador de turno na classe do bot que o aplicativo registrou com o método *processar atividade* do adaptador. O middleware implementa um método _on Turn_ que o adaptador chama.

O manipulador de ativação usa um contexto de ativação como argumento, normalmente a lógica do aplicativo em execução dentro da função de manipulador de ativação processará o conteúdo da atividade de entrada e gerará uma ou mais atividades em resposta, enviando-as usando a função *Enviar atividade* no contexto de ativação. Chamar *enviar atividade* no contexto de turno faz com que os componentes de middleware sejam invocados nas atividades de saída. Os componentes de middleware são executados antes e depois da função de manipulador de ativação do bot. A execução é inerentemente aninhada e, como tal, às vezes é conhecida como uma transparência.

O tópico de [middleware](bot-builder-concept-middleware.md) descreve o middleware em maior profundidade.

### <a name="bot-state-and-storage"></a>Estado e armazenamento do bot

Assim como acontece com outros aplicativos Web, um bot é inerentemente sem estado.
O estado em um bot segue os mesmos paradigmas dos aplicativos Web modernos, e o SDK do bot Framework fornece abstrações de gerenciamento de estado e camada de armazenamento para facilitar o gerenciamento de estado.

O tópico [Gerenciamento de estado](bot-builder-concept-state.md) descreve esses recursos de estado e armazenamento.

### <a name="messaging-endpoint-and-provisioning"></a>Ponto de extremidade e provisionamento do sistema de mensagens

Normalmente, seu aplicativo precisará de um ponto de extremidade REST no qual receber mensagens. Também será necessário provisionar recursos para o bot de acordo com a plataforma que você decidir usar.

Siga um dos guias de início rápido ([C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md), [JavaScript](../javascript/bot-builder-javascript-quickstart.md), [python](../python/bot-builder-python-quickstart.md)) para criar e testar um simples bot de eco.

## <a name="http-details"></a>Detalhes do HTTP

As atividades chegam ao bot do serviço Bot Framework por meio de uma solicitação HTTP POST. O bot responde à solicitação POST de entrada com um código de status HTTP 200. As atividades enviadas do bot para o canal são enviadas em uma HTTP POST separada para o serviço Bot Framework. Isso, por sua vez, é confirmado com um código de status HTTP 200.

O protocolo não especifica a ordem em que essas solicitações POST e suas confirmações são feitas. No entanto, para se ajustar a estruturas de serviço HTTP comuns, essas solicitações normalmente são aninhadas, o que significa que a solicitação HTTP de saída é feita do bot dentro do escopo da solicitação HTTP de entrada. Esse padrão é ilustrado no diagrama anterior. Como há duas conexões HTTP distintas de ponta a ponta, o modelo de segurança deve servir a ambas.

> [!NOTE]
> O bot tem 15 segundos para confirmar a chamada com um status 200 na maioria dos canais. Se o bot não responder em 15 segundos, ocorrerá um erro HTTP GatewayTimeout (504).

## <a name="the-activity-processing-stack"></a>A pilha de processamento de atividade

Vamos analisar o diagrama de sequência anterior com um foco na chegada de uma atividade de mensagem.

![pilha de processamento de atividade](media/bot-builder-activity-processing-stack.png)

No exemplo acima, o bot respondeu à atividade de mensagem com outra atividade de mensagem contendo a mesma mensagem de texto. O processamento começa com a solicitação HTTP POST, com as informações de atividade transmitidas como uma carga JSON, chegando ao servidor Web. Em C#, isso normalmente será um projeto ASP.NET, em um projeto de Node.js JavaScript, provavelmente será uma das estruturas populares, como Express ou restify.

O _adaptador_ , um componente integrado do SDK, é o núcleo do runtime do SDK. A atividade é executada como JSON no corpo HTTP POST. Esse JSON é desserializado para criar o objeto de _atividade_ que é enviado para o adaptador por meio de seu método de _atividade de processo_ . Ao receber a atividade, o adaptador cria um _contexto de turno_ e chama o middleware.

Conforme mencionado acima, o contexto de turno fornece o mecanismo para que o bot envie atividades de saída, geralmente em resposta a uma atividade de entrada. Para conseguir isso, o contexto de ativação fornece métodos de resposta de _atividade_ de _envio_ , _atualização_ e exclusão. Cada método de resposta é executado em um processo assíncrono.

[!INCLUDE [alert-await-send-activity](../includes/alert-await-send-activity.md)]

<!-- TODO Need to reorganize and rewrite parts of this. -->
<!-- JF-Open question:
    Do we need more explanation of the "role of the Azure Bot Service", and if so, what do we say about it?
-->

## <a name="bot-templates"></a>Modelos de bot

Você precisa escolher o uso da camada de aplicativo para seu aplicativo; no entanto, a estrutura de bot tem modelos e exemplos para ASP.NET (C#), restify (JavaScript) e aiohttp (Python). A documentação é escrita supondo que você use uma dessas plataformas, mas o SDK não precisa dela. Consulte os guias de início rápido ([C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md), [JavaScript](../javascript/bot-builder-javascript-quickstart.md), [python](../python/bot-builder-python-quickstart.md)) para obter instruções sobre como acessar e instalar os modelos.

Um bot é um aplicativo Web e os modelos são fornecidos para cada versão de idioma do SDK.
Todos os modelos fornecem uma implementação de ponto de extremidade padrão e um adaptador.
Cada modelo inclui:

- Provisionamento de recursos
- Uma implementação de ponto de extremidade HTTP específica a um idioma que roteia as atividades de entrada para um adaptador.
- Um objeto de adaptador
- Um objeto bot

A principal diferença entre os diferentes tipos de modelo está no objeto bot.
Os modelos são:

- **Bot vazio**
  - Inclui um manipulador de atividade que aprecia um usuário para a conversa enviando uma mensagem "Olá, mundo" na primeira vez que a conversa.
- **Bot de Eco**
  - Usa um manipulador de atividade para requerer usuários e retornar a entrada do usuário.
- **Bot principal**
  - Reúne muitos recursos do SDK e demonstra as práticas recomendadas para um bot.
  - Usa um manipulador de atividade para usuários de boas-vindas.
  - Usa uma caixa de diálogo de componente e uma caixa de diálogo filho para gerenciar a conversa.
  - As caixas de diálogo usam Reconhecimento vocal (LUIS) e QnA Maker recursos.

## <a name="additional-information"></a>Informações adicionais

### <a name="managing-bot-resources"></a>Gerenciando recursos de bot

Os recursos de bot, como ID do aplicativo, senhas, chaves ou segredos para serviços conectados, precisarão ser gerenciados adequadamente. Para saber mais sobre como fazer isso, consulte as [diretrizes de segurança do bot Framework](bot-builder-security-guidelines.md) e sobre como [gerenciar recursos de bot](bot-file-basics.md).

### <a name="channel-adapters"></a>Adaptadores de canal

O SDK também permite que você use adaptadores de canal, nos quais o próprio adaptador executa as tarefas que o serviço do conector de bot faria normal para um canal.

O SDK fornece alguns adaptadores de canal em alguns idiomas.
Mais adaptadores de canal estão disponíveis por meio dos repositórios Botkit e Community.
Para obter mais detalhes, consulte a tabela de [canais e adaptadores](https://aka.ms/v4-botbuilder-repo#channels-and-adapters)do kit de SDK do bot Framework.

### <a name="the-bot-connector-rest-api"></a>A API REST do conector de bot

O SDK do bot Framework encapsula e se baseia na API REST do conector de bot. Se você quiser entender as solicitações HTTP subjacentes que dão suporte ao SDK, consulte a [autenticação](../rest-api/bot-framework-rest-connector-authentication.md) do conector e os artigos associados.
As atividades que um bot envia e recebe em conformidade com o [esquema de atividade do bot Framework](https://aka.ms/botSpecs-activitySchema).

## <a name="next-steps"></a>Próximas etapas

- Para entender a função do estado dos bots, consulte [gerenciar o estado](bot-builder-concept-state.md).
- Para entender os principais conceitos de desenvolvimento de bots para o Microsoft Teams, confira [Como os bots do Microsoft Teams funcionam](bot-builder-basics-teams.md)
