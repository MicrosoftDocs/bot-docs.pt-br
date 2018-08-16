---
title: Bots de exemplo para o SDK do Construtor de Bot para .NET | Microsoft Docs
description: Explore os bots de exemplo que podem ajudá-lo a começar rapidamente seu desenvolvimento de bot com o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/2018
ms.openlocfilehash: 129c3a2b92297980e6b5e209f6e7f400a854d3a1
ms.sourcegitcommit: 9a38d76afb0e82fdccc1f36f9b1a65042671e538
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39515066"
---
::: moniker range="azure-bot-service-3.0"

# <a name="bot-builder-sdk-for-net-samples"></a>Exemplos de SDK do Construtor de Bot para .NET

[!INCLUDE [pre-release-label](~/includes/pre-release-label-v3.md)]

Estes exemplos demonstram bots centrados na tarefa que mostram como aproveitar os recursos no SDK do Construtor de Bot para .NET. Você pode usar os exemplos para ajudá-lo a começar a construir rapidamente excelentes bots com recursos avançados.

## <a name="get-the-samples"></a>Obtenha as amostras
Para obter as amostras, clone o repositório GitHub [BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples) usando o Git.

```cmd
git clone https://github.com/Microsoft/BotBuilder-Samples.git
cd BotBuilder-Samples
```

Os bots de exemplo criados com o SDK do Construtor de Bot para .NET são organizados no diretório **CSharp**.

Você também pode visualizar as amostras no GitHub e implantá-las diretamente no Azure.

## <a name="core"></a>Núcleo
Estas amostras mostram as técnicas básicas para construir bots ricos e capazes.

Amostra | DESCRIÇÃO
------------ | ------------- 
[Enviar o anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-SendAttachment) | Um robô de amostra que envia anexos de mídia simples (imagens) ao usuário. 
[Receber anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ReceiveAttachment) | Um bot de exemplo que recebe os anexos enviados pelo usuário e faz os downloads. 
[Criar nova conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CreateNewConversation)  | Um robô de amostra que inicia uma nova conversa usando um endereço de usuário armazenado anteriormente.
[Obter membros de uma conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GetConversationMembers) | Um robô de amostra que recupera a lista de membros da conversa e detecta quando ela é alterada. 
[Linha direta](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLine) | Um robô de amostra e um cliente personalizado que se comunicam entre si usando a API de linha direta. 
[Linha direta (WebSockets)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLineWebSockets) | Um robô de amostra e um cliente personalizado que se comunicam entre si usando o Direct Line API + WebSockets. 
[Várias caixas de diálogo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-MultiDialogs) | Um exemplo de bot que mostra vários tipos de diálogos.
[API de estado](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-State) | Um robô de amostra stateless que rastreia o contexto de uma conversa.
[API de estado personalizadas](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CustomState) | Um robô de amostra stateless que rastreia o contexto de uma conversa usando um provedor de armazenamento personalizado.
[ChannelData](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ChannelData) | Um exemplo de bot que envia metadados nativos para o Facebook usando ChannelData.
[AppInsights](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-AppInsights) | Um robô de amostra que registra a telemetria em uma instância do Application Insights.

## <a name="search"></a>Search
Este exemplo mostra como usar o Azure Search em bots controlada por dados.

Amostra | DESCRIÇÃO
------------ | -------------
[Azure Search](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search) | Dois bots de amostra que ajudam o usuário navegue grandes quantidades de conteúdo.


## <a name="cards"></a>Cartões
Estes exemplos mostram como enviar cartões ricos no Bot Framework.

Amostra | DESCRIÇÃO
------------ | -------------
[Cartões ricos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-RichCards) | Um bot de exemplo que envia vários tipos diferentes de cartões ricos.
[Carrossel de cartões](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-CarouselCards) | Um robô de amostra que envia vários cartões ricos em uma única mensagem usando o layout Carrossel.

## <a name="intelligence"></a>Inteligência
Esses exemplos mostram como adicionar recursos de inteligência artificial a um bot usando as APIs do Bing e Microsoft Cognitive Services.

Amostra | DESCRIÇÃO
------------ | -------------
[LUIS](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-LUIS) | Um robô de amostra que usa o LuisDialog para integrar com um aplicativo LUIS.ai.
[Legenda da imagem](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption) | Um robô de amostra que obtém uma legenda de imagem usando a API do Microsoft Cognitive Services Vision.
[Conversão de fala em texto](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SpeechToText)  | Um robô de amostra que obtém texto do áudio usando a Bing Speech API.
[ Produtos semelhantes ](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SimilarProducts) | Um robô de amostra que encontra produtos visualmente semelhantes usando a API de pesquisa de imagens do Bing. 
[Zummer](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-Zummer) | Um robô de amostra que encontra artigos da Wikipédia usando a API de pesquisa do Bing.

## <a name="reference-implementation"></a>Implementação de referência
Este exemplo foi projetado para mostrar um cenário de ponta a ponta. É uma ótima fonte de fragmentos de código se você estiver procurando implementar recursos mais complexos em seu bot.


Amostra | DESCRIÇÃO
------------ | -------------
[Flores Contoso](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-ContosoFlowers) | Um bot de exemplo que usa muitos recursos do Bot Framework.

::: moniker-end

::: moniker range="azure-bot-service-4.0"
# <a name="bot-builder-sdk-v4-net-samples"></a>Exemplos de SDK v4 do Construtor de Bot para .NET
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Estes exemplos demonstram bots centrados na tarefa que mostram como aproveitar os recursos no SDK do Construtor de Bot para .NET. Você pode usar os exemplos para ajudá-lo a começar a construir rapidamente excelentes bots com recursos avançados. 

Observação: O SDK v4 está sendo desenvolvido ativamente e, portanto, deve ser usado apenas para experimento. 

Para obter as amostras, clone o repositório GitHub [botbuilder-dotnet](https://github.com/Microsoft/botbuilder-dotnet) usando o Git.
```cmd
git clone https://github.com/Microsoft/botbuilder-dotnet.git
cd botbuilder-dotnet\samples-final
```
Os bots de exemplo criados com o SDK do Construtor de Bot para .NET são organizados no diretório **samples-final**.


::: moniker-end

