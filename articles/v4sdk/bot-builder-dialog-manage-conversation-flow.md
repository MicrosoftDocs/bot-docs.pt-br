---
title: Implementar fluxo de conversa sequencial | Microsoft Docs
description: Saiba como gerenciar um fluxo de conversa simples com diálogos no SDK do Bot Framework.
keywords: fluxo de conversa simples, fluxo de conversa sequencial, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 680d9148b463bbb5d10f4a6a06cc7b32b824b66e
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215426"
---
# <a name="implement-sequential-conversation-flow"></a>Implementar fluxo de conversa sequencial

[!INCLUDE[applies-to](../includes/applies-to.md)]

A coleta de informações por meio da apresentação de perguntas é uma das principais formas de um bot interagir com os usuários. A biblioteca de diálogos torna mais fácil fazer perguntas e validar as respostas para que elas correspondam a um tipo de dados específico ou atendam às regras de validação personalizadas.

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de caixas de diálogo. Em uma interação simples, o bot percorre uma sequência fixa de etapas e a conversa termina. Em geral, um diálogo é útil quando o bot precisa coletar informações do usuário. Este tópico fornece detalhes sobre como implementar um fluxo de conversa simples criando prompts e chamando-os de um diálogo de cascata. 

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state]e [biblioteca de diálogos][concept-dialogs].
- Uma cópia do exemplo de **prompt de vários turnos** em [**CSharp**][cs-sample] ou em [**JavaScript**][js-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

No exemplo de prompt de vários turnos, usamos um diálogo em cascata, alguns prompts e um diálogo de componente para criar uma interação simples que faz uma série de perguntas ao usuário. O código usa um diálogo para percorrer estas etapas:

| Etapas        | Tipo de prompt  |
|:-------------|:-------------| 
| Perguntar ao usuário qual é seu modo de transporte | Prompt de escolha |
| Perguntar o nome do usuário | Prompt de texto |
| Perguntar se o usuário deseja fornecer a idade | Prompt de confirmação |
| Se a resposta for Sim, perguntar a idade dele  | Prompt de número com validação para aceitar somente idades entre 0 e 150. |
| Perguntar se as informações coletadas estão “OK” | Reutilizar prompt de confirmação |

Finalmente, se ele responder sim, exibir as informações coletadas; caso contrário, dizer ao usuário que as informações dele não serão mantidas.

## <a name="create-the-main-dialog"></a>Criar diálogo principal

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo, instale o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

O bot interage com o usuário por meio de `UserProfileDialog`. Quando criarmos a classe `DialogBot` do bot, vamos definir o `UserProfileDialog` como o diálogo principal. O bot, em seguida, usa um método auxiliar `Run` para acessar o diálogo.

![diálogo de perfil do usuário](media/user-profile-dialog.png)

**Dialogs\UserProfileDialog.cs**

Começamos criando `UserProfileDialog`, que deriva da classe `ComponentDialog` e tem seis etapas.

No construtor `UserProfileDialog`, crie as etapas de cascata, os prompts e o diálogo de cascata, e adicione-os ao conjunto do diálogo. Os avisos precisam estar no mesmo conjunto do diálogo no qual eles são usados.

[!code-csharp[Constructor snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=22-41)]

Em seguida, implementamos as etapas usadas pelo diálogo. Para usar um prompt, chame-o de uma etapa no seu diálogo e recupere o resultado do prompt na etapa seguinte usando `stepContext.Result`. Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicita a entrada; em seguida, ele retorna o valor válido ou recomeça do princípio com um novo prompt até receber uma entrada válida.

Você sempre deve ter um retorno de `DialogTurnResult` não nulo em uma etapa de cascata. Se isso não acontecer, o diálogo poderá não funcionar como planejado. Aqui, mostramos a implementação para o `NameStepAsync` no diálogo de cascata.

[!code-csharp[Name step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=56-61)]

Em `AgeStepAsync`, especificamos um prompt de repetição para quando a entrada do usuário não é validada, seja porque está em um formato que o prompt não consegue analisar, seja porque a entrada não é aprovada pelos critérios de validação. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará o texto de prompt inicial para voltar a solicitar a entrada ao usuário.

[!code-csharp[Age step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=74-93&highlight=10)]

**UserProfile.cs**

O modo de transporte, o nome e a idade do usuário são salvos em uma instância da classe `UserProfile`.

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/UserProfile.cs?range=9-16)]

**Dialogs\UserProfileDialog.cs**

