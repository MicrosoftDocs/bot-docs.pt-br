---
title: Migrar um bot v3 do .NET para um bot v4 do .NET Framework – Serviço de Bot
description: Pegamos um bot v3 do .NET existente e o migramos para o SDK v4 usando o mesmo projeto.
keywords: migração de bot, formflow, diálogos, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ea6230ef311955e2306927e880f8b74b25fbd895
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95452380"
---
# <a name="migrate-a-net-v3-bot-to-a-net-framework-v4-bot"></a>Migrar um bot v3 do .NET para um bot v4 do .NET Framework

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Neste artigo, converteremos o [v3 ContosoHelpdeskChatBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V3) em um bot v4 _sem converter o tipo de projeto_. Ele permanecerá um projeto do .NET Framework.
Essa conversão é dividida entre estas etapas:

1. Atualizar e instalar pacotes NuGet
1. Atualizar o arquivo Global.asax.cs
1. Atualizar a classe MessagesController
1. Converter os diálogos

O resultado dessa conversão é o [ContosoHelpdeskChatBot v4 do .NET Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework).
Para migrar para um bot v4 do .NET Core no novo projeto, consulte [Migrar um bot v3 do .NET para um bot v4 do .NET Core](conversion-core.md).

O SDK da v4 do Bot Framework é baseado na mesma API REST subjacente que o SDK da v3. No entanto, a v4 do SDK é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle aos desenvolvedores sobre seus bots. Alterações importantes no SDK incluem:

- O estado é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade.
- Alteração da configuração do manipulador de turnos e da passagem de suas atividades.
- Não há mais elementos pontuáveis. Você pode buscar comandos “globais” no manipulador de turnos antes de passar o controle para seus diálogos.
- Uma nova biblioteca de diálogos que é muito diferente da versão anterior. Você precisará converter os diálogos antigos para o novo sistema de diálogo usando os diálogos do componente e em cascata e a implementação da comunidade de diálogos Formflow para v4.

Para obter mais informações sobre alterações específicas, confira [as diferenças entre a v3 e a v4 do SDK do .NET](migration-about.md).

## <a name="update-and-install-nuget-packages"></a>Atualizar e instalar pacotes NuGet

1. Atualize o **Microsoft.Bot.Builder.Azure** e o  **Microsoft.Bot.Builder.Integration.AspNet.WebApi** para a versão mais recente estável.

    Isso também atualizará os pacotes **Microsoft.Bot.Builder** e **Microsoft.Bot.Connector**, já que se tratam de dependências.

1. Exclua o pacote **Microsoft.Bot.Builder.History**. Ele não é parte do SDK da v4.
1. Adicione **Autofac.WebApi2**.

    Usaremos isso para ajudar na injeção de dependência no ASP.NET.

1. Adicione **Bot.Builder.Community.Dialogs.Formflow**.

    Trata-se de uma biblioteca de comunidade para a criação de diálogos v4 de arquivos de definição do Formflow v3. Ela conta com **Microsoft.Bot.Builder.Dialogs** como uma de suas dependências, assim, isso também é instalado para nós.

> [!TIP]
> Se o projeto for destinado ao .NET Framework 4.6, você precisará atualizá-lo para 4.6.1 ou posterior, pois o **Bot.Builder.Community.Dialogs.Formflow** é uma biblioteca .NET Standard 2.0.
> Para obter mais informações, confira [Suporte à implementação do .NET](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

Se você compilar neste momento, receberá erros do compilador. Você pode ignorá-los. Quando terminarmos nossa conversão, teremos um código funcional.

## <a name="update-your-globalasaxcs-file"></a>Atualizar o arquivo Global.asax.cs

Parte do scaffolding foi alterada, e temos de configurar as partes da infraestrutura do [gerenciamento de estado](../bot-builder-concept-state.md) por conta própria na v4. Por exemplo, a v4 usa um adaptador de bot para manipular a autenticação e as atividades de encaminhamento ao seu código de bot, e declaramos nossas propriedades de estado com antecedência.

Vamos criar uma propriedade de estado para `DialogState`, que agora é necessária para oferecer suporte ao diálogo na v4. Vamos usar injeção de dependência para obter as informações necessárias para o controlador e o código do bot.

Em **Global.asax.cs**:

1. Atualize as instruções `using`:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=4-13)]

1. Remova essas linhas do método `Application_Start`:  
    [!code-csharp[Removed lines](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V3/ContosoHelpdeskChatBot/Global.asax.cs?range=23-24)]

    E insira esta linha:  
    [!code-csharp[Reference BotConfig.Register](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=22)]

