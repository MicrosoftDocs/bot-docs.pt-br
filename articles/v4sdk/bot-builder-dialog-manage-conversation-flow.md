---
title: Implementar o fluxo de conversa sequencial – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa simples com diálogos no SDK do Bot Framework.
keywords: fluxo de conversa simples, fluxo de conversa sequencial, diálogos, prompts, cascatas, conjunto de diálogos
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/26/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: dfc3efb73c57389fb8462e704a3d45ee8f0dfe6b
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717602"
---
# <a name="implement-sequential-conversation-flow"></a>Implementar fluxo de conversa sequencial

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

A coleta de informações por meio da apresentação de perguntas é uma das principais formas de um bot interagir com os usuários. A biblioteca de diálogos fornece recursos internos úteis como classes *prompt* que tornam fácil fazer perguntas e validar as respostas para que elas correspondam a um tipo de dados específico ou atendam às regras de validação personalizadas.

Você pode gerenciar fluxos de conversa simples e complexos usando a biblioteca de caixas de diálogo. Em uma interação simples, o bot percorre uma sequência fixa de etapas e a conversa termina. Uma caixa de diálogo é útil quando o bot precisa coletar informações do usuário.

Este artigo mostra como implementar um fluxo de conversa simples criando prompts e chamando-os a partir de uma caixa de diálogo de cascata.

