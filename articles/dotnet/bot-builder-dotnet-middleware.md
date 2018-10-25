---
title: Interceptar mensagens | Microsoft Docs
description: Saiba como interceptar mensagens entre o usuário e o bot usando o SDK do construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 21607dae5c2a8d08ed4b7bf1b6e6983cd9bf1196
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999723"
---
# <a name="intercept-messages"></a>Interceptar mensagens

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.js](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introducton to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="intercept-and-log-messages"></a>Mensagens de interceptação e log

O exemplo de código a seguir mostra como interceptar mensagens que são trocadas entre o usuário e o bot usando o conceito de **middleware** no SDK do Bot Builder para .NET. 

Primeiro, crie uma classe e `DebugActivityLogger` defina um método `LogAsync` para especificar qual ação é tomada para cada mensagem interceptada. Este exemplo imprime apenas algumas informações sobre cada mensagem.

```cs
public class DebugActivityLogger : IActivityLogger
{
    public async Task LogAsync(IActivity activity)
    {
        Debug.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity()?.Text}");
    }
}
```

Em seguida, adicione o seguinte código ao `Global.asax.cs`.  Cada mensagem que é trocada entre usuário e bot (em qualquer direção) irá agora disparar o método `LogAsync` na classe `DebugActivityLogger`. 

```cs
    public class WebApiApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            var builder = new ContainerBuilder();
            builder.RegisterType<DebugActivityLogger>().AsImplementedInterfaces().InstancePerDependency();
            builder.Update(Conversation.Container);

            GlobalConfiguration.Configure(WebApiConfig.Register);
        }
    }
```

Embora este exemplo simplesmente imprime algumas informações sobre cada mensagem, você pode atualizar o `LogAsync` método para especificar as ações que você deseja executar para cada mensagem. 

## <a name="sample-code"></a>Exemplo de código 

Para um exemplo completo que mostre como interceptar e registrar mensagens usando o SDK do Bot Builder para .NET, consulte o <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-Middleware" target="_blank">exemplo de Middleware</a> no GitHub. 

## <a name="additional-resources"></a>Recursos adicionais

- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">SDK do Construtor de Bot para referência .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-Middleware" target="_blank">Exemplo de middleware (GitHub)</a>
