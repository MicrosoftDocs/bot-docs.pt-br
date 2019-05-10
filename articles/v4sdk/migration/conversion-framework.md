---
title: Migrar um bot existente dentro do mesmo projeto do .NET Framework | Microsoft Docs
description: Pegamos um bot v3 existente e o migramos para o SDK v4 usando o mesmo projeto.
keywords: migração de bot, formflow, diálogos, bot v3
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 02/11/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ea6b859761a3bc8c1424d50d8bad0b7f1f50e86d
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033457"
---
# <a name="migrate-a-net-sdk-v3-bot-to-v4"></a>Migrar um bot do SDK do .NET v3 para v4

Neste artigo, converteremos o v3 [ContosoHelpdeskChatBot](https://github.com/Microsoft/intelligent-apps/tree/master/ContosoHelpdeskChatBot/ContosoHelpdeskChatBot) em um bot v4 _sem converter o tipo de projeto_. Ele permanecerá um projeto do .NET Framework.
Essa conversão é dividida entre estas etapas:

1. Atualizar e instalar pacotes NuGet
1. Atualizar o arquivo Global.asax.cs
1. Atualizar a classe MessagesController
1. Converter os diálogos

<!--TODO: Link to the converted bot...[ContosoHelpdeskChatBot](https://github.com/EricDahlvang/intelligent-apps/tree/v4netframework/ContosoHelpdeskChatBot).-->

O SDK da v4 do Bot Framework é baseado na mesma API REST subjacente que o SDK da v3. No entanto, a v4 do SDK é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle aos desenvolvedores sobre seus bots. Alterações importantes no SDK incluem:

- O estado é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade.
- Alteração da configuração do manipulador de turnos e da passagem de suas atividades.
- Não há mais pontuáveis. Você pode buscar comandos “globais” no manipulador de turnos antes de passar o controle para seus diálogos.
- Uma nova biblioteca de diálogos que é muito diferente da versão anterior. Você precisará converter os diálogos antigos para o novo sistema de diálogo usando os diálogos do componente e em cascata e a implementação da comunidade de diálogos Formflow para v4.

Para obter mais informações sobre alterações específicas, confira [as diferenças entre a v3 e a v4 do SDK do .NET](migration-about.md).

## <a name="update-and-install-nuget-packages"></a>Atualizar e instalar pacotes NuGet

1. Atualize o **Microsoft.Bot.Builder.Azure** para a versão estável mais recente.

    Isso também atualizará os pacotes **Microsoft.Bot.Builder** e **Microsoft.Bot.Connector**, já que se tratam de dependências.

1. Exclua o pacote **Microsoft.Bot.Builder.History**. Ele não é parte do SDK da v4.
1. Adicione **Autofac.WebApi2**.

    Usaremos isso para ajudar na injeção de dependência no ASP.NET.

1. Adicione **Bot.Builder.Community.Dialogs.Formflow**.

    Trata-se de uma biblioteca de comunidade para a criação de diálogos v4 de arquivos de definição do Formflow v3. Ela conta com **Microsoft.Bot.Builder.Dialogs** como uma de suas dependências, assim, isso também é instalado para nós.

Se você compilar neste momento, receberá erros do compilador. Você pode ignorá-los. Quando terminarmos nossa conversão, teremos um código funcional.

<!--
## Add a BotDataBag class

This file will contain wrappers to add a v3-style **IBotDataBag** to make dialog conversion simpler.

```csharp
using System.Collections.Generic;

namespace ContosoHelpdeskChatBot
{
    public class BotDataBag : Dictionary<string, object>, IBotDataBag
    {
        public bool RemoveValue(string key)
        {
            return base.Remove(key);
        }

        public void SetValue<T>(string key, T value)
        {
            this[key] = value;
        }

        public bool TryGetValue<T>(string key, out T value)
        {
            if (!ContainsKey(key))
            {
                value = default(T);
                return false;
            }

            value = (T)this[key];

            return true;
        }
    }

    public interface IBotDataBag
    {
        int Count { get; }

        void Clear();

        bool ContainsKey(string key);

        bool RemoveValue(string key);

        void SetValue<T>(string key, T value);

        bool TryGetValue<T>(string key, out T value);
    }
}
```
-->

## <a name="update-your-globalasaxcs-file"></a>Atualizar o arquivo Global.asax.cs

Parte do scaffolding foi alterada, e temos de configurar as partes da infraestrutura do [gerenciamento de estado](../bot-builder-concept-state.md) por conta própria na v4. Por exemplo, a v4 usa um adaptador de bot para manipular a autenticação e as atividades de encaminhamento ao seu código de bot, e declaramos nossas propriedades de estado com antecedência.

Vamos criar uma propriedade de estado para `DialogState`, que agora é necessária para oferecer suporte ao diálogo na v4. Vamos usar injeção de dependência para obter as informações necessárias para o controlador e o código do bot.

Em **Global.asax.cs**:

1. Atualize as instruções `using`:
    ```csharp
    using System.Configuration;
    using System.Reflection;
    using System.Web.Http;
    using Autofac;
    using Autofac.Integration.WebApi;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Connector.Authentication;
    ```
1. Remova estas linhas do método **Application_Start**.
    ```csharp
    BotConfig.UpdateConversationContainer();
    this.RegisterBotModules();
    ```
    E insira esta linha.
    ```csharp
    GlobalConfiguration.Configure(BotConfig.Register);
    ```
1. Remova o método **RegisterBotModules**, que não é mais referenciado.
1. Substitua o método **BotConfig.UpdateConversationContainer** pelo método **BotConfig.Register**, no qual registraremos os objetos necessários para dar suporte à injeção de dependência.
    > [!NOTE]
    > Esse bot não está usando os estados de _usuário_ ou _conversa privada_. As linhas para incluí-los são transformadas em comentários aqui.
    ```csharp
    public static void Register(HttpConfiguration config)
    {
        ContainerBuilder builder = new ContainerBuilder();
        builder.RegisterApiControllers(Assembly.GetExecutingAssembly());

        SimpleCredentialProvider credentialProvider = new SimpleCredentialProvider(
            ConfigurationManager.AppSettings[MicrosoftAppCredentials.MicrosoftAppIdKey],
            ConfigurationManager.AppSettings[MicrosoftAppCredentials.MicrosoftAppPasswordKey]);

        builder.RegisterInstance(credentialProvider).As<ICredentialProvider>();

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, everything stored in memory will be gone.
        IStorage dataStore = new MemoryStorage();

        // Create Conversation State object.
        // The Conversation State object is where we persist anything at the conversation-scope.
        ConversationState conversationState = new ConversationState(dataStore);
        builder.RegisterInstance(conversationState).As<ConversationState>();

        //var userState = new UserState(dataStore);
        //var privateConversationState = new PrivateConversationState(dataStore);

        // Create the dialog state property acccessor.
        IStatePropertyAccessor<DialogState> dialogStateAccessor
            = conversationState.CreateProperty<DialogState>(nameof(DialogState));
        builder.RegisterInstance(dialogStateAccessor).As<IStatePropertyAccessor<DialogState>>();

        IContainer container = builder.Build();
        AutofacWebApiDependencyResolver resolver = new AutofacWebApiDependencyResolver(container);
        config.DependencyResolver = resolver;
    }
    ```

## <a name="update-your-messagescontroller-class"></a>Atualizar a classe MessagesController

Aqui é onde o manipulador de turnos é executado na v4, assim, ele precisa mudar muito. Exceto para o manipulador de turnos em si, grande parte disso pode ser pensada como texto clichê. No seu arquivo **Controllers\MessagesController.cs**:

1. Atualize as instruções `using`:
    ```csharp
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Bot.Builder.Community.Dialogs.FormFlow;
    using ContosoHelpdeskChatBot.Dialogs;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Connector.Authentication;
    using Microsoft.Bot.Schema;
    ```
1. Remova o atributo `[BotAuthentication]` da classe. Na v4, o adaptador do bot lidará com a autenticação.
1. Adicione estes campos. O **ConversationState** gerenciará o estado do escopo para a conversa, sendo necessário o **IStatePropertyAccessor\<DialogState >** para dar suporte aos diálogos na v4.
    ```csharp
    private readonly ConversationState _conversationState;
    private readonly ICredentialProvider _credentialProvider;
    private readonly IStatePropertyAccessor<DialogState> _dialogData;

    private readonly DialogSet _dialogs;
    ```
1. Adicione um construtor para:
    - Inicializar os campos da instância.
    - Usar a injeção de dependência no ASP.NET para obter os valores de parâmetros. (Registramos instâncias dessas classes em **Global.asax.cs** para dar suporte a isso.)
    - Criar e inicializar um conjunto de diálogos, no qual podemos criar um contexto de diálogo. (É necessário fazer isso explicitamente na v4.)
    ```csharp
    public MessagesController(
        ConversationState conversationState,
        ICredentialProvider credentialProvider,
        IStatePropertyAccessor<DialogState> dialogData)
    {
        _conversationState = conversationState;
        _dialogData = dialogData;
        _credentialProvider = credentialProvider;

        _dialogs = new DialogSet(dialogData);
        _dialogs.Add(new RootDialog(nameof(RootDialog)));
    }
    ```
1. Substitua o corpo do método **Post**. Aqui é onde vamos criar nosso adaptador e usá-lo para chamar nosso loop de mensagens (manipulador de turnos). Estamos usando o `SaveChangesAsync` no final de cada turno para salvar todas as alterações feitas no estado do bot.

    ```csharp
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {

        var botFrameworkAdapter = new BotFrameworkAdapter(_credentialProvider);

        var invokeResponse = await botFrameworkAdapter.ProcessActivityAsync(
            Request.Headers.Authorization?.ToString(),
            activity,
            OnTurnAsync,
            default(CancellationToken));

        if (invokeResponse == null)
        {
            return Request.CreateResponse(HttpStatusCode.OK);
        }
        else
        {
            return Request.CreateResponse(invokeResponse.Status);
        }
    }
    ```
1. Adicione um método **OnTurnAsync** que contém o código do [manipulador de turnos](../bot-builder-basics.md#the-activity-processing-stack) do bot.
    > [!NOTE]
    > Os pontuáveis não existem assim na v4. Procuramos uma mensagem `cancel` do usuário no manipulador de turnos do bot, antes de dar continuidade a qualquer diálogo ativo.
    ```csharp
    protected async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        // We're only handling message activities in this bot.
        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Create the dialog context for our dialog set.
            DialogContext dc = await _dialogs.CreateContextAsync(turnContext, cancellationToken);

            // Globally interrupt the dialog stack if the user sent 'cancel'.
            if (turnContext.Activity.Text.Equals("cancel", StringComparison.InvariantCultureIgnoreCase))
            {
                Activity reply = turnContext.Activity.CreateReply($"Ok restarting conversation.");
                await turnContext.SendActivityAsync(reply);
                await dc.CancelAllDialogsAsync();
            }

            try
            {
                // Continue the active dialog, if any. If we just cancelled all dialog, the
                // dialog stack will be empty, and this will return DialogTurnResult.Empty.
                DialogTurnResult dialogResult = await dc.ContinueDialogAsync();
                switch (dialogResult.Status)
                {
                    case DialogTurnStatus.Empty:
                        // There was no active dialog in the dialog stack; start the root dialog.
                        await dc.BeginDialogAsync(nameof(RootDialog));
                        break;

                    case DialogTurnStatus.Complete:
                        // The last dialog on the stack completed and the stack is empty.
                        await dc.EndDialogAsync();
                        break;

                    case DialogTurnStatus.Waiting:
                    case DialogTurnStatus.Cancelled:
                        // The active dialog is waiting for a response from the user, or all
                        // dialogs were cancelled and the stack is empty. In either case, we
                        // don't need to do anything here.
                        break;
                }
            }
            catch (FormCanceledException)
            {
                // One of the dialogs threw an exception to clear the dialog stack.
                await turnContext.SendActivityAsync("Cancelled.");
                await dc.CancelAllDialogsAsync();
                await dc.BeginDialogAsync(nameof(RootDialog));
            }
        }
    }
    ```
1. Como estamos apenas manipulando atividades de _mensagem_, podemos excluir o método **HandleSystemMessage**.

### <a name="delete-the-cancelscorable-and-globalmessagehandlersbotmodule-classes"></a>Excluir as classes CancelScorable e GlobalMessageHandlersBotModule

Como os pontuáveis não existem na v4 e já atualizamos o manipulador de turnos para reagir a uma mensagem `cancel`, podemos excluir as classes **CancelScorable** (em **Dialogs\CancelScorable.cs**) e  **GlobalMessageHandlersBotModule**.

## <a name="convert-your-dialogs"></a>Converter os diálogos

Faremos muitas alterações nos diálogos originais para migrá-los para o SDK da v4. Não se preocupe com os erros de compilador por enquanto. Eles serão resolvidos depois de concluir a conversão.
Com o intuito de não fazer mais modificações do que o necessário no código original, alguns avisos do compilador permanecerão depois da conclusão da migração.

Todos os nossos diálogos serão derivados de `ComponentDialog`, em vez de implementar a interface `IDialog<object>` da v3.

Esse bot tem quatro diálogos que precisam ser convertidos:

| | |
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

- Use a propriedade `DialogContext.Context` para obter o contexto de turno atual.
- As etapas de cascata têm um parâmetro `WaterfallStepContext`, que deriva de `DialogContext`.
- Todas as classes concretas de diálogo e de prompt derivam da classe `Dialog` abstrata.
- Você atribui uma ID ao criar um diálogo do componente. Cada diálogo presente em um conjunto de diálogos precisa ser atribuído a uma ID exclusiva dentro desse conjunto.

### <a name="update-the-root-dialog"></a>Atualizar o diálogo raiz

Nesse bot, o diálogo raiz solicita que o usuário escolha uma das opções de um conjunto e depois inicie um diálogo filho com base nessa escolha. Em seguida, um loop é executado durante todo o tempo de vida da conversa.

- É possível configurar o fluxo principal como um diálogo em cascata, que é um novo conceito no SDK da v4. Ele será executado por meio de um conjunto fixo de etapas em ordem. Para obter mais informações, confira [Implementar fluxo de conversa sequencial](~/v4sdk/bot-builder-dialog-manage-conversation-flow.md).
- O processo de prompt agora é manipulado por meio de classes de prompt, que são diálogos filho curtos que solicitam entradas, fazem certo processamento e validação mínimos e retornam um valor. Para obter mais informações, confira [coletar entrada do usuário usando um prompt de diálogo](~/v4sdk/bot-builder-prompts.md).

No arquivo **Dialogs/RootDialog.cs**:

1. Atualize as instruções `using`:
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    ```
1. É preciso converter as opções `HelpdeskOptions` de uma lista de cadeias de caracteres para uma lista de opções. Isso será usado com um prompt de escolha, que aceitará o número da opção (na lista), o valor de opção ou qualquer um dos sinônimos da opção como uma entrada válida.
    ```csharp
    private static List<Choice> HelpdeskOptions = new List<Choice>()
    {
        new Choice(InstallAppOption) { Synonyms = new List<string>(){ "install" } },
        new Choice(ResetPasswordOption) { Synonyms = new List<string>(){ "password" } },
        new Choice(LocalAdminOption)  { Synonyms = new List<string>(){ "admin" } }
    };
    ```
1. Adicione um construtor. O código faz o seguinte:
   - Uma ID é atribuída a cada instância de um diálogo quando ele é criado. A ID do diálogo faz parte do conjunto ao qual o diálogo está sendo adicionado. Lembre-se de que o bot tem um conjunto de diálogos que foi inicializado na classe **MessageController**. Cada `ComponentDialog` tem seu próprio conjunto de diálogos interno, com seu próprio conjunto de IDs de diálogo.
   - Ele adiciona outros diálogos, incluindo o prompt de escolha, como diálogos filho. Aqui, estamos simplesmente usando o nome de classe para cada ID de diálogo.
   - Ele define um diálogo em cascata de três etapas. Implementaremos essas etapas em alguns instantes.
     - Primeiro, o diálogo solicitará que o usuário escolha uma tarefa a ser realizada.
     - Em seguida, iniciará o diálogo filho associado a essa escolha.
     - Por fim, se reiniciará.
   - Cada etapa da cascata é um delegado, e as implementaremos a seguir, obtendo o código existente do diálogo original sempre que possível.
   - Ao iniciar um diálogo de componente, ele iniciará seu _diálogo inicial_. Por padrão, esse é o primeiro diálogo filho adicionado a um diálogo de componente. Para atribuir um filho diferente como o diálogo inicial, defina manualmente a propriedade `InitialDialogId` do componente.
    ```csharp
    public RootDialog(string id)
        : base(id)
    {
        AddDialog(new WaterfallDialog("choiceswaterfall", new WaterfallStep[]
            {
                PromptForOptionsAsync,
                ShowChildDialogAsync,
                ResumeAfterAsync,
            }));
        AddDialog(new InstallAppDialog(nameof(InstallAppDialog)));
        AddDialog(new LocalAdminDialog(nameof(LocalAdminDialog)));
        AddDialog(new ResetPasswordDialog(nameof(ResetPasswordDialog)));
        AddDialog(new ChoicePrompt("options"));
    }
    ```
1. Podemos excluir o método **StartAsync**. Quando um diálogo de componente for iniciado, ele iniciará seu diálogo _inicial_ automaticamente. Nesse caso, é o diálogo em cascata que definimos no construtor. Ele também inicia automaticamente em sua primeira etapa.
1. Vamos excluir os métodos **MessageReceivedAsync** e **ShowOptions** e vamos substituí-los pela primeira etapa da nossa cascata. Esses dois métodos saudaram o usuário e solicitaram que ele escolhesse uma das opções disponíveis.
   - Aqui você pode ver que a lista de opções e as mensagens de saudação e erro são fornecidas como opções na chamada para nosso prompt de escolha.
   - Não precisamos especificar o próximo método a ser chamado no diálogo porque a cascata continuará até a próxima etapa, quando o prompt de escolha é concluído.
   - O prompt de escolha fará um loop até receber uma entrada válida ou até a pilha inteira dede diálogo ser cancelada.
    ```csharp
    private async Task<DialogTurnResult> PromptForOptionsAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Prompt the user for a response using our choice prompt.
        return await stepContext.PromptAsync(
            "options",
            new PromptOptions()
            {
                Choices = HelpdeskOptions,
                Prompt = MessageFactory.Text(GreetMessage),
                RetryPrompt = MessageFactory.Text(ErrorMessage)
            },
            cancellationToken);
    }
    ```
1. É possível substituir **OnOptionSelected** pela segunda etapa da nossa cascata. Ainda iniciaremos um diálogo filho com base na entrada do usuário.
   - O prompt de escolha retorna um valor `FoundChoice`. Isso é mostrado na propriedade de contexto `Result` da etapa. A pilha de diálogo trata todos os valores retornados como objetos. Se o valor retornado for um dos seus diálogos, você saberá qual é o tipo de valor do objeto. Para obter uma lista com o que cada tipo de prompt retorna, confira [tipos de prompt](../bot-builder-concept-dialog.md#prompt-types).
   - Como o prompt de escolha não lançará uma exceção, é possível remover o bloco try-catch.
   - Precisamos adicionar uma passagem para que esse método sempre retorne um valor apropriado. Esse código nunca deve ser atingido, mas se o for, ele permitirá que o diálogo “falhe graciosamente”.
    ```csharp
    private async Task<DialogTurnResult> ShowChildDialogAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // string optionSelected = await userReply;
        string optionSelected = (stepContext.Result as FoundChoice).Value;

        switch (optionSelected)
        {
            case InstallAppOption:
                //context.Call(new InstallAppDialog(), this.ResumeAfterOptionDialog);
                //break;
                return await stepContext.BeginDialogAsync(
                    nameof(InstallAppDialog),
                    cancellationToken);
            case ResetPasswordOption:
                //context.Call(new ResetPasswordDialog(), this.ResumeAfterOptionDialog);
                //break;
                return await stepContext.BeginDialogAsync(
                    nameof(ResetPasswordDialog),
                    cancellationToken);
            case LocalAdminOption:
                //context.Call(new LocalAdminDialog(), this.ResumeAfterOptionDialog);
                //break;
                return await stepContext.BeginDialogAsync(
                    nameof(LocalAdminDialog),
                    cancellationToken);
        }

        // We shouldn't get here, but fail gracefully if we do.
        await stepContext.Context.SendActivityAsync(
            "I don't recognize that option.",
            cancellationToken: cancellationToken);
        // Continue through to the next step without starting a child dialog.
        return await stepContext.NextAsync(cancellationToken: cancellationToken);
    }
    ```
1. Por fim, substitua o antigo método **ResumeAfterOptionDialog** pela última etapa da nossa cascata.
    - Em vez encerrar o diálogo e retornar o número do tíquete como fizemos no diálogo original, estamos reiniciando a cascata ao substituir a pilha da instância original por uma nova instância de si mesmo. Podemos fazer isso, uma vez que o aplicativo original sempre ignorou o valor retornado (o número de tíquete) e reiniciou o diálogo raiz.
    ```csharp
    private async Task<DialogTurnResult> ResumeAfterAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        try
        {
            //var message = await userReply;
            var message = stepContext.Context.Activity;

            int ticketNumber = new Random().Next(0, 20000);
            //await context.PostAsync($"Thank you for using the Helpdesk Bot. Your ticket number is {ticketNumber}.");
            await stepContext.Context.SendActivityAsync(
                $"Thank you for using the Helpdesk Bot. Your ticket number is {ticketNumber}.",
                cancellationToken: cancellationToken);

            //context.Done(ticketNumber);
        }
        catch (Exception ex)
        {
            // await context.PostAsync($"Failed with message: {ex.Message}");
            await stepContext.Context.SendActivityAsync(
                $"Failed with message: {ex.Message}",
                cancellationToken: cancellationToken);

            // In general resume from task after calling a child dialog is a good place to handle exceptions
            // try catch will capture exceptions from the bot framework awaitable object which is essentially "userReply"
            logger.Error(ex);
        }

        // Replace on the stack the current instance of the waterfall with a new instance,
        // and start from the top.
        return await stepContext.ReplaceDialogAsync(
            "choiceswaterfall",
            cancellationToken: cancellationToken);
    }
    ```

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
    ```csharp
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using ContosoHelpdeskChatBot.Models;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    ```
1. Defina constantes para as IDs que usaremos para os prompts e diálogos. Isso facilita a manutenção do código do diálogo, uma vez que a cadeia de caracteres a ser usada é definida em um único local.
    ```csharp
    // Set up our dialog and prompt IDs as constants.
    private const string MainId = "mainDialog";
    private const string TextId = "textPrompt";
    private const string ChoiceId = "choicePrompt";
    ```
1. Defina constantes para as chaves que usaremos para monitorar o estado do diálogo.
    ```csharp
    // Set up keys for managing collected information.
    private const string InstallInfo = "installInfo";
    ```
1. Adicione um construtor e inicialize o conjunto de diálogos do componente. Estamos explicitamente definindo a propriedade `InitialDialogId` neste momento, o que significa que o principal diálogo em cascata não precisa ser o primeiro adicionado ao conjunto. Por exemplo, caso prefira, você poderia adicionar prompts primeiro sem causar um problema de tempo de execução.
    ```csharp
    public InstallAppDialog(string id)
        : base(id)
    {
        // Initialize our dialogs and prompts.
        InitialDialogId = MainId;
        AddDialog(new WaterfallDialog(MainId, new WaterfallStep[] {
            GetSearchTermAsync,
            ResolveAppNameAsync,
            GetMachineNameAsync,
            SubmitRequestAsync,
        }));
        AddDialog(new TextPrompt(TextId));
        AddDialog(new ChoicePrompt(ChoiceId));
    }
    ```
1. É possível substituir **StartAsync** pela primeira etapa da nossa cascata.
    - Nós mesmos precisamos gerenciar o estado, portanto, vamos rastrear o objeto do aplicativo de instalação no estado de diálogo.
    - A mensagem solicitando entradas do usuário se torna uma opção na chamada ao prompt.
    ```csharp
    private async Task<DialogTurnResult> GetSearchTermAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Create an object in dialog state in which to track our collected information.
        stepContext.Values[InstallInfo] = new InstallApp();

        // Ask for the search term.
        return await stepContext.PromptAsync(
            TextId,
            new PromptOptions
            {
                Prompt = MessageFactory.Text("Ok let's get started. What is the name of the application? "),
            },
            cancellationToken);
    }
    ```
1. É possível substituir o **appNameAsync** e o **multipleAppsAsync** pela segunda etapa da nossa cascata.
    - Agora estamos obtendo o resultado do prompt em vez de apenas observar a última mensagem do usuário.
    - A consulta de banco de dados e as instruções “se” são organizadas da mesma forma que em **appNameAsync**. O código de cada bloco da instrução foi atualizado para trabalhar com diálogos da v4.
        - Se tivermos uma ocorrência, atualizaremos o estado do diálogo e seguiremos para a próxima etapa.
        - Se tivermos várias ocorrências, usaremos o prompt de nossa escolha para solicitar que o usuário escolha uma das opções da lista. Isso significa que podemos simplesmente excluir o **multipleAppsAsync**.
        - Se não tivermos nenhuma ocorrência, encerraremos esse diálogo e retornaremos nulos para o diálogo raiz.
    ```csharp
    private async Task<DialogTurnResult> ResolveAppNameAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Get the result from the text prompt.
        var appname = stepContext.Result as string;

        // Query the database for matches.
        var names = await this.getAppsAsync(appname);

        if (names.Count == 1)
        {
            // Get our tracking information from dialog state and add the app name.
            var install = stepContext.Values[InstallInfo] as InstallApp;
            install.AppName = names.First();

            return await stepContext.NextAsync();
        }
        else if (names.Count > 1)
        {
            // Ask the user to choose from the list of matches.
            return await stepContext.PromptAsync(
                ChoiceId,
                new PromptOptions
                {
                    Prompt = MessageFactory.Text("I found the following applications. Please choose one:"),
                    Choices = ChoiceFactory.ToChoices(names),
                },
                cancellationToken);
        }
        else
        {
            // If no matches, exit this dialog.
            await stepContext.Context.SendActivityAsync(
                $"Sorry, I did not find any application with the name '{appname}'.",
                cancellationToken: cancellationToken);

            return await stepContext.EndDialogAsync(null, cancellationToken);
        }
    }
    ```
1. O **appNameAsync** também solicitou o nome do computador do usuário depois que ele resolveu a consulta. Vamos capturar essa parte da lógica na próxima etapa da cascata.
    - Novamente, na v4, nós mesmos temos de gerenciar o estado. A única dificuldade é que podemos chegar a essa etapa por dois branches de lógica diferentes na etapa anterior.
    - Solicitaremos ao usuário um nome de computador usando o mesmo prompt de texto de antes, mas fornecendo opções diferentes desta vez.
    ```csharp
    private async Task<DialogTurnResult> GetMachineNameAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Get the tracking info. If we don't already have an app name,
        // Then we used the choice prompt to get it in the previous step.
        var install = stepContext.Values[InstallInfo] as InstallApp;
        if (install.AppName is null)
        {
            install.AppName = (stepContext.Result as FoundChoice).Value;
        }

        // We now need the machine name, so prompt for it.
        return await stepContext.PromptAsync(
            TextId,
            new PromptOptions
            {
                Prompt = MessageFactory.Text(
                    $"Found {install.AppName}. What is the name of the machine to install application?"),
            },
            cancellationToken);
    }
    ```
1. A lógica de **machineNameAsync** é encapsulada na etapa final da nossa cascata.
    - Recuperamos o nome do computador do resultado do prompt de texto e atualizamos o estado do diálogo.
    - Estamos removendo a chamada para atualizar o banco de dados porque o código de suporte está em um projeto diferente.
    - Depois, enviaremos a mensagem de êxito para o usuário e finalizaremos o diálogo.
    ```csharp
    private async Task<DialogTurnResult> SubmitRequestAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        if (stepContext.Reason != DialogReason.CancelCalled)
        {
            // Get the tracking info and add the machine name.
            var install = stepContext.Values[InstallInfo] as InstallApp;
            install.MachineName = stepContext.Context.Activity.Text;

            //TODO: Save to this information to the database.
        }

        await stepContext.Context.SendActivityAsync(
            $"Great, your request to install {install.AppName} on {install.MachineName} has been scheduled.",
            cancellationToken: cancellationToken);

        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    ```
1. Para simular o banco de dados, atualizamos **getAppsAsync** para consultar uma lista estática em vez do banco de dados.
    ```csharp
    private async Task<List<string>> getAppsAsync(string Name)
    {
        List<string> names = new List<string>();

        // Simulate querying the database for applications that match.
        return (from app in AppMsis
            where app.ToLower().Contains(Name.ToLower())
            select app).ToList();
    }

    // Example list of app names in the database.
    private static readonly List<string> AppMsis = new List<string>
    {
        "µTorrent 3.5.0.44178",
        "7-Zip 17.1",
        "Ad-Aware 9.0",
        "Adobe AIR 2.5.1.17730",
        "Adobe Flash Player (IE) 28.0.0.105",
        "Adobe Flash Player (Non-IE) 27.0.0.130",
        "Adobe Reader 11.0.14",
        "Adobe Shockwave Player 12.3.1.201",
        "Advanced SystemCare Personal 11.0.3",
        "Auslogics Disk Defrag 3.6",
        "avast! 4 Home Edition 4.8.1351",
        "AVG Anti-Virus Free Edition 9.0.0.698",
        "Bonjour 3.1.0.1",
        "CCleaner 5.24.5839",
        "Chmod Calculator 20132.4",
        "CyberLink PowerDVD 17.0.2101.62",
        "DAEMON Tools Lite 4.46.1.328",
        "FileZilla Client 3.5",
        "Firefox 57.0",
        "Foxit Reader 4.1.1.805",
        "Google Chrome 66.143.49260",
        "Google Earth 7.3.0.3832",
        "Google Toolbar (IE) 7.5.8231.2252",
        "GSpot 2701.0",
        "Internet Explorer 903235.0",
        "iTunes 12.7.0.166",
        "Java Runtime Environment 6 Update 17",
        "K-Lite Codec Pack 12.1",
        "Malwarebytes Anti-Malware 2.2.1.1043",
        "Media Player Classic 6.4.9.0",
        "Microsoft Silverlight 5.1.50907",
        "Mozilla Thunderbird 57.0",
        "Nero Burning ROM 19.1.1005",
        "OpenOffice.org 3.1.1 Build 9420",
        "Opera 12.18.1873",
        "Paint.NET 4.0.19",
        "Picasa 3.9.141.259",
        "QuickTime 7.79.80.95",
        "RealPlayer SP 12.0.0.319",
        "Revo Uninstaller 1.95",
        "Skype 7.40.151",
        "Spybot - Search & Destroy 1.6.2.46",
        "SpywareBlaster 4.6",
        "TuneUp Utilities 2009 14.0.1000.353",
        "Unlocker 1.9.2",
        "VLC media player 1.1.6",
        "Winamp 5.56 Build 2512",
        "Windows Live Messenger 2009 16.4.3528.331",
        "WinPatrol 2010 31.0.2014",
        "WinRAR 5.0",
    };
    ```

### <a name="update-the-local-admin-dialog"></a>Atualizar o diálogo do administrador local

Na v3, esse diálogo saudou o usuário, iniciou o diálogo Formflow e salvou o resultado em um banco de dados. Isso se traduz facilmente em uma cascata de duas etapas.

1. Atualize as instruções `using`. Observe que esse diálogo inclui um diálogo de Formflow da v3. Na v4, podemos usar a biblioteca de Formflow da comunidade.
    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Bot.Builder.Community.Dialogs.FormFlow;
    using ContosoHelpdeskChatBot.Models;
    using Microsoft.Bot.Builder.Dialogs;
    ```
