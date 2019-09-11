---
title: Reconhecimento Vocal | Microsoft Docs
description: Saiba como adicionar inteligência artificial aos bots com os Serviços Cognitivos da Microsoft para torná-los mais úteis e interativas.
keywords: LUIS, intenção, reconhecedor, ferramenta de expedição, qna, qna maker
author: ivorb
ms.author: kamrani
manager: rstand
ms.topic: article
ms.service: bot-service
ms.date: 09/19/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 83d6a4b43cdd3132730ee78079260d2a2d79b228
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299483"
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

[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/home) ajuda você com a determinação da **intenção** do usuário, que é o que eles desejam fazer, a partir do que eles dizem, portanto, seu bot pode responder adequadamente. LUIS é especialmente útil quando o que eles dizem ao seu bot não segue uma estrutura previsível ou um padrão específico. Se um bot tem uma interface de usuário convencional, na qual o usuário fala ou digita uma resposta, pode haver variações intermináveis em *declarações*, que são a entrada falada ou textual do usuário.

Por exemplo, considere as várias maneiras de um usuário de um bot de viagem pode pedir para reservar um voo.

![Várias declarações formadas de modo diferente para reservar um voo](media/cognitive-services-add-bot-language/cognitive-services-luis-utterances.png)

Essas declarações podem ter estruturas diferentes e contêm vários sinônimos para "voo" que você ainda não pensou. Em seu bot, pode ser difícil gravar a lógica que corresponde a todas as declarações e ainda distinguir outras intenções que contenham as mesmas palavras. Além disso, seu bot precisa extrair *entidades*, que são as palavras importantes, como locais e horas. LUIS facilita esse processo por meio da identificação contextualmente intenções e entidades para você.