1. Remova o método `RegisterBotModules`, que não é mais referenciado.

1. Substitua o método `BotConfig.UpdateConversationContainer` por este método `BotConfig.Register`, no qual registraremos os objetos de que precisamos para dar suporte à injeção de dependência. Este bot não usa o estado de _user_ nem de _private conversation_, portanto, criamos apenas o objeto de gerenciamento do estado “conversation”.  
    [!code-csharp[Define BotConfig.Register](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=31-61)]

## <a name="update-your-messagescontroller-class"></a>Atualizar a classe MessagesController

Aqui é onde o bot inicia um turno no v4, então, isso precisa mudar muito. Exceto para o manipulador de turnos do bot em si, grande parte disso pode ser pensada como texto clichê. No seu arquivo **Controllers\MessagesController.cs**:

1. Atualize as instruções `using`:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=4-8)]

1. Remova o atributo `[BotAuthentication]` da classe. Na v4, o adaptador do bot lidará com a autenticação.

1. Adicione esses campos e um construtor para inicializá-los. ASP.NET e Autofac usam a injeção de dependência para obter os valores de parâmetros. (Registramos os objetos “adapter” e “bot” em **Global.asax.cs** para dar suporte a isso.)  
    [!code-csharp[Fields and constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=14-21)]

1. Substitua o corpo do método `Post`. Usamos o adaptador para chamar o loop de mensagem do nosso bot (manipulador de turno).  
    [!code-csharp[Post method](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=23-31)]

### <a name="delete-the-cancelscorable-and-globalmessagehandlersbotmodule-classes"></a>Excluir as classes CancelScorable e GlobalMessageHandlersBotModule

Como os pontuáveis não existem no v4 e já atualizamos o manipulador de turnos para reagir a uma mensagem `cancel`, podemos excluir as classes `CancelScorable` (em **Dialogs\CancelScorable.cs**) e `GlobalMessageHandlersBotModule`.

## <a name="create-your-bot-class"></a>Criar sua classe de bot

No v4, a lógica de loop de mensagem ou do manipulador de turno está, fundamentalmente, em um arquivo de bot. Estamos derivando a partir de `ActivityHandler`, que define manipuladores para tipos comuns de atividades.

1. Crie um arquivo **Bots\DialogBots.cs**.

1. Atualize as instruções `using`:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=4-8)]

1. Derive `DialogBot` de `ActivityHandler`e adicione um parâmetro genérico para a caixa de diálogo.  
    [!code-csharp[Class definition](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=19)]

1. Adicione esses campos e um construtor para inicializá-los. Novamente, ASP.NET e Autofac usam a injeção de dependência para obter os valores de parâmetros.  
    [!code-csharp[Fields and constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=21-28)]

1. Substitua `OnMessageActivityAsync` para invocar nossa caixa de diálogo principal. (Vamos definir o método de extensão `Run` em breve.)  
    [!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=38-47)]

1. Substitua `OnTurnAsync` para salvar o estado de conversa no final do turno. No V4, precisamos fazer isso explicitamente para gravar o estado na camada de persistência. O método `ActivityHandler.OnTurnAsync` chama métodos específicos do manipulador de atividades com base no tipo de atividade recebida, portanto, salvamos o estado após a chamada do método base.  
    [!code-csharp[OnTurnAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=30-36)]

### <a name="create-the-run-extension-method"></a>Criar o método de extensão Run

Estamos criando um método de extensão para consolidar o código necessário para executar uma caixa de diálogo de componente bare do nosso bot.

Criar um arquivo **DialogExtensions.cs** e implementar um método de extensão `Run`.  
[!code-csharp[The extension](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/DialogExtensions.cs?range=4-41)]

## <a name="convert-your-dialogs"></a>Converter os diálogos

Faremos muitas alterações nos diálogos originais para migrá-los para o SDK da v4. Não se preocupe com os erros de compilador por enquanto. Eles serão resolvidos depois de concluir a conversão.
Com o intuito de não fazer mais modificações do que o necessário no código original, alguns avisos do compilador permanecerão depois da conclusão da migração.

Todos os nossos diálogos serão derivados de `ComponentDialog`, em vez de implementar a interface `IDialog<object>` da v3.

Esse bot tem quatro diálogos que precisam ser convertidos:

| caixa de diálogo | Descrição |
|---|---|
| [RootDialog](#update-the-root-dialog) | Apresenta opções e inicia os outros diálogos. |
| [InstallAppDialog](#update-the-install-app-dialog) | Manipula as solicitações para instalar um aplicativo em um computador. |
| [LocalAdminDialog](#update-the-local-admin-dialog) | Manipula as solicitações para direitos de administrador do computador local. |
| [ResetPasswordDialog](#update-the-reset-password-dialog) | Manipula as solicitações para redefinir sua senha. |

Elas coletam entradas, mas não executam nenhuma dessas operações em seu computador.

### <a name="make-solution-wide-dialog-changes"></a>Fazer alterações de diálogo em todas as soluções

1. Na solução inteira, substitua todas as ocorrências de `IDialog<object>` por `ComponentDialog`.
1. Na solução inteira, substitua todas as ocorrências de `IDialogContext` por `DialogContext`.
1. Em cada classe de diálogo, remova o atributo `[Serializable]`.

O fluxo de controle e o sistema de mensagens dentro dos diálogos não são mais manipulados da mesma forma, portanto, será preciso analisar como converter cada diálogo.

| Operação | Código da v3 | Código da v4 |
| :--- | :--- | :--- |
| Manipula o início do diálogo | Implementa `IDialog.StartAsync` | Torna essa a primeira etapa de um diálogo em cascata ou implementar `Dialog.BeginDialogAsync` |
| Manipula a continuação do diálogo | Chama `IDialogContext.Wait` | Adiciona etapas a um diálogo em cascata ou implementar `Dialog.ContinueDialogAsync` |
| Envia uma mensagem para o usuário | Chama `IDialogContext.PostAsync` | Chama `ITurnContext.SendActivityAsync` |
| Inicia um diálogo filho | Chama `IDialogContext.Call` | Chama `DialogContext.BeginDialogAsync` |
| Sinaliza que o diálogo atual foi concluído | Chama `IDialogContext.Done` | Chama `DialogContext.EndDialogAsync` |
| Obtém a entrada do usuário | Usa um parâmetro `IAwaitable<IMessageActivity>` | Usa um prompt de dentro de uma cascata ou usa `ITurnContext.Activity` |

Observações sobre o código da v4:

- Dentro do código de diálogo, use a propriedade `DialogContext.Context` para obter o contexto de turno atual.
- As etapas de cascata têm um parâmetro `WaterfallStepContext`, que deriva de `DialogContext`.
- Todas as classes concretas de diálogo e de prompt derivam da classe `Dialog` abstrata.
- Você atribui uma ID ao criar um diálogo do componente. Cada diálogo presente em um conjunto de diálogos precisa ser atribuído a uma ID exclusiva dentro desse conjunto.

### <a name="update-the-root-dialog"></a>Atualizar o diálogo raiz

Nesse bot, o diálogo raiz solicita que o usuário escolha uma das opções de um conjunto e depois inicie um diálogo filho com base nessa escolha. Em seguida, um loop é executado durante todo o tempo de vida da conversa.

- É possível configurar o fluxo principal como um diálogo em cascata, que é um novo conceito no SDK da v4. Ele será executado por meio de um conjunto fixo de etapas em ordem. Para obter mais informações, confira [Implementar fluxo de conversa sequencial](~/v4sdk/bot-builder-dialog-manage-conversation-flow.md).
- O processo de prompt agora é manipulado por meio de classes de prompt, que são diálogos filho curtos que solicitam entradas, fazem certo processamento e validação mínimos e retornam um valor. Para obter mais informações, confira [coletar entrada do usuário usando um prompt de diálogo](~/v4sdk/bot-builder-prompts.md).

No arquivo **Dialogs/RootDialog.cs**:

1. Atualize as instruções `using`:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=4-10)]

1. É preciso converter as opções `HelpdeskOptions` de uma lista de cadeias de caracteres para uma lista de opções. Isso será usado com um prompt de escolha, que aceitará o número da opção (na lista), o valor de opção ou qualquer um dos sinônimos da opção como uma entrada válida.  
    [!code-csharp[HelpDeskOptions](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=28-33)]

1. Adicione um construtor. O código faz o seguinte:
   - Uma ID é atribuída a cada instância de um diálogo quando ele é criado. A ID do diálogo faz parte do conjunto ao qual o diálogo está sendo adicionado. Lembre-se de que o bot foi inicializado com um objeto dialog na classe `MessageController`. Cada `ComponentDialog` tem seu próprio conjunto de diálogos interno, com seu próprio conjunto de IDs de diálogo.
   - Ele adiciona outros diálogos, incluindo o prompt de escolha, como diálogos filho. Aqui, estamos simplesmente usando o nome de classe para cada ID de diálogo.
   - Ele define um diálogo em cascata de três etapas. Implementaremos essas etapas em alguns instantes.
     - Primeiro, o diálogo solicitará que o usuário escolha uma tarefa a ser realizada.
     - Em seguida, iniciará o diálogo filho associado a essa escolha.
     - Por fim, se reiniciará.
   - Cada etapa da cascata é um delegado, e as implementaremos a seguir, obtendo o código existente do diálogo original sempre que possível.
   - Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_. Por padrão, esse é o primeiro diálogo filho adicionado a um diálogo de componente. Estamos explicitamente definindo a propriedade `InitialDialogId`, o que significa que o principal diálogo em cascata não precisa ser o primeiro adicionado ao conjunto. Por exemplo, caso prefira, você poderia adicionar prompts primeiro sem causar um problema de tempo de execução.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=35-49)]

