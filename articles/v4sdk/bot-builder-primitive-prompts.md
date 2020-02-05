---
title: Crie seus próprios prompts para coletar entradas do usuário – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com solicitações primitivas no SDK do Bot Framework.
keywords: fluxo da conversa, prompts, estado da conversa, estado do usuário, prompts personalizados
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ecf9f7fea20ebf85f738c10b963ed9219001f9d4
ms.sourcegitcommit: 36d6f06ffafad891f6efe4ff7ba921de8a306a94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76895704"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Crie seus próprio prompts para coletar entradas do usuário

[!INCLUDE[applies-to](../includes/applies-to.md)]

Uma conversa entre um bot e um usuário muitas vezes envolve solicitar ao usuário algumas informações, analisar as respostas e atuar com base nessas informações. Seu bot deve controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Um *estado* do bot é uma informação que o bot controla para responder apropriadamente às mensagens recebidas. 

> [!TIP]
> A biblioteca de diálogos fornece prompts internos que oferecem mais funcionalidade do que os usuários podem usar. Exemplos desses prompts podem ser encontrados no artigo [Implementar fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prerequisites"></a>Prerequisites

- O código neste artigo baseia-se no exemplo de Solicitar Entrada aos Usuários. Você precisará de uma cópia do **[exemplo de C#](https://aka.ms/cs-primitive-prompt-sample), do [exemplo de JavaScript](https://aka.ms/js-primitive-prompt-sample) ou do [exemplo de Python](https://aka.ms/python-primitive-prompt-sample)** .
- Conhecimento sobre [gerenciar estado](bot-builder-concept-state.md) e como [salvar dados do usuário e da conversa](bot-builder-howto-v4-state.md).

## <a name="about-the-sample-code"></a>Sobre o código de exemplo

O exemplo de bot faz uma série de perguntas ao usuário, valida algumas das respostas dele e salva as entrada que ele faz. O diagrama a seguir mostra a relação entre o bot, o perfil do usuário e as classes de fluxo da conversa. 

## <a name="ctabcsharp"></a>[C#](#tab/csharp)
![custom-prompts](media/CustomPromptBotSample-Overview.png)

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `ConversationFlow.Question` interna para controlar onde estamos na conversa.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
![custom-prompts](media/CustomPromptBotSample-JS-Overview.png)

- Uma classe `userProfile` para as informações do usuário que o bot coletará.
- Uma classe `conversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `conversationFlow.question` interna para controlar onde estamos na conversa.

## <a name="pythontabpython"></a>[Python](#tab/python)
![custom-prompts](media/CustomPromptBotSample-Python-Overview.png)

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `ConversationFlow.Question` interna para controlar onde estamos na conversa.

---

O estado do usuário controlará nome do usuário, idade e data escolhida. O estado da conversa controlará o que acabamos de solicitar ao usuário.
Como não planejamos implantar esse bot, configuraremos os estados do usuário e da conversa para usar o _armazenamento de memória_. 

Usamos o manipulador de turno de mensagem do bot e as propriedades do estado da conversa para gerenciar o fluxo da conversa e a coleta de entradas. Em nosso bot, registraremos as informações da propriedade do estado recebidas durante cada iteração do manipulador de turno de mensagem.

## <a name="create-conversation-and-user-objects"></a>Criar objetos da conversa e do usuário

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Os objetos de estado da conversa e do usuário são criados na inicialização e a dependência é injetada no construtor de bot. 

**Startup.cs**  
[!code-csharp[Startup](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Startup.cs?range=28-35)]

**Bots/CustomPromptBot.cs**  
[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=21-28)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No **index. js**, crie as propriedades de estado e o bot e, em seguida, chame o método do bot `run` em `processActivity`.

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/index.js?range=33-39)]

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/index.js?range=63-69)]

## <a name="pythontabpython"></a>[Python](#tab/python)

Em **app.py**, crie as propriedades de estado e o bot.

[!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/app.py?range=67-73)]

---

## <a name="create-property-accessors"></a>Criar acessadores de propriedade

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Começamos criando acessadores de propriedade que nos forneçam um identificador para `BotState` dentro do método `OnMessageActivityAsync`. Em seguida, chamamos o método `GetAsync` para obter a chave de escopo correta:

**Bots/CustomPromptBot.cs**  
[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-37)]

Finalmente, salvamos os dados usando o método `SaveChangesAsync`.

[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=41-44)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No construtor, criamos os acessadores de propriedade de estado e configuramos os objetos de gerenciamento de estado (criados acima) para nossa conversa.

**bots/customPromptBot.js**  
[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=23-29)]

Em seguida, definimos um segundo manipulador, `onDialog`, para executar após o manipulador de mensagens principal (explicado na próxima seção). Esse segundo manipulador garante que o nosso estado seja salvo em cada turno.

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=41-48)]

## <a name="pythontabpython"></a>[Python](#tab/python)

No construtor, criamos os acessadores de propriedade de estado e configuramos os objetos de gerenciamento de estado (criados acima) para nossa conversa.

**bots/custom_prompt_bot.py** [!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=40-44)]

Em seguida, salve os dados usando o método `save_changes()`.
[!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=53-55)]

---

## <a name="the-bots-message-turn-handler"></a>O manipulador de turno de mensagens do bot

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para lidar com atividades de mensagem, usamos o método auxiliar _FillOutUserProfileAsync()_ , antes de salvar o estado usando _SaveChangesAsync()_ . Veja a seguir o código completo.

**Bots/CustomPromptBot.cs**  
[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-44)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para lidar com atividades de mensagem, configuramos os dados da conversa e do usuário e, em seguida, usamos o método auxiliar `fillOutUserProfile()`. Veja aqui o código completo da manipulador de turno.

**bots/customPromptBot.js**  
[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=31-39)]

## <a name="pythontabpython"></a>[Python](#tab/python)
Para lidar com atividades de mensagem, configuramos os dados da conversa e do usuário e, em seguida, usamos o método auxiliar `_fill_out_user_profile`. Veja aqui o código completo da manipulador de turno.

**bots/custom_prompt_bot.py** [!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=46-55)]

---

## <a name="filling-out-the-user-profile"></a>Preenchendo o perfil do usuário

Começaremos com a coleta de informações. Cada uma fornecerá uma interface semelhante.

- O valor retornado indica se a entrada é uma resposta válida para a pergunta.
- Se a validação é bem-sucedida, produz um valor analisado e normalizado para salvar.
- Se a validação falha, produz uma mensagem com a qual o bot pode solicitar as informações novamente.

 Na próxima seção, definiremos os métodos auxiliares para analisar e validar a entrada do usuário.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots/CustomPromptBot.cs**  
[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=46-103)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.js**  
[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=52-116)]

## <a name="pythontabpython"></a>[Python](#tab/python)

**bots/custom_prompt_bot.py** [!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=57-126)]

---

## <a name="parse-and-validate-input"></a>Analisar e validar a entrada

Usaremos os critérios a seguir para validar a entrada.

- O **nome** deve ser uma cadeia de caracteres preenchida. Iremos normalizar eliminando o espaço em branco.
- A **idade** deve ser entre 18 e 120. Iremos normalizar retornando um número inteiro.
- A **data** deve ser qualquer data ou hora pelo menos uma hora no futuro.
  Iremos normalizar retornando apenas a parte da data da entrada analisada.

> [!NOTE]
> Para as entradas da idade e da data, podemos usar as bibliotecas [Reconhecedores de Texto/Microsoft](https://github.com/Microsoft/Recognizers-Text/) para fazer a análise inicial.
> Embora forneçamos o código de exemplo, não explicamos como funcionam as bibliotecas de reconhecedores de texto; é apenas uma maneira de analisar a entrada.
> Para obter mais informações sobre essas bibliotecas, confira o arquivo **LEIAME** do repositório.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione os seguintes métodos de validação ao bot.

**Bots/CustomPromptBot.cs**  
[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=105-203)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.cs**  
[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=118-189)]

## <a name="pythontabpython"></a>[Python](#tab/python)

**bots/custom_prompt_bot.py** [!code-python[custom prompt bot](~/../botbuilder-python/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=127-189)]
---

## <a name="test-the-bot-locally"></a>Testar o bot localmente
Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) para testar o bot localmente.

1. Execute o exemplo localmente em seu computador. Se precisar de instruções, consulte o arquivo LEIAME do [exemplo de C#](https://aka.ms/cs-primitive-prompt-sample), do [exemplo de JS](https://aka.ms/js-primitive-prompt-sample) ou do [exemplo de Python](https://aka.ms/python-primitive-prompt-sample).
1. Para testá-lo, use o emulador, conforme mostrado abaixo.

![primitive-prompts](media/primitive-prompts.png)

## <a name="additional-resources"></a>Recursos adicionais

A [Biblioteca de caixas de diálogo](bot-builder-concept-dialog.md) fornece classes que automatizam muitos aspectos do gerenciamento das conversas. 

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Implementar o fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md)