> [!TIP]
> Para obter exemplos de como escrever seus próprios prompts sem usar a biblioteca de caixas de diálogo, veja o artigo [Criar seus próprios prompts para coletar entrada do usuário](bot-builder-primitive-prompts.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [noções básicas de bot][concept-basics], [gerenciamento de estado][concept-state] e [biblioteca de diálogos][concept-dialogs].
- Uma cópia da amostra de **prompts de várias ativações** em [**C#**][cs-sample], [**JavaScript**][js-sample]ou [**Python**][python-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

O exemplo de prompts de múltipla ativação usa uma caixa de diálogo de cascata, algumas solicitações e uma caixa de diálogo de componente para criar uma interação simples que solicita ao usuário uma série de perguntas. O código usa um diálogo para percorrer estas etapas:

| Etapas        | Tipo de prompt  |
|:-------------|:-------------|
| Perguntar ao usuário qual é seu modo de transporte | Prompt de escolha |
| Perguntar o nome do usuário | Prompt de texto |
| Perguntar se o usuário deseja fornecer a idade | Prompt de confirmação |
| Se eles responderam Sim, peça sua idade | Prompt de número, com validação para aceitar somente idades maiores que 0 e menores que 150 |
| Se ele não estiver usando o Microsoft Teams, solicite uma imagem do perfil | Prompt de anexo, com validação para permitir um anexo ausente |
| Pergunte se as informações coletadas estão "OK" | Reutilizar prompt de confirmação |

Finalmente, se ele responder sim, exibir as informações coletadas; caso contrário, dizer ao usuário que as informações dele não serão mantidas.

## <a name="create-the-main-dialog"></a>Criar diálogo principal

# <a name="c"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo, instale o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

O bot interage com o usuário por meio de `UserProfileDialog`. Ao criar a classe do bot `DialogBot` , o `UserProfileDialog` é definido como sua caixa de diálogo principal. O bot, em seguida, usa um método auxiliar `Run` para acessar o diálogo.

![Caixa de diálogo perfil de usuário do C#](media/user-profile-dialog.png)

**Dialogs\UserProfileDialog.cs**

Comece criando o `UserProfileDialog` que deriva da `ComponentDialog` classe e tem sete etapas.

No construtor `UserProfileDialog`, crie as etapas de cascata, os prompts e o diálogo de cascata, e adicione-os ao conjunto do diálogo. Os avisos precisam estar no mesmo conjunto do diálogo no qual eles são usados.

[!code-csharp[Constructor snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=20-47)]

Em seguida, adicione as etapas que a caixa de diálogo usa para solicitar entrada. Para usar um prompt, chame-o de uma etapa no seu diálogo e recupere o resultado do prompt na etapa seguinte usando `stepContext.Result`. Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicita a entrada. Em seguida, ele retorna o valor válido ou começa desde o início com um reprompt até receber uma entrada válida.

Você sempre deve ter um retorno de `DialogTurnResult` não nulo em uma etapa de cascata. Se você não fizer isso, sua caixa de diálogo poderá não funcionar conforme projetado. Mostrado abaixo está a implementação para `NameStepAsync` na caixa de diálogo de cascata.

[!code-csharp[Name step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=61-66)]

No `AgeStepAsync` , especifique um prompt de repetição para quando a entrada do usuário não for validada, seja porque ela está em um formato que o prompt não pode analisar ou a entrada falha em um critério de validação. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará o texto de prompt inicial para voltar a solicitar a entrada ao usuário.

[!code-csharp[Age step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=79-98&highlight=10)]

**UserProfile.cs**

O modo de transporte, o nome e a idade do usuário são salvos em uma instância da classe `UserProfile`.

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/UserProfile.cs?range=11-20)]

**Dialogs\UserProfileDialog.cs**

Na última etapa, verifique o `stepContext.Result` retornado pela caixa de diálogo chamada na etapa de cascata anterior. Se o valor de retorno for true, o acessador de perfil do usuário obterá e atualizará o perfil do usuário. Para obter o perfil do usuário, chame `GetAsync` e defina os valores das `userProfile.Transport` Propriedades, `userProfile.Name` `userProfile.Age` e `userProfile.Picture` . Por fim, resuma as informações para o usuário antes de chamar `EndDialogAsync` , que termina a caixa de diálogo. O fim do diálogo o remove da pilha de diálogo e retorna um resultado opcional ao pai dele. O pai é o método ou diálogo que iniciou o diálogo recém-terminado.

[!code-csharp[SummaryStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=136-178&highlight=5-11,41-42)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar as caixas de diálogo, seu projeto precisa instalar o pacote do npm, **botbuilder-dialogs**.

O bot interage com o usuário por meio de um `UserProfileDialog`. Ao criar o bot `DialogBot` , o `UserProfileDialog` é definido como sua caixa de diálogo principal. O bot, em seguida, usa um método auxiliar `run` para acessar o diálogo.

![Caixa de diálogo perfil de usuário JavaScript](media/user-profile-dialog-js.png)

**dialogs/userProfileDialog.js**

Comece criando o `UserProfileDialog` que deriva da `ComponentDialog` classe e tem sete etapas.

No construtor `UserProfileDialog`, crie as etapas de cascata, os prompts e o diálogo de cascata, e adicione-os ao conjunto do diálogo. Os avisos precisam estar no mesmo conjunto do diálogo no qual eles são usados.

[!code-javascript[Constructor snippet](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=29-51)]

Em seguida, adicione as etapas que a caixa de diálogo usa para solicitar entrada. Para usar um prompt, chame-o de uma etapa no diálogo e recupere o resultado do prompt na etapa a seguir do contexto de etapa; no nosso caso, usando `step.result`. Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicita a entrada. Em seguida, ele retorna o valor válido ou começa desde o início com uma nova solicitação até receber uma entrada válida.

Você sempre deve ter um retorno de `DialogTurnResult` não nulo em uma etapa de cascata. Se você não fizer isso, sua caixa de diálogo poderá não funcionar conforme projetado. Mostrado abaixo está a implementação para o `nameStep` na caixa de diálogo de cascata.

[!code-javascript[name step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=79-82)]

No `ageStep` , especifique um prompt de repetição para quando a entrada do usuário não for validada, seja porque ela está em um formato que o prompt não pode analisar, ou a entrada falha em um critério de validação, especificado no Construtor acima. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará o texto de prompt inicial para voltar a solicitar a entrada ao usuário.

[!code-javascript[age step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=94-105&highlight=5)]

**userProfile.js**

O modo de transporte, o nome e a idade do usuário são salvos em uma instância da classe `UserProfile`.

[!code-javascript[user profile](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/userProfile.js?range=4-11)]

**dialogs/userProfileDialog.js**

Na última etapa, verifique o `step.result` retornado pela caixa de diálogo chamada na etapa de cascata anterior. Se o valor de retorno for true, o acessador de perfil do usuário obterá e atualizará o perfil do usuário. Para obter o perfil do usuário, chame `get` e, em seguida, defina os valores das `userProfile.transport` `userProfile.name` Propriedades, `userProfile.age` e `userProfile.picture` . Por fim, resuma as informações para o usuário antes de chamar `endDialog` , que termina a caixa de diálogo. O fim do diálogo o remove da pilha de diálogo e retorna um resultado opcional ao pai dele. O pai é o método ou diálogo que iniciou o diálogo recém-terminado.

[!code-javascript[summary step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=137-167&highlight=3-9,29-30)]

**Criar o método de extensão para executar o diálogo de cascata**

Um `run` método auxiliar, definido dentro `userProfileDialog` de, é usado para criar e acessar o contexto da caixa de diálogo. Aqui, `accessor` é o acessador de propriedade de estado para a propriedade de estado do diálogo e `this` é o diálogo do componente de perfil do usuário. Como as caixas de diálogo de componente definem um conjunto de diálogo interno, um conjunto de caixas de diálogo externo deve ser criado e visível para o código do manipulador de mensagens e usado para criar um contexto de caixa de diálogo.

O contexto de diálogo é criado com a chamada do método `createContext` e é usado para interagir com o conjunto do diálogo no manipulador de turno do bot. O contexto do diálogo inclui o contexto do turno atual, o diálogo pai e o estado do diálogo, que fornece um método para preservar informações no diálogo.

O contexto da caixa de diálogo permite que você inicie uma caixa de diálogo com a ID da cadeia de caracteres ou continue a caixa de diálogo atual (como uma caixa de diálogo de cascata que tem várias etapas). O contexto do diálogo é passado para todas as etapas de cascata e diálogos do bot.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=59-68)]