1. Podemos excluir o método `StartAsync`. Quando um diálogo de componente for iniciado, ele iniciará seu diálogo _inicial_ automaticamente. Nesse caso, é o diálogo em cascata que definimos no construtor. Ele também inicia automaticamente em sua primeira etapa.

1. Nós excluiremos os métodos `MessageReceivedAsync` e `ShowOptions` e os substituiremos pela primeira etapa da nossa cascata. Esses dois métodos saudaram o usuário e solicitaram que ele escolhesse uma das opções disponíveis.
   - Aqui você pode ver que a lista de opções e as mensagens de saudação e erro são fornecidas como opções na chamada para nosso prompt de escolha.
   - Não precisamos especificar o próximo método a ser chamado no diálogo porque a cascata continuará até a próxima etapa, quando o prompt de escolha é concluído.
   - O prompt de escolha fará um loop até receber uma entrada válida ou até a pilha inteira dede diálogo ser cancelada.  
    [!code-csharp[PromptForOptionsAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=51-65)]

1. Podemos substituir `OnOptionSelected` pela segunda etapa da nossa cascata. Ainda iniciaremos um diálogo filho com base na entrada do usuário.
   - O prompt de escolha retorna um valor `FoundChoice`. Isso é mostrado na propriedade de contexto `Result` da etapa. A pilha de diálogo trata todos os valores retornados como objetos. Se o valor retornado for um dos seus diálogos, você saberá qual é o tipo de valor do objeto. Para obter uma lista com o que cada tipo de prompt retorna, confira [tipos de prompt](../bot-builder-concept-waterfall-dialogs.md#prompt-types).
   - Como o prompt de escolha não lançará uma exceção, nós podemos remover o bloco try-catch.
   - Precisamos adicionar uma passagem para que esse método sempre retorne um valor apropriado. Esse código nunca deve ser atingido, mas se o for, ele permitirá que o diálogo “falhe graciosamente”.  
    [!code-csharp[ShowChildDialogAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=67-102)]

1. Por fim, substitua o antigo método `ResumeAfterOptionDialog` pela última etapa da nossa cascata.
    - Em vez encerrar o diálogo e retornar o número do tíquete como fizemos no diálogo original, estamos reiniciando a cascata ao substituir a pilha da instância original por uma nova instância de si mesmo. Podemos fazer isso, uma vez que o aplicativo original sempre ignorou o valor retornado (o número de tíquete) e reiniciou o diálogo raiz.  
    [!code-csharp[ResumeAfterAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=104-138)]

### <a name="update-the-install-app-dialog"></a>Atualizar o diálogo de instalação do aplicativo

O diálogo de instalação do aplicativo executa algumas tarefas lógicas, as quais definiremos como um diálogo em cascata de quatro etapas. A forma como você fatora o código existente em etapas de cascata é um exercício lógico para cada diálogo. Em cada etapa, é anotado o método original do qual veio o código.

1. Solicita uma cadeia de caracteres de pesquisa ao usuário.
1. Consulta um banco de dados em busca de correspondências possíveis.
   - Se houver uma ocorrência, selecione-a e continue.
   - Se houver várias ocorrências, o usuário será solicitado a escolher uma.
   - Se não houver nenhuma ocorrência, o diálogo será fechado.
1. Solicita que o usuário informe um computador no qual instalar o aplicativo.
1. Grava as informações em um banco de dados e envia uma mensagem de confirmação.

No arquivo **Dialogs/InstallAppDialog.cs**:

1. Atualize as instruções `using`:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=4-11)]

