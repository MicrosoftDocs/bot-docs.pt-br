---
title: Usar funções personalizadas no serviço de bot de geração de linguagem
description: Saiba como adicionar funções personalizadas a expressões adaptáveis e usá-las em geração de linguagem.
keywords: geração de linguagem, modelos de LG, funções personalizadas, expressões adaptáveis, C#, JS, bot
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 8/13/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ac71f7434f18336fb91776ac610a8b02c9bd4730
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416820"
---
# <a name="use-custom-functions-in-language-generation"></a>Usar funções personalizadas na geração de linguagem

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os desenvolvedores podem usar as [funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) suportadas por expressões adaptáveis e funções personalizadas em [modelos de geração de linguagem (LG)](../file-format/bot-builder-lg-file-format.md). Este artigo mostra como adicionar uma função personalizada em seu bot a expressões adaptáveis e usar a função em um modelo LG.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot](../v4sdk/bot-builder-basics.md), [expressões adaptáveis](../v4sdk/bot-builder-concept-adaptive-expressions.md), [geração de linguagem](../v4sdk/bot-builder-concept-language-generation.md)e o [formato de arquivo. LG](../file-format/bot-builder-lg-file-format.md). A familiaridade com [funções predefinidas](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md) também é útil.
- Uma cópia do exemplo de funções personalizadas em [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions) ou [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/language-generation/20.custom-functions).

## <a name="about-the-sample"></a>Sobre o exemplo

Este exemplo de funções personalizadas do LG é um exemplo de como adicionar uma função personalizada simples a expressões adaptáveis e, em seguida, usar essa expressão em um modelo LG. O bot solicita um número ao usuário e, se a entrada for válida, retorna a raiz quadrada. A função que computa a raiz quadrada, `contoso.sqrt` , é definida na lógica de bot e é usada no modelo LG que gera respostas de bot.

Este artigo usa uma abordagem de baixo para cima para adicionar e usar funções personalizadas em modelos LG. Você aprenderá a:

- Adicionar [pacotes](#packages) você precisa usar expressões adaptáveis e LG em seu bot
- [Adicionar uma função personalizada às expressões adaptáveis](#add-a-custom-function-to-adaptive-expressions) na lógica do bot
- [usar sua função personalizada em um modelo LG](#use-your-custom-function-in-an-lg-template)
- [carregar e usar seu modelo em seu bot](#load-and-use-your-lg-template-in-your-bot)
- [testar o bot](#test-the-bot)

## <a name="packages"></a>Pacotes

### <a name="c"></a>[C#](#tab/cs)

Para usar expressões adaptáveis e LG, instale os pacotes **Microsoft. bot. Builder. LanguageGeneration** e **AdaptiveExpressions** . O exemplo já tem o pacote instalado. Em seguida, adicione o trecho a seguir ao arquivo principal do bot.

**Bots/CustomFunctionBot. cs**

[!code-csharp[packages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=9-10)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar expressões adaptáveis e LG, instale o **botbuilder-LG** e os pacotes **de expressões adaptáveis** instalados. O exemplo já tem o pacote instalado. Em seguida, adicione o trecho a seguir ao arquivo principal do bot.

**bot.js**

[!code-javascript[packages](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=5-6)]

---

## <a name="add-a-custom-function-to-adaptive-expressions"></a>Adicionar uma função personalizada a expressões adaptáveis

Para usar funções personalizadas em seu bot, você precisa adicioná-las a expressões adaptáveis. Esta seção mostra como adicionar uma função personalizada chamada `contoso.sqrt` para expressões adaptáveis.

### <a name="c"></a>[C#](#tab/cs)

Comece adicionando uma constante de cadeia de caracteres com o nome da sua função personalizada ao seu construtor de bot. O nome da função deve ser curto, mas reconhecível. Neste exemplo, a função personalizada é denominada `contoso.sqrt` :

**Bots/CustomFunctionBot. cs**

[!code-csharp[function-name](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=26)]

> [!IMPORTANT]
> Prefixe suas funções para evitar colisões de namespace.

No nome da função `contoso` é o prefixo e `sqrt` é curto para a raiz quadrada, que a função retorna.

Agora você pode definir a lógica para sua função em seu construtor de bot e adicioná-la a expressões adaptáveis usando a `Expression.Functions.Add()` função. Adicionar sua função personalizada à expressão adaptável torna possível usar sua função em modelos LG, assim como você pode fazer com qualquer uma das funções predefinidas.

O trecho de código a seguir mostra como adicionar uma função, definida como `mySqrtFnName` , a expressões adaptáveis. Essa função retorna a raiz quadrada de um único argumento, `args` , se válido, e `null` se não for:

[!code-csharp[add-function](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=28-41)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Comece adicionando uma constante de cadeia de caracteres com o nome da sua função personalizada. O nome da função deve ser curto, mas reconhecível. Neste exemplo, a função personalizada é denominada `contoso.sqrt` :

**bot.js**

[!code-javascript[function-name](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=10)]

> [!IMPORTANT]
> Prefixe suas funções para evitar colisões de namespace.

No nome da função `contoso` é o prefixo e `sqrt` é curto para a raiz quadrada, que a função retorna.

Agora você pode definir a lógica para sua função em seu construtor de bot e adicioná-la a expressões adaptáveis usando a `Expression.functions.add()` função. Adicionar sua função personalizada à expressão adaptável torna possível usar sua função em modelos LG, assim como você pode fazer com qualquer uma das funções predefinidas.

O trecho de código a seguir mostra como adicionar uma função, definida como `mySqrtFnName` , a expressões adaptáveis. Essa função retorna a raiz quadrada de um único argumento, `args` , se válido, e `null` se não for:

[!code-javascript[add-function](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=18-30)]

---

## <a name="use-your-custom-function-in-an-lg-template"></a>Usar sua função personalizada em um modelo LG

Depois de adicionar sua função personalizada a expressões adaptáveis, você pode usá-la em modelos LG. Esta seção descreve como configurar a resposta do bot dependendo se a entrada do usuário era válida.

### <a name="c"></a>[C#](#tab/cs)

Há duas definições de modelo em **Main. LG**: o [modelo de resposta simples](../file-format/bot-builder-lg-file-format.md#simple-response-template) `sqrtReadBack` e o [modelo de resposta condicional](../file-format/bot-builder-lg-file-format.md#conditional-response-template) `sqrtTemplate` :

**Recursos/Main. LG**

[!code-csharp[sqrtReadBack](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Resources/main.lg?range=4-5)]

Esse modelo gera uma resposta que contém a entrada do usuário `${text}` e o resultado do segundo [modelo if-else](../file-format/bot-builder-lg-file-format.md#if-else-template) condicional `sqrtTemplate` :

[!code-csharp[sqrtTemplate](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Resources/main.lg?range=9-13)]

Neste modelo, o resultado de `contoso.sqrt(text)` é usado para determinar a resposta:

- Se o resultado não for **nulo**, a linha sob o `IF` bloco será usada em `sqrtReadBack` . Observe que essa linha usa uma expressão embutida, `${coalesce(contoso.sqrt(text), 'NaN)}` . A função de [União](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#coalesce) predefinida retorna o resultado de `contoso.sqrt(text)` se ela não for nula e `NaN` se for nula.
- Se o resultado for **nulo**, a linha sob o `ELSE` bloco será usada em `sqrtReadBack` .

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Há duas definições de modelo em **Main. LG**: o [modelo de resposta simples](../file-format/bot-builder-lg-file-format.md#simple-response-template) `sqrtReadBack` e o [modelo de resposta condicional](../file-format/bot-builder-lg-file-format.md#conditional-response-template) `sqrtTemplate` :

**recursos/Main. LG**

[!code-javascript[sqrtReadBack](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Resources/main.lg?range=4-5)]

Esse modelo gera uma resposta que contém a entrada do usuário `${text}` e o resultado do segundo [modelo if-else](../file-format/bot-builder-lg-file-format.md#if-else-template) condicional `sqrtTemplate` :

[!code-javascript[sqrtTemplate](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/resources/main.lg?range=9-13)]

Neste modelo, o resultado de `contoso.sqrt(text)` é usado para determinar a resposta:

- Se o resultado não for **nulo**, a linha sob o `IF` bloco será usada em `sqrtReadBack` . Observe que essa linha usa uma expressão embutida, `${coalesce(contoso.sqrt(text), 'NaN)}` . A função de [União](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md#coalesce) predefinida retorna o resultado de `contoso.sqrt(text)` se ela não for nula e `NaN` se for nula.
- Se o resultado for **nulo**, a linha sob o `ELSE` bloco será usada em `sqrtReadBack` .

---

## <a name="load-and-use-your-lg-template-in-your-bot"></a>Carregar e usar seu modelo LG em seu bot

Agora que você criou o modelo de resposta do bot `sqrtReadBack` em **Main. LG**, você pode usar o modelo na lógica do bot.

### <a name="c"></a>[C#](#tab/cs)

Para iniciar, crie um `Templates` objeto privado chamado `_templates` .

**Bots/CustomFunctionBot. cs**

[!code-csharp[create-_templates](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=19)]

O `_templates` objeto é usado para fazer referência a modelos em seus arquivos. LG.

Em seguida, combine o caminho para o suporte de plataforma cruzada. Certifique-se de incluir **Main. LG** adicionando o seguinte:

[!code-csharp[cross-path](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=23)]

Agora você pode analisar os arquivos no `lgFilePath` e carregar seus modelos LG, conforme mostrado abaixo. Por padrão, isso usa `Expression.Function` , que inclui sua função personalizada.

[!code-csharp[load-templates](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=44)]

Seus modelos agora estão carregados e você pode referenciá-los por nome em seu bot. Neste exemplo, o resultado da avaliação `sqrtReadBack` é usado como o `replyText` enviado ao usuário.

[!code-csharp[use-template-in-code](~/../BotBuilder-Samples/samples/csharp_dotnetcore/language-generation/20.extending-with-custom-functions/Bots/CustomFunctionBot.cs?range=47-51)]

Agora você está pronto para testar o bot.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Para começar, combine o caminho para o suporte de plataforma cruzada. Certifique-se de incluir o caminho para **./Resources/Main.LG**, visto abaixo:

**bot.js**

[!code-javascript[cross-path](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=16)]

Agora você pode analisar os arquivos em `lgFilePath` e carregar seus modelos LG. Por padrão, isso usa `Expression.functions` , que inclui sua função personalizada:

[!code-javascript[load-templates](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=33)]

Seus modelos agora estão carregados e você pode referenciá-los por nome em seu bot. Neste exemplo, o resultado da avaliação `sqrtReadBack` é usado como o `replyText` enviado ao usuário.

[!code-javascript[use-template-in-code](~/../BotBuilder-Samples/samples/javascript_nodejs/language-generation/20.custom-functions/bot.js?range=36-43)]

Agora você está pronto para testar o bot.

---

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale a versão mais recente do [emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme).

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAme para o exemplo de [C#](https://aka.ms/dotnet-lg-20-custom-function-sample)ou [JavaScript](https://aka.ms/js-lg-20-custom-functions-sample) .
1. No emulador, digite qualquer coisa. Você observará que o emulador retornará a raiz quadrada dos números inseridos e `NaN` para todas as outras entradas.

![testar o bot](../v4sdk/media/language-generation/botbuilder-howto-custom-functions/test-bot.png)

## <a name="additional-information"></a>Informações adicionais

- [Modelos de resposta estruturada](language-generation-structured-response-template.md)
- [Formato de arquivo .lg](../file-format/bot-builder-lg-file-format.md)
- [Expressões adaptáveis, funções predefinidas, referência](../adaptive-expressions/adaptive-expressions-prebuilt-functions.md)

<!--
## Next steps

> [!div class="nextstepaction"]->