# <a name="python"></a>[Python](#tab/python)

Para usar diálogos, instale os pacotes PyPI **botbuilder-dialogs** e **botbuilder-ai** executando `pip install botbuilder-dialogs` e `pip install botbuilder-ai` em um terminal.

O bot interage com o usuário por meio de `UserProfileDialog`. Quando a classe do bot `DialogBot` é criada, a `UserProfileDialog` é definida como sua caixa de diálogo principal. O bot, em seguida, usa um método auxiliar `run_dialog` para acessar o diálogo.

![Caixa de diálogo perfil de usuário do Python](media/user-profile-dialog-python.png)

**dialogs\user_profile_dialog.py**

Comece criando o `UserProfileDialog` que deriva da `ComponentDialog` classe e tem sete etapas.

No construtor `UserProfileDialog`, crie as etapas de cascata, os prompts e o diálogo de cascata, e adicione-os ao conjunto do diálogo. Os avisos precisam estar no mesmo conjunto do diálogo no qual eles são usados.

[!code-python[Constructor snippet](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=26-57)]

Em seguida, adicione as etapas que a caixa de diálogo usa para solicitar entrada. Para usar um prompt, chame-o de uma etapa no seu diálogo e recupere o resultado do prompt na etapa seguinte usando `step_context.result`. Nos bastidores, os prompts são uma caixa de diálogo em duas etapas. Primeiro, o prompt solicita a entrada. Em seguida, ele retorna o valor válido ou começa desde o início com uma nova solicitação até receber uma entrada válida.

Você sempre deve ter um retorno de `DialogTurnResult` não nulo em uma etapa de cascata. Se você não fizer isso, sua caixa de diálogo poderá não funcionar conforme projetado. Aqui você pode ver a implementação do `name_step` na caixa de diálogo de cascata.

[!code-python[name step](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=73-79)]

No `age_step` , especifique um prompt de repetição para quando a entrada do usuário não for validada, seja porque ela está em um formato que o prompt não pode analisar, ou a entrada falha em um critério de validação, especificado no Construtor acima. Nesse caso, se nenhum prompt de nova tentativa foi fornecido, o prompt usará o texto de prompt inicial para voltar a solicitar a entrada ao usuário

[!code-python[age step](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=100-116)]

**data_models\user_profile.py**

O modo de transporte, o nome e a idade do usuário são salvos em uma instância da classe `UserProfile`.

[!code-python[user profile](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/data_models/user_profile.py?range=7-16)]

**dialogs\user_profile_dialog.py**

Na última etapa, verifique o `step_context.result` retornado pela caixa de diálogo chamada na etapa de cascata anterior. Se o valor de retorno for true, o acessador de perfil do usuário obterá e atualizará o perfil do usuário. Para obter o perfil do usuário, chame `get` e, em seguida, defina os valores das `user_profile.transport` `user_profile.name` Propriedades, e `user_profile.age` . Por fim, resuma o para o usuário antes de chamar `end_dialog` , que termina a caixa de diálogo. O fim do diálogo o remove da pilha de diálogo e retorna um resultado opcional ao pai dele. O pai é o método ou diálogo que iniciou o diálogo recém-terminado.