Na última etapa, verificamos o `stepContext.Result` retornado pelo diálogo chamado na etapa anterior da cascata. Se o valor retornado for true, usaremos o acessador de perfil do usuário para obter e atualizar o perfil do usuário. Para obter o perfil do usuário, chamamos o método `GetAsync` e definimos os valores das propriedades `userProfile.Transport`, `userProfile.Name` e `userProfile.Age`. Por fim, resumimos as informações para o usuário antes de chamar `EndDialogAsync`, que termina o diálogo. O fim do diálogo o remove da pilha de diálogo e retorna um resultado opcional ao pai dele. O pai é o método ou diálogo que iniciou o diálogo recém-terminado.

[!code-csharp[SummaryStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=108-134&highlight=5-10,25-26)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar diálogos, seu projeto precisa instalar o pacote npm **botbuilder-dialogs**.

O bot interage com o usuário por meio de `UserProfileDialog`. Quando criarmos o bot `DialogBot`, definiremos `UserProfileDialog` como o diálogo principal. O bot, em seguida, usa um método auxiliar `run` para acessar o diálogo.

![diálogo de perfil do usuário](media/user-profile-dialog-js.png)

**dialogs\userProfileDialog.js**

Começamos criando `UserProfileDialog`, que deriva da classe `ComponentDialog` e tem seis etapas.

No construtor `UserProfileDialog`, crie as etapas de cascata, os prompts e o diálogo de cascata, e adicione-os ao conjunto do diálogo. Os avisos precisam estar no mesmo conjunto do diálogo no qual eles são usados.

[!code-javascript[Constructor snippet](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=25-47)]

Em seguida, implementamos as etapas usadas pelo diálogo. Para usar um prompt, chame-o de uma etapa no diálogo e recupere o resultado do prompt na etapa a seguir do contexto de etapa; no nosso caso, usando `step.result`. Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicita a entrada; em seguida, ele retorna o valor válido ou recomeça do princípio com um novo prompt até receber uma entrada válida.

Você sempre deve ter um retorno de `DialogTurnResult` não nulo em uma etapa de cascata. Se isso não acontecer, o diálogo poderá não funcionar como planejado. Aqui, mostramos a implementação para o `nameStep` no diálogo de cascata.

[!code-javascript[name step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=75-78)]

Em `ageStep`, especificamos um prompt de repetição para quando a entrada do usuário não é validada, seja porque está em um formato que o prompt não consegue analisar, seja porque a entrada não é aprovada pelos critérios de validação especificados no construtor acima. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará o texto de prompt inicial para voltar a solicitar a entrada ao usuário.

[!code-javascript[age step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=90-101&highlight=5)]

**userProfile.js**

O modo de transporte, o nome e a idade do usuário são salvos em uma instância da classe `UserProfile`.

[!code-javascript[user profile](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/userProfile.js?range=4-10)]

**Dialogs\UserProfileDialog.cs**

Na última etapa, verificamos o `step.result` retornado pelo diálogo chamado na etapa anterior da cascata. Se o valor retornado for true, usaremos o acessador de perfil do usuário para obter e atualizar o perfil do usuário. Para obter o perfil do usuário, chamamos o método `get` e definimos os valores das propriedades `userProfile.transport`, `userProfile.name` e `userProfile.age`. Por fim, resumimos as informações para o usuário antes de chamar `endDialog`, que termina o diálogo. O fim do diálogo o remove da pilha de diálogo e retorna um resultado opcional ao pai dele. O pai é o método ou diálogo que iniciou o diálogo recém-terminado.

[!code-javascript[summary step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=115-136&highlight=4-8,20-21)]

---

## <a name="create-the-extension-method-to-run-the-waterfall-dialog"></a>Criar o método de extensão para executar o diálogo de cascata

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Definimos um método de extensão `Run` que usaremos para criar e acessar o contexto de diálogo. Aqui, `accessor` é o acessador de propriedade de estado para a propriedade de estado do diálogo e `dialog` é o diálogo do componente de perfil do usuário. Uma vez que os diálogos de componente definem um conjunto interno de diálogo, precisamos criar um conjunto externo de diálogo que fique visível para o código do manipulador de mensagens e usá-lo para criar um contexto de diálogo.

O contexto de diálogo é criado com a chamada do método `CreateContext` e é usado para interagir com o conjunto do diálogo no manipulador de turno do bot. O contexto do diálogo inclui o contexto do turno atual, o diálogo pai e o estado do diálogo, que fornece um método para preservar informações no diálogo.

O contexto do diálogo permite iniciar um diálogo com a ID da cadeia de caracteres ou continuar o diálogo atual (por exemplo, um diálogo em cascata que tem várias etapas). O contexto do diálogo é passado para todas as etapas de cascata e diálogos do bot.

**DialogExtensions.cs**

[!code-csharp[Run method](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/DialogExtensions.cs?range=13-24)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Definimos um método auxiliar `run` em `userProfileDialog` que usaremos para criar e acessar o contexto de diálogo. Aqui, `accessor` é o acessador de propriedade de estado para a propriedade de estado do diálogo e `this` é o diálogo do componente de perfil do usuário. Uma vez que os diálogos de componente definem um conjunto interno de diálogo, precisamos criar um conjunto externo de diálogo que fique visível para o código do manipulador de mensagens e usá-lo para criar um contexto de diálogo.

O contexto de diálogo é criado com a chamada do método `createContext` e é usado para interagir com o conjunto do diálogo no manipulador de turno do bot. O contexto do diálogo inclui o contexto do turno atual, o diálogo pai e o estado do diálogo, que fornece um método para preservar informações no diálogo.

O contexto do diálogo permite iniciar um diálogo com a ID da cadeia de caracteres ou continuar o diálogo atual (por exemplo, um diálogo em cascata que tem várias etapas). O contexto do diálogo é passado para todas as etapas de cascata e diálogos do bot.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=55-64)]