1. Podemos remover a propriedade de instância para `LocalAdmin`, pois o resultado estará disponível no estado do diálogo.
1. Defina constantes para as IDs que usaremos para os diálogos. Na biblioteca da comunidade, a ID do diálogo construído é sempre definida como o nome da classe produzida pelo diálogo.
    ```csharp
    // Set up our dialog and prompt IDs as constants.
    private const string MainDialog = "mainDialog";
    private static string AdminDialog { get; } = nameof(LocalAdminPrompt);
    ```
1. Adicione um construtor e inicialize o conjunto de diálogos do componente. O diálogo Formflow é criado da mesma forma. Estamos apenas adicionando-o ao conjunto do diálogo do nosso componente no construtor.
    ```csharp
    public LocalAdminDialog(string dialogId) : base(dialogId)
    {
        InitialDialogId = MainDialog;

        AddDialog(new WaterfallDialog(MainDialog, new WaterfallStep[]
        {
            BeginFormflowAsync,
            SaveResultAsync,
        }));
        AddDialog(FormDialog.FromForm(BuildLocalAdminForm, FormOptions.PromptInStart));
    }
    ```
1. É possível substituir **StartAsync** pela primeira etapa da nossa cascata. Já criamos o Formflow no construtor, e as outras duas instruções se traduzem nisso.
    ```csharp
    private async Task<DialogTurnResult> BeginFormflowAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        await stepContext.Context.SendActivityAsync("Great I will help you request local machine admin.");

        // Begin the Formflow dialog.
        return await stepContext.BeginDialogAsync(AdminDialog, cancellationToken: cancellationToken);
    }
    ```
