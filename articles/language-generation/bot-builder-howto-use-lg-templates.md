---
title: Usar modelos de geração de linguagem em seu bot – Serviço de Bot
description: Saiba como usar modelos de geração de linguagem em seus bots.
keywords: language generation, lg templates, C#, JS, bot
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 5/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: cf7c5c53a13f28b544d135e09c7fa31c4b773e9b
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416795"
---
# <a name="use-language-generation-templates-in-your-bot"></a>Usar modelos de geração de linguagem em seu bot

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os modelos LG (geração de linguagem) facilitam para os desenvolvedores de bot enviar uma variedade de mensagens e tipos de mídia para os usuários. Este artigo mostra como aproveitar os modelos LG para enviar cartões e mensagens de texto simples e como avaliar a entrada de texto dos usuários.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do [LUIS](https://luis.ai).
- Uma cópia da amostra de bot básico de geração de linguagem em [C#](https://aka.ms/lg-csharp-13-core-bot-sample) ou [JavaScript](https://aka.ms/lg-javascript-13-core-bot-sample).
- Conhecimento dos [conceitos básicos sobre bots](../v4sdk/bot-builder-basics.md) e da [geração de linguagem](../v4sdk/bot-builder-concept-language-generation.md).

## <a name="about-the-sample"></a>Sobre o exemplo

Esta amostra de bot básico LG mostra um exemplo de um aplicativo de reservas de voo em aeroportos. Ele usa um serviço do LUIS para reconhecer a entrada do usuário e retornar a melhor intenção reconhecida pelo LUIS.

Este artigo usa uma abordagem de baixo para cima para usar modelos LG em seus bots. Você saberá como:

- criar um objeto `Templates` e modelos de referência [na lógica de bot](#call-templates-in-files)
- criar um [modelo de resposta simples](#create-a-simple-response-template)
- criar um [modelo de resposta condicional](#create-a-conditional-response-template)
- criar um [modelo de cartões](#create-a-cards-template)
- adicionar o [LUIS ao bot](#add-luis-to-your-bot)
- [testar o bot](#test-the-bot)

## <a name="call-templates-in-files"></a>Chamar modelos em arquivos

Para usar modelos em arquivos .lg, você precisará referenciá-los na lógica de bot. As instruções abaixo mostram como os modelos LG que você criará são referenciados na lógica principal de bot carregando-os em um objeto `Templates`. O exemplo usa os modelos de **welcomeCard.lg**.

### <a name="c"></a>[C#](#tab/cs)

Verifique se você tem o pacote **Microsoft.Bot.Builder.LanguageGeneration**. Adicione o seguinte snippet para carregar o pacote:

**Bots/DialogAndWelcomeBot.cs**

[!code-csharp[add-package](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=12)]

Depois de carregar o pacote, crie um objeto `Templates` particular chamado **_templates**:

[!code-csharp[create-Templates](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=19)]

O objeto **_templates** é usado para referenciar modelos nos arquivos .lg.

Combine o caminho para o suporte multiplataforma e analise o caminho que contém **welcomeCard.lg** adicionando o seguinte ao código:

[!code-csharp[combine-path](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=25-27)]

Agora você pode referenciar modelos por meio do **welcomeCard.lg** pelo nome, visto abaixo:

[!code-csharp[reference-template](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=49-57&highlight=7)]

Observe como o modelo `WelcomeCard` é referenciado na chamada `SendActivityAsync()`.

### <a name="javascript"></a>[JavaScript](#tab/js)

Verifique se você tem o **botbuilder-lg** empacotado instalado. Adicione o seguinte snippet para carregar o pacote:

**bots/dialogAndWelcomeBot.js**

[!code-javascript[add-package](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/13.core-bot/bots/dialogAndWelcomeBot.js?range=6)]

Para usar modelos, analise **welcomeCard.lg** e salve os modelos LG em **lgTemplates**:

[!code-javascript[create-Templates](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/13.core-bot/bots/dialogAndWelcomeBot.js?range=11)]

O objeto **lgtemplates** é usado para referenciar modelos nos arquivos .lg.

Agora você pode referenciar modelos por meio do **welcomeCard.lg** pelo nome, visto abaixo:

[!code-javascript[reference-welcome-card-template](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/13.core-bot/bots/dialogAndWelcomeBot.js?range=31-43&highlight=5)]

Observe como o modelo `WelcomeCard` é referenciado na criação da constante **welcomeCard**.

---

Agora que o bot pode referenciar modelos, é hora de começar a criar modelos em arquivos LG. Usando a LG, você adiciona com facilidade uma variedade de conversas.

## <a name="create-a-simple-response-template"></a>Criar um modelo de resposta simples

Um [modelo de resposta simples](../file-format/bot-builder-lg-file-format.md#simple-response-template) inclui uma ou mais variações de texto que são usadas para composição e expansão. Uma das variações fornecidas será selecionada aleatoriamente pela biblioteca LG.

Os modelos de resposta simples em **BookingDialog.lg**, como `PromptForDestinationCity`, `PromptForDepartureCity` e `ConfirmPrefix`, adicionam várias solicitações de reserva de voo.

**Resources/BookingDialog.lg**

[!code-lg[confirm-message](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Resources/BookingDialog.LG?range=4-6)]

Por exemplo, uma chamada a `PromptForDepartureCity`, vista acima, produzirá uma das duas solicitações de texto possíveis:

- _Para onde você deseja viajar?_
- _Qual é a cidade de destino?_

### <a name="reference-memory"></a>Memória de referência

Assim como os modelos mais complexos, os modelos de resposta simples podem referenciar a memória. Em **BookingDialog.LG**, o modelo de resposta simples `ConfirmMessage` referencia as propriedades `Destination`, `Origin` e `TravelDate`:

**Resources/BookingDialog.lg**

[!code-lg[confirm-message](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Resources/BookingDialog.LG?range=17-19)]

Se o usuário inserir _Seattle_ como a `Origin`, _Paris_ como o `Destination` e _24/05/2020_ como a `TravelDate`, o bot produzirá um dos seguintes resultados:

- *Vejo que você vai viajar para: Paris de: Seattle em: 24/05/2020*
- *em 24/05/2020, viajando de Seattle a Paris*

## <a name="create-a-conditional-response-template"></a>Criar um modelo de resposta condicional

Um [modelo de resposta condicional](../file-format/bot-builder-lg-file-format.md#conditional-response-template) permite criar um conteúdo selecionado com base em uma condição. Todas as condições são expressas por meio de [expressões adaptáveis](../v4sdk/bot-builder-concept-adaptive-expressions.md).

O modelo `PromptForMissingInformation` em **BookingDialog.lg** é um exemplo de [modelo if-else](../file-format/bot-builder-lg-file-format.md#conditional-response-template). O modelo if-else permite criar um modelo que escolhe uma coleção com base em uma ordem de condições em cascata. No modelo, serão solicitadas informações ao usuário se as propriedades estiverem definidas como `null`:

**Resources/BookingDialog.lg**

[!code-lg[conditional](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Resources/BookingDialog.LG?range=31-39)]

Se uma propriedade for nula, o bot chamará o modelo associado a essa propriedade. Se todas as propriedades forem valores não nulos, o modelo `ConfirmBooking` será chamado.

### <a name="reference-other-templates"></a>Referenciar outros modelos

As variações nos modelos podem referenciar outros modelos. No exemplo acima, se uma propriedade for `null`, o modelo chamará o modelo relevante para solicitar as informações ausentes.

Por exemplo, se `Destination` for igual a `null`, o modelo `PromptforDestinationCity` será chamado por meio de `${PromptForDestinationCity()}` para obter as informações ausentes de destino do voo. Se nenhuma das propriedades for nula, o modelo chamará a solicitação `ConfirmBooking`.

## <a name="create-a-cards-template"></a>Criar um modelo de cartões

Os modelos de geração de linguagem podem usar cartões e mídia para criar uma experiência de conversa mais sofisticada. Em **welcomeCard.lg**, quatro modelos são usados para criar o [Cartão Adaptável](https://aka.ms/msbot-adaptivecards), que é exibido quando você inicia o bot pela primeira vez.

`Adaptive Card` define um objeto JSON de cartão adaptável:

**Resources/welcomeCard.lg**

[!code-lg[adaptive-card](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Resources/welcomeCard.LG?range=24-58)]

Esse cartão exibe uma imagem e usa modelos LG para o cabeçalho do cartão um conjunto de ações sugeridas.

### <a name="c"></a>[C#](#tab/cs)

 As `actions` são preenchidas com uma chamada a `cardActionTemplate(title, url, type)` e a obtenção de `title`, `url` e `type` do método `OnMembersAddedAsync()` em **DialogAndWelcomeBot.cs**:

**Bots/DialogAndWelcomeBot.cs**

[!code-csharp[fill-card](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=32-47)]

O `title` é o texto no botão de ação sugerida e a `url` é a URL aberta quando o botão recebe um clique.

### <a name="javascript"></a>[JavaScript](#tab/js)

 As `actions` são preenchidas com uma chamada a `cardActionTemplate(title, url, type)` e a obtenção de `title`, `url` e `type` do método `OnMembersAddedAsync()` em **dialogAndWelcomeBot.js**:

**bots/dialogAndWelcomeBot.js**

[!code-javascript[fill-card](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/13.core-bot/bots/dialogAndWelcomeBot.js?range=13-29)]

O `title` é o texto no botão de ação sugerida e a `url` é a URL aberta quando o botão recebe um clique.

---

Por fim, o `WelcomeCard` chama o modelo `AdaptiveCard` para retornar o objeto JSON de cartão adaptável.

**Resources/welcomeCard.lg**

[!code-lg[fill-card](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/13.core-bot/Resources/welcomeCard.LG?range=11-14)]

Para obter mais informações sobre a função `ActivityAttachment()`, leia [Injetar funções da biblioteca LG](functions-injected-from-language-generation.md)


## <a name="add-luis-to-your-bot"></a>Adicionar o LUIS ao bot

Depois de atualizar a lógica do bot e os modelos LG, você estará pronto para adicionar o LUIS ao bot. Siga as etapas nas seções abaixo para adicionar o LUIS ao bot:

- [Criar um aplicativo LUIS no portal do LUIS](https://aka.ms/bot-service-add-luis-to-bot#create-a-luis-app-in-the-luis-portal).
- [Recuperar as informações do aplicativo no portal LUIS](https://aka.ms/bot-service-add-luis-to-bot##retrieve-application-information-from-the-luisai-portal)
- [Atualizar o arquivo de configurações do bot](https://aka.ms/bot-service-add-luis-to-bot#update-the-settings-file)

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente.

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o [Exemplo de C#](https://aka.ms/cs-core-sample), [Exemplo de JS](https://aka.ms/js-core-sample) ou [Exemplo de Python](https://aka.ms/python-core-sample).

1. No emulador, digite uma mensagem, como "viagem para Paris" ou "ir de Paris para Berlim". Use qualquer enunciado encontrado no arquivo FlightBooking.json para treinar a intenção "Reservar voo".

![Entrada de reserva do LUIS](../v4sdk/media/how-to-luis/luis-user-travel-input.png)

Se a melhor intenção retornada pelo LUIS for "Reservar voo", seu bot fará perguntas adicionais até que tenha informações suficientes armazenadas para criar uma reserva de viagem. Nesse ponto, ele retornará essas informações de reserva para o usuário.

![Resultado da reserva do LUIS](../v4sdk/media/how-to-luis/luis-travel-result.png)

Nesse momento, a lógica de bot do código reiniciará e você poderá continuar a criar reservas adicionais.

## <a name="additional-information"></a>Informações adicionais

- [Modelos de resposta estruturada](language-generation-structured-response-template.md)
- [Formato de arquivo .lg](../file-format/bot-builder-lg-file-format.md)
- [Expressões adaptáveis, funções predefinidas, referência](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md)

<!--
## Next steps

> [!div class="nextstepaction"]->
