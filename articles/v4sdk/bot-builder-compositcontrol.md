---
title: Gerenciar a complexidade da caixa de diálogo | Microsoft Docs
description: Saiba como modularizar a complexidade da caixa de diálogo usando caixas de diálogo de componente no SDK do Bot Framework.
keywords: controle composto, lógica de bot modular
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/14/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e397d2a4da5240512e6ad57a446daf8dadfe21d7
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417186"
---
# <a name="manage-dialog-complexity"></a>Gerenciar a complexidade da caixa de diálogo

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

<!--TODO This sample doesn't really need a separate LG tab. We need to decide whether we'll use the LG updated sample or the old sample.-->

Com os diálogos de componente, é possível criar diálogos independentes para lidar com cenários específicos, como dividir um conjunto de diálogos grande em partes mais gerenciáveis. Cada uma dessas partes tem seu próprio conjunto de diálogos e evita qualquer conflito de nome com o conjunto de diálogos que as contém. As caixas de diálogo de componente são reutilizáveis, pois podem ser:

- Adicionadas a outro `ComponentDialog` ou `DialogSet` em seu bot.
- Exportadas como parte de um pacote.
- Usadas em outros bots.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [biblioteca de diálogos][concept-dialogs] e como [gerenciar conversas][simple-flow].
- Uma cópia do exemplo de prompt de vários turnos em [**C#** ][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-the-sample"></a>Sobre o exemplo

No exemplo de prompt de vários turnos, usamos um diálogo em cascata, alguns prompts e um diálogo de componente para criar uma interação simples que faz uma série de perguntas ao usuário. O código usa um diálogo para percorrer estas etapas:

| Etapas        | Tipo de prompt  |
|:-------------|:-------------|
| Perguntar ao usuário qual é seu modo de transporte | Prompt de escolha |
| Perguntar o nome do usuário | Prompt de texto |
| Perguntar se o usuário deseja fornecer a idade | Prompt de confirmação |
| Se a resposta for Sim, perguntar a idade dele  | Prompt de número com validação para aceitar somente idades entre 0 e 150. |
| Perguntar se as informações coletadas estão “OK” | Reutilizar prompt de confirmação |

Finalmente, se ele responder sim, exibir as informações coletadas; caso contrário, dizer ao usuário que as informações dele não serão mantidas.

## <a name="implement-the-component-dialog"></a>Implementar o diálogo de componente

No exemplo de prompt de vários turnos, usamos um _diálogo em cascata_, alguns _prompts_ e um _diálogo de componente_ para criar uma interação simples que faz uma série de perguntas ao usuário.

Um diálogo de componente encapsula um ou mais diálogos. O diálogo de componente tem um conjunto de diálogos interno e os diálogos e prompts que você adiciona a esse conjunto têm IDs próprias, visíveis somente no diálogo de componente.

# <a name="c"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo, instale o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

**Dialogs\UserProfileDialog.cs**

Aqui, a classe `UserProfileDialog` é derivada da classe `ComponentDialog`.

[!code-csharp[Class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=16)]

No construtor, o método `AddDialog` adiciona diálogos e prompts ao diálogo de componente. O primeiro item que você adicionar com esse método será definido como o diálogo inicial, mas é possível alterar isso definindo explicitamente a propriedade `InitialDialogId`. Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_.

[!code-csharp[Constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=20-47)]

Esta é a implementação da primeira etapa do diálogo em cascata.

[!code-csharp[First step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=61-66)]

Para obter mais informações sobre como implementar diálogos em cascata, confira como [implementar fluxo da conversa sequencial](bot-builder-dialog-manage-complex-conversation-flow.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar as caixas de diálogo, seu projeto precisa instalar o pacote do npm, **botbuilder-dialogs**.

**dialogs/userProfileDialog.js**

Aqui, a classe `UserProfileDialog` estende `ComponentDialog`.

[!code-javascript[Class](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=28)]

No construtor, o método `AddDialog` adiciona diálogos e prompts ao diálogo de componente. O primeiro item que você adicionar com esse método será definido como o diálogo inicial, mas é possível alterar isso definindo explicitamente a propriedade `InitialDialogId`. Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_.

[!code-javascript[Constructor](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=29-51)]

Esta é a implementação da primeira etapa do diálogo em cascata.

[!code-javascript[First step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=70-77)]

Para obter mais informações sobre como implementar diálogos em cascata, confira como [implementar fluxo da conversa sequencial](bot-builder-dialog-manage-complex-conversation-flow.md).

# <a name="python"></a>[Python](#tab/python)

Para usar diálogos, instale os pacotes PyPI **botbuilder-dialogs** e **botbuilder-ai** executando `pip install botbuilder-dialogs` e `pip install botbuilder-ai` em um terminal.

**dialogs/user_profile_dialog.py**

Aqui, a classe `UserProfileDialog` estende `ComponentDialog`.

[!code-python[Class](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=25)]

No construtor, o método `add_dialog` adiciona diálogos e prompts ao diálogo de componente. O primeiro item que você adicionar com esse método será definido como o diálogo inicial, mas é possível alterar isso definindo explicitamente a propriedade `initial_dialog_id`. Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_.

[!code-python[Constructor](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=25-57)]

Esta é a implementação da primeira etapa do diálogo em cascata.

[!code-python[First step](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=59-71)]

Para obter mais informações sobre como implementar diálogos em cascata, confira como [implementar fluxo da conversa sequencial](bot-builder-dialog-manage-complex-conversation-flow.md).

---

Em tempo de execução, o diálogo de componente mantém sua própria pilha de diálogo. Quando o diálogo de componente é iniciado:

- Uma instância é criada e adicionada à pilha de diálogo externa.
- Ele cria uma pilha de diálogo interna que adiciona ao próprio estado.
- Ele começa seu diálogo inicial e adiciona-o à pilha de diálogo interna.

Do contexto pai, ele terá a aparência do componente que for o diálogo ativo. De dentro do componente, ele terá a aparência do diálogo inicial que for o diálogo ativo.

### <a name="implement-the-rest-of-the-dialog-and-add-it-to-the-bot"></a>Implemente o restante do diálogo e adicione-o ao bot.

No conjunto de diálogo externo, aquele ao qual você adiciona o diálogo de componente, o diálogo de componente tem a ID que você criou com ele. No conjunto externo, o componente tem a aparência de um único diálogo, muito semelhante aos prompts.

Para usar um diálogo de componente, adicione uma instância dele ao conjunto de diálogo do bot, que é o conjunto de diálogo externo.

# <a name="c"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

No exemplo, isso é feito usando o método `RunAsync` chamado do método `OnMessageActivityAsync` do bot.

[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=42-48&highlight=6)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/userProfileDialog.js**

No exemplo, adicionamos um método `run` ao diálogo de perfil do usuário.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=59-68)]

