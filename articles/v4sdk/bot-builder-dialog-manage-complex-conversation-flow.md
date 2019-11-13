---
title: Criar fluxo de conversa avançado usando branches e loops | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa complexo com diálogos no SDK do Bot Framework.
keywords: fluxo de conversa complexo, repetição, loop, menu, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ec56a36feb747160e1a82f9831aa323074d46801
ms.sourcegitcommit: 312a4593177840433dfee405335100ce59aac347
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933620"
---
# <a name="create-advanced-conversation-flow-using-branches-and-loops"></a>Criar fluxo de conversa avançado usando ramificações e loops

[!INCLUDE[applies-to](../includes/applies-to.md)]

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de caixas de diálogo.
Neste artigo, mostraremos como gerenciar conversas complexas que geram ramificações e loops.
Também mostraremos como passar argumentos entre partes diferentes do diálogo.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre [noções básicas de bots][concept-basics], [gerenciamento de estado][concept-state], a [biblioteca de diálogos][concept-dialogs] e como [implementar um fluxo de conversa sequencial][simple-dialog].
- Uma cópia da caixa de diálogo de exemplo em [**C#** ][cs-sample] ou [**JavaScript**][js-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo representa um bot que pode inscrever usuários para avaliação de até duas empresas de uma lista.

`DialogAndWelcomeBot` estende `DialogBot`, que define os manipuladores para diferentes atividades e o manipulador de turnos do bot. `DialogBot` executa as caixas de diálogo:

- O método de _execução_ é usado por `DialogBot` para iniciar a caixa de diálogo.
- `MainDialog` é o pai das outras duas caixas de diálogo, que são chamadas em determinados momentos nas caixas de diálogo. Detalhes sobre essas caixas de diálogo são fornecidos no decorrer deste artigo.

As caixas de diálogo são divididas em `MainDialog`, `TopLevelDialog`, e `ReviewSelectionDialog` caixas de diálogo de componente, que juntas têm a seguinte função:

- Pedir o nome e a idade do usuário e depois criar _ramificações_ com base na idade do usuário.
  - Se o usuário for muito novo, elas não pedem que o usuário avalie as empresas.
  - Se o usuário tiver idade suficiente, elas começam a coletar as preferências de avaliação do usuário.
    - Elas permitem que o usuário selecione uma empresa para avaliação.
    - Se o usuário escolhe uma empresa, elas executam um _loop_ para permitir que outra empresa seja selecionada.
- Por fim, elas agradecem a participação do usuário.

Elas usam caixas de diálogo em cascata e alguns prompts para gerenciar conversas complexas.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

![Fluxo de bot complexo](./media/complex-conversation-flow.png)

Para usar as caixas de diálogo, seu projeto precisa instalar o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

**Startup.cs**

Nós registramos serviços para o bot em `Startup`. Esses serviços estão disponíveis para outros blocos do código por meio da injeção de dependência.

- Serviços básicos para bot: um provedor de credenciais, um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.
- A caixa de diálogo que o bot usará.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Startup.cs?range=22-36)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![Fluxo de bot complexo](./media/complex-conversation-flow-js.png)

Para usar as caixas de diálogo, seu projeto precisa instalar o pacote do npm, **botbuilder-dialogs**.

**index.js**

Nós criamos serviços para bot, que os outros blocos do código precisam.

- Serviços básicos para bot: um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.
- A caixa de diálogo que o bot usará.

[!code-javascript[ConfigureServices](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/index.js?range=26-65)]

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

## <a name="define-a-class-in-which-to-store-the-collected-information"></a>Defina uma classe para armazenar as informações coletadas

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**UserProfile.cs**

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/UserProfile.cs?range=8-16)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**userProfile.js**

[!code-javascript[UserProfile class](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/userProfile.js?range=4-12)]

---

## <a name="create-the-dialogs-to-use"></a>Crie caixas de diálogo para usar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\MainDialog.cs**

Definimos uma caixa de diálogo de componente, `MainDialog`, que contém algumas etapas importantes e direciona as caixas de diálogo, além de gerar avisos. A etapa inicial chama `TopLevelDialog` que é explicado abaixo.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/MainDialog.cs?range=31-50&highlight=3)]

**Dialogs\TopLevelDialog.cs**