1. É possível substituir **ResumeAfterLocalAdminFormDialog** pela segunda etapa da nossa cascata. Precisamos obter o valor retornado do contexto da etapa em vez de uma propriedade de instância.
    ```csharp
    private async Task<DialogTurnResult> SaveResultAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Get the result from the Formflow dialog when it ends.
        if (stepContext.Reason != DialogReason.CancelCalled)
        {
            var admin = stepContext.Result as LocalAdminPrompt;

            //TODO: Save to this information to the database.
        }

        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    ```
1. O **BuildLocalAdminForm** permanece praticamente o mesmo, com a diferença que o Formflow não atualiza a propriedade da instância.
    ```csharp
    // Nearly the same as before.
    private IForm<LocalAdminPrompt> BuildLocalAdminForm()
    {
        //here's an example of how validation can be used in form builder
        return new FormBuilder<LocalAdminPrompt>()
            .Field(nameof(LocalAdminPrompt.MachineName),
            validate: async (state, value) =>
            {
                ValidateResult result = new ValidateResult { IsValid = true, Value = value };
                //add validation here

                //this.admin.MachineName = (string)value;
                return result;
            })
            .Field(nameof(LocalAdminPrompt.AdminDuration),
            validate: async (state, value) =>
            {
                ValidateResult result = new ValidateResult { IsValid = true, Value = value };
                //add validation here

                //this.admin.AdminDuration = Convert.ToInt32((long)value) as int?;
                return result;
            })
            .Build();
    }
    ```