[!code-python[summary step](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=166-204)]

**Criar o método de extensão para executar o diálogo de cascata**

Um `run_dialog()` método auxiliar é definido em **Helpers \ dialog_helper. py** que é usado para criar e acessar o contexto da caixa de diálogo. Aqui, `accessor` é o acessador de propriedade de estado para a propriedade de estado do diálogo e `dialog` é o diálogo do componente de perfil do usuário. Como as caixas de diálogo de componente definem um conjunto de diálogo interno, um conjunto de caixas de diálogo externo deve ser criado e visível para o código do manipulador de mensagens e usá-lo para criar um contexto de caixa de diálogo.

Crie o contexto da caixa de diálogo chamando o `create_context` , que é usado para interagir com o conjunto de diálogo de dentro do manipulador de folheio do bot. O contexto do diálogo inclui o contexto do turno atual, o diálogo pai e o estado do diálogo, que fornece um método para preservar informações no diálogo.

O contexto do diálogo permite iniciar um diálogo com a ID da cadeia de caracteres ou continuar o diálogo atual (por exemplo, um diálogo em cascata que tem várias etapas). O contexto do diálogo é passado para todas as etapas de cascata e diálogos do bot.

[!code-python[run method](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/helpers/dialog_helper.py?range=8-19)]

---

## <a name="run-the-dialog"></a>Executar o diálogo

# <a name="c"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

O manipulador `OnMessageActivityAsync` usa o método `RunAsync` para iniciar ou continuar o diálogo. `OnTurnAsync` usa os objetos de gerenciamento de estado do bot para manter qualquer alteração de estado no armazenamento. O método `ActivityHandler.OnTurnAsync` chama os vários métodos de manipulador da atividade, como `OnMessageActivityAsync`. Dessa forma, o estado é salvo após a conclusão do manipulador de mensagens, mas antes que a ativação seja concluída.

[!code-csharp[overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O método `onMessage` registra um ouvinte que chama o método `run` da caixa de diálogo para iniciar ou continuar o diálogo.

Separadamente, o bot substitui o método `ActivityHandler.run` para salvar a conversa e o estado do usuário no armazenamento. Dessa forma, o estado é salvo após a conclusão do manipulador de mensagens, mas antes que a ativação seja concluída.

**bots/dialogBot.js**

[!code-javascript[message listener](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=24-31&highlight=5)]

[!code-javascript[override](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=34-43&highlight=7-9)]

# <a name="python"></a>[Python](#tab/python)

O manipulador `on_message_activity` usa o método auxiliar para iniciar ou continuar o diálogo. O `on_turn` método usa os objetos de gerenciamento de estado do bot para manter qualquer alteração de estado no armazenamento. O método `on_message_activity` é chamado por último, depois que outros manipuladores definidos são executados, como `on_turn`. Dessa forma, o estado é salvo após a conclusão do manipulador de mensagens, mas antes que a ativação seja concluída.

**bots\dialog_bot.py** [!code-python[overrides](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/bots/dialog_bot.py?range=39-51&highlight=4-6)]

---

## <a name="register-services-for-the-bot"></a>Registrar serviços para o bot

Este bot usa os seguintes serviços:

- Serviços básicos para bot: um provedor de credenciais, um adaptador e a implantação do bot.
- Serviços para gerenciamento de estado: armazenamento, estado do usuário e estado da conversa.
- A caixa de diálogo que o bot usará.

# <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

Registre os serviços para o bot no `Startup` . Esses serviços estão disponíveis para outros blocos do código por meio da injeção de dependência.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Startup.cs?range=15-37)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

Registre os serviços para o bot no `index.js` .

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/index.js?range=19-60)]

# <a name="python"></a>[Python](#tab/python)

Registre os serviços para o bot no `app.py` .

[!code-python[configure services](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/app.py?range=28-77)]

---

> [!NOTE]
> O armazenamento de memória é usado somente para testes e não deve ser usado na produção.
> Certifique-se de usar um tipo persistente de armazenamento para um bot de produção.

## <a name="to-test-the-bot"></a>Para testar o bot

