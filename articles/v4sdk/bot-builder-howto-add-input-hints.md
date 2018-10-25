---
title: Adicionar dicas de entrada a mensagens | Microsoft Docs
description: Saiba como adicionar dicas de entrada a mensagens usando o SDK do Construtor de Bot.
keywords: Dicas de entrada, aceitação da entrada, espera por uma entrada, ignorar entrada, fala
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 08/24/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d55accd5ad9ad7db12d0b0e6865e04dcf7718110
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49996734"
---
# <a name="add-input-hints-to-messages"></a>Adicionar dicas de entrada a mensagens

[!INCLUDE [pre-release-label](~/includes/pre-release-label.md)]

Ao especificar uma dica de entrada para uma mensagem, você pode indicar se seu bot está aceitando, esperando ou ignorando a entrada do usuário após a entrega da mensagem ao cliente. Para muitos canais, isso permite que os clientes definam o estado dos controles de entrada do usuário de acordo. Por exemplo, se a dica de entrada da mensagem indicar que o bot está ignorando a entrada do usuário, o cliente poderá fechar o microfone e desabilitar a caixa de entrada para impedir que o usuário forneça uma entrada.

Certifique-se de que as bibliotecas necessárias estão incluídas para dicas de entrada.

# <a name="ctabcs"></a>[C#](#tab/cs)

```cs
using Microsoft.Bot.Schema;
```

<!--TODO: Remove the following remark after the next release of the NuGet packages.-->

A classe **MessageFactory**, usada nesses exemplos, é definida no seguinte namespace.

```cs
using Microsoft.Bot.Builder.Core.Extensions;
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const {InputHints, MessageFactory} = require('botbuilder');
```

---

## <a name="accepting-input"></a>Aceitar a entrada

Para indicar que seu bot está passivamente pronto para entrada mas não está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como _accepting input_. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja fechado, mas ainda acessível ao usuário. Por exemplo, a Cortana abrirá o microfone para aceitar a entrada do usuário se o usuário mantiver pressionado o botão de microfone. O código a seguir cria uma mensagem que indica que o bot está aceitando entrada do usuário.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.AcceptingInput);
await context.SendActivityAsync(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.AcceptingInput);
await context.sendActivity(basicMessage);
```

---

## <a name="expecting-input"></a>Esperando uma entrada

Para indicar que seu bot está aguardando uma resposta do usuário, defina a dica de entrada da mensagem como _expecting input_. Em vários canais, isso fará com que a caixa de entrada do cliente seja habilitada e o microfone seja aberto. O código a seguir cria um exemplo que indica que o bot está esperando a entrada do usuário.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.ExpectingInput);
await context.SendActivityAsync(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.ExpectingInput);
await context.sendActivity(basicMessage);
```

---

## <a name="ignoring-input"></a>Ignorando a entrada

Para indicar que seu bot não está pronto para receber a entrada do usuário, defina a dica de entrada da mensagem como _ignoring input_. Em vários canais, isso fará com que a caixa de entrada do cliente seja desabilitada e o microfone seja fechado. O código a seguir cria um exemplo que indica que o bot está ignorando a entrada do usuário.

# <a name="ctabcs"></a>[C#](#tab/cs)

```csharp
var reply = MessageFactory.Text(
    "This is the text that will be displayed.",
    "This is the text that will be spoken.",
    InputHints.IgnoringInput);
await context.SendActivityAsync(reply).ConfigureAwait(false);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```javascript
const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.', InputHints.IgnoringInput);
await context.sendActivity(basicMessage);
```

---

## <a name="default-values-for-input-hint"></a>Valores padrão para a dica de entrada

Se você não definir a dica de entrada para uma mensagem, o SDK do Construtor de Bot definirá automaticamente para você usando essa lógica:

- Se seu bot envia um prompt, a dica de entrada para a mensagem especificará que seu bot está **esperando entrada**.</li>
- Se seu bot envia uma única mensagem, a dica de entrada para a mensagem especificará que seu bot está **aceitando a entrada**.</li>
- Se seu bot envia uma série de mensagens consecutivas, a dica de entrada para todos, mas a mensagem final na série especificará que seu bot está **ignorando a entrada**, e a dica de entrada para a mensagem final na série especificará que seu bot está **aceitando a entrada**.

