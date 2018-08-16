---
title: Gravar seu próprio middleware | Microsoft Docs
description: Saiba como gravar seu próprio middleware.
keywords: middleware, middleware personalizado, curto-circuito, fallback, manipuladores de atividade
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6bc73b2886374fbb50d8257c387df54f21a12ed7
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297173"
---
# <a name="write-your-own-middleware"></a>Gravar seu próprio middleware

O middleware permite a você gravar plug-ins avançados para os bots, o que também pode ser usado por outras pessoas. Aqui, mostraremos como adicionar e implementar o middleware básico e mostrar como ele funciona. O SDK v4 fornece determinados middlewares a você, para fins como gerenciamento de estado, LUIS, QnAMaker e tradução. Confira o SDK do Bot Builder para [.NET](https://github.com/Microsoft/botbuilder-dotnet) ou [JavaScript](https://github.com/Microsoft/botbuilder-js) para obter mais informações.

## <a name="adding-middleware"></a>Adicionar middleware

No exemplo a seguir, com base em nosso exemplo de HelloBot básico, duas peças diferentes de middleware são adicionadas aos nossos serviços com uma nova instância de cada uma dessas classes.

> [!IMPORTANT]
> Lembre-se de que a ordem na qual elas são adicionados às opções determina a ordem na qual elas são executadas. Certifique-se de considerar como isso funcionará se estiver usando mais do que uma peça de middleware.

**Startup.cs**

# <a name="ctabcsaddmiddleware"></a>[C#](#tab/csaddmiddleware)

Adicione chamadas de método `options.Middleware.Add(new MyMiddleware());` às opções de serviço do bot para cada peça de middleware que você quer adicionar.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<HelloBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        options.Middleware.Add(new MyMiddleware());
        options.Middleware.Add(new MyOtherMiddleware());
    });
}
```
# <a name="javascripttabjsaddmiddleware"></a>[JavaScript](#tab/jsaddmiddleware)

Adicione `adapter.use(MyMiddleware());` ao adaptador para cada peça de middleware que você quer adicionar.

```javascript
// Create adapter
const adapter = new botbuilder.BotFrameworkAdapter({
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});

adapter.use(MyMiddleware());
adapter.use(MyOtherMiddleware());
```

---


## <a name="implementing-your-middleware"></a>Implementar o Middleware

Cada peça de middleware é herdada de uma interface de middlware e sempre implementa seu manipulador de processamento, que é executado em cada atividade enviada ao bot. Para cada peça de middleware adicionada, o manipulador de processamento tem uma chance de modificar o objeto de contexto ou executar uma tarefa, como registro em log, antes de permitir que outra lógica de middleware ou bot interaja com o objeto de contexto que continua seguindo pelo pipeline.

# <a name="ctabcsetagoverwrite"></a>[C#](#tab/csetagoverwrite)

Cada peça de middleware é herdada de `IMiddleware` e sempre implementa `OnTurn()`.

**ExampleMiddleware.cs**
```csharp
public class MyMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {            
        // This simple middleware reports the request type and if we responded
        await context.SendActivity($"Request type: {context.Activity.Type}");
        
        await next();            

        // Report if any responses were recorded
        string response = context.Responded ? "yes" : "no";
        await context.SendActivity($"Responded?  {response}");
    }
}

public class MyOtherMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        // simple middleware to add an additional send activity
        await context.SendActivity($"My other middleware just saying Hi before the bot logic");

        await next();
    }
}

```

# <a name="javascripttabjsimplementmiddleware"></a>[JavaScript](#tab/jsimplementmiddleware)

Cada peça de middleware é herdada de `MiddlewareSet` e sempre implementa `onTurn()`.

**ExampleMiddleware.js**
```js
adapter.use({onTurn: async (context, next) =>{

    // This simple middleware reports the activity type and if we responded
    await context.sendActivity(`Activity type: ${context.activity.type}`); 
    await next();            

    // Report if any responses were recorded
    const response = context.activity.text ? "yes" : "no";
    await context.sendActivity(`Responded?  ${response}`);

}}, {onTurn: async (context, next) => {

     // simple middleware to add an additional send activity
     await context.sendActivity("My other middleware just saying Hi before the bot logic");

     await next();
}})
```

---

Chamar `next()` faz com que a execução continue para a próxima peça de middleware. A capacidade de escolher quando a execução é passada permite a você gravar código que é executado **após** o restante da pilha do middleware ter sido executada. Podemos agir na 'borda à direita' do manipulador de processamento, após a execução da lógica do bot e outro middleware.  No exemplo acima, o primeiro middleware implementado fez exatamente isso, relatando se nós respondemos a esse objeto de contexto, antes de passar a execução de volta ao pipeline.

## <a name="short-circuit-routing"></a>Roteamento de curto-circuito

Em alguns casos, você talvez queira interromper qualquer processamento adicional da atividade recebida, o que chamamos causar curto-circuito. Isso é útil para casos em que o middleware se encarrega inteiramente de uma solicitação, fornece uma resposta fácil para comandos específicos ou pode manipular uma solicitação de entrada sem que a lógica do bot precise vê-la.

Vamos criar uma peça de middleware que enviará uma resposta e evitará qualquer outro roteamento adicional da solicitação sempre que o usuário disser "ping":

# <a name="ctabcsmiddlewareshortcircuit"></a>[C#](#tab/csmiddlewareshortcircuit)
```cs
public class ExampleMiddleware : IMiddleware
{
    public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        var utterance = context.Activity?.AsMessageActivity()?.Text.Trim().ToLower();

        if (utterance == "ping") 
        {
            context.SendActivity("pong");
            return;
        } 
        else 
        {
            await next();
        }
    }
}
```
# <a name="javascripttabjsmiddlewareshortcircuit"></a>[JavaScript](#tab/jsmiddlewareshortcircuit)
```JavaScript
adapter.use({onTurn: async (context, next) =>{
    const utterance = (context.activity.text || '').trim().toLowerCase();
        if (utterance == "ping") 
        {
            await context.sendActivity("pong");
            return;
        } 
        else 
        {
            await next();
        }

}})

```

---

## <a name="fallback-processing"></a>Processamento de fallback

Outra coisa que talvez seja necessário fazer é responder a uma solicitação que ainda não foi respondida. Isso é facilmente realizado usando a borda à direita do manipulador de processamento, verificando a propriedade `context.Responded`. Vamos criar uma simples peça de middleware que automaticamente diz "não entendi," se o bot falhar ao manipular a solicitação:

# <a name="ctabcsfallback"></a>[C#](#tab/csfallback)
```cs
public async Task OnTurn(ITurnContext context, MiddlewareSet.NextDelegate next)
{
    await next();

    if (!context.Responded) 
    {
        context.SendActivity("I didn't understand.");
    }
}
```
# <a name="javascripttabjsfallback"></a>[JavaScript](#tab/jsfallback)
```JavaScript
adapter.use({onTurn: async (context, next) =>{
    await next();

    if (!context.responded) 
    {
       await context.sendActivity("I didn't understand.");
    }

}})
```

---

> [!NOTE] 
> Isso pode não funcionar em todos os casos, como quando outro middleware pode ser capaz de responder ao usuário ou quando o bot recebe uma mensagem corretamente, mas não responde. Responder com "não entendi" seria enganoso para o nosso usuário.