1. Defina uma constante para a chave que usaremos para rastrear as informações coletadas.  
    [!code-csharp[Key ID](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=17-18)]

1. Adicione um construtor e inicialize o conjunto de diálogos do componente.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=20-33)]

1. Podemos substituir `StartAsync` pela primeira etapa da nossa cascata.
    - Nós mesmos precisamos gerenciar o estado, portanto, vamos rastrear o objeto do aplicativo de instalação no estado de diálogo.
    - A mensagem solicitando entradas do usuário se torna uma opção na chamada ao prompt.  
    [!code-csharp[GetSearchTermAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=35-50)]

1. Podemos substituir `appNameAsync` e `multipleAppsAsync` pela segunda etapa da nossa cascata.
    - Agora estamos obtendo o resultado do prompt em vez de apenas observar a última mensagem do usuário.
    - A consulta de banco de dados e as instruções if são organizadas da mesma forma que em `appNameAsync`. O código de cada bloco da instrução foi atualizado para trabalhar com diálogos da v4.
        - Se tivermos uma ocorrência, atualizaremos o estado do diálogo e seguiremos para a próxima etapa.
        - Se tivermos várias ocorrências, usaremos o prompt de nossa escolha para solicitar que o usuário escolha uma das opções da lista. Isso significa que podemos simplesmente excluir o `multipleAppsAsync`.
        - Se não tivermos nenhuma ocorrência, encerraremos esse diálogo e retornaremos nulos para o diálogo raiz.  
    [!code-csharp[ResolveAppNameAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=52-91)]

1. O `appNameAsync` também solicitou o nome do computador do usuário depois que ele resolveu a consulta. Vamos capturar essa parte da lógica na próxima etapa da cascata.
    - Novamente, na v4, nós mesmos temos de gerenciar o estado. A única dificuldade é que podemos chegar a essa etapa por dois branches de lógica diferentes na etapa anterior.
    - Solicitaremos ao usuário um nome de computador usando o mesmo prompt de texto de antes, mas fornecendo opções diferentes desta vez.  
    [!code-csharp[GetMachineNameAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=93-114)]

1. A lógica de `machineNameAsync` é encapsulada na etapa final da nossa cascata.
    - Recuperamos o nome do computador do resultado do prompt de texto e atualizamos o estado do diálogo.
    - Estamos removendo a chamada para atualizar o banco de dados porque o código de suporte está em um projeto diferente.
    - Depois, enviaremos a mensagem de êxito para o usuário e finalizaremos o diálogo.  
    [!code-csharp[SubmitRequestAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=116-135)]

1. Para simular a chamada de banco de dados, nós simulamos `getAppsAsync` para consultar uma lista estática em vez do banco de dados.  
    [!code-csharp[GetAppsAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=137-200)]

### <a name="update-the-local-admin-dialog"></a>Atualizar o diálogo do administrador local

Na v3, esse diálogo saudou o usuário, iniciou o diálogo Formflow e salvou o resultado em um banco de dados. Isso se traduz facilmente em uma cascata de duas etapas.

1. Atualize as instruções `using`. Observe que esse diálogo inclui um diálogo de Formflow da v3. Na v4, podemos usar a biblioteca de Formflow da comunidade.  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=4-8)]

1. Podemos remover a propriedade de instância para `LocalAdmin`, pois o resultado estará disponível no estado do diálogo.

1. Adicione um construtor e inicialize o conjunto de diálogos do componente. O diálogo Formflow é criado da mesma forma. Estamos apenas adicionando-o ao conjunto do diálogo do nosso componente no construtor.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=14-23)]

1. Podemos substituir `StartAsync` pela primeira etapa da nossa cascata. Já criamos o Formflow no construtor, e as outras duas instruções se traduzem nisso. Observe que `FormBuilder` atribui o nome do tipo do modelo como a ID da caixa de diálogo gerada, que é `LocalAdminPrompt` para este modelo.  
    [!code-csharp[BeginFormflowAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=25-35)]

1. Podemos substituir `ResumeAfterLocalAdminFormDialog` pela segunda etapa da nossa cascata. Precisamos obter o valor retornado do contexto da etapa em vez de uma propriedade de instância.  
    [!code-csharp[SaveResultAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=37-50)]

1. O `BuildLocalAdminForm` permanece praticamente o mesmo, com a diferença que o Formflow não atualiza a propriedade da instância.  
    [!code-csharp[BuildLocalAdminForm](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=52-76)]