1. Caso ainda não tenha feito isso, instale o [emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o Emulador, conecte-se ao seu bot e envie mensagens conforme mostrado abaixo.

![Execução de exemplo do diálogo de prompt de vários turnos](../media/emulator-v4/multi-turn-prompt.png)

## <a name="additional-information"></a>Informações adicionais

### <a name="about-dialog-and-bot-state"></a>Sobre o estado do diálogo e do bot

Nesse bot, os acessadores de propriedade de dois Estados são definidos:

- Um deles criado dentro do estado de conversa para a propriedade de estado do diálogo. O estado da caixa de diálogo controla onde o usuário está dentro das caixas de diálogo de um conjunto de caixas de diálogo e é atualizado pelo contexto da caixa de diálogo, como quando os métodos _BEGIN DIALOG_ ou _continue Dialog_ são chamados.
- Um deles criado dentro do estado do usuário para a propriedade de perfil do usuário. O bot usa isso para rastrear informações sobre o usuário, e você deve gerenciar explicitamente esse estado no código da caixa de diálogo.

Os métodos _get_ e _set_ de um acessador de propriedade de estado obtêm e definem o valor da propriedade no cache do objeto de gerenciamento de estado. O cache é preenchido na primeira vez em que o valor de uma propriedade de estado é solicitado em um turno, mas deve ser mantido explicitamente. Para manter as alterações em ambas as propriedades de estado, uma chamada para o método _Save Changes_ , do objeto de gerenciamento de estado correspondente, é executada.

Este exemplo atualiza o estado de perfil do usuário a partir do diálogo. Essa prática pode funcionar para um bot simples, mas não funcionará se você quiser reutilizar uma caixa de diálogo entre bots.

Há várias opções para manter as etapas de diálogo e o estado de bot separados. Por exemplo, após o diálogo reunir todas as informações, você pode:

- Use o método *End Dialog* para fornecer os dados coletados como valor de retorno de volta para o contexto pai. Pode ser o manipulador de folheio do bot ou uma caixa de diálogo ativa anterior na pilha de diálogo e é assim que as classes de prompt são criadas.
- Gere uma solicitação para um serviço apropriado. Isso pode funcionar bem se seu bot atuar como um front-end para um serviço maior.

### <a name="definition-of-a-prompt-validator-method"></a>Definição de um método validador de prompt

# <a name="c"></a>[C#](#tab/csharp)

**UserProfileDialog.cs**

Veja abaixo um exemplo de código do validador para a `AgePromptValidatorAsync` definição do método. `promptContext.Recognized.Value` contém o valor analisado, que é um inteiro aqui para o prompt do número. `promptContext.Recognized.Succeeded` indica se o prompt foi capaz de analisar a entrada do usuário ou não. O validador deve retornar false para indicar que o valor não foi aceito e a caixa de diálogo de prompt deve perguntar novamente ao usuário; caso contrário, retorna true para aceitar a entrada e retornar da caixa de diálogo de prompt. Observe que você pode alterar o valor no validador de acordo com seu cenário.

[!code-csharp[prompt validator method](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=180-184)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs\userProfileDialog.js**

Veja abaixo um exemplo de código do validador para a `agePromptValidator` definição do método. `promptContext.recognized.value` contém o valor analisado, que é um inteiro aqui para o prompt do número. `promptContext.recognized.succeeded` indica se o prompt foi capaz de analisar a entrada do usuário ou não. O validador deve retornar false para indicar que o valor não foi aceito e a caixa de diálogo de prompt deve perguntar novamente ao usuário; caso contrário, retorna true para aceitar a entrada e retornar da caixa de diálogo de prompt. Observe que você pode alterar o valor no validador de acordo com seu cenário.

[!code-javascript[age prompt validator](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=169-172)]

# <a name="python"></a>[Python](#tab/python)

**dialogs/user_profile_dialog.py**

Veja abaixo um exemplo de código do validador para a `age_prompt_validator` definição do método. `prompt_context.recognized.value` contém o valor analisado, que é um inteiro aqui para o prompt do número. `prompt_context.recognized.succeeded` indica se o prompt foi capaz de analisar a entrada do usuário ou não. O validador deve retornar false para indicar que o valor não foi aceito e a caixa de diálogo de prompt deve perguntar novamente ao usuário; caso contrário, retorna true para aceitar a entrada e retornar da caixa de diálogo de prompt. Observe que você pode alterar o valor no validador de acordo com seu cenário.

[!code-python[prompt validator method](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=207-212)]

---

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
[python-sample]: https://aka.ms/python-multi-prompts-sample