### <a name="update-the-reset-password-dialog"></a>Atualizar o diálogo de redefinição de senha

Na v3, esse diálogo saudou o usuário, autorizou o usuário com um código-chave, falhou ou iniciou o diálogo Formflow e, em seguida, redefiniu a senha. Isso ainda se traduz bem em uma cascata.

1. Atualize as instruções `using`. Observe que esse diálogo inclui um diálogo de Formflow da v3. Na v4, podemos usar a biblioteca de Formflow da comunidade.
    ```csharp
    using System;
    using System.Threading;
    using System.Threading.Tasks;
    using Bot.Builder.Community.Dialogs.FormFlow;
    using ContosoHelpdeskChatBot.Models;
    using Microsoft.Bot.Builder.Dialogs;
    ```
1. Defina constantes para as IDs que usaremos para os diálogos. Na biblioteca da comunidade, a ID do diálogo construído é sempre definida como o nome da classe produzida pelo diálogo.
    ```csharp
    // Set up our dialog and prompt IDs as constants.
    private const string MainDialog = "mainDialog";
    private static string ResetDialog { get; } = nameof(ResetPasswordPrompt);
    ```
1. Adicione um construtor e inicialize o conjunto de diálogos do componente. O diálogo Formflow é criado da mesma forma. Estamos apenas adicionando-o ao conjunto do diálogo do nosso componente no construtor.
    ```csharp
    public ResetPasswordDialog(string dialogId) : base(dialogId)
    {
        InitialDialogId = MainDialog;

        AddDialog(new WaterfallDialog(MainDialog, new WaterfallStep[]
        {
            BeginFormflowAsync,
            ProcessRequestAsync,
        }));
        AddDialog(FormDialog.FromForm(BuildResetPasswordForm, FormOptions.PromptInStart));
    }
    ```