---

## <a name="run-the-dialog"></a>Executar o diálogo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

O manipulador `OnMessageActivityAsync` usa o método de extensão para iniciar ou continuar o diálogo. Em `OnTurnAsync`, usamos objetos de gerenciamento de estado do bot para persistir as alterações no estado para o armazenamento. (O método `ActivityHandler.OnTurnAsync` chama os vários métodos de manipulador da atividade, como `OnMessageActivityAsync`. Dessa forma, salvamos o estado depois que o manipulador de mensagens é concluído, mas antes da conclusão do próprio turno.)

[!code-csharp[overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O manipulador `onMessage` usa o método auxiliar para iniciar ou continuar o diálogo. Em `onDialog`, usamos objetos de gerenciamento de estado do bot para persistir as alterações no estado para o armazenamento. (O método `onDialog` é chamado por último, depois que outros manipuladores definidos são executados, como `onMessage`. Dessa forma, salvamos o estado depois que o manipulador de mensagens é concluído, mas antes da conclusão do próprio turno.)

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=30-44)]

---

## <a name="register-services-for-the-bot"></a>Registrar serviços para o bot

Tal bot usa os _serviços_ a seguir.

- Serviços básicos para bot: um provedor de credenciais, um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.
- A caixa de diálogo que o bot usará.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Startup.cs**

Nós registramos serviços para o bot em `Startup`. Esses serviços estão disponíveis para outros blocos do código por meio da injeção de dependência.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Startup.cs?range=17-41)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Nós registramos serviços para o bot em `index.js`. 

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/index.js?range=18-49)]

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

![Execução de exemplo do diálogo de prompt de vários turnos](../media/emulator-v4/multi-turn-prompt.png)

## <a name="additional-information"></a>Informações adicionais

### <a name="about-dialog-and-bot-state"></a>Sobre o estado do diálogo e do bot

Neste bot, definimos dois acessadores de propriedade de estado:

- Um deles criado dentro do estado de conversa para a propriedade de estado do diálogo. O estado do diálogo controla onde o usuário está dentro dos diálogos de um conjunto de diálogos, e é atualizado pelo contexto de diálogo, por exemplo, quando chamamos os métodos para iniciar ou continuar um diálogo.
- Um deles criado dentro do estado do usuário para a propriedade de perfil do usuário. O bot o utiliza para rastrear as informações que ele tem sobre o usuário, e podemos gerenciar explicitamente esse estado no código do nosso diálogo.

Os métodos _get_ e _set_ de um acessador de propriedade de estado obtêm e definem o valor da propriedade no cache do objeto de gerenciamento de estado. O cache é preenchido na primeira vez em que o valor de uma propriedade de estado é solicitado em um turno, mas deve ser mantido explicitamente. Para manter as alterações nas duas propriedades de estado, podemos chamar o método _salvar alterações_ do objeto de gerenciamento de estado correspondente.

Este exemplo atualiza o estado de perfil do usuário a partir do diálogo. Essa prática pode ser útil para um bot simples, mas não funcionará se você quiser reutilizar um diálogo entre os bots.

Há várias opções para manter as etapas de diálogo e o estado de bot separados. Por exemplo, após o diálogo reunir todas as informações, você pode:

- Usar o método terminar diálogo para fornecer os dados coletados como valor retornado ao contexto-pai. Pode ser o manipulador de turnos do bot ou um diálogo ativo anterior na pilha de diálogos. É assim que as classes de prompt são criadas.
- Gere uma solicitação para um serviço apropriado. Isso pode funcionar bem se seu bot atuar como um front-end para um serviço maior.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar o reconhecimento de idioma natural ao seu bot](bot-builder-howto-v4-luis.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-multi-prompts-sample
[js-sample]: https://aka.ms/js-multi-prompts-sample
