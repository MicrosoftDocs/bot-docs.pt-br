---
title: Gerenciar recursos com um arquivo .bot | Microsoft Docs
description: Descreve a finalidade e uso do arquivo de bot.
keywords: arquivo de bot, .bot, arquivo .bot, msbot, recursos do bot, gerenciar recursos do bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/11/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: db7957c611df4a3010469f86f51184b52d49addb
ms.sourcegitcommit: 4139ef7ebd8bb0648b8af2406f348b147817d4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58073852"
---
# <a name="manage-resources-with-a-bot-file"></a>Gerenciar recursos com um arquivo .bot

Em geral, os bots consomem vários serviços diferentes, como [LUIS.ai](https://luis.ai) ou [QnaMaker.ai](https://qnamaker.ai). Durante o desenvolvimento de um bot, não há um local uniforme para armazenar os metadados sobre os serviços que estão em uso.  Isso nos impede de criar ferramentas que olhem para um bot como um todo.

Para resolver esse problema, criamos um **arquivo .bot** a fim de unir todas as referências do serviço em um só lugar e permitir o uso de ferramentas.  Por exemplo, o Emulador do Bot Framework ([V4](https://aka.ms/Emulator-wiki-getting-started)) usa um arquivo .bot para criar uma visão unificada dos serviços conectados consumidos por seu bot.  

Com um arquivo .bot, você pode registrar serviços como:

* **Localhost** pontos de extremidade do depurador local
* [**Serviço de Bot do Azure**](https://azure.microsoft.com/en-us/services/bot-service/) registros do Serviço de Bot do Azure.
* [**LUIS.AI**](https://www.luis.ai/) o LUIS permite que seu bot se comunique com as pessoas usando uma linguagem natural. 
* [**QnA Maker**](https://qnamaker.ai/) compile, treine e publique rapidamente um bot simples de perguntas e respostas com base em URLs de perguntas frequentes, documentos estruturados ou conteúdo editorial.
* [**Dispatch**](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) modelos para expedição entre vários serviços.
* [**Azure Application Insights**](https://azure.microsoft.com/en-us/services/application-insights/) para análise de insights e de bot.
* [**Armazenamento de Blobs do Azure**](https://azure.microsoft.com/en-us/services/storage/blobs/) para persistência do estado do bot. 
* [**Azure Cosmos DB**](https://azure.microsoft.com/en-us/services/cosmos-db/) - serviço de banco de dados multimodelo distribuído no mundo inteiro para persistência do estado do bot.

Além desses, seu bot pode contar com outros serviços personalizados. Você pode aproveitar o recurso de [serviço genérico](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/add-services.md) para conectar uma configuração de serviço genérico.

## <a name="when-is-a-bot-file-created"></a>Quando um arquivo .bot é criado? 
- Se você criar um bot usando o [Serviço de Bot do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryResultsListBlade/selectedSubMenuItemId/%7B%22menuItemId%22%3A%22gallery%2FCognitiveServices_MP%2FBotService%22%2C%22resourceGroupId%22%3A%22%22%2C%22resourceGroupLocation%22%3A%22%22%2C%22dontDiscardJourney%22%3Afalse%2C%22launchingContext%22%3A%7B%22source%22%3A%5B%22GalleryFeaturedMenuItemPart%22%5D%2C%22menuItemId%22%3A%22CognitiveServices_MP%22%2C%22subMenuItemId%22%3A%22BotService%22%7D%7D), um arquivo .bot será criado automaticamente com a lista de serviços conectados provisionada. O .bot é criptografado por padrão.
- Se você criar um bot usando o [Modelo](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) do SDK do Bot Framework V4 para Visual Studio, ou o [Gerador do Yeoman](https://www.npmjs.com/package/generator-botbuilder) do Bot Builder, um arquivo .bot será criado automaticamente. Nenhum serviço conectado é provisionado nesse fluxo e o arquivo do bot não é criptografado.
- Se você estiver começando com os [exemplos do BotBuilder](https://github.com/Microsoft/botbuilder-samples), cada exemplo para o SDK do Bot Framework V4 incluirá um arquivo .bot não criptografado. 
- Você também pode criar um arquivo de bot usando a ferramenta [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md).

## <a name="what-does-a-bot-file-look-like"></a>Qual é a aparência de um arquivo de bot? 
Veja um exemplo de arquivo [.bot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json).
Para saber mais sobre como criptografar e descriptografar o arquivo .bot, confira [Segredos de Bot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/bot-file-encryption.md).

## <a name="why-do-i-need-a-bot-file"></a>Por que eu preciso de um arquivo .bot?

Um arquivo .bot **não** é um requisito para criar bots com o SDK do Bot Framework. Você pode continuar usando o appsettings.json, web.config, env, keyvault ou qualquer mecanismo que achar adequado para controlar as referências de serviço e as chaves das quais seu bot depende. No entanto, para testar o bot usando o Emulador, será necessário um arquivo .bot. A boa notícia é que o Emulador pode criar um arquivo .bot para teste. Para fazer isso, inicie o Emulador, clique no link **criar uma nova configuração de bot** na página de Boas-vindas. Na caixa de diálogo que aparece, digite um **Nome de Bot** e uma **URL de Ponto de Extremidade**. Em seguida, conecte-se.

As vantagens de usar o arquivo .bot são:
- Ele fornece um padrão de armazenamento de recursos, independentemente da linguagem/plataforma usada.   
- O Emulador do Bot Framework e as ferramentas da CLI dependem e funcionam bem com os serviços de controle conectados em um formato consistente (em um arquivo .bot) 
- Fica mais difícil criar soluções de ferramentas elegantes para gerenciamento e criação de serviços sem um esquema bem definido (arquivo .bot).  


## <a name="using-bot-file-in-your-bot-framework-sdk-bot"></a>Usar o arquivo .bot em seu bot do SDK do Bot Framework

Você pode usar o arquivo .bot para obter informações de configuração de serviço no código do seu bot. A biblioteca de Configuração do BotFramework disponível para [C#](https://www.nuget.org/packages/Microsoft.Bot.Configuration) e [JS](https://www.npmjs.com/package/botframework-config) ajuda você a carregar um arquivo de bot e dá suporte a vários métodos para consultar e obter as informações de configuração de serviço apropriadas.

## <a name="additional-resources"></a>Recursos adicionais
Consulte o arquivo leiame do [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md) para saber mais sobre como usar um arquivo de bot.
