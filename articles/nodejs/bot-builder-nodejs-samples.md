---
title: Exemplo de bots para o Bot Builder SDK for Node.js | Microsoft Docs
description: Explore uma grande seleção de robôs de exemplo que podem ajudar a acelerar o desenvolvimento de bots com o Bot Builder SDK for Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 43c178c4bbdf0bb04384bb8ada397066e6f7dd12
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574612"
---
# <a name="bot-builder-sdk-for-nodejs-samples"></a>SDK do construtor de bot para obter exemplos de Node. js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Esses exemplos demonstram bots com foco em tarefas que mostram como aproveitar os recursos do SDK do Bot Builder para Node.js. Você pode usar as amostras para ajudá-lo a começar rapidamente a criar grandes bots com recursos avançados.

## <a name="get-the-samples"></a>Obtenha as amostras
Para obter as amostras, clone o [repositório GitHub do BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples) usando o Git.

```cmd
git clone https://github.com/Microsoft/BotBuilder-Samples.git
cd BotBuilder-Samples
```

Os robôs de amostra criados com o Bot Builder SDK para Node.js são organizados no diretório **Node**.

Você também pode visualizar as amostras no GitHub e implantá-las diretamente no Azure.

## <a name="core"></a>Núcleo
Estas amostras mostram as técnicas básicas para construir bots ricos e capazes.

Amostra | DESCRIÇÃO
------------ | ------------- 
[Enviar o anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-SendAttachment) | Um robô de amostra que envia anexos de mídia simples (imagens) ao usuário. 
[Receber anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ReceiveAttachment) | Um bot de exemplo que recebe os anexos enviados pelo usuário e faz os downloads. 
[Criar nova conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-CreateNewConversation)  | Um robô de amostra que inicia uma nova conversa usando um endereço de usuário armazenado anteriormente.
[Obter membros de uma conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-GetConversationMembers) | Um robô de amostra que recupera a lista de membros da conversa e detecta quando ela é alterada. 
[Linha direta](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-DirectLine) | Um robô de amostra e um cliente personalizado que se comunicam entre si usando a API de linha direta. 
[Linha direta (WebSockets)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-DirectLineWebSockets) | Um robô de amostra e um cliente personalizado que se comunicam entre si usando o Direct Line API + WebSockets. 
[Várias caixas de diálogo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-MultiDialogs) | Um exemplo de bot que mostra vários tipos de diálogos.
[API de estado](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-State) | Um robô de amostra stateless que rastreia o contexto de uma conversa.
[API de estado personalizadas](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-CustomState) | Um robô de amostra stateless que rastreia o contexto de uma conversa usando um provedor de armazenamento personalizado.
[ChannelData](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ChannelData) | Um exemplo de bot que envia metadados nativos para o Facebook usando ChannelData.
[AppInsights](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-AppInsights) | Um robô de amostra que registra a telemetria em uma instância do Application Insights.

## <a name="search"></a>Search
Este exemplo mostra como usar o Azure Search em bots controlada por dados.

Amostra | DESCRIÇÃO
------------ | -------------
[Azure Search](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-Search) | Dois bots de amostra que ajudam o usuário navegue grandes quantidades de conteúdo.


## <a name="cards"></a>Cartões
Estes exemplos mostram como enviar cartões ricos no Bot Framework.

Amostra | DESCRIÇÃO
------------ | -------------
[Cartões ricos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/cards-RichCards) | Um bot de exemplo que envia vários tipos diferentes de cartões ricos.
[Carrossel de cartões](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/cards-CarouselCards) | Um robô de amostra que envia vários cartões ricos em uma única mensagem usando o layout Carrossel.

## <a name="intelligence"></a>Inteligência
Esses exemplos mostram como adicionar recursos de inteligência artificial a um bot usando as APIs do Bing e Microsoft Cognitive Services.

Amostra | DESCRIÇÃO
------------ | -------------
[LUIS](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS) | Um robô de amostra que usa o LuisDialog para integrar com um aplicativo LUIS.ai.
[Legenda da imagem](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-ImageCaption) | Um robô de amostra que obtém uma legenda de imagem usando a API do Microsoft Cognitive Services Vision.
[Conversão de fala em texto](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-SpeechToText)  | Um robô de amostra que obtém texto do áudio usando a Bing Speech API.
[ Produtos semelhantes ](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-SimilarProducts) | Um robô de amostra que encontra produtos visualmente semelhantes usando a API de pesquisa de imagens do Bing. 
[Zummer](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-Zummer) | Um robô de amostra que encontra artigos da Wikipédia usando a API de pesquisa do Bing.

## <a name="reference-implementation"></a>Implementação de referência
Este exemplo foi projetado para mostrar um cenário de ponta a ponta. É uma ótima fonte de fragmentos de código se você estiver procurando implementar recursos mais complexos em seu bot.


Amostra | DESCRIÇÃO
------------ | -------------
[Flores Contoso](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/demo-ContosoFlowers) | Um robô de amostra que usa muitos recursos do Bot Framework.