**bots/dialogBot.js**

O método `run` é chamado pelo método `onMessage` do bot.

[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=24-31&highlight=5)]

# <a name="python"></a>[Python](#tab/python)

**helpers/dialog_helper.py**

No exemplo, adicionamos um método `run_dialog` ao diálogo de perfil do usuário.

[!code-python[DialogHelper.run_dialog](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/helpers/dialog_helper.py?range=8-19)]

O método `run_dialog` é chamado pelo método `on_message_activity` do bot.

**bots/dialog_bot.py** [!code-python[om_message_activity](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/bots/dialog_bot.py?range=46-51&highlight=2-6)]

---

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

![Execução de exemplo do diálogo de prompt de vários turnos](../media/emulator-v4/multi-turn-prompt.png)

## <a name="additional-information"></a>Informações adicionais

### <a name="how-cancellation-works-for-component-dialogs"></a>Como funciona o cancelamento nos diálogos de componente

Se você chamar _cancel all dialogs_ pelo contexto do diálogo de componente, esse diálogo cancelará todos os diálogo de sua pilha interna e, em seguida, encerrará, devolvendo o controle ao próximo diálogo na pilha externa.

Se você chamar _cancel all dialogs_ pelo contexto externo, o componente será cancelado, bem como os demais diálogos no contexto externo.

Lembre-se disso ao gerenciar diálogos de componente aninhado em seu bot.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar conversas complexas que se ramificam e entram em loop.

> [!div class="nextstepaction"]
> [Manipular interrupções do usuário](bot-builder-dialog-manage-complex-conversation-flow.md)

<!-- Footnote-style links -->

<!--concepts-->
[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md
<!--how to-->
[simple-flow]: bot-builder-dialog-manage-conversation-flow.md
[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md
<!--samples-->
[cs-sample]: https://aka.ms/cs-multi-prompts-sample
[js-sample]: https://aka.ms/js-multi-prompts-sample
[python-sample]: https://aka.ms/python-multi-prompts-sample
[lg-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/language-generation/05.multi-turn-prompt
