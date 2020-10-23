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
ms.openlocfilehash: d2170e23e785e447a92db2a2aef5be622e88b22c
ms.sourcegitcommit: e37cf15f4907910560f34445a0fbdd7ae75b4787
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440115"
---
# <a name="create-a-bot-using-adaptive-dialogs"></a>Criar um bot usando diálogos adaptáveis  

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo mostra como usar recursos de **Diálogo adaptável** e **Geração de Linguagem** para obter a mesma funcionalidade obtida com o modelo em cascata.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Uma cópia do exemplo de **solicitação de vários ativações** em [**C#**][cs-sample].

### <a name="preliminary-steps-to-add-an-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável a um bot

Você precisará seguir as etapas descritas abaixo para adicionar um diálogo adaptável a um bot.

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `Microsoft.Bot.Builder.Dialogs.Adaptive` ao seu projeto de bot.
1. Atualize o adaptador de bot para adicionar o armazenamento e os objetos de estado de conversa e usuário a cada contexto de turno.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.

## <a name="about-the-sample"></a>Sobre o exemplo

Esta amostra usa um diálogo adaptável, algumas solicitações e um diálogo de componente para criar uma interação simples que faz uma série de perguntas ao usuário. As perguntas são criadas por meio de modelos LG:

- Para o C#, definido em [RootDialog.lg](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.lg)

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

Para usar os diálogos, instale o pacote NuGet **Microsoft.Bot.Builder.Dialogs.Adaptive**.

O bot interage com o usuário por meio de uma caixa de diálogo adaptável raiz. Em seguida, o bot usa o `DialogManager.OnTurnAsync` para executar o diálogo.

![Diálogo raiz](media/bot-builder-root-dialog-adaptive.png)

**Dialogs\RootDialog.cs**

O código começa instanciando a caixa de diálogo adaptável raiz. Neste momento, as `WelcomeUserSteps` e as `OnBeginDialogSteps` a seguir são adicionadas ao diálogo. Observe a definição de `paths` referenciando o arquivo `Dialogs\RootDialog.lg` que contém os modelos LG usados na criação do diálogo adaptável.

[!code-csharp[define dialog snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=17-38&highlight=3-4,21)]

Observe também:

- O gerador de modelo LG é adicionado ao diálogo adaptável para habilitar o uso dos **modelos LG**.
- Os dois gatilhos são adicionados, com as ações sendo fornecidas pelos dois métodos auxiliares.

O `WelcomeUserSteps` método fornece as ações a serem executadas quando o gatilho for acionado. As ações `Foreach` iteram na lista `membersAdded` para saudar o usuário adicionado à conversa.

> [!NOTE]
> No contexto de diálogos adaptáveis e gatilhos, todos os diálogos são ações válidas e os tipos de ação (`Foreach`, `IfCondition`, `SendActivity`) são todos diálogos.\
> Alguns canais enviam dois eventos de atualização de conversa: um para o bot adicionado à conversa e outro para o usuário.
> O código filtra os casos em que o próprio bot é o destinatário da mensagem. Para obter mais informações, confira [Atividades categorizadas por canal](../bot-service-channels-reference.md#welcome).

[!code-csharp[Welcome user](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=40-64)]

As `GatherUserInformation` implementam as **etapas** usadas pelo diálogo. Elas definem as solicitações usando os modelos LG do arquivo `RootDialog.lg`. O código abaixo mostra como a solicitação `Name` é criada.

[!code-csharp[gather user information](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=66-133)]

A ação `IfCondition` usa uma expressão adaptável para solicitar a idade do usuário ou enviar uma mensagem de reconhecimento, dependendo da resposta à pergunta anterior. Novamente, ela usa modelos LG para formatar as solicitações e as mensagens.

[!code-csharp[if condition](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs?range=91-119)]

## <a name="register-the-adaptive-dialog"></a>Registrar o diálogo adaptável

Para permitir o uso do diálogo adaptável, o código de inicialização precisa registrar o diálogo conforme mostrado nas linhas realçadas abaixo, junto com outros serviços.

A caixa de diálogo adaptável raiz é criada quando o bot é criado.

## <a name="run-the-dialog"></a>Executar o diálogo

**Bots/Dialogs.cs**

O `DialogManager.OnTurnAsync` executa o diálogo adaptável com atividades.
A implementação mostrada pode executar qualquer tipo de `Dialog`. O `ConversationState` é usado pelo sistema de diálogo. No entanto, o `UserState` não pode ter sido usado em uma implementação de diálogo. O método `DialogManager.OnTurnAsync` fica responsável por salvar o estado.

[!code-csharp[Dialogs](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Bots/DialogBot.cs?range=30-34&highlight=4)]

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