1. É possível substituir **StartAsync** pela primeira etapa da nossa cascata. Já criamos o Formflow no construtor. Caso contrário, estamos mantendo a mesma lógica, traduzindo apenas as chamadas da v3 em seus equivalentes da v4.
    ```csharp
    private async Task<DialogTurnResult> BeginFormflowAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        await stepContext.Context.SendActivityAsync("Alright I will help you create a temp password.");

        // Check the passcode and fail out or begin the Formflow dialog.
        if (sendPassCode(stepContext))
        {
            return await stepContext.BeginDialogAsync(ResetDialog, cancellationToken: cancellationToken);
        }
        else
        {
            //here we can simply fail the current dialog because we have root dialog handling all exceptions
            throw new Exception("Failed to send SMS. Make sure email & phone number has been added to database.");
        }
    }
    ```
1. O **sendPassCode** é deixado principalmente como um exercício. O código original é transformado em comentário, e o método simplesmente retorna true. Além disso, podemos remover o endereço de email novamente, pois ele não foi usado no bot original.
    ```csharp
    private bool sendPassCode(DialogContext context)
    {
        //bool result = false;

        //Recipient Id varies depending on channel
        //refer ChannelAccount class https://docs.botframework.com/en-us/csharp/builder/sdkreference/dd/def/class_microsoft_1_1_bot_1_1_connector_1_1_channel_account.html#a0b89cf01fdd73cbc00a524dce9e2ad1a
        //as well as Activity class https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html
        //int passcode = new Random().Next(1000, 9999);
        //Int64? smsNumber = 0;
        //string smsMessage = "Your Contoso Pass Code is ";
        //string countryDialPrefix = "+1";

        // TODO: save PassCode to database
        //using (var db = new ContosoHelpdeskContext())
        //{
        //    var reset = db.ResetPasswords.Where(r => r.EmailAddress == email).ToList();
        //    if (reset.Count >= 1)
        //    {
        //        reset.First().PassCode = passcode;
        //        smsNumber = reset.First().MobileNumber;
        //        result = true;
        //    }

        //    db.SaveChanges();
        //}

        // TODO: send passcode to user via SMS.
        //if (result)
        //{
        //    result = Helper.SendSms($"{countryDialPrefix}{smsNumber.ToString()}", $"{smsMessage} {passcode}");
        //}

        //return result;
        return true;
    }
    ```
