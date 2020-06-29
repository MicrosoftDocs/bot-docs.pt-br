---
title: Manipuladores de mensagens globais usando pontuáveis
description: Crie diálogos mais flexíveis usando pontuáveis no SDK do Bot Framework para .NET.
author: matthewshim-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 101de0274c0daf4cbeb003703e299e0598f12a8a
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "70297292"
---
# <a name="global-message-handlers-using-scorables"></a>Manipuladores de mensagens globais usando pontuáveis

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Os usuários tentam acessar algumas funcionalidades em um bot usando palavras como "ajuda", "cancelar" ou "começar novamente" no meio de uma conversa quando o bot está esperando uma resposta diferente. Você pode projetar seu bot para manipular normalmente essas solicitações usando diálogos pontuáveis.

Os diálogos pontuáveis monitoram todas as mensagens de entrada e determinam se uma mensagem é acionável de alguma forma. As mensagens que são pontuáveis recebem uma pontuação entre [0-1] por cada diálogo pontuável. O diálogo pontuável que determina a pontuação mais alta é adicionado à parte superior da pilha de diálogos e, em seguida, passa a resposta para o usuário. Depois que o diálogo pontuável conclui a execução, a conversa continua do ponto em que parou.

Os pontuáveis permitem que você crie conversas mais flexíveis, possibilitando aos usuários 'interromper' o fluxo da conversa normal encontrado em diálogos normais.

## <a name="create-a-scorable-dialog"></a>Criar um diálogo pontuável

Primeiro, defina um novo [diálogo](bot-builder-dotnet-dialogs.md). O código a seguir usa um diálogo derivado da interface `IDialog`.

```cs
public class SampleDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        await context.PostAsync("This is a Sample Dialog which is Scorable. Reply with anything to return to the prior prior dialog.");

        context.Wait(this.MessageReceived);
    }

    private async Task MessageReceived(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        var message = await result;

        if ((message.Text != null) && (message.Text.Trim().Length > 0))
        {
            context.Done<object>(null);
        }
        else
        {
            context.Fail(new Exception("Message was not a string or was an empty string."));
        }
    }
}
```
Para tornar um diálogo pontuável, crie uma classe que herda da classe abstrata `ScorableBase`. O código a seguir mostra uma classe `SampleScorable`.

```cs
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
using Microsoft.Bot.Builder.Internals.Fibers;
using Microsoft.Bot.Builder.Scorables.Internals;

public class SampleScorable : ScorableBase<IActivity, string, double>
{
    private readonly IDialogTask task;

    public SampleScorable(IDialogTask task)
    {
        SetField.NotNull(out this.task, nameof(task), task);
    }
}
```
A classe abstrata `ScorableBase` herda da interface `IScorable`. Você precisará implementar os seguintes métodos `IScorable` na classe:

- `PrepareAsync` é o primeiro método chamado na instância pontuável. Ele aceita a atividade de mensagem de entrada, analisa e define o estado do diálogo, que é passado para todos os outros métodos da interface `IScorable`.

```cs
protected override async Task<string> PrepareAsync(IActivity item, CancellationToken token)
{
        // TODO: insert your code here
}
```

- O método `HasScore` verifica a propriedade de estado para determinar se o diálogo pontuável deve fornecer uma pontuação para a mensagem. Se ele retornar falso, a mensagem será ignorada pelo diálogo pontuável.

```cs
protected override bool HasScore(IActivity item, string state)
{
        // TODO: insert your code here
}
```

- `GetScore` só será disparado se `HasScore` retornar verdadeiro. Você provisionará a lógica nesse método para determinar a pontuação para uma mensagem entre 0-1.

```cs
protected override double GetScore(IActivity item, string state)
{
        // TODO: insert your code here
}
```
- No método `PostAsync`, defina as ações principais a serem executadas para a classe pontuável. Todos os diálogos pontuáveis monitorarão as mensagens de entrada e atribuirão as pontuações às mensagens válidas, com base no método GetScore dos pontuáveis. Em seguida, a classe pontuável que determina a pontuação mais alta (entre 0 e -1,0) disparará o método `PostAsync` do pontuável.

```cs
protected override Task PostAsync(IActivity item, string state, CancellationToken token)
{
        //TODO: insert your code here
}
```

- `DoneAsync` é chamado após a conclusão do processo de pontuação. Use esse método para descartar os recursos no escopo.

```cs
protected override Task DoneAsync(IActivity item, string state, CancellationToken token)
{
        //TODO: insert your code here
}
```

## <a name="create-a-module-to-register-the-iscorable-service"></a>Criar um módulo para registrar o serviço IScorable

Em seguida, defina um `Module` que registrará a classe `SampleScorable` como um componente. Isso provisionará o serviço `IScorable`.

```cs
public class GlobalMessageHandlersBotModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        base.Load(builder);

        builder
            .Register(c => new SampleScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();
    }
}
```
## <a name="register-the-module"></a>Registrar o módulo  

A última etapa do processo é aplicar o `SampleScorable` ao Contêiner de Conversa do bot. Isso registrará o serviço pontuável no pipeline de manipulação de mensagens do Bot Framework. O seguinte código mostra como atualizar o `Conversation.Container` na inicialização do aplicativo de bot em **Global.asax.cs**:

```cs
public class WebApiApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        this.RegisterBotModules();
        GlobalConfiguration.Configure(WebApiConfig.Register);
    }

    private void RegisterBotModules()
    {
        var builder = new ContainerBuilder();
        builder.RegisterModule(new ReflectionSurrogateModule());

        //Register the module within the Conversation container
        builder.RegisterModule<GlobalMessageHandlersBotModule>();

        builder.Update(Conversation.Container);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais
* [Amostra de manipuladores de mensagens globais](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers)
* [Amostra de bot pontuável simples](https://github.com/Microsoft/BotFramework-Samples/tree/master/blog-samples/CSharp/ScorableBotSample)
* [Visão geral de caixas de diálogo](bot-builder-dotnet-dialogs.md)
* [AutoFac](https://autofac.org/)
