---
title: Bots de exemplo para o SDK do Construtor de Bot para .NET | Microsoft Docs
description: Explore os bots de exemplo que podem ajudá-lo a começar rapidamente seu desenvolvimento de bot com o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/2018
ms.openlocfilehash: 7e19ec2c3e523003c0831544e42eeb88765d8317
ms.sourcegitcommit: dcbc8ad992a3e242a11ebcdf0ee99714d919a877
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39352855"
---
::: moniker range="azure-bot-service-3.0"

# <a name="bot-builder-sdk-for-net-samples"></a>Exemplos de SDK do Construtor de Bot para .NET

Estes exemplos demonstram bots centrados na tarefa que mostram como aproveitar os recursos no SDK do Construtor de Bot para .NET. Você pode usar os exemplos para ajudá-lo a começar a construir rapidamente excelentes bots com recursos avançados.

## <a name="get-the-samples"></a>Obter as amostras
Para obter as amostras, clone o repositório GitHub [BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples) usando o Git.

```cmd
git clone https://github.com/Microsoft/BotBuilder-Samples.git
cd BotBuilder-Samples
```

Os bots de exemplo criados com o SDK do Construtor de Bot para .NET são organizados no diretório **CSharp**.

Você também pode exibir os exemplos no GitHub e implantá-los diretamente no Azure.

## <a name="core"></a>Núcleo
Estes exemplos mostram as técnicas básicas para criar bots sofisticados e capazes.

Amostra | DESCRIÇÃO
------------ | ------------- 
[Enviar anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-SendAttachment) | Um bot de exemplo que envia os anexos de mídia simples (imagens) para o usuário. 
[Receber anexo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ReceiveAttachment) | Um bot de exemplo que recebe os anexos enviados pelo usuário e faz os downloads. 
[Criar uma nova conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CreateNewConversation)  | Um bot de exemplo que inicia uma nova conversa usando um endereço de usuário armazenado anteriormente.
[Obter membros de uma conversa](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GetConversationMembers) | Um bot de exemplo que recupera a lista de membros da conversa e detecta quando ela é alterada. 
[Linha direta](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLine) | Um bot de exemplo e um cliente personalizado que se comunicam entre si usando a API de linha direta. 
[Linha direta (WebSockets)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLineWebSockets) | Um bot de exemplo e um cliente personalizado que se comunicam entre si usando a API de linha direta + WebSockets. 
[Várias caixas de diálogo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-MultiDialogs) | Um bot de exemplo que mostra os vários tipos de caixas de diálogo.
[API de estado](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-State) | Um bot de exemplo sem monitoração de estado que controla o contexto de uma conversa.
[API de estado personalizada](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-CustomState) | Um bot de exemplo sem monitoração de estado que controla o contexto de uma conversa usando um provedor de armazenamento personalizado.
[ChannelData](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-ChannelData) | Um bot de exemplo que envia os metadados nativos ao Facebook usando ChannelData.
[AppInsights](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-AppInsights) | Um bot de exemplo que registra a telemetria em uma instância do Application Insights.

## <a name="search"></a>Search
Este exemplo mostra como usar o Azure Search em bots controlados por dados.

Amostra | DESCRIÇÃO
------------ | -------------
[Azure Search](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search) | Dois bots de amostra que ajudam o usuário a navegar grandes quantidades de conteúdo.


## <a name="cards"></a>Cartões
Estes exemplos mostram como enviar cartões ricos no Bot Framework.

Amostra | DESCRIÇÃO
------------ | -------------
[Cartões ricos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-RichCards) | Um bot de exemplo que envia vários tipos diferentes de cartões ricos.
[Carrossel de cartões](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/cards-CarouselCards) | Um bot de exemplo que envia vários cartões ricos dentro de uma única mensagem usando o layout de carrossel.

## <a name="intelligence"></a>Inteligência
Estes exemplos mostram como adicionar recursos de inteligência artificial para um bot usando o Bing e APIs de Serviços Cognitivos da Microsoft.

Amostra | DESCRIÇÃO
------------ | -------------
[LUIS](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-LUIS) | Um bot de exemplo que usa o diálogo LUIS para integrar um aplicativo LUIS.ai.
[Legenda da imagem](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption) | Um bot de exemplo que obtém uma legenda de imagem usando a API de Visão de Serviços Cognitivos da Microsoft.
[Conversão de Fala em Texto](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SpeechToText)  | Um bot de exemplo que obtém o texto de áudio usando a API de Fala do Bing.
[Produtos similares](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-SimilarProducts) | Um bot de exemplo que localiza produtos visualmente similares usando a API de Pesquisa de Imagem do Bing. 
[Zummer](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-Zummer) | Um bot de exemplo que localiza artigos da Wikipédia usando a API de Pesquisa do Bing.

## <a name="reference-implementation"></a>Implementação de referência
Este exemplo é projetado para demonstrar um cenário de ponta a ponta. É uma ótima fonte de fragmentos de código se você estiver procurando implementar recursos mais complexos em seu bot.


Amostra | DESCRIÇÃO
------------ | -------------
[Contoso Flowers](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-ContosoFlowers) | Um bot de exemplo que usa muitos recursos do Bot Framework.

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

