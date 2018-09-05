---
title: Reconhecimento Vocal | Microsoft Docs
description: Saiba como adicionar inteligência artificial aos bots com os Serviços Cognitivos da Microsoft para torná-los mais úteis e interativas.
keywords: LUIS, intenção, reconhecedor, ferramenta de expedição, qna, qna maker
author: DeniseMak
ms.author: v-demak
manager: rstand
ms.topic: article
ms.prod: bot-framework
ms.date: 03/22/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 70e703e8c3d7251856e70b3d3601e0d62cb98882
ms.sourcegitcommit: ee63d9dc1944a6843368bdabf5878950229f61d0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42795062"
---
# <a name="language-understanding"></a>Reconhecimento Vocal

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Bots podem usar uma variedade de estilos de conversa, das estruturadas e orientadas a formas livres e em aberto. Um bot precisa decidir o que fazer em seguida, no seu fluxo de conversa, com base no que o usuário disse, e em uma conversa em aberto, há uma maior variedade de respostas do usuário.

| Interativa | Aberto |
|------|------|
| Eu sou o bot de viagem. Selecione uma das seguintes opções: localizar voos, encontrar hotéis, encontrar aluguel de carro. | Posso ajudar você a reservar a viagem. O que deseja fazer? |
| Você precisa de alguma outra coisa? Clique em sim ou não. | Você precisa de alguma outra coisa? |

A interação entre usuários e bots geralmente é de forma livre e os bots precisam reconhecer a linguagem de forma natural e contextual. Este artigo explica como o reconhecimento vocal (LUIS) ajuda você a determinar o que os usuários desejam, identificar os conceitos e as entidades em frases e, finalmente, permitir que seus bots respondam com a ação apropriada.

## <a name="recognize-intent"></a>Reconhecer intenção

[LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) ajuda você com a determinação da **intenção** do usuário, que é o que eles desejam fazer, a partir do que eles dizem, portanto, seu bot pode responder adequadamente. LUIS é especialmente útil quando o que eles dizem ao seu bot não segue uma estrutura previsível ou um padrão específico. Se um bot tem uma interface de usuário convencional, na qual o usuário fala ou digita uma resposta, pode haver variações intermináveis em *declarações*, que são a entrada falada ou textual do usuário.

Por exemplo, considere as várias maneiras de um usuário de um bot de viagem pode pedir para reservar um voo.

![Várias declarações formadas de modo diferente para reservar um voo](media/cognitive-services-add-bot-language/cognitive-services-luis-utterances.png)

Essas declarações podem ter estruturas diferentes e contêm vários sinônimos para "voo" que você ainda não pensou. Em seu bot, pode ser difícil gravar a lógica que corresponde a todas as declarações e ainda distinguir outras intenções que contenham as mesmas palavras. Além disso, seu bot precisa extrair *entidades*, que são as palavras importantes, como locais e horas. LUIS facilita esse processo por meio da identificação contextualmente intenções e entidades para você.

Quando você projeta seu bot para entrada de linguagem natural, você determina quais intenções e entidades seu bot precisa reconhecer e pensa em como elas se conectará às ações que seu bot usa. Em <a href="https://www.luis.ai" target="_blank">LUIS</a>, você define intenções e entidades personalizadas e especifica o comportamento fornecendo exemplos para cada intenção e rotulando as entidades dentro deles.

Seu bot usa a intenção reconhecida por LUIS para determinar o tópico de conversa ou iniciar um fluxo da conversa. Por exemplo, quando um usuário diz: "Eu gostaria de reservar um voo" seu bot detecta a intenção de BookFlight e invoca o fluxo da conversa para iniciar uma pesquisa para voos. LUIS detecta entidades, como a cidade de destino e a data de partida na declaração original que dispara a intenção e, posteriormente, no fluxo de conversa. Uma vez que o bot tem todas as informações necessárias, ele pode atender a intenção do usuário.

![Uma conversa com um bot é disparada pela intenção de BookFlight](media/cognitive-services-add-bot-language/cognitive-services-luis-conversation-high-level.png)

### <a name="recognize-intent-in-common-scenarios"></a>Reconhecer intenções em cenários comuns

Para economizar tempo de desenvolvimento, o LUIS fornece modelos de linguagem previamente treinados que reconhecem declarações comuns para as categorias comuns de bots. <!-- Consider if you'll use prebuilt or custom intents and entities: -->