Quando você projeta seu bot para entrada de linguagem natural, você determina quais intenções e entidades seu bot precisa reconhecer e pensa em como elas se conectará às ações que seu bot usa. Em [luis.ai](https://www.luis.ai), você define intenções e entidades personalizadas e especifica o comportamento fornecendo exemplos para cada intenção e rotulando as entidades dentro deles.

Seu bot usa a intenção reconhecida por LUIS para determinar o tópico de conversa ou iniciar um fluxo da conversa. Por exemplo, quando um usuário diz: "Eu gostaria de reservar um voo" seu bot detecta a intenção de BookFlight e invoca o fluxo da conversa para iniciar uma pesquisa para voos. LUIS detecta entidades, como a cidade de destino e a data de partida na declaração original que dispara a intenção e, posteriormente, no fluxo de conversa. Uma vez que o bot tem todas as informações necessárias, ele pode atender a intenção do usuário.

![Uma conversa com um bot é disparada pela intenção de BookFlight](media/cognitive-services-add-bot-language/cognitive-services-luis-conversation-high-level.png)

### <a name="recognize-intent-in-common-scenarios"></a>Reconhecer intenções em cenários comuns

Para economizar tempo de desenvolvimento, o LUIS fornece modelos de linguagem previamente treinados que reconhecem declarações comuns para as categorias comuns de bots. 

Os **domínios predefinidos** são coleções de intenções e entidades pré-treinadas, prontos para uso, que funcionam bem em conjunto para cenários comuns, como compromissos, lembretes, gerenciamento, saúde, entretenimento, comunicação, reservas e muito mais. O domínio predefinido **Utilitários** ajuda seu bot a lidar com tarefas comuns, como Cancelar, Confirmar, Ajudar, Repetir e Parar. Dê uma olhada nos [domínios predefinidos](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-use-prebuilt-domains) que o LUIS oferece.

**As entidades predefinidas** ajudam seu bot a reconhecer os tipos comuns de informações como datas, horas, números, temperatura, moeda, região e idade. Consulte [usar as entidades predefinidas](https://docs.microsoft.com/azure/cognitive-services/LUIS/pre-builtentities) para obter informações sobre os tipos que LUIS pode reconhecer.

## <a name="how-your-bot-gets-messages-from-luis"></a>Como seu bot obtém as mensagens do LUIS

Depois de ter configurado e conectado o LUIS, seu bot pode enviar a mensagem para o seu aplicativo LUIS, que retorna uma resposta JSON que contém as intenções e entidades. Em seguida, você pode usar o [contexto de turno](~/v4sdk/bot-builder-basics.md#defining-a-turn) no _manipulador de turno_ do seu bot para rotear o fluxo da conversa com base na intenção na resposta de LUIS. 

![Como as intenções e entidades são passadas ao seu bot](./media/cognitive-services-add-bot-language/cognitive-services-luis-message-flow-bot-code.png)

Para começar a usar um aplicativo LUIS com o seu bot, confira [Usar LUIS para reconhecimento vocal](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0).

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

Os aplicativos LUIS fornecem um recurso para melhorar o desempenho do seu aplicativo, examinando as mensagens que os usuários enviaram a ele. Consulte [declarações sugeridas](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances) para obter uma explicação passo a passo.


## <a name="integrate-multiple-luis-apps-and-qna-services-with-the-dispatch-tool"></a>Integrar vários aplicativos LUIS e serviços do QnA com a ferramenta de Expedição

Ao criar um bot multiuso que compreende vários tópicos de conversa, você pode começar a desenvolver serviços para cada função separadamente e, em seguida, integrá-los. Esses serviços podem incluir aplicativos de Reconhecimento Vocal (LUIS) e serviços do QnAMaker. Aqui estão alguns exemplos de cenários em que um bot pode combinar vários aplicativos LUIS, vários serviços QnAMaker ou uma combinação dos dois:

* Um bot de assistente pessoal permite que o usuário invoque uma variedade de comandos. Cada categoria de comandos forma uma "habilidade" que pode ser desenvolvida separadamente, e cada uma delas tem um aplicativo LUIS.
* Um bot pesquisa várias bases de conhecimento para encontrar respostas para perguntas frequentes (FAQs).
* Um bot para uma empresa tem aplicativos LUIS para criação de contas de clientes e emissão de pedidos e também tem um serviço QnAMaker para suas perguntas frequentes.  

### <a name="the-dispatch-tool"></a>A ferramenta de expedição

A ferramenta de expedição ajuda você a integrar vários aplicativos LUIS e serviços QnA Maker com seu bot, por meio da criação de um *aplicativo de expedição*, que é um novo aplicativo LUIS que encaminha mensagens para os serviços do QnAMaker e LUIS apropriado. Consulte o [tutorial de expedição](./bot-builder-tutorial-dispatch.md) para obter um tutorial passo a passo que combina vários aplicativos LUIS e QnA Maker em um bot.

## <a name="use-luis-to-improve-speech-recognition"></a>Use LUIS para melhorar o reconhecimento de fala

Para um bot que irá se dirigir para os usuários, integrá-lo com o LUIS pode ajudar seu bot a identificar palavras que podem ser interpretados incorretamente durante a conversão de fala em texto.  Por exemplo, em um cenário de xadrez, um usuário pode dizer: "Mover o peão para A7". Sem contexto para a intenção do usuário, o enunciado poderia ser reconhecido como: "Mover o pião para 7". Ao criar entidades que representam as coordenadas e peças de xadrez e rotulá-las em declarações, você fornece contexto para o reconhecimento de fala identificá-las. Você pode [habilitar a desobstrução de reconhecimento de fala](https://docs.microsoft.com/azure/bot-service/bot-service-manage-speech-priming?view=azure-bot-service-4.0) com canais de Bot Framework que são integrados com a Fala do Bing, como Web Chat, o emulador do Bot Framework e Cortana.  

## <a name="additional-resources"></a>Recursos adicionais
Consulte a documentação dos [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/) para obter mais informações.
