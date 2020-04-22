---
title: Crie seus próprios prompts para coletar entradas do usuário – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com solicitações primitivas no SDK do Bot Framework.
keywords: fluxo da conversa, prompts, estado da conversa, estado do usuário, prompts personalizados
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/7/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 71dfb4fc5f74634ad49b72608326a27c3aa4a620
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80648204"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Crie seus próprio prompts para coletar entradas do usuário

[!INCLUDE[applies-to](../includes/applies-to.md)]

Uma conversa entre um bot e um usuário muitas vezes envolve solicitar ao usuário algumas informações, analisar as respostas e atuar com base nessas informações. Seu bot deve controlar o contexto de uma conversa, para que ele possa gerenciar seu comportamento e lembrar-se das respostas às perguntas anteriores. Um *estado* do bot é uma informação que o bot controla para responder apropriadamente às mensagens recebidas.

> [!TIP]
> A biblioteca de diálogos fornece prompts internos que oferecem mais funcionalidade do que os usuários podem usar. Exemplos desses prompts podem ser encontrados no artigo [Implementar fluxo de conversa sequencial](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

- O código neste artigo baseia-se no exemplo de Solicitar Entrada aos Usuários. Você precisará de uma cópia do **[exemplo de C#](https://aka.ms/cs-primitive-prompt-sample), do [exemplo de JavaScript](https://aka.ms/js-primitive-prompt-sample) ou do [exemplo de Python](https://aka.ms/python-primitive-prompt-sample)** .
- Conhecimento sobre [gerenciar estado](bot-builder-concept-state.md) e como [salvar dados do usuário e da conversa](bot-builder-howto-v4-state.md).

## <a name="about-the-sample-code"></a>Sobre o código de exemplo

O exemplo de bot faz uma série de perguntas ao usuário, valida algumas das respostas dele e salva as entrada que ele faz. O diagrama a seguir mostra a relação entre o bot, o perfil do usuário e as classes de fluxo da conversa.

## <a name="c"></a>[C#](#tab/csharp)

![custom-prompts](media/CustomPromptBotSample-Overview.png)

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `ConversationFlow.Question` interna para controlar onde estamos na conversa.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

![custom-prompts](media/CustomPromptBotSample-JS-Overview.png)

- Uma classe `userProfile` para as informações do usuário que o bot coletará.
- Uma classe `conversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `conversationFlow.question` interna para controlar onde estamos na conversa.

## <a name="python"></a>[Python](#tab/python)

![custom-prompts](media/CustomPromptBotSample-Python-Overview.png)

- Uma classe `UserProfile` para as informações do usuário que o bot coletará.
- Uma classe `ConversationFlow` para controlar o estado da nossa conversa durante a coleta de informações do usuário.
- Uma enumeração `ConversationFlow.Question` interna para controlar onde estamos na conversa.

---

O estado do usuário controlará nome do usuário, idade e data escolhida. O estado da conversa controlará o que acabamos de solicitar ao usuário.
Como não planejamos implantar esse bot, configuraremos os estados do usuário e da conversa para usar o _armazenamento de memória_.

Usamos o manipulador de turno de mensagem do bot e as propriedades do estado da conversa para gerenciar o fluxo da conversa e a coleta de entradas. Em nosso bot, registraremos as informações da propriedade do estado recebidas durante cada iteração do manipulador de turno de mensagem.

## <a name="create-conversation-and-user-objects"></a>Criar objetos da conversa e do usuário

## <a name="c"></a>[C#](#tab/csharp)

Crie o usuário e os objetos de estado da conversa na inicialização e consuma-os por meio da injeção de dependência no construtor de bot.

**Startup.cs** [!code-csharp[Startup.cs](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Startup.cs?range=23-30)]

**Bots/CustomPromptBot.cs** [!code-csharp[constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=21-28)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Crie o usuário e os objetos de estado da conversa em **index.js** e consuma-os no construtor de bot.

**index.js**  
[!code-javascript[index.js](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/index.js?range=33-39)]

**bots/customPromptBot.js**  
[!code-javascript[constructor](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=20-22)]
[!code-javascript[constructor](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=27-29)]

## <a name="python"></a>[Python](#tab/python)

Crie o usuário e os objetos de estado da conversa em **app.py** e consuma-os no construtor de bot.

**app.py** [!code-python[app.py](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/app.py?range=67-73)]

**bots/custom_prompt_bot.py** [!code-python[constructor](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=29-41)]

---

## <a name="create-property-accessors"></a>Criar acessadores de propriedade

## <a name="c"></a>[C#](#tab/csharp)

Crie acessadores de propriedade para as propriedades de perfil de usuário e de fluxo de conversa e, em seguida, chame `GetAsync` para recuperar o valor da propriedade do estado.

**Bots/CustomPromptBot.cs**  
[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-37)]

Antes do fim da rodada, chame `SaveChangesAsync` para gravar qualquer alteração de estado no armazenamento.

[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=41-44)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Crie acessadores de propriedade para as propriedades de perfil de usuário e de fluxo de conversa e, em seguida, chame `get` para recuperar o valor da propriedade do estado.

**bots/customPromptBot.js**  
[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=31-33)]

Antes do fim da rodada, chame `saveChanges` para gravar qualquer alteração de estado no armazenamento.

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=42-51)]

## <a name="python"></a>[Python](#tab/python)

No construtor, criamos os acessadores de propriedade de estado e configuramos os objetos de gerenciamento de estado (criados acima) para nossa conversa.

**bots/custom_prompt_bot.py** [!code-python[on_message_activity](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=46-49)]

Antes do fim da rodada, chame `SaveChangesAsync` para gravar qualquer alteração de estado no armazenamento.

[!code-python[on_message_activity](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=53-55)]

---

## <a name="the-bots-message-turn-handler"></a>O manipulador de turno de mensagens do bot

Ao lidar com atividades de mensagens, o manipulador de mensagens usa um método auxiliar para gerenciar a conversa e fazer solicitações ao usuário. O método auxiliar é descrito na seção a seguir.

## <a name="c"></a>[C#](#tab/csharp)

**Bots/CustomPromptBot.cs** [!code-csharp[message handler](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-44)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.js** [!code-javascript[message handler](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=31-39)]

## <a name="python"></a>[Python](#tab/python)

**bots/custom_prompt_bot.py** [!code-python[message handler](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=46-55)]

---

## <a name="filling-out-the-user-profile"></a>Preenchendo o perfil do usuário

O bot solicita ao usuário informações, com base na pergunta, se houver, que o bot fez na rodada anterior. A entrada é analisada por meio de um método de validação.

Cada método de validação segue um design semelhante:

- O valor retornado indica se a entrada é uma resposta válida para a pergunta.
- Se a validação é bem-sucedida, produz um valor analisado e normalizado para salvar.
- Se a validação falha, produz uma mensagem com a qual o bot pode solicitar as informações novamente.

Os métodos de validação são descritos na seção a seguir.

## <a name="c"></a>[C#](#tab/csharp)

**Bots/CustomPromptBot.cs** [!code-csharp[FillOutUserProfileAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=46-103)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.js** [!code-javascript[fillOutUserProfile](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=53-118)]

## <a name="python"></a>[Python](#tab/python)

**bots/custom_prompt_bot.py** [!code-python[_fill_out_user_profile](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=57-125)]

---

## <a name="parse-and-validate-input"></a>Analisar e validar a entrada

O bot usa os critérios a seguir para validar a entrada.

- O **nome** deve ser uma cadeia de caracteres preenchida. É normalizado eliminando o espaço em branco.
- A **idade** deve ser entre 18 e 120. É normalizado retornando um número inteiro.
- A **data** deve ser qualquer data ou hora pelo menos uma hora no futuro.
  É normalizado retornando apenas a parte da data da entrada analisada.

> [!NOTE]
> Para as entradas da idade e da data, podemos usar as bibliotecas [Reconhecedores de Texto/Microsoft](https://github.com/Microsoft/Recognizers-Text/) para fazer a análise inicial.
> Embora forneçamos o código de exemplo, não explicamos como funcionam as bibliotecas de reconhecedores de texto; é apenas uma maneira de analisar a entrada.
> Para obter mais informações sobre essas bibliotecas, confira o arquivo **LEIAME** do repositório.

## <a name="c"></a>[C#](#tab/csharp)

**Bots/CustomPromptBot.cs** [!code-csharp[validation methods](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=105-203)]

## <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.cs** [!code-javascript[validation methods](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=120-190)]

## <a name="python"></a>[Python](#tab/python)

**bots/custom_prompt_bot.py** [!code-python[validation methods](~/../botbuilder-samples/samples/python/44.prompt-for-user-input/bots/custom_prompt_bot.py?range=127-189)]

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
