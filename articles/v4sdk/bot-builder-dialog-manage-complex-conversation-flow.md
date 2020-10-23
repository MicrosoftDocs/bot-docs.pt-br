---
title: Criar fluxo de conversa avançado usando branches e loops – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa complexo com diálogos no SDK do Bot Framework.
keywords: fluxo de conversa complexo, repetição, loop, menu, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/26/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88262c740cfa1d339e4a19a7fa1fc1253a6ca46e
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417147"
---
# <a name="create-advanced-conversation-flow-using-branches-and-loops"></a>Criar fluxo de conversa avançado usando ramificações e loops

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Você pode criar fluxos de conversa complexos usando a biblioteca de diálogos.
Este artigo aborda como gerenciar conversas complexas com ramificações e loop e como passar argumentos entre diferentes partes do diálogo.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre [noções básicas de bots][concept-basics], [gerenciamento de estado][concept-state], a [biblioteca de diálogos][concept-dialogs] e como [implementar um fluxo de conversa sequencial][simple-dialog].
- Uma cópia do exemplo de diálogo complexo em [**C#** ][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo representa um bot que pode inscrever usuários para avaliação de até duas empresas de uma lista.
O bot usa três diálogos componentes para gerenciar o fluxo de conversa.
Cada diálogo componente inclui uma caixa de diálogo de cascata e os prompts necessários para coletar a entrada do usuário.
Esses diálogos são descritos mais detalhadamente nas seções a seguir.
Ele usa o estado da conversa para gerenciar os diálogos e usa o estado do usuário para salvar informações sobre o usuário e quais empresas eles desejam avaliar.

O bot deriva do manipulador de atividade. Como muitos dos bots de exemplo, ele dá boas vindas ao usuário, usa caixas de diálogo para manipular mensagens do usuário e salva o estado do usuário e da conversa antes do término da conversa.

### <a name="c"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo, instale o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

![Fluxo de bot complexo](./media/complex-conversation-flow.png)

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar as caixas de diálogo, seu projeto precisa instalar o pacote do npm, **botbuilder-dialogs**.

![Fluxo de bot complexo](./media/complex-conversation-flow-js.png)

### <a name="python"></a>[Python](#tab/python)

Para usar diálogos, seu projeto precisa instalar o pacote PyPI **botbuilder-dialogs** executando `pip install botbuilder-dialogs`.

![Fluxo de bot complexo](./media/complex-conversation-flow-python.png)

---

## <a name="define-the-user-profile"></a>Definir o perfil do usuário

O perfil do usuário conterá informações coletadas pelos diálogos: o nome do usuário, a idade e as empresas selecionadas para avaliação.

### <a name="c"></a>[C#](#tab/csharp)

**UserProfile.cs**

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/UserProfile.cs?range=8-16)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**userProfile.js**

[!code-javascript[UserProfile class](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/userProfile.js?range=4-12)]

### <a name="python"></a>[Python](#tab/python)

**data_models/user_profile.py**

[!code-python[UserProfile class](~/../botbuilder-samples/samples/python/43.complex-dialog/data_models/user_profile.py?range=7-13)]

---

## <a name="create-the-dialogs"></a>Criar os diálogos

Este bot contém três diálogos:

- O diálogo principal inicia o processo geral e, em seguida, resume as informações coletadas.
- O diálogo de nível superior coleta as informações do usuário e inclui a lógica de ramificação, com base na idade do usuário.
- O diálogo de seleção da avaliação permite que o usuário selecione iterativamente as empresas a serem avaliadas. Ele usa lógica de loop para fazer isso.

### <a name="the-main-dialog"></a>O diálogo principal

O diálogo principal tem duas etapas:

1. Iniciar o diálogo de nível superior.
1. Recuperar e resumir o perfil do usuário coletado pelo diálogo de nível superior, salvar essas informações no estado do usuário e, finalmente, sinalizar o final do diálogo principal.

#### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\MainDialog.cs**

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/MainDialog.cs?range=31-50)]

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/mainDialog.js?range=43-55)]

#### <a name="python"></a>[Python](#tab/python)

**dialogs\main_dialog.py**

[!code-python[step implementations](~/../botbuilder-samples/samples/python/43.complex-dialog/dialogs/main_dialog.py?range=29-50)]

---

### <a name="the-top-level-dialog"></a>O diálogo de nível superior

O diálogo de nível superior tem quatro etapas:

1. Perguntar o nome do usuário.
1. Perguntar a idade do usuário.
1. Iniciar o diálogo de seleção da avaliação ou prosseguir para a próxima etapa, com base na idade do usuário.
1. Por fim, agradecer a participação do usuário e retornar as informações coletadas.