O diálogo inicial principal tem quatro etapas:

1. Perguntar o nome do usuário.
1. Perguntar a idade do usuário.
1. Ramificação com base na idade do usuário.
1. Por fim, agradecer a participação do usuário e retornar as informações coletadas.

Na primeira etapa, estamos limpando o perfil do usuário para que a caixa de diálogo comece com um perfil em branco a cada vez. Uma vez que a última etapa traz informações ao final, a `AcknowledgementStepAsync` conclui com o salvamento no estado do usuário, e depois retorna essas informações para a caixa de diálogo principal, para que sejam usadas na etapa final.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/TopLevelDialog.cs?range=39-96&highlight=3-4,47-49,56-57)]

**Dialogs\ReviewSelectionDialog.cs**

A caixa de diálogo de seleção de revisão é iniciada a partir da caixa de diálogo principal `StartSelectionStepAsync`, e tem duas etapas:

1. Pedir que o usuário escolha uma empresa para avaliação, ou escolha `done` para concluir.
1. Repetir esse diálogo ou sair, conforme apropriado.

Nesse design, a caixa de diálogo principal sempre precederá a caixa de diálogo de seleção da avaliação na pilha, e a caixa de diálogo de seleção da avaliação pode ser considerada filha da caixa de diálogo principal.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/ReviewSelectionDialog.cs?range=42-106)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Definimos uma caixa de diálogo de componente, `MainDialog`, que contém algumas etapas importantes e direciona as caixas de diálogo, além de gerar avisos. A etapa inicial chama `TopLevelDialog` que é explicado abaixo.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/mainDialog.js?range=43-55&highlight=2)]

**dialogs/topLevelDialog.js**

O diálogo inicial principal tem quatro etapas:

1. Perguntar o nome do usuário.
1. Perguntar a idade do usuário.
1. Ramificação com base na idade do usuário.
1. Por fim, agradecer a participação do usuário e retornar as informações coletadas.

Na primeira etapa, estamos limpando o perfil do usuário para que a caixa de diálogo comece com um perfil em branco a cada vez. Uma vez que a última etapa traz informações ao final, a `acknowledgementStep` conclui com o salvamento no estado do usuário, e depois retorna essas informações para a caixa de diálogo principal, para que sejam usadas na etapa final.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/topLevelDialog.js?range=32-76&highlight=2-3,37-39,43-44)]

**dialogs/reviewSelectionDialog.js**

A caixa de diálogo de seleção de revisão é iniciada a partir da caixa de diálogo principal `startSelectionStep`, e tem duas etapas:

1. Pedir que o usuário escolha uma empresa para avaliação, ou escolha `done` para concluir.
1. Repetir esse diálogo ou sair, conforme apropriado.

Nesse design, a caixa de diálogo principal sempre precederá a caixa de diálogo de seleção da avaliação na pilha, e a caixa de diálogo de seleção da avaliação pode ser considerada filha da caixa de diálogo principal.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/reviewSelectionDialog.js?range=33-78)]

---

## <a name="implement-the-code-to-manage-the-dialog"></a>Implante o código para gerenciar a caixa de diálogo

O manipulador de turnos do bot repete o fluxo de conversa definido por esses diálogos.
Quando recebemos uma mensagem do usuário:

1. Continue o diálogo ativo, se houver um.
   - Se não houver um diálogo ativo, limparemos o perfil do usuário e iniciaremos o diálogo principal.
   - Se o dialogo ativo for concluído, coletaremos e salvaremos as informações retornadas e exibiremos uma mensagem de status.
   - Caso contrário, o diálogo ativo ainda estará na metade do processo, e não precisaremos fazer mais nada no momento.
1. Salve o estado da conversa, para que as atualizações do estado do diálogo sejam mantidas.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

<!-- **DialogExtensions.cs**

In this sample, we've defined a `Run` helper method that we will use to create and access the dialog context.
Since component dialog defines an inner dialog set, we have to create an outer dialog set that's visible to the message handler code, and use that to create a dialog context.

- `dialog` is the main component dialog for the bot.
- `turnContext` is the current turn context for the bot.

[!code-csharp[Run method](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/DialogExtensions.cs?range=13-24)]

-->

**Bots\DialogBot.cs**