* Os **domínios predefinidos** são coleções de intenções e entidades pré-treinadas, prontos para uso, que funcionam bem em conjunto para cenários comuns, como compromissos, lembretes, gerenciamento, saúde, entretenimento, comunicação, reservas e muito mais. O domínio predefinido **Utilitários** ajuda seu bot a lidar com tarefas comuns, como Cancelar, Confirmar, Ajudar, Repetir e Parar. Consulte a amostra de lembretes para [C#]( https://github.com/Microsoft/botbuilder-dotnet/tree/master/samples-final/8.AspNetCore-LUIS-Bot) ou [JavaScript](https://github.com/Microsoft/botbuilder-js/tree/master/samples/luis-bot-es6) para obter um exemplo de como usar um domínio predefinido no seu bot e dê uma olhada nos [domínios predefinidos](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-use-prebuilt-domains) que LUIS oferece.
* **As entidades predefinidas** ajudam seu bot a reconhecer os tipos comuns de informações como datas, horas, números, temperatura, moeda, região e idade.

Consulte [Extrair resultados LUIS digitados][luis-v4-typed-entities] para obter um exemplo que usa o LUIS para extrair as datas. Consulte [Usar as entidades predefinidas](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/pre-builtentities) para obter informações sobre os tipos que LUIS pode reconhecer.

<!-- TODO: Link to Bot Framework design guidance about LUIS apps, when this is ready -->

## <a name="how-your-bot-gets-messages-from-luis"></a>Como seu bot obtém as mensagens do LUIS
Toda vez que seu bot integrado de LUIS recebe uma declaração, o bot a envia para o aplicativo LUIS, o qual retorna uma resposta JSON que contém as intenções e entidades. O SDK do Construtor de Bot fornece a funcionalidade (implementado como [middleware](bot-builder-concept-middleware.md)) para processar automaticamente as respostas do LUIS e passá-las ao seu bot. Você pode usar o [contexto de turno](bot-builder-concept-activity-processing.md#turn-context) no _manipulador de turno_ do seu bot para rotear o fluxo da conversa com base na intenção na resposta de LUIS. 

![Como as intenções e entidades são passadas ao seu bot](./media/cognitive-services-add-bot-language/cognitive-services-luis-message-flow-bot-code.png)

Consulte o seguinte para obter uma introdução à integração de um aplicativo LUIS com um bot:

* [Usando o LUIS para o fluxo da conversa][luis-v4-how-to]

## <a name="best-practices-for-language-understanding"></a>Práticas recomendadas para Reconhecimento Vocal

Considere as seguintes práticas ao criar um modelo de linguagem para o bot.

### <a name="consider-the-number-of-intents"></a>Considerar o número de intenções

Os aplicativos LUIS reconhecem as intenções classificando uma declaração em uma das várias categorias. Um resultado natural é determinar que a categoria correta entre um grande número de intenções pode reduzir a capacidade de um aplicativo LUIS para diferenciá-las.

Uma maneira de reduzir o número de intenções é usar um design hierárquico. Considere o caso de um bot de assistente pessoal que tem três intenções relacionadas ao clima, três intenções relacionadas à automação residencial, e três outras intenções utilitárias que são Ajuda, Cancelar e Saudação. Se você colocar todas as intenções no mesmo aplicativo LUIS, você já têm 9, e conforme você adiciona recursos ao bot, você pode acabar com dezenas. Em vez disso, você pode usar um aplicativo LUIS dispatcher para determinar se a solicitação do usuário é para clima, automação residencial ou utilitário, e invocar o aplicativo LUIS para a categoria determinada pelo dispatcher. Nesse caso, cada um dos aplicativos LUIS inicia apenas com 3 intenções.

### <a name="use-a-none-intent"></a>Usar uma Intenção None

Geralmente ocorre que os usuários do seu bot dirão algo inesperado ou não relacionado para o fluxo da conversa atual. A intenção _None_ é fornecida para lidar com essas mensagens.

Se você não treinar uma intenção para tratar os casos de fallback, padrão ou "nenhuma das anteriores", seu aplicativo LUIS pode classificar somente mensagens para as intenções que foram definidas. Por exemplo, digamos que você tenha um aplicativo LUIS com duas intenções: `HomeAutomation.TurnOn` e `HomeAutomation.TurnOff`. Se essas forem as únicas intenções e a entrada é algo não relacionado, como "agendar um compromisso na sexta-feira", seu aplicativo LUIS não tem outra opção senão classificar essa mensagem como HomeAutomation.TurnOn ou HomeAutomation.TurnOff. Se seu aplicativo LUIS tem uma intenção `None` com alguns exemplos, você pode fornecer alguma lógica de fallback em seu bot para lidar com declarações inesperadas.

A intenção de `None` é muito útil para melhorar os resultados do reconhecimento. Nesse cenário de automação residencial, "agendar um compromisso na sexta-feira" pode produzir a intenção de `HomeAutomation.TurnOn` com uma confiança baixa e seu bot deve rejeitá-la. Você pode adicionar essas frases ao seu modelo sob a intenção do `None`, para que eles sejam corretamente resolvidos para `None`.

### <a name="review-the-utterances-that-luis-app-receives"></a>Examine as declarações que recebe do aplicativo LUIS

Os aplicativos LUIS fornecem um recurso para melhorar o desempenho do seu aplicativo, examinando as mensagens que os usuários enviaram a ele. Consulte [Examinas as declarações sugeridas](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances) para obter uma explicação passo a passo.


## <a name="integrate-multiple-luis-apps-and-qna-services-with-the-dispatch-tool"></a>Integrar vários aplicativos LUIS e serviços do QnA com a ferramenta de Expedição

<!-- 1. Modular. 2. Better performance for classification --> Ao criar um bot multiuso que compreende vários tópicos de conversa, você pode começar a desenvolver serviços para cada função separadamente e, em seguida, integrá-los juntos. Esses serviços podem incluir aplicativos de Reconhecimento Vocal (LUIS) e serviços do QnAMaker. Aqui estão alguns exemplos de cenários em que um bot pode combinar vários aplicativos LUIS, vários serviços QnAMaker ou uma combinação dos dois:

* Um bot de assistente pessoal permite que o usuário invoque uma variedade de comandos. Cada categoria de comandos forma uma "habilidade" que pode ser desenvolvida separadamente, e cada uma delas tem um aplicativo LUIS.
* Um bot pesquisa várias bases de conhecimento para encontrar respostas para perguntas frequentes (FAQs).
* Um bot para uma empresa tem aplicativos LUIS para criação de contas de clientes e emissão de pedidos e também tem um serviço QnAMaker para suas perguntas frequentes.  

### <a name="the-dispatch-tool"></a>A ferramenta de expedição

A ferramenta de expedição ajuda você a integrar vários aplicativos LUIS e serviços QnA Maker com seu bot, por meio da criação de um *aplicativo de expedição*, que é um novo aplicativo LUIS que encaminha mensagens para os serviços do QnAMaker e LUIS apropriado. Consulte o [tutorial de expedição](./bot-builder-tutorial-dispatch.md) para obter um tutorial passo a passo que combina vários aplicativos LUIS e QnA Maker em um bot.

## <a name="use-luis-to-improve-speech-recognition"></a>Use LUIS para melhorar o reconhecimento de fala

Para um bot que irá se dirigir para os usuários, integrá-lo com o LUIS pode ajudar seu bot a identificar palavras que podem ser interpretados incorretamente durante a conversão de fala em texto.  Por exemplo, em um cenário de xadrez, um usuário pode dizer: "Move knight to A 7". Sem contexto para a intenção do usuário, a declaração poderia ser reconhecida como: "Move night 287". Ao criar entidades que representam as coordenadas e peças de xadrez e rotulá-las em declarações, você fornece contexto para o reconhecimento de fala identificá-las. Você pode [Habilitar a desobstrução de reconhecimento de fala][speechrecognitionpriming] com canais de Bot Framework que são integrados com a API de Fala do Bing, como Web Chat, o emulador do Bot Framework e Cortana.  

## <a name="additional-resources"></a>Recursos adicionais

* [Reconhecimento vocal](~/bot-service-concept-intelligence.md#language-understanding)
* <a href="https://www.luis.ai" target="_blank">Site do LUIS</a>

<!-- Links -->
[luis_home]: https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home
[middleware]: bot-builder-concept-middleware.md
<!-- TODO: this link is a placeholder, need to find existing speech priming article -->
[speechrecognitionpriming]: ../bot-service-channel-connect-webchat-speech.md

[luis-v4-typed-entities]: bot-builder-howto-v4-luisgen.md
[luis-v4-how-to]: bot-builder-howto-v4-luis.md
[luis-v4-cs-quickstart]: https://github.com/Microsoft/botbuilder-dotnet/wiki/Using-LUIS-and-QnA-Maker
[luis-v4-js-quickstart]: https://github.com/Microsoft/botbuilder-js/wiki/Using-LUIS-and-QnA-Maker

## <a name="next-steps"></a>Próximas etapas

Serviços Cognitivos fornece maneiras de adicionar inteligência a seu bot.

> [!div class="nextstepaction"]
> [Serviços Cognitivos para bots](../bot-service-concept-intelligence.md)