A primeira etapa cria um perfil do usuário vazio como parte do estado do diálogo. O diálogo começa com um perfil vazio e adiciona informações ao perfil conforme progride. Quando termina, a última etapa retorna as informações coletadas.

Na terceira etapa (iniciar seleção), o fluxo de conversa se ramifica, com base na idade do usuário.

#### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\TopLevelDialog.cs**

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/TopLevelDialog.cs?range=39-96&highlight=30-42)]

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/topLevelDialog.js**

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/topLevelDialog.js?range=32-76&highlight=25-33)]

#### <a name="python"></a>[Python](#tab/python)

**dialogs\top_level_dialog.py**

[!code-python[step implementations](~/../botbuilder-samples/samples/python/43.complex-dialog/dialogs/top_level_dialog.py?range=43-95&highlight=29-38)]

---

### <a name="the-review-selection-dialog"></a>O diálogo de seleção da avaliação

O diálogo de seleção da avaliação tem duas etapas:

1. Pedir que o usuário escolha uma empresa para avaliação ou `done` para concluir.
   - Se o diálogo tiver sido iniciado com informações iniciais, as informações estarão disponíveis por meio da propriedade _options_ do contexto da etapa de cascata. O diálogo de seleção da avaliação pode ser reiniciado e usa essa possibilidade para permitir que o usuário escolha mais de uma empresa para avaliar.
   - Se o usuário já tiver selecionado uma empresa para avaliar, essa empresa será removida das opções disponíveis.
   - Uma opção `done` é adicionada para permitir que o usuário saia do loop com antecedência.
1. Repetir esse diálogo ou sair, conforme apropriado.
   - Se o usuário escolher uma empresa para avaliar, adicione-a à lista.
   - Se o usuário tiver escolhido duas empresas ou tiver optado por sair, encerre o diálogo e retorne a lista coletada.
   - Caso contrário, reinicie o diálogo, inicializando-o com o conteúdo de sua lista.

#### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\ReviewSelectionDialog.cs**

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/ReviewSelectionDialog.cs?range=42-106&highlight=55-64)]

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/reviewSelectionDialog.js**

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/reviewSelectionDialog.js?range=33-78&highlight=39-45)]

#### <a name="python"></a>[Python](#tab/python)

**dialogs/review_selection_dialog.py**

[!code-python[step implementations](~/../botbuilder-samples/samples/python/43.complex-dialog/dialogs/review_selection_dialog.py?range=42-99&highlight=51-58)]

---

## <a name="run-the-dialogs"></a>Executar os diálogos

A classe _dialog bot_ estende o manipulador de atividade e contém a lógica para executar os diálogos.
A classe _dialog and welcome bot_ estende o bot de diálogo para também dar boas-vindas a um usuário quando ele ingressar na conversa.

O manipulador de turnos do bot repete o fluxo de conversa definido por esses três diálogos.
Quando ele recebe uma mensagem do usuário:

1. Ele executa o diálogo principal.
   - Se a pilha de diálogo estiver vazia, será iniciado o diálogo principal.
   - Caso contrário, os diálogos ainda estarão no meio do processo e isso dará continuidade no diálogo ativo.
1. Isso salvará o estado, para que as atualizações do usuário, da conversa e do diálogo sejam mantidas.

### <a name="c"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

[!code-csharp[Overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Bots/DialogBot.cs?range=33-48&highlight=5-7,14-15)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/dialogBot.js**

[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogBot.js?range=24-32&highlight=4-5)]
[!code-javascript[run](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogBot.js?range=35-44&highlight=7-9)]

### <a name="python"></a>[Python](#tab/python)

**bots/dialog_bot.py**

[!code-python[Overrides](~/../botbuilder-samples/samples/python/43.complex-dialog/bots/dialog_bot.py?range=29-41&highlight=4-6,9-13)]

---

## <a name="register-services-for-the-bot"></a>Registrar serviços para o bot

Crie e registre serviços conforme necessário:

- Serviços básicos para bot: um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.
- O diálogo raiz que o bot usará.

### <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Startup.cs?range=16-35)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[ConfigureServices](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/index.js?range=26-43)]

### <a name="python"></a>[Python](#tab/python)

**app.py**

[!code-python[ConfigureServices](~/../botbuilder-samples/samples/python/43.complex-dialog/app.py?range=30-33)]

[!code-python[ConfigureServices](~/../botbuilder-samples/samples/python/43.complex-dialog/app.py?range=71-78)]

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

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
[python-sample]: https://aka.ms/python-complex-dialog-sample