1. O **BuildResetPasswordForm** não tem alterações.
1. É possível substituir **ResumeAfterLocalAdminFormDialog** pela segunda etapa da nossa cascata, e obteremos o valor retornado do contexto da etapa. Removemos o endereço de email com o qual o diálogo original não fez nada e fornecemos um resultado fictício em vez de consultar o banco de dados. Estamos mantendo a mesma lógica, traduzindo apenas as chamadas da v3 em seus equivalentes da v4.
    ```csharp
    private async Task<DialogTurnResult> ProcessRequestAsync(
        WaterfallStepContext stepContext,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Get the result from the Formflow dialog when it ends.
        if (stepContext.Reason != DialogReason.CancelCalled)
        {
            var prompt = stepContext.Result as ResetPasswordPrompt;
            int? passcode;

            // TODO: Retrieve the passcode from the database.
            passcode = 1111;

            if (prompt.PassCode == passcode)
            {
                string temppwd = "TempPwd" + new Random().Next(0, 5000);
                await stepContext.Context.SendActivityAsync(
                    $"Your temp password is {temppwd}",
                    cancellationToken: cancellationToken);
            }
        }

        return await stepContext.EndDialogAsync(null, cancellationToken);
    }
    ```

### <a name="update-models-as-necessary"></a>Atualizar modelos conforme o necessário