O manipulador de mensagens chama o método `RunAsync` para gerenciar a caixa de diálogo e nós substituímos o manipulador de turnos para salvar alterações na conversa e no estado do usuário que possam ter ocorrido durante o turno. A base `OnTurnAsync` chamará o método `OnMessageActivityAsync`, garantindo que as chamadas de salvamento aconteçam no final do turno.

[!code-csharp[Overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

**Bots\DialogAndWelcome.cs**

`DialogAndWelcomeBot` estende `DialogBot` acima para fornecer uma mensagem de boas-vindas quando o usuário ingressa na conversa e é o que é criado no `Startup.cs`.

[!code-csharp[On members added](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Bots/DialogAndWelcome.cs?range=21-38)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Neste exemplo, definimos um método `run` que usaremos para criar e acessar o contexto de caixa de diálogo.
Uma vez que a caixa de diálogo de componente define um conjunto interno de caixa de diálogo, precisamos criar um conjunto externo de caixa de diálogo que seja visível para o código do manipulador de mensagem, podendo usá-lo para criar um contexto de caixa de diálogo.

- `turnContext` é o contexto de turno atual para o bot.
- `accessor` é um acessador que criamos para gerenciar o estado da caixa de diálogo.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/mainDialog.js?range=32-41)]

**bots/dialogBot.js**

O manipulador de mensagens chama o método auxiliar `run` para gerenciar a caixa de diálogo, e nós implantamos o manipulador de turnos para salvar quaisquer alterações na conversa e no estado do usuário que possam ter ocorrido durante o turno. A chamada para `next` permitirá que a implantação da base chame o método`onDialog`, garantindo que as chamadas de salvamento aconteçam no final do turno.

[!code-javascript[Overrides](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogBot.js?range=24-41)]

**bots/dialogAndWelcomeBot.js**

`DialogAndWelcomeBot` estende `DialogBot` acima para fornecer uma mensagem de boas-vindas quando o usuário ingressa na conversa e é o que é criado no `index.js`.

[!code-javascript[On members added](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogAndWelcomeBot.js?range=10-21)]

---

## <a name="branch-and-loop"></a>Branch e loop

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\TopLevelDialog.cs**

Aqui está uma amostra de lógica de branch tirada de uma etapa da caixa de diálogo _principal_:

[!code-csharp[branching logic](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/TopLevelDialog.cs?range=68-80)]

**Dialogs\ReviewSelectionDialog.cs**

Aqui está uma amostra de lógica de looping tirada de uma etapa da caixa de diálogo _seleção de revisão_:

[!code-csharp[looping logic](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/ReviewSelectionDialog.cs?range=96-105)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/topLevelDialog.js**

Aqui está uma amostra de lógica de branch tirada de uma etapa da caixa de diálogo _principal_:

[!code-javascript[branching logic](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/topLevelDialog.js?range=56-64)]

**dialogs/reviewSelectionDialog.js**

Aqui está uma amostra de lógica de looping tirada de uma etapa da caixa de diálogo _seleção de revisão_:

[!code-javascript[looping logic](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/reviewSelectionDialog.js?range=71-77)]

---

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

![teste de exemplo de diálogo complexo](~/media/emulator-v4/test-complex-dialog.png)

## <a name="additional-resources"></a>Recursos adicionais

Para obter uma introdução sobre como implementar um diálogo, confira [implementar fluxo da conversa sequencial][simple-dialog], que usa um único diálogo em cascata e alguns prompts para criar uma interação simples que faz ao usuário uma série de perguntas.

A biblioteca Diálogos inclui uma validação básica de prompts. Você também pode adicionar uma validação personalizada. Para obter mais informações, confira [coletar entrada do usuário usando um prompt de diálogo][dialog-prompts].

Para simplificar o código do seu diálogo e reutilizá-lo em vários bots, defina as partes de um conjunto de diálogos como uma classe separada.
Para saber mais, confira [reutilizar diálogos][component-dialogs].

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reutilizar as caixas de diálogo](bot-builder-compositcontrol.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[simple-dialog]: bot-builder-dialog-manage-conversation-flow.md
[dialog-prompts]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-complex-dialog-sample
[js-sample]: https://aka.ms/js-complex-dialog-sample
