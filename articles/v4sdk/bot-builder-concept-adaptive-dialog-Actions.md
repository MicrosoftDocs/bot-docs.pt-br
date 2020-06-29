---
title: Ações – SDK do Bot Framework – Caixas de diálogo adaptáveis
description: Coletar entrada do usuário usando caixas de diálogo adaptáveis
keywords: bot, usuário, eventos, gatilhos, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 04/27/2020
ms.openlocfilehash: 1d60fb65bad1c2940cbd3e104fb0fc0365ca594b
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073972"
---
<!--P2: Once the samples are done, link to them in each section on the individual actions to point to them as examples of how they are used-->
# <a name="actions-in-adaptive-dialogs"></a>Ações em caixas de diálogo adaptáveis

As ações ajudam a criar e manter o fluxo de conversa de bots quando um evento é capturado por um [gatilho][2]. De modo semelhante às caixas de diálogo adaptáveis, que contêm uma lista de gatilhos, os gatilhos contêm uma lista de ações que, depois que o gatilho é acionado, é executada para realizar qualquer conjunto de ações necessárias, como, por exemplo, atender à solicitação de um usuário. Além de criar e manter o fluxo de conversação do bot, você pode usar ações para enviar mensagens, responder a perguntas do usuário usando uma [base de dados de conhecimento][3], fazer cálculos e executar qualquer número de tarefas computacionais para o usuário. Com as caixas de diálogo adaptáveis, o caminho do bot, que flui em uma caixa de diálogo, pode se ramificar e fazer um loop. O bot pode fazer e responder perguntas, validar a entrada dos usuários, manipular e armazenar valores na [memória][11] e tomar decisões com base na entrada do usuário.

> [!IMPORTANT]
> As ações são caixas de diálogo, e qualquer caixa de diálogo pode ser usada como uma ação, portanto, as ações têm todo o poder e a flexibilidade de que você precisa para criar um bot totalmente funcional e robusto. Embora as ações incluídas no SDK do Bot Framework sejam amplas, você também pode criar suas próprias ações personalizadas para executar praticamente qualquer tarefa ou processo especializado necessário.

## <a name="prerequisites"></a>Pré-requisitos

* [Introdução às caixas de diálogo adaptáveis][1]
* [Eventos e gatilhos em caixas de diálogo adaptáveis][2]

## <a name="actions"></a>Ações

As ações incluídas com o SDK do Bot Framework fornecem a capacidade de executar codificação condicional, tal como:

* Ramificação e looping.
* Tarefas de gerenciamento de caixa de diálogo, como iniciar uma nova caixa de diálogo ou cancelar, encerrar ou repetir uma caixa de diálogo.
* Tarefas de gerenciamento de memória, como criar, excluir ou editar uma propriedade salva na [memória][11].
* Acesso a recursos externos, como enviar uma [solicitação HTTP](#httprequest).
* Executar uma [solicitação de logon OAuth][4] e muitas outras.

>[!TIP]
> Ao contrário de uma caixa de diálogo em cascata em que cada etapa é uma função, cada ação de uma caixa de diálogo adaptável é uma caixa de diálogo totalmente funcional com todo o poder e a flexibilidade necessários. Isso permite que caixas de diálogo adaptáveis por design possam:
>
> * Fornecer uma maneira mais fácil de lidar com interrupções. <!--TODO P1: [interruptions][6]-->
> * Executar ramificação condicional com base no contexto ou no estado.

As caixas de diálogo adaptáveis fornecem suporte para as seguintes ações:

### <a name="send-a-response"></a>Enviar uma resposta

| Atividade a realizar                         | Nome da ação                    | O que essa ação faz                                              |
| ---------------------------------------------- | ------------------------------ | ------------------------------------------------------------------ |
| Enviar qualquer atividade, como responder a um usuário.| [SendActivity](#send-activity) | Envia qualquer tipo de atividade, como responder a usuários. |

Para obter um exemplo de código, confira [Enviar um exemplo de resposta](#send-a-response-example).

### <a name="requesting-user-input"></a>Solicitar entrada do usuário

Confira informações sobre como solicitar a entrada do usuário em [Solicitar entrada do usuário usando caixas de diálogo adaptáveis][7].

>[!TIP]
> As _entradas_ são um tipo importante e muito útil de ação, abordadas no artigo [Solicitar entrada do usuário usando caixas de diálogo adaptáveis][7].

### <a name="create-a-condition"></a>Criar uma condição

As duas primeiras ações são instruções condicionais criadas para ajudar o bot a tomar decisões com base em qualquer condição predefinida que você tenha criado. Essas condições são especificadas por um conjunto de instruções condicionais com expressões boolianas que são avaliadas como um valor booliano de true ou false.

As ações restantes estão relacionadas a instruções de looping que permitem repetir a execução de um bloco de código para cada elemento em uma coleção.

| Atividade a realizar | Nome da ação                | O que essa ação faz                                                                                |
| ---------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------- |
| Ramificação: if/else        | [IfCondition](#ifcondition)| Usada para criar instruções If e If-Else usadas para executar o código somente se uma condição for verdadeira. |
| Ramificação: Switch (várias opções) | [SwitchCondition](#switchcondition) | Usada para criar um menu com várias opções.                                            |
| Loop: para cada item    | [ForEach](#foreach)        | Executa um loop por meio de um conjunto de valores armazenados em uma matriz.                                                     |
| Loop: para cada página (vários itens) | [ForEachPage](#foreachpage) | Executa um loop por meio de um grande conjunto de valores armazenados em uma página de uma matriz por vez.             |
| Sair de um loop            | [BreakLoop](#break-loop)   | Interrompe um loop.                                                                                 |
| Continuar um loop        | [ContinueLoop](#continue-loop) | Continua o loop.                                                                               |
| Ir para uma ação diferente| [GotoAction](#goto-action) | Passa imediatamente para a ação especificada e continua a execução. Determinada por actionID.            |

Confira exemplos de código em [Criar exemplos de uma condição](#create-a-condition-examples).

<!--TODO P1: Regarding BreakLoop & ContinueLoop - Need better explanation.
There's a mix of concepts going on here. There's the action sequence, which are the action list that's getting run for any given trigger. And then there are dialogs, which are either a parent dialog, a child dialog, or an action in the chain. Reducing confusion between action sequences and dialogs would probably help.  -->

### <a name="dialog-management"></a>Gerenciamento de caixa de diálogo

| Atividade a realizar | Nome da ação                      | O que essa ação faz                                                     |
| ---------------------- | -------------------------------- | ------------------------------------------------------------------------- |
| Começar uma nova caixa de diálogo     | [BeginDialog](#begindialog)      | Começa a executar outra caixa de diálogo. Quando essa caixa de diálogo for concluída, a execução do gatilho atual será retomada.    |
| Cancelar um diálogo        | `CancelDialog`<!--[CancelDialog](#canceldialog)-->| Cancela a caixa de diálogo ativa. Use quando desejar que a caixa de diálogo seja fechada imediatamente, mesmo que isso signifique parar o processo intermediário.|
| Cancelar todas as caixas de diálogo     | [CancelAllDialogs](#cancelalldialog)| Cancela todas as caixas de diálogo ativas, incluindo qualquer caixa de diálogo pai ativa. Use caso você queira remover todas as caixas de diálogo da pilha; é possível limpar a pilha de caixas de diálogo chamando o método cancelar todas as caixas de diálogo no contexto da caixa de diálogo. Emite o evento `CancelAllDialogs`.|
| Fechar esta caixa de diálogo        | [EndDialog](#enddialog)          | Encerra a caixa de diálogo ativa.  Use quando desejar que a caixa de diálogo seja concluída e retorne os resultados antes de ser encerrada. Emite o evento `EndDialog`.|
| Encerrar o turno da caixa de diálogo        | [EndTurn](#endturn)              | Encerra o turno atual da conversa sem encerrar a caixa de diálogo.          |
| Repetir essa caixa de diálogo     | [RepeatDialog](#repeatdialog)    | Usado para reiniciar a caixa de diálogo pai.                                        |
| Substituir essa caixa de diálogo    | [ReplaceDialog](#replacedialog)  | Substitui a caixa de diálogo atual por uma nova                             |
| Atualizar uma atividade     | [UpdateActivity](#update-activity)| Atualiza uma atividade que foi enviada.                    |
| Obter membros da atividade | [GetActivityMembers](#get-activity-members)| Obtém uma lista de membros da atividade e salva-a em uma propriedade na [memória][11].|
| GetConversationMembers| [GetConversationMembers](#get-conversation-members) | Obtém uma lista de membros da conversa e salva-a em uma propriedade na [memória][11].|
| EditActions    | [EditActions](#editactions) | Permite editar imediatamente a sequência de ação atual com base na entrada do usuário. Especialmente útil ao lidar com interrupções. <!--TODO P1: [interruptions][6]--> |

Confira os exemplos de códigos em [Exemplos de gerenciamento de caixa de diálogo](#dialog-management-examples).

<!--TODO P1: Revisit table structure for activities: https://github.com/MicrosoftDocs/bot-docs-pr/pull/2115#discussion_r420272686 --->

### <a name="manage-properties"></a>Gerenciar propriedades

| Atividade a realizar | Nome da ação                           | O que essa ação faz                                 |
| ---------------------- | ------------------------------------- | ----------------------------------------------------- |
| Editar uma matriz          | [EditArray](#editarray)               | Executa operações de edição em uma matriz.                  |
| Excluir uma propriedade      | [DeleteProperty](#deleteproperty)     | Remove uma propriedade da [memória][11].                  |
| Excluir propriedades      | [DeleteProperties](#deleteproperties) | Exclui mais de uma propriedade em uma única ação.     |
| Criar ou atualizar uma propriedade | [SetProperty](#setproperty)      | Define o valor de uma propriedade na [memória][11].               |
| Criar ou atualizar propriedades | [SetProperties](#setproperties)  | Inicializa uma ou mais propriedades em uma única ação. |

Confira os exemplos de códigos em [Exemplos de gerenciamento de propriedades](#manage-properties-examples).

### <a name="access-external-resources"></a>Acessar recursos externos

| Atividade a realizar | Nome da ação                  | O que essa ação faz                                                          |
| ---------------------- | ---------------------------- | ------------------------------------------------------------------------------ |
| Iniciar uma caixa de diálogo de skill   | [BeginSkill](#beginskill)    | Use a caixa de diálogo adaptável de skill para executar um skill.                                  |
| Enviar uma solicitação HTTP   | [HttpRequest](#httprequest)  | Faz solicitações HTTP para qualquer ponto de extremidade.                                            |
| Emitir um evento personalizado    | [EmitEvent](#emitevent)      | Aciona um evento personalizado que o bot pode responder usando um [gatilho personalizado][8]. |
| Desconectar um usuário        | [SignOutUser](#sign-out-user)| Desconecta o usuário atualmente conectado.                                         |
| Chamar código personalizado       | [CodeAction](#codeaction)    | Chama o seu código personalizado.                                                     |

Confira exemplos de código em [Exemplos de acesso a recursos externos](#access-external-resource-examples).

### <a name="debugging-options"></a>Opções de depuração

| Atividade a realizar | Nome da ação                     | O que essa ação faz                                                       |
| ---------------------- | ------------------------------- | --------------------------------------------------------------------------- |
| Registrar no console         | [LogAction](#log-action)        | Grava no console e, opcionalmente, envia a mensagem como uma atividade de rastreamento. |
| Emitir um evento de rastreamento     | [TraceActivity](#traceactivity) | Envia uma atividade de rastreamento com qualquer payload que você especificar.                   |

Confira exemplos de códigos em [Exemplos de opção de depuração](#debugging-option-examples).

## <a name="source-code-examples"></a>Exemplos de código-fonte

### <a name="send-a-response-example"></a>Exemplo de envio de resposta

#### <a name="send-activity"></a>Enviar atividade

Envia uma atividade.

``` C#
// Example of a simple SendActivity step
var greetUserDialog = new AdaptiveDialog("greetUserDialog");
greetUserDialog.Triggers.Add(new OnIntent()
{
    Intent = "greetUser", 
    Actions = new List<Dialog>() {
        new SendActivity("Hello")
    }
});

// Example that includes reference to property on bot state.
var greetUserDialog = new AdaptiveDialog("greetUserDialog");
greetUserDialog.Triggers.Add(new OnIntent()
{
    Intent = "greetUser",
    Actions = new List<Dialog>()
    {
        new TextInput()
        {
            Property = "user.name",
            Prompt = new ActivityTemplate("What is your name?")
        },
        new SendActivity("Hello, ${user.name}")
    }
});
```

Confira [geração de linguagem em caixas de diálogo adaptáveis][9] para saber mais sobre como usar a geração de linguagem em vez de codificar textos de resposta real na ação _enviar atividade_.

### <a name="create-a-condition-examples"></a>Exemplos de criação de condição

#### <a name="ifcondition"></a>IfCondition

Ramifica o fluxo de conversação com base em uma condição específica. As condições são expressadas usando [Expressões adaptáveis][10].

``` C#
var addToDoDialog = new AdaptiveDialog("addToDoDialog");
addToDoDialog.Triggers.Add(new OnIntent()
{
    Intent = "addToDo",
    Actions = new List<Dialog>()
    {
        // Save the userName entity from a recognizer.
        new SaveEntity("dialog.addTodo.title", "@todoTitle"),
        new TextInput()
        {
            Prompt = new ActivityTemplate("What is the title of your todo?"),
            Property = "dialog.addTodo.title"
        },
        // Add the current todo to the todo's list for this user.
        new EditArray()
        {
            ItemsProperty = "user.todos",
            Value = "=dialog.addTodo.title"
            ChangeType = EditArray.ArrayChangeType.Push
        },
        new SendActivity("Ok, I have added ${dialog.addTodo.title} to your todos."),
        new IfCondition()
        {
            Condition = "toLower(dialog.addTodo.title) == 'call santa'",
            Actions = new List<Dialog>()
            {
                new SendActivity("Yes master. On it right now \\[You have unlocked an easter egg] :)")
            }
        },
        new SendActivity("You now have ${count(user.todos)} items in your todo.")
    }
});
```

#### <a name="switchcondition"></a>SwitchCondition

Ramifica um fluxo de conversação com base no resultado de uma avaliação de expressão. Confira mais informações em [Expressões adaptáveis][10].

``` C#
// Create an adaptive dialog.
var cardDialog = new AdaptiveDialog("cardDialog");
cardDialog.Triggers.Add(new OnIntent()
{
    Intent = "ShowCards",
    Actions = new List<Dialog>()
    {
        // Add choice input.
        new ChoiceInput()
        {
            // Output from the user is automatically set to this property
            Property = "turn.cardDialog.cardChoice",

            // List of possible styles supported by choice prompt.
            Style = ListStyle.Auto,
            Prompt = new ActivityTemplate("What card would you like to see?"),
            Choices = new ChoiceSet(new List<Choice>() {
                new Choice("Adaptive card"),
                new Choice("Hero card"),
                new Choice("Video card")
            })
        },
        // Use SwitchCondition step to dispatch to right dialog based on choice input.
        new SwitchCondition()
        {
            Condition = "turn.cardDialog.cardChoice",
            Cases = new List<Case>()
            {
                new Case("Adaptive card",  new List<Dialog>() { new SendActivity("${AdaptiveCardRef()}") } ),
                new Case("Hero card", new List<Dialog>() { new SendActivity("${HeroCard()}") } ),
                new Case("Video card",     new List<Dialog>() { new SendActivity("${VideoCard()}") } ),
            },
            Default = new List<Dialog>()
            {
                new SendActivity("[AllCards]")
            }
        }
}));
```

#### <a name="foreach"></a>ForEach

O loop ForEach é uma maneira conveniente de recuperar elementos de uma matriz ou coleção. Geralmente, é usado para executar uma ação em cada item de uma coleção.

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new SetProperty()
                {
                    Property = "turn.colors",
                    Value = "=createArray('red', 'blue', 'green', 'yellow', 'orange', 'indigo')"
                },
                new Foreach()
                {
                    ItemsProperty = "turn.colors",
                    Actions = new List<Dialog>()
                    {
                        // By default, dialog.foreach.value holds the value of the item
                        // dialog.foreach.index holds the index of the item.
                        // You can use short hands to refer to these via
                        //      $foreach.value
                        //      $foreach.index
                        new SendActivity("${$foreach.index}: Found '${$foreach.value}' in the collection!")
                    }
                }
            }
        }
    }
};
```

#### <a name="foreachpage"></a>ForEachPage

Usado para aplicar etapas a itens em uma coleção. O tamanho da página denota quantos itens da coleção são selecionados por vez.

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new SetProperty()
                {
                    Property = "turn.colors",
                    Value = "=createArray('red', 'blue', 'green', 'yellow', 'orange', 'indigo')"
                },
                new ForeachPage()
                {
                    ItemsProperty = "turn.colors",
                    PageSize = 2,
                    Actions = new List<Dialog>()
                    {
                        // By default, dialog.foreach.page holds the value of the page
                        //      $foreach.page
                        new SendActivity("Page content: ${join($foreach.page, ', ')}")
                    }
                }
            }
        }
    }
};
```

#### <a name="break-loop"></a>Interromper loop

Interrompe um loop ou a sequência de ação atual.

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new SetProperty()
                {
                    Property = "turn.colors",
                    Value = "=createArray('red', 'blue', 'green', 'yellow', 'orange', 'indigo')"
                },
                new Foreach()
                {
                    ItemsProperty = "turn.colors",
                    Actions = new List<Dialog>()
                    {
                        new IfCondition()
                        {
                            Condition = "$foreach.value == 'green'",
                            Actions = new List<Dialog>()
                            {
                                new BreakLoop()
                            },
                            ElseActions = new List<Dialog>()
                            {
                                // By default, dialog.foreach.value holds the value of the item
                                // dialog.foreach.index holds the index of the item.
                                // You can use short hands to refer to these via 
                                //      $foreach.value
                                //      $foreach.index
                                new SendActivity("${$foreach.index}: Found '${$foreach.value}' in the collection!")
                            }
                        },
                    }
                }
            }
        }
    }
};
```

#### <a name="continue-loop"></a>Continuar loop

Continua o loop ou a sequência de ação atual sem processar o restante das instruções.

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new SetProperty()
                {
                    Property = "turn.colors",
                    Value = "=createArray('red', 'blue', 'green', 'yellow', 'orange', 'indigo')"
                },
                new Foreach()
                {
                    ItemsProperty = "turn.colors",
                    Actions = new List<Dialog>()
                    {
                        new IfCondition()
                        {
                            // Skip items at even position in the collection.
                            Condition = "$foreach.index % 2 == 0",
                            Actions = new List<Dialog>()
                            {
                                new ContinueLoop()
                            },
                            ElseActions = new List<Dialog>()
                            {
                                // By default, dialog.foreach.value holds the value of the item
                                // dialog.foreach.index holds the index of the item.
                                // You can use short hands to refer to these via 
                                //      $foreach.value
                                //      $foreach.index
                                new SendActivity("${$foreach.index}: Found '${$foreach.value}' in the collection!")
                            }
                        },
                    }
                }
            }
        }
    }
};
```

#### <a name="goto-action"></a>Ação ir para

Salte para uma ação rotulada dentro do escopo da ação atual.

```C#
var adaptiveDialog = new AdaptiveDialog()
{
    Triggers = new List<OnCondition>()
    {
        new OnBeginDialog()
        {
            Actions = new List<Dialog>()
            {
                new GotoAction()
                {
                    ActionId = "end"
                },
                new SendActivity("this will be skipped."),
                new SendActivity()
                {
                    Id = "end",
                    Activity = new ActivityTemplate("The End.")
                }
            }
        }
    }
};
```

### <a name="dialog-management-examples"></a>Exemplos de gerenciamento de caixa de diálogo

#### <a name="begindialog"></a>BeginDialog

Inicia uma nova caixa de diálogo e a envia para a pilha de caixas de diálogo. A ação _begin dialog_ requer o nome do diálogo de destino, que pode ser qualquer tipo de diálogo, incluindo o diálogo Adaptável, Em cascata etc.

A ação _begin dialog_ define uma propriedade chamada `ResultProperty` que permite especificar onde salvar os resultados quando o diálogo for encerrado.

``` C#
new BeginDialog("BookFlightDialog")
{
    // Any value returned by BookFlightDialog will be captured in the property specified here.
    ResultProperty = "$bookFlightResult"
}
```

> [!TIP]
> Assim como ao invocar qualquer diálogo no SDK do Bot Framework, ao chamar `BeginDialog` para invocar um diálogo adaptável, você pode usar o parâmetro `options` para transmitir informações de entrada ao diálogo.

#### <a name="enddialog"></a>EndDialog

Encerra a caixa de diálogo ativa ao removê-la da pilha e retorna um resultado opcional na caixa de diálogo pai.

Por padrão, as caixas de diálogo adaptáveis têm `defaultResultProperty` definida como `dialog.results`, de modo que qualquer coisa definida no [escopo da memória][11] retornará automaticamente para o chamador em cenários em que a caixa de diálogo foi encerrada automaticamente. Se você encerrar o diálogo usando a ação _encerrar diálogo_, precisará especificar o que será retornado para o chamador na propriedade `value`.

``` C#
new EndDialog()
{
    // Value property indicates value to return to the caller.
    Value = "=$userName"
}
```

> [!TIP]
> As caixas de diálogo adaptáveis serão encerradas automaticamente por padrão se a caixa de diálogo tiver concluído a execução de todas as suas ações. Para substituir esse comportamento, defina a propriedade `AutoEndDialog` na Caixa de Diálogo Adaptável como false.

#### <a name="cancelalldialog"></a>CancelAllDialog

Exclui todas as caixas de diálogo na pilha, inclusive as caixas de diálogo pai e filho.

``` C#
new CancelAllDialog()
```

#### <a name="endturn"></a>EndTurn

Encerra o turno atual da conversa sem encerrar a caixa de diálogo.

``` C#
new EndTurn()
```

#### <a name="repeatdialog"></a>RepeatDialog

Reinicia a caixa de diálogo pai. Será especialmente útil se você estiver tentando ter uma conversa em que o bot está paginando para o usuário os resultados nos quais ele pode navegar.

> [!IMPORTANT]
> Certifique-se de usar `EndTurn()` ou uma das entradas para coletar informações do usuário para que você não acabe acidentalmente implementando um loop infinito.

<!--TODO P2: Need a better code example--->

``` C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new TextInput()
                {
                    Prompt = new ActivityTemplate("Give me your favorite color. You can always say cancel to stop this."),
                    Property = "turn.favColor",
                },
                new EditArray()
                {
                    ArrayProperty = "user.favColors",
                    Value = "=turn.favColor",
                    ChangeType = EditArray.ArrayChangeType.Push
                },
                // This is required because TextInput will skip prompt if the property exists - which it will from the previous turn.
                // Alternately you can also set `AlwaysPrompt = true` on the TextInput.
                new DeleteProperty() {
                    Property = "turn.favColor"
                },
                // Repeat dialog step will restart this dialog.
                new RepeatDialog()
            }
        },
        new OnIntent("CancelIntent")
        {
            Actions = new List<Dialog>()
            {
                new SendActivity("You have ${count(user.favColors)} favorite colors - ${join(user.favColors, ',', 'and')}"),
                new EndDialog()
            }
        }
    },
    Recognizer = new RegexRecognizer()
    {
        Intents = new List<IntentPattern>()
        {
            new IntentPattern()
            {
                Intent = "HelpIntent",
                Pattern = "(?i)help"
            },
            new IntentPattern()
            {
                Intent = "CancelIntent",
                Pattern = "(?i)cancel|never mind"
            }
        }
    }
};
```

#### <a name="replacedialog"></a>ReplaceDialog

Inicia uma nova caixa de diálogo e substitui a caixa de diálogo atualmente ativa na pilha por uma nova.

``` C#
// This sample illustrates the use of ReplaceDialog tied to explicit user confirmation
// to switch to a different dialog.

// Create an adaptive dialog.
var getUserName = new AdaptiveDialog("getUserName");
getUserName.Triggers.Add(new OnIntent()
{
    Intent = "getUserName",
    Actions = new List<Dialog>()
    {
        new TextInput()
        {
            Property = "user.name",
            Prompt = new ActivityTemplate("What is your name?")
        },
        new SendActivity("Hello ${user.name}, nice to meet you!")
    }
});

getUserName.Triggers.Add(new OnIntent()
{
    Intent = "GetWeather",
    Actions = new List<Dialog>()
    {
        // confirm with user that they do want to switch to another dialog
        new ChoiceInput()
        {
            Prompt = new ActivityTemplate("Are you sure you want to switch to talk about the weather?"),
            Property = "turn.contoso.getWeather.confirmChoice",
            Choices = new ChoiceSet(new List<Choice>()
            {
                new Choice("Yes"),
                new Choice("No")
            })
        },
        new SwitchCondition()
        {
            Condition = "turn.contoso.getWeather.confirmChoice",
            Cases = new List<Case>()
            {
                // Call ReplaceDialog to switch to a different dialog.
                // BeginDialog will keep current dialog in the stack to be resumed after child dialog ends.
                // ReplaceDialog will remove current dialog from the stack and add the new dialog.
                {
                    Value = "Yes",
                    Actions = new List<Dialog>()
                    {
                        new ReplaceDialog("getWeatherDialog")
                    }
                },
                {
                    Value = "No",
                    Actions = new List<Dialog>()
                    {
                        new EndDialog()
                    }
                }
            }
        }
    }
});
```

#### <a name="update-activity"></a>Atualizar atividade

Atualiza uma atividade enviada anteriormente. Essa ID é retornada da chamada para `SendActivity`.

```C#
new UpdateActivity ()
{
    ActivityId = "id",
    Activity = new ActivityTemplate("updated value")
}
```

#### <a name="delete-activity"></a>Excluir atividade

Exclui uma atividade enviada anteriormente. Requer a ID da atividade anterior.

```C#
new DeleteActivity ()
{
    ActivityId = "id"
}
```

#### <a name="get-activity-members"></a>Obter membros da atividade

Obtém os membros da atividade associada a esse turno e salva a lista em uma propriedade.

```C#
new GetActivityMembers()
{
    Property = "turn.activityMembers"
}
```

#### <a name="get-conversation-members"></a>Obter membros da conversa

Obtém os membros da conversa atual e salva a lista em uma propriedade.

```C#
new GetConversationMembers()
{
    Property = "turn.convMembers"
}
```

#### <a name="editactions"></a>EditActions

Modifica a sequência de ações atual. Especialmente útil ao lidar com uma interrupção. Você pode usar `EditActions` para inserir ou remover ações em qualquer lugar da sequência, incluindo a adição de ações no início ou no final da sequência.

``` C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
{
    Generator = new TemplateEngineLanguageGenerator(),
    Recognizer = new RegexRecognizer()
    {
        Intents = new List<IntentPattern>()
        {
            new IntentPattern()
            {
                Intent = "appendSteps",
                Pattern = "(?i)append"
            },
            new IntentPattern()
            {
                Intent = "insertSteps",
                Pattern = "(?i)insert"
            },
            new IntentPattern()
            {
                Intent = "endSteps",
                Pattern = "(?i)end"
            }
        }
    },
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new ChoiceInput() 
                {
                    Prompt = new ActivityTemplate("What type of EditAction would you like to see?"),
                    Property = "$userChoice",
                    AlwaysPrompt = true,
                    Choices = new ChoiceSet(new List<Choice>()
                    {
                        new Choice("Append actions"),
                        new Choice("Insert actions"),
                        new Choice("End actions"),
                    })
                },
                new SendActivity("This message is after your EditActions choice..")
            }
        },
        new OnIntent()
        {
            Intent = "appendSteps",
            Actions = new List<Dialog>() {
                new SendActivity("In append steps .. Steps specified via EditSteps will be added to the current plan."),
                new EditActions()
                {
                    Actions = new List<Dialog>() {
                        // These steps will be appended to the current set of steps being executed.
                        new SendActivity("I was appended!")
                    },
                    ChangeType = ActionChangeType.AppendActions
                }
            }
        },
        new OnIntent() {
            Intent = "insertSteps",
            Actions = new List<Dialog>() {
                new SendActivity("In insert steps .. "),
                new EditActions()
                {
                    Actions = new List<Dialog>() {
                        // These steps will be inserted before the current steps being executed.
                        new SendActivity("I was inserted")
                    },
                    ChangeType = ActionChangeType.InsertActions
                }
            }
        },
        new OnIntent()
        {
            Intent = "endSteps",
            Actions = new List<Dialog>()
            {
                new SendActivity("In end steps .. "),
                new EditActions()
                {
                    // The current sequence will be ended. This is especially useful if you are looking to end an active interruption.
                    ChangeType = ActionChangeType.EndSequence
                }
            }
        }
    }
};
```

### <a name="manage-properties-examples"></a>Exemplos de gerenciamento de propriedades

#### <a name="setproperty"></a>SetProperty

Usado para definir o valor de uma propriedade na [memória][11]. O valor pode ser uma cadeia de caracteres explícita ou uma expressão. Confira mais em [expressões adaptáveis][10].

``` C#
new SetProperty()
{
    Property = "user.firstName",
    // If the value of user.name is 'Mahatma Gandhi', this sets first name to 'Mahatma'
    Value = "=split(user.name, ' ')[0]"
},
```

#### <a name="setproperties"></a>SetProperties

Inicializa uma ou mais propriedades em uma única ação.

```C#
new SetProperties()
{
    Assignments = new List<PropertyAssignment>()
    {
        new PropertyAssignment()
        {
            Property = "user.name",
            Value = "Vishwac"
        },
        new PropertyAssignment()
        {
            Property = "user.age",
            Value = "=coalesce($age, 30)"
        }
    }
}
```

#### <a name="deleteproperty"></a>DeleteProperty

Remove uma propriedade da [memória][11].

``` C#
new DeleteProperty
{
    Property = "user.firstName"
}
```

#### <a name="deleteproperties"></a>DeleteProperties

Exclui mais de uma propriedade em uma única ação.

```C#
new DeleteProperties()
{
    Properties = new List<StringExpression>()
    {
        new StringExpression("user.name"),
        new StringExpression("user.age")
    }
}
```

#### <a name="editarray"></a>EditArray

Usado para executar operações de edição em uma propriedade de matriz.

``` C#
var addToDoDialog = new AdaptiveDialog("addToDoDialog");
addToDoDialog.Triggers.Add(new OnIntent()
{
    Intent = "addToDo",
    Actions = new List<Dialog>() {
        // Save the userName entity from a recognizer.
        new SaveEntity("dialog.addTodo.title", "@todoTitle"),
        new TextInput()
        {
            Prompt = new ActivityTemplate("What is the title of your todo?"),
            Property = "dialog.addTodo.title"
        },
        // Add the current todo to the todo's list for this user.
        new EditArray()
        {
            ItemsProperty = "user.todos",
            Value = "=dialog.addTodo.title"
            ChangeType = EditArray.ArrayChangeType.Push
        },
        new SendActivity("Ok, I have added ${dialog.addTodo.title} to your todos."),
        new SendActivity("You now have ${count(user.todos)} items in your todo.")
}));
```

### <a name="access-external-resource-examples"></a>Exemplos de acesso a recursos externos

#### <a name="beginskill"></a>BeginSkill

A ação `BeginSkill` inicia o skill especificado, gerencia o encaminhamento de atividades para o skill, recebe atividades do skill e processa o resultado do skill, caso haja algum, quando o skill é encerrado.

<!-- TODO--->

#### <a name="httprequest"></a>HttpRequest

Use para fazer solicitações HTTP para qualquer ponto de extremidade.

<!--TODO P1: Would be good to call out that the properties support data binding. So you can have reference to memory in URI, body etc.
the body property supports adaptive expressions, so it contains the body of the request or an expression that evaluates to the body of the request. --->

``` C#
new HttpRequest()
{
    // Set response from the http request to turn.httpResponse property in memory.
    ResultProperty = "turn.httpResponse",
    Method = HttpRequest.HttpMethod.POST,
    Header = new Dictionary<string,string> (), /* request header */
    Body = { }                                 /* request body */
};

```

#### <a name="emitevent"></a>EmitEvent

Usado para gerar um evento personalizado ao qual o bot pode responder. Você pode controlar o comportamento de propagação no evento gerado para que ele possa ficar contido apenas em sua caixa de diálogo ou emergir da cadeia pai.

```C#
var rootDialog = new AdaptiveDialog(nameof(AdaptiveDialog))
    {
        Generator = new TemplateEngineLanguageGenerator(),
        Triggers = new List<OnCondition>()
        {
            new OnUnknownIntent()
            {
                Actions = new List<Dialog>()
                {
                    new TextInput()
                    {
                        Prompt = new ActivityTemplate("What's your name?"),
                        Property = "user.name",
                        AlwaysPrompt = true,
                        OutputFormat = "toLower(this.value)"
                    },
                    new EmitEvent()
                    {
                        EventName = "contoso.custom",
                        EventValue = "=user.name",
                        BubbleEvent = true,
                    },
                    new SendActivity("Your name is ${user.name}"),
                    new SendActivity("And you are ${$userType}")
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",

                // You can use conditions (expression) to examine value of the event as part of the trigger selection process.
                Condition = "turn.dialogEvent.value && (substring(turn.dialogEvent.value, 0, 1) == 'v')",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "VIP"
                    }
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",

                // You can use conditions (expression) to examine value of the event as part of the trigger selection process.
                Condition = "turn.dialogEvent.value && (substring(turn.dialogEvent.value, 0, 1) == 's')",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "Special"
                    }
                }
            },
            new OnDialogEvent()
            {
                Event = "contoso.custom",
                Actions = new List<Dialog>()
                {
                    new SendActivity("In custom event: '${turn.dialogEvent.name}' with the following value '${turn.dialogEvent.value}'"),
                    new SetProperty()
                    {
                        Property = "$userType",
                        Value = "regular customer"
                    }
                }
            }
        }
    };
```

#### <a name="sign-out-user"></a>Desconectar usuário

Desconecte o usuário atualmente conectado que entrou usando uma [Entrada do `OAuth`][4].

```C#
new SignOutUser()
{
    UserId = "userid",
    ConnectionName = "connection-name"
}
```

#### <a name="codeaction"></a>CodeAction
<!--TODO P1: We should create a separate article that describes how to migrate waterfall steps to code actions. There are a couple of gotchas. (https://github.com/MicrosoftDocs/bot-docs-pr/pull/2115#discussion_r425436817)-->
Como o nome indica, essa ação permite chamar uma parte personalizada do código.

``` C#
// Example customCodeStep method
private async Task<DialogTurnResult> CodeActionSampleFn(DialogContext dc, System.Object options)
{
    await dc.Context.SendActivityAsync(MessageFactory.Text("In custom code step"));
    // This code step decided to just return the input payload as the result.
    return dc.EndDialogAsync(options)
}

// Adaptive dialog that calls a code step.
var rootDialog = new AdaptiveDialog(rootDialogName) {
    Triggers = new List<OnCondition>()
    {
        new OnUnknownIntent()
        {
            Actions = new List<Dialog>()
            {
                new CodeAction(CodeActionSampleFn),
                new SendActivity("After code step")
            }
        }
    }
};
```

### <a name="debugging-option-examples"></a>Exemplos de opção de depuração

#### <a name="traceactivity"></a>TraceActivity

Envia uma atividade de rastreamento com o payload que você especificar.

> [!NOTE]
> As atividades de rastreamento podem ser capturadas como transcrições e são enviadas somente ao emulador para ajudar na depuração.

``` C#
new TraceActivity()
{
    // Name of the trace event.
    Name = "contoso.TraceActivity",
    ValueType = "Object",
    // Property from memory to include in the trace
    ValueProperty = "user"
}
```

#### <a name="log-action"></a>Ação de log

Grava no console e, opcionalmente, envia a mensagem como uma atividade de rastreamento.

``` C#
new LogAction()
{
    Text = new TextTemplate("Hello"),
    // Automatically sends the provided text as a trace activity
    TraceActivity = true
}
```

## <a name="additional-information"></a>Informações adicionais

* Confira mais informações sobre as ações específicas para a coleta de entradas de usuários no artigo [Solicitar entrada do usuário usando caixas de diálogo adaptáveis][7].
* Confira mais sobre expressões adaptáveis no artigo [Expressões adaptáveis][10].

[1]:bot-builder-adaptive-dialog-introduction.md
[2]:bot-builder-concept-adaptive-dialog-triggers.md
[3]:https://www.qnamaker.ai/
[4]:bot-builder-concept-adaptive-dialog-inputs.md#oauthinput
[5]:bot-builder-concept-dialog.md
[6]:bot-builder-concept-adaptive-dialog-inputs.md#interruptions
[7]:bot-builder-concept-adaptive-dialog-inputs.md
[8]:bot-builder-concept-adaptive-dialog-triggers.md#custom-events
[9]:bot-builder-concept-adaptive-dialog-generators.md
[10]:bot-builder-concept-adaptive-expressions.md
[11]:bot-builder-concept-adaptive-dialog-memory-states.md
[12]:https://www.qnamaker.ai/
[13]:https://github.com/microsoft/botbuilder-samples