Precisamos atualizar as instruções `using` em alguns dos modelos que referenciam à biblioteca Formflow.

1. Em `LocalAdminPrompt`, altere-os para isto:
    ```csharp
    using Bot.Builder.Community.Dialogs.FormFlow;
    ```
1. Em `ResetPasswordPrompt`, altere-os para isto:
    ```csharp
    using Bot.Builder.Community.Dialogs.FormFlow;
    using System;
    ```

## <a name="update-webconfig"></a>Atualizar Web. config

Transforme as chaves de configuração em comentários para **MicrosoftAppId** e **MicrosoftAppPassword**. Isso permitirá que você depure o bot localmente sem a necessidade de fornecer esses valores ao emulador.

## <a name="run-and-test-your-bot-in-the-emulator"></a>Executar e testar seu bot no emulador

Neste ponto, devemos ser capazes de executar o bot localmente no IIS e anexar a ele com o emulador.

1. Executar o bot no IIS.
1. Inicie o emulador e conecte-se ao ponto de extremidade do bot (por exemplo: **http://localhost:3978/api/messages**).
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
- [Coletar entradas do usuário usando um prompt de caixa de diálogo](../bot-builder-prompts.md)

<!-- TODO:
- The conceptual piece
- The migration to a .NET Core project
-->