### <a name="update-the-reset-password-dialog"></a>Atualizar o diálogo de redefinição de senha

Na v3, esse diálogo saudou o usuário, autorizou o usuário com um código-chave, falhou ou iniciou o diálogo Formflow e, em seguida, redefiniu a senha. Isso ainda se traduz bem em uma cascata.

1. Atualize as instruções `using`. Observe que esse diálogo inclui um diálogo de Formflow da v3. Na v4, podemos usar a biblioteca de Formflow da comunidade.  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=4-9)]

1. Adicione um construtor e inicialize o conjunto de diálogos do componente. O diálogo Formflow é criado da mesma forma. Estamos apenas adicionando-o ao conjunto do diálogo do nosso componente no construtor.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=15-25)]

1. Podemos substituir `StartAsync` pela primeira etapa da nossa cascata. Já criamos o Formflow no construtor. Caso contrário, estamos mantendo a mesma lógica, traduzindo apenas as chamadas da v3 em seus equivalentes da v4.  
    [!code-csharp[BeginFormflowAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=27-45)]

1. O `sendPassCode` é deixado principalmente como um exercício. O código original é transformado em comentário, e o método simplesmente retorna true. Além disso, podemos remover o endereço de email novamente, pois ele não foi usado no bot original.  
    [!code-csharp[SendPassCode](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=47-81)]

1. `BuildResetPasswordForm` não tem alterações.

1. Podemos substituir `ResumeAfterResetPasswordFormDialog` pela segunda etapa da nossa cascata e obteremos o valor retornado do contexto da etapa. Removemos o endereço de email com o qual o diálogo original não fez nada e fornecemos um resultado fictício em vez de consultar o banco de dados. Estamos mantendo a mesma lógica, traduzindo apenas as chamadas da v3 em seus equivalentes da v4.  
    [!code-csharp[ProcessRequestAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=90-113)]

### <a name="update-models-as-necessary"></a>Atualizar modelos conforme o necessário

Precisamos atualizar as instruções `using` em alguns dos modelos que referenciam à biblioteca Formflow.

1. Em `LocalAdminPrompt`, altere-os para isto:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Models/LocalAdminPrompt.cs?range=4)]

1. Em `ResetPasswordPrompt`, altere-os para isto:  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Models/ResetPasswordPrompt.cs?range=4-5)]

## <a name="update-webconfig"></a>Atualizar Web. config

Transforme as chaves de configuração em comentários para **MicrosoftAppId** e **MicrosoftAppPassword**. Isso permitirá que você depure o bot localmente sem a necessidade de fornecer esses valores ao emulador.

## <a name="run-and-test-your-bot-in-the-emulator"></a>Executar e testar seu bot no emulador

Neste ponto, devemos ser capazes de executar o bot localmente no IIS e anexar a ele com o emulador.

1. Executar o bot no IIS.
1. Inicie o emulador e conecte-se ao ponto de extremidade do bot (por exemplo, **http://localhost:3978/api/messages** ).
    - Se esta for a primeira vez que você estiver executando o bot, clique em **Arquivo > Novo Bot** e siga as instruções na tela. Caso contrário, clique em **Arquivo > Abrir Bot** para abrir um bot existente.
    - Verifique as configurações da porta novamente na configuração. Por exemplo, se o bot foi aberto no navegador em `http://localhost:3979/` e depois no emulador, defina o ponto de extremidade do bot como `http://localhost:3979/api/messages`.
1. Todos os quatro diálogos devem funcionar, e você pode definir pontos de interrupção nas etapas de cascata para verificar qual é o estado e o contexto de diálogo nesses pontos.

## <a name="additional-resources"></a>Recursos adicionais

Tópicos conceituais da v4:

- [Como funcionam os bots](../bot-builder-basics.md)
- [Gerenciar estado](../bot-builder-concept-state.md)
- [Biblioteca de caixas de diálogo](../bot-builder-concept-dialog.md)

Tópicos de instruções da v4:

- [Enviar e receber mensagens de texto](../bot-builder-howto-send-messages.md)
- [Salvar dados de usuário e de conversa](../bot-builder-howto-v4-state.md)
- [Implementar o fluxo de conversa sequencial](../bot-builder-dialog-manage-conversation-flow.md)
- [Depurar com o emulador](../../bot-service-debug-emulator.md)
- [Adicionar telemetria ao seu bot](../bot-builder-telemetry.md)
