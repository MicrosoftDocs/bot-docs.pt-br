---
title: Criar um bot usando diálogos adaptáveis – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com diálogos adaptáveis no SDK do Bot Framework.
keywords: conversation flow, repeat, loop, menu, dialogs, prompts, adaptive, language generation
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/08/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a6f20d9d156e817c253bbca869fe23f88ac4e1eb
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073889"
---
# <a name="create-a-bot-using-adaptive-dialogs"></a>Criar um bot usando diálogos adaptáveis  

[!INCLUDE[applies-to](../includes/applies-to.md)]

Este artigo mostra como usar recursos de **Diálogo adaptável** e **Geração de Linguagem** para obter a mesma funcionalidade obtida com o modelo em cascata.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Uma cópia da amostra de **solicitação de vários turnos** na versão prévia em [**C#** ][cs-sample] ou [**JavaScript**][js-sample].

### <a name="preliminary-steps-to-add-an-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável a um bot

Você precisará seguir as etapas descritas abaixo para adicionar um diálogo adaptável a um bot.

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `Microsoft.Bot.Builder.Dialogs.Adaptive` ao seu projeto de bot.
1. Atualize o adaptador de bot para adicionar o armazenamento e os objetos de estado de conversa e usuário a cada contexto de turno.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.

## <a name="about-the-sample"></a>Sobre o exemplo

Esta amostra usa um diálogo adaptável, algumas solicitações e um diálogo de componente para criar uma interação simples que faz uma série de perguntas ao usuário. As perguntas são criadas por meio de modelos LG:

- Para o C#, definido em [RootDialog.lg](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.lg)
- Para JavaScript, definido em [userProfileDialog.lg](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.lg) (JavaScript).

O código usa um diálogo para percorrer estas etapas:

> [!div class="mx-tdCol2BreakAll"]
> | Etapas        | Modelo LG  |
> |:-------------|:-------------|
> | Perguntar ao usuário qual é seu modo de transporte | `ModeOfTransportPrompt` |
> | Perguntar o nome do usuário | `AskForName` |
> | Perguntar se o usuário deseja fornecer a idade | `AgeConfirmPrompt` |
> | Se a resposta for Sim, perguntar a idade dele | Solicitação de `AskForAge` com validação para aceitar somente idades maiores que 0 e menores que 150 |
> | Perguntar se as informações coletadas estão “OK” | Solicitação de `ConfirmPrompt` |

Finalmente, se ele responder sim, exibir as informações coletadas; caso contrário, dizer ao usuário que as informações dele não serão mantidas.

## <a name="create-the-main-dialog"></a>Criar diálogo principal

# <a name="c"></a>[C#](#tab/csharp)

Para usar os diálogos, instale o pacote NuGet **Microsoft.Bot.Builder.Dialogs.Adaptive**.

O bot interage com o usuário por meio do `RootDialog`. Quando o `RootDialog` do bot é criado, o `AdaptiveDialog` é definido como o diálogo principal. Em seguida, o bot usa o `DialogManager.OnTurnAsync` para executar o diálogo.

![Diálogo raiz](media/bot-builder-root-dialog-adaptive.png)

**Dialogs/RootDialog.cs**

O código começa criando uma instância da classe `RootDialog` que, por sua vez, cria uma instância do `AdaptiveDialog`. Neste momento, as `WelcomeUserSteps` e as `OnBeginDialogSteps` a seguir são adicionadas ao diálogo.
O diálogo criado é adicionado ao `DialogSet`, e o nome é salvo no estado do diálogo. Por fim, o nome do diálogo inicial a ser executado é atribuído à `InitialDialogId`. Observe a definição de `paths` referenciando o arquivo `RootDialog.lg` que contém os modelos LG usados na criação do diálogo adaptável.

[!code-csharp[RootDialog snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=21-52&highlight=6-25)]

O diálogo raiz é um diálogo de componente:

[!code-csharp[RootDialog snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=21&highlight=1)]

Observe também:

- O gerador de modelo LG é adicionado ao diálogo adaptável para habilitar o uso dos **modelos LG**.
- Os dois gatilhos são adicionados, com as ações sendo fornecidas pelos dois métodos auxiliares.

Em `WelcomeUserSteps`, o método fornece as ações a serem executadas quando o gatilho for acionado. As ações `Foreach` iteram na lista `membersAdded` para saudar o usuário adicionado à conversa.

> [!NOTE]
> No contexto de diálogos adaptáveis e gatilhos, todos os diálogos são ações válidas e os tipos de ação (`Foreach`, `IfCondition`, `SendActivity`) são todos diálogos.
> Alguns canais enviam dois eventos de atualização de conversa: um para o bot adicionado à conversa e outro para o usuário.
> O código filtra os casos em que o próprio bot é o destinatário da mensagem. Para obter mais informações, confira [Atividades categorizadas por canal](https://docs.microsoft.com/azure/bot-service/bot-service-channels-reference?view=azure-bot-service-4.0#welcome).

[!code-csharp[RootDialog snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=54-76&highlight=13-20)]

As `OnBeginDialogSteps` implementam as **etapas** usadas pelo diálogo. Elas definem as solicitações usando os modelos LG do arquivo `RootDialog.lg`. O código abaixo mostra como a solicitação `Name` é criada.

A ação `IfCondition` usa uma expressão adaptável para solicitar a idade do usuário ou enviar uma mensagem de reconhecimento, dependendo da resposta à pergunta anterior. Novamente, ela usa modelos LG para formatar as solicitações e as mensagens.

[!code-csharp[RootDialog snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=80-144&highlight=12-16,31-59)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar os diálogos, o projeto precisa instalar o pacote npm **botbuilder-dialogs-adaptive**.
A amostra descreve como usar recursos de **Diálogo adaptável** e **Geração de Linguagem** para obter a mesma funcionalidade do modelo em cascata.

Na inicialização, o `UserProfileDialog` do diálogo principal é inicializado. O bot o utiliza para interagir com o usuário.

**dialogs/userProfileDialog.js**

O código começa criando uma instância da classe `UserProfileDialog ` que, por sua vez, cria uma instância do diálogo raiz do `AdaptiveDialog`. Neste momento, as etapas do diálogo são criadas com os modelos de **Geração de Linguagem**.

As `OnBeginDialog` implementam as **etapas** usadas pelo diálogo. Elas definem as solicitações usando os modelos LG do arquivo `userProfileDialog.lg`.

[!code-javascript[userProfileDialog constructor](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.js?range=30-31)]

[!code-javascript[userProfileDialog constructor](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/dialogs/userProfileDialog.js?range=89-90)]

---

## <a name="register-the-adaptive-dialog"></a>Registrar o diálogo adaptável

Para permitir o uso do diálogo adaptável, o código de inicialização precisa registrar o diálogo conforme mostrado nas linhas realçadas abaixo, junto com outros serviços.

# <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

Você registra os diálogos adaptáveis na classe `Startup`, juntamente com outros serviços.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs?range=22-55&highlight=5-18)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

O código cria o diálogo de componente e os serviços em `index.js`. Especialmente:

- O diálogo de componente raiz.
- Serviços básicos para bot: um provedor de credenciais, um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.

Importe os serviços de bot necessários e a classe de diálogo de componente `userProfileDialog`.

[!code-javascript[index-import](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=7-13)]

Crie um estado de conversa com o provedor de armazenamento na memória.

[!code-javascript[index-storage](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=49-54)]

Crie o diálogo principal e o bot.

[!code-javascript[index-main-dialog](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=56-58)]

Ouça as solicitações de entrada e encaminhe a mensagem para o manipulador principal do bot.

[!code-javascript[index-run](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/index.js?range=68-74)]

---

## <a name="run-the-dialog"></a>Executar o diálogo

# <a name="c"></a>[C#](#tab/csharp)

**Bots/Dialogs.cs**

O `DialogManager.OnTurnAsync` executa o diálogo adaptável com atividades.
A implementação mostrada pode executar qualquer tipo de `Dialog`. O `ConversationState` é usado pelo sistema de diálogo. No entanto, o `UserState` não pode ter sido usado em uma implementação de diálogo. O método `DialogManager.OnTurnAsync` fica responsável por salvar o estado.

[!code-csharp[Dialogs](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Bots/DialogBot.cs?range=18-40&highlight=20)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**bots/dialogBot.js**

O `DialogBot` estende o `ActivityHandler` e executa o diálogo adaptável com atividades.
As informações de estado contidas pelo `conversationState` e pelos `userState` são armazenadas para uso do `dialogManager`.

[!code-javascript[index-run](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt/bots/dialogBot.js?range=7-30&highlight=13-14,19-2)]

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o Emulador, conecte-se ao seu bot e envie mensagens conforme mostrado abaixo.

![Execução de exemplo do diálogo de prompt de vários turnos](../media/emulator-v4/multi-turn-prompt-adaptive-sample.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um bot usando tipos de caixa de diálogo combinados](bot-builder-mixed-dialogs.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt
[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/javascript_nodejs/01.multi-turn-prompt
