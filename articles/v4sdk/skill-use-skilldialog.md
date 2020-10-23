---
title: Usar uma caixa de diálogo para consumir um skill | Microsoft Docs
description: Saiba como consumir um skill usando caixas de diálogo com o SDK do Bot Framework.
keywords: habilidades
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/15/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2cc2a1527ccd7df3d262c990ffa7fd6457686a8f
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417383"
---
# <a name="use-a-dialog-to-consume-a-skill"></a>Usar uma caixa de diálogo para consumir um skill

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo demonstra como usar uma _caixa de diálogo de skill_ em um consumidor de skill.
A caixa de diálogo de skill posta as atividades do bot pai para o bot de skill e retorna as respostas do skill para o usuário.
O bot de skill acessado por esse consumidor pode lidar com as atividades de mensagens e eventos.
Para obter um exemplo de manifesto de skill e informações sobre como implementar o skill, confira como [usar caixas de diálogo em um skill](skill-actions-in-dialogs.md).

Para saber mais sobre como usar um bot de skill fora das caixas de diálogo, confira como [implementar um consumidor de skill](skill-implement-consumer.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento [básico sobre bots](bot-builder-basics.md), [como os bots de skill funcionam](skills-conceptual.md) e como [implementar um consumidor de skill](skill-implement-consumer.md).
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Uma cópia do exemplo **skills skillDialog** em [**C#** ](https://aka.ms/skills-using-dialogs-cs), [**JavaScript**](https://aka.ms/skills-using-dialogs-js) ou [**Python**](https://aka.ms/skills-using-dialogs-py).

## <a name="about-this-sample"></a>Sobre este exemplo

O exemplo **skills skillDialog** inclui projetos para dois bots:

- O _bot raiz de caixa de diálogo_, que usa uma classe de _caixa de diálogo de skill_ para consumir um skill.
- O _bot de skill de caixa de diálogo_, que usa uma caixa de diálogo para lidar com atividades provenientes de consumidores de skill.

Este artigo se concentra em como usar uma classe de _caixa de diálogo de skill_ em um bot raiz para gerenciar o skill, enviar atividades de mensagens e eventos e cancelar o skill.

<!--
If you use middleware in your skill consumer, see how to [use middleware in a skill consumer](skill-middleware-in-consumer.md) to avoid some common problems.
-->
Para saber mais sobre outros aspectos da criação de um consumidor de skill, confira como [implementar um consumidor de skill](skill-implement-consumer.md).

### <a name="c"></a>[C#](#tab/cs)

![Diagrama de classe de consumidor de skills](./media/skill-dialog/dialog-root-bot-cs.png)

### <a name="javascript"></a>[JavaScript](#tab/js)

![Diagrama de classe de consumidor de skills](./media/skill-dialog/dialog-root-bot-js.png)

### <a name="python"></a>[Python](#tab/python)

![Diagrama de classe de consumidor de skills](./media/skill-dialog/dialog-root-bot-py.png)

---

Para saber mais sobre o bot de skill de caixa de diálogo, confira como [usar caixa de diálogo em um skill](skill-actions-in-dialogs.md).

## <a name="resources"></a>Recursos

A autenticação de bot para bot requer que cada bot participante tenha uma appID e uma senha válidas.

Registre tanto o skill quanto o consumidor de skills com o Azure. Você pode usar um Registro de Canais de Bot. Para obter mais informações, veja como [registrar um bot com o Serviço de Bot do Azure](../bot-service-quickstart-registration.md).

## <a name="application-configuration"></a>Configuração de aplicativo

1. Adicione a ID do aplicativo e a senha do bot raiz.
1. Adicione o ponto de extremidade do host de habilidades (o serviço ou a URL de retorno de chamada) ao qual as habilidades devem responder ao consumidor de habilidades.
1. Adicione uma entrada para cada skill que o consumidor de skills usará. Cada entrada inclui:
   - Uma ID que o consumidor de skills usará para identificar cada skill.
   - A ID do aplicativo do skill.
   - O ponto de extremidade de mensagens do skill.

### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\appsettings.json**

- Adicione a ID do aplicativo e a senha do bot raiz.
- Adicione a ID do aplicativo para o bot de skill de eco à matriz `BotFrameworkSkills`.

[!code-json[configuration file](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/appsettings.json?highlight=2-3,9)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/.env**

- Adicione a ID do aplicativo e a senha do bot raiz.
- Adicione a ID do aplicativo ao bot de skill de eco.

[!code-javascript[configuration file](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/.env?highlight=1-2,6)]

### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/config.py**

- Adicione a ID do aplicativo e a senha do bot raiz.
- Adicione a ID do aplicativo ao bot de skill de eco.

[!code-python[configuration file](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/config.py?range=14-25&highlight=1,3,9)]

---

## <a name="dialog-logic"></a>Lógica de caixa de diálogo

A caixa de diálogo principal do bot inclui uma _caixa de diálogo de skill_ para cada skill consumido por esse bot. A caixa de diálogo de skill gerencia o skill por meio de vários objetos relacionados a skills para você, como o _cliente de skill_ e os objetos de _fábrica de IDs de conversa de skills_.
A caixa de diálogo principal também demonstra como cancelar o skill (por meio da caixa de diálogo de skill) com base na entrada do usuário.

O skill que esse bot usa dá suporte a alguns recursos diferentes. Ele pode reservar um voo ou obter o clima de uma cidade. Além disso, se ele receber uma mensagem fora desses contextos e um reconhecedor LUIS estiver configurado, ele tentará interpretar a intenção do usuário.

O manifesto de skill ([**C#** ](https://aka.ms/skilldialog-manifest-cs), [**JavaScript**](https://aka.ms/skilldialog-manifest-js), [**Python**](https://aka.ms/skilldialog-manifest-py)) descreve as ações que o skill pode executar, os respectivos parâmetros de entrada e saída e os pontos de extremidade do skill.
Observe que o skill pode lidar com um evento "BookFlight" ou "GetWeather". Ele também pode manipular mensagens.

A caixa de diálogo principal inclui código para:

- [Inicializar a caixa de diálogo principal](#initialize-the-main-dialog)
- [Selecionar um skill](#select-a-skill)
- [Selecionar uma ação de skill](#select-a-skill-action)
- [Iniciar um skill](#start-a-skill)
- [Resumir o resultado do skill](#summarize-the-skill-result)
- [Permitir que o usuário cancele o skill](#allow-the-user-to-cancel-the-skill)

A caixa de diálogo principal herda da classe _caixa de diálogo de componente_. Para saber mais sobre as caixas de diálogo de componente, confira como [gerenciar a complexidade da caixa de diálogo](bot-builder-compositcontrol.md).

### <a name="initialize-the-main-dialog"></a>Inicializar a caixa de diálogo principal

A caixa de diálogo principal inclui caixas de diálogo (para gerenciar o fluxo de conversas fora do skill) e uma caixa de diálogo de skill (para gerenciar os skills).
A cascata inclui as etapas a seguir, descritas em mais detalhes nas próximas seções.

1. O usuário deverá selecionar o skill a ser usado. (O bot raiz consome um skill).
1. O usuário deverá selecionar a ação a ser usada para esse skill. (O bot de skill define três ações).
1. Inicie o skill escolhido com uma atividade inicial com base na ação escolhida.
1. Depois que o skill for concluído, exiba os resultados, se houver. Em seguida, reinicie a cascata.

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

A classe `MainDialog` deriva de `ComponentDialog`.
Além do estado da conversa, a caixa de diálogo precisa da ID do aplicativo do bot raiz e faz referência à fábrica de IDs de conversa de skills, ao cliente HTTP de skill e aos objetos de configuração de skills.

O construtor de caixa de diálogo verifica seus parâmetros de entrada, adiciona caixas de diálogo de skills, adiciona prompts e caixas de diálogo em cascata para gerenciar o fluxo de conversa fora do skill e cria um acessador de propriedade para acompanhar o skill ativo, se houver.

O construtor chama `AddSkillDialogs`, um método auxiliar, para criar um `SkillDialog` para cada skill incluído no arquivo de configuração, como lido do arquivo de configuração para um objeto `SkillsConfiguration`.

[!code-csharp[AddSkillDialogs](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=204-223&highlight=18)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

A classe `MainDialog` deriva de `ComponentDialog`.
Além do estado da conversa, a caixa de diálogo precisa da ID do aplicativo do bot raiz e faz referência à fábrica de IDs de conversa de skills, ao cliente HTTP de skill e aos objetos de configuração de skills. O código recupera a ID do aplicativo do bot do ambiente do usuário.

O construtor de caixa de diálogo verifica seus parâmetros de entrada, adiciona caixas de diálogo de skills, adiciona prompts e caixas de diálogo em cascata para gerenciar o fluxo de conversa fora do skill e cria um acessador de propriedade para acompanhar o skill ativo, se houver.

O construtor chama `AddSkillDialogs`, um método auxiliar, para criar um `SkillDialog` para cada skill incluído no arquivo de configuração, como lido do arquivo de configuração para um objeto `SkillsConfiguration`.

[!code-javascript[addSkillDialogs](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=175-194&highlight=18)]

### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

A classe `MainDialog` deriva de `ComponentDialog`.
Além do estado da conversa, a caixa de diálogo precisa da ID do aplicativo do bot raiz e faz referência à fábrica de IDs de conversa de skills, ao cliente HTTP de skill e aos objetos de configuração de skills.

O construtor de caixa de diálogo verifica seus parâmetros de entrada, adiciona caixas de diálogo de skills, adiciona prompts e caixas de diálogo em cascata para gerenciar o fluxo de conversa fora do skill e cria um acessador de propriedade para acompanhar o skill ativo, se houver.

O construtor chama `AddSkillDialogs`, um método auxiliar, para criar um `SkillDialog` para cada skill incluído no arquivo de configuração, como lido do arquivo de configuração para um objeto `SkillConfiguration`.

[!code-python[_add_skill_dialogs](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=239-263)]

---

### <a name="select-a-skill"></a>Selecionar um skill

Em sua primeira etapa, a caixa de diálogo principal solicita ao usuário qual o skill que ele gostaria de chamar e usa o prompt de opção "SkllPrompt" para obter a resposta. (Esse bot define apenas um skill).

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

[!code-csharp[SelectSkillStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=103-118&highlight=11)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

[!code-javascript[selectSkillStep](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=87-102&highlight=11)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

[!code-python[_select_skill_step](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=131-155)]

---

### <a name="select-a-skill-action"></a>Selecionar uma ação de skill

Na próxima etapa, a caixa de diálogo principal:

1. Salva informações sobre o skill selecionado pelo usuário.
1. Solicita ao usuário qual ação de skill ele gostaria de usar e usa o prompt de opção "SkillActionPrompt" para obter a resposta.
   - Ele usa um método auxiliar para obter uma lista de ações a serem escolhidas.
   - O validador de prompts associado a esse prompt usará o padrão para enviar o skill de uma mensagem se a entrada do usuário não corresponder a uma das opções.

As opções incluídas neste bot ajudam a testar as ações definidas para esse skill. Normalmente, você deve ler as opções do manifesto do skill e apresentar as opções para o usuário com base nessa lista.

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

[!code-csharp[SelectSkillActionStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=120-140)]

[!code-csharp[GetSkillActions](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=225-242)]

[!code-csharp[SkillActionPromptValidator](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=142-152)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

[!code-javascript[selectSkillActionStep](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=104-124)]

[!code-javascript[getSkillActions](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=196-212)]

[!code-javascript[skillActionPromptValidator](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=270-279)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

[!code-python[_select_skill_action_step](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=157-184)]

[!code-python[_get_skill_actions](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=280-294)]

[!code-python[_skill_action_prompt_validator](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=265-278)]

---

### <a name="start-a-skill"></a>Iniciar um skill

Na próxima etapa, a caixa de diálogo principal:

1. Recupera informações sobre o skill e a atividade de skill que o usuário selecionou.
1. Usa um método auxiliar para criar a atividade a ser enviada inicialmente para o skill.
1. Cria as opções de caixa de diálogo para iniciar a caixa de diálogo de skill. Isso inclui a atividade inicial a ser enviada.
1. Salva o estado antes de chamar o skill. (Isso é necessário, pois a resposta do skill pode chegar a uma instância diferente do consumidor de skill).
1. Inicia o caixa de diálogo de skill, passando a ID do skill a ser chamada e as opções para chamá-la.

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

[!code-csharp[CallSkillActionStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=154-179&highlight=10,19,25)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

[!code-javascript[callSkillActionStep](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=126-150&highlight=10,18,24)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

[!code-python[_call_skill_action_step](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=186-211)]

---

### <a name="summarize-the-skill-result"></a>Resumir o resultado do skill

Na última etapa, a caixa de diálogo principal:

1. Exibe o resultado para o usuário, caso o skill tenha retornado um valor.
1. Limpa o skill ativo do estado de caixa de diálogo.
1. Remove a propriedade de skill ativa do estado de conversa.
1. Reinicia a si mesma (a caixa de diálogo principal).

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

[!code-csharp[FinalStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=181-202&highlight=9-11)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

[!code-javascript[finalStep](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=152-173&highlight=9-11)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

[!code-python[_final_step](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=213-237)]

---

### <a name="allow-the-user-to-cancel-the-skill"></a>Permitir que o usuário cancele o skill

A caixa de diálogo principal substitui o comportamento padrão do método _on continue dialog_ para permitir que o usuário cancele o skill atual, se houver. O método:

- caso haja um skill ativo e o usuário envie uma mensagem "abort", cancela todas as caixa de diálogo e enfileira a caixa de diálogo principal para reiniciar.
- Em seguida, chama a implementação base do método _on continue dialog_ para continuar processando o turno atual.

#### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Dialogs\MainDialog.cs**

[!code-csharp[OnContinueDialogAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Dialogs/MainDialog.cs?range=86-101)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/dialogs/mainDialog.js**

[!code-javascript[onContinueDialog](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/dialogs/mainDialog.js?range=73-85)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/dialogs/main_dialog.py**

[!code-python[on_continue_dialog](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/dialogs/main_dialog.py?range=113-129)]

---

## <a name="activity-handler-logic"></a>Lógica do manipulador de atividades

Como a lógica de skill para cada turno é manipulada por uma caixa de diálogo principal, o manipulador de atividade é muito parecido com outros exemplos de caixa de diálogo.

### <a name="c"></a>[C#](#tab/cs)

**DialogRootBot\Bots\RootBot.cs**

[!code-csharp[class definition](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Bots/RootBot.cs?range=15-16)]

[!code-csharp[constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Bots/RootBot.cs?range=18-25)]

[!code-csharp[OnTurnAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogRootBot/Bots/RootBot.cs?range=27-42)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogRootBot/bots/rootBot.js**

[!code-javascript[class definition](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/bots/rootBot.js?range=8)]

[!code-javascript[partial constructor plus onTurn](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/bots/rootBot.js?range=14-29)]

[!code-javascript[run](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogRootBot/bots/rootBot.js?range=47-56)]

### <a name="python"></a>[Python](#tab/python)

**dialog-root-bot/bots/root_bot.py**

[!code-python[class definition](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/bots/root_bot.py?range=16)]

[!code-python[constructor and on_turn](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-root-bot/bots/root_bot.py?range=17-36)]

---

## <a name="service-registration"></a>Registro do serviço

Os serviços necessários para usar uma caixa de diálogo de skill são os mesmos necessários para um consumidor de skill em geral.
Confira como [implementar um consumidor de skill](skill-implement-consumer.md) para ver uma discussão sobre os serviços necessários.

## <a name="test-the-root-bot"></a>Testar o bot raiz

Você pode testar o consumidor de skills no Emulador como se fosse um bot normal. No entanto, você precisa executar os bots skills e consumidores de skill ao mesmo tempo. Confira como [usar caixas de diálogo em um skill](skill-actions-in-dialogs.md) para saber mais sobre como configurar o skill.

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente.

1. Execute o bot de skill de caixa de diálogo e o bot raiz de caixa de diálogo localmente em seu computador. Se você precisar de instruções, confira o arquivo LEIAME para obter os exemplos de [C#](https://aka.ms/skills-using-dialogs-cs), [JavaScript](https://aka.ms/skills-using-dialogs-js) ou [Python](https://aka.ms/skills-using-dialogs-py).
1. Use o Emulador para testar o bot.
   - Quando você ingressa na conversa pela primeira vez, o bot exibe uma mensagem de boas-vindas e pergunta qual skill você gostaria de chamar. O bot de skill para este exemplo tem apenas um skill.
   - Selecione **DialogSkillBot**.
1. O bot a seguir solicita que você escolha uma ação para o skill. Escolha "BookFlight".
   1. Responda aos prompts.
   1. O skill é concluído, e o bot raiz exibe os detalhes da reserva antes de solicitar novamente o skill que você gostaria de chamar.
1. Selecione **DialogSkillBot** novamente e "BookFlight".
   1. Responda ao primeiro prompt e, em seguida, digite "abort" para interromper o skill.
   1. O bot raiz cancela o skill e solicita o skill que você gostaria de chamar.

## <a name="additional-information"></a>Informações adicionais

Confira como [implementar um consumidor de skills](skill-implement-consumer.md) para saber como implementar um consumidor de skills em geral.

<!-- [Use middleware in a skill consumer](skill-middleware-in-consumer.md) describes how to avoid some common problems. -->
