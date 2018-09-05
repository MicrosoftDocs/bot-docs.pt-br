---
title: Anatomia de um bot | Microsoft Docs
description: Como dividir as várias partes de um bot e como elas funcionam
keywords: ''
author: ivorb
ms.author: ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 06/25/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 238be22eff746edff30a5446d20991dfaedc945a
ms.sourcegitcommit: 44f100a588ffda19c275b118f4f97029f12d1449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2018
ms.locfileid: "42928284"
---
# <a name="anatomy-of-a-bot"></a>Anatomia de um bot

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Um bot, no [sentido mais básico](bot-builder-basics.md), é um aplicativo de conversa em que os usuários se comunicam usando mensagens. Ele segue a estrutura básica de um aplicativo Web no seu respectivo idioma e se baseia no SDK do Bot Framework.

Um bot é composto de vários componentes diferentes, permitindo que você [envie mensagens](./bot-builder-howto-send-messages.md) e controle o [estado](./bot-builder-storage-concept.md). Você pode se comunicar com seu bot por meio do [emulador](../bot-service-debug-emulator.md), [WebChat](../bot-service-manage-test-webchat.md) ou em produção por meio de um dos [canais](bot-concepts.md). 

Aqui, vamos explorar um Bot de Eco básico passo a passo e examinar cada parte que é reunida para fazê-lo funcionar.

## <a name="files-created-for-echo-bot"></a>Arquivos criados para o Bot de Eco

Cada linguagem de programação cria arquivos diferentes para o bot de Eco que, neste exemplo, chamamos de **BasicEcho**, e esses arquivos se enquadram em três seções diferentes: a seção do sistema, os itens auxiliares e o núcleo do bot. A tabela a seguir lista os principais arquivos para C# e JavaScript.

| C# | JavaScript |
| --- | --- |
| `Properties > launchSettings.json` <br> `wwwroot > default.htm` <br> `appsettings.json` <br> `BasicEcho.bot` <br> `EchoBot.cs` <br> `EchoState.cs` <br> `Program.cs` <br> `readme.md` <br> `Startup.cs` | `.env` <br> `app.js` <br> `package.json` <br> `README.md` <br> `basicEcho.bot` |

Abaixo, orientaremos você por meio de alguns dos principais recursos desses arquivos por seção. Alguns desses arquivos têm seu próprio conjunto de includes, que são as duas bibliotecas de programação geral e específica do bot. Esses includes fornecem um conjunto de funções necessárias, bem como algumas que talvez sejam necessárias em seu aplicativo. Embora não falemos sobre os detalhes desses includes, se tiver curiosidade, use o Visual Studio para dar uma olhada nas definições e ver de quais namespaces eles fazem parte.

## <a name="system-section"></a>Seção do sistema

A seção de sistema tem o que você precisa para fazer seu bot funcionar como um aplicativo padrão. Ele inclui os arquivos de configuração, o adaptador e alguns arquivos JSON. Esses itens podem (e geralmente devem) ser deixados de lado, exceto alguns arquivos de configuração que talvez precisem de suas informações específicas.

# <a name="ctabcs"></a>[C#](#tab/cs)

Um bot é um tipo de estrutura do aplicativo [Web do ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-2.1). Se examinar os [conceitos básicos do ASP.NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x), você verá um código semelhante em arquivos como appsettings.json, Program.cs e Startup.cs discutidos abaixo. Esses arquivos são necessários para todos os aplicativos Web e não são específicos do bot. O código em alguns desses arquivos não será copiado aqui, mas você o verá ao executar o bot.

### <a name="appsettingsjson"></a>appsettings.json

Esse arquivo contém apenas as informações de configuração do bot, principalmente a ID do aplicativo e a senha, em um formato JSON básico.  Ao testar com o [emulador](../bot-service-debug-emulator.md), essas informações não são necessárias e podem ser deixadas em branco, mas elas são necessárias em produção.

### <a name="programcs"></a>Program.cs

Esse arquivo é necessário para que seu aplicativo Web ASP.NET funcione e especifique a classe `Startup` a ser executada.

### <a name="startupcs"></a>Startup.cs

**Startup.cs** tem outras partes interessantes que serão abordadas posteriormente, mas não falaremos aqui sobre as seções do sistema.

O construtor para `Startup` especifica as variáveis de ambiente e as configurações do aplicativo e cria a configuração do aplicativo Web.

O método `Configure` termina a configuração do seu aplicativo, especificando que o aplicativo use o Bot Framework e alguns outros arquivos. Todos os bots que usam o Bot Framework precisarão dessa chamada de configuração.

```cs
using System;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Bot.Builder.TraceExtensions;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace BasicEcho
{
    public class Startup
    {
        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();

            Configuration = builder.Build();
        }

        public IConfiguration Configuration { get; }

        // ...
        // Definition of ConfigureServices covered in the next section
        // ...

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseDefaultFiles()
                .UseStaticFiles()
                .UseBotFramework();
        }
    }
}

```

### <a name="launchsettingsjson-and-readmemd"></a>launchsettings.JSON e readme.md

**launchSettings.json** simplesmente contém um conjunto de configurações para aplicativos Web, e **readme.md** é uma explicação simples de uma linha sobre o bot de eco. O conteúdo desses arquivos não é importante para você compreender esse bot. 

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

A seção do sistema contém principalmente **package.json**, o arquivo **.env**, **app.js** e **README.md**. O código em alguns arquivos não será copiado aqui, mas você o verá ao executar o bot.

### <a name="packagejson"></a>package.json

**package.json** especifica as dependências e suas versões associadas para o bot. Isso é configurado pelo modelo e seu sistema.

### <a name="env-file"></a>arquivo. env

O arquivo **.env** especifica as informações de configuração para o bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto. 

Para usar o arquivo de configuração **.env**, o modelo precisará de um pacote adicional incluído.  Primeiro, obtenha o pacote `dotenv` do npm:

`npm install dotenv`

Em seguida, adicione a seguinte linha ao seu bot com as outras bibliotecas necessárias:

```javascript
const dotenv = require('dotenv');
```

### <a name="appjs"></a>app.js

A parte superior do seu arquivo `app.js` tem o servidor e o adaptador que permitem que seu bot se comunique com o usuário e envie respostas. O servidor escutará na porta especificada da configuração **.env** ou fazer fallback para 3978 para estabelecer conexão com o emulador. O adaptador atuará como o condutor para o bot, direcionando a comunicação de entrada e saída, autenticação e assim por diante. 

```javascript
// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({ 
    appId: process.env.MICROSOFT_APP_ID, 
    appPassword: process.env.MICROSOFT_APP_PASSWORD 
});
``` 

### <a name="readmemd"></a>README.md

**README.md** fornece algumas informações úteis sobre alguns aspectos da criação de um bot, como a estrutura de um bot básico ou o que são *Diálogos*, mas isso não é importante para compreender seu bot.

---


## <a name="helper-items"></a>Itens auxiliares

Como qualquer programa, ter métodos auxiliares e outras definições pode simplificar o nosso código. Há algumas partes do nosso bot que se enquadram nessa categoria, como o arquivo `.bot`, que são importantes para nosso bot, mas que não fazem parte necessariamente da lógica principal do bot.

### <a name="bot-file"></a>arquivo .bot

O arquivo `.bot` contém informações, incluindo o ponto de extremidade, a ID do aplicativo e a senha, que permitem que os [canais](bot-concepts.md) se conectem ao seu bot. Esse arquivo é criado quando você começa a criar um bot a partir de um modelo, mas é possível criar um personalizado por meio do emulador ou de outras ferramentas.

O conteúdo do arquivo deve corresponder ao que você vê aqui, a menos que você nomeie seu bot como algo diferente de *BasicEcho*. Talvez você precise alterar e atualizar essas informações, dependendo de como seu bot é usado, mas para executar este Bot de Eco, nenhuma alteração será necessária no momento.

```json
{
  "name": "BasicEcho",
  "secretKey": "",
  "services": [
    {
      "appId": "",
      "id": "http://localhost:3978/api/messages",
      "type": "endpoint",
      "appPassword": "",
      "endpoint": "http://localhost:3978/api/messages",
      "name": "BasicEcho"
    }
  ]
}
```

# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="echostatecs"></a>EchoState.cs

**EchoState.cs** contém uma classe simples que nosso bot usa para manter nosso estado atual. Ele contém apenas um `int` que usamos para incrementar o contador de turnos. Falaremos mais sobre o estado na próxima seção mas, por enquanto, você só precisa entender que `EchoState` é nossa classe que contém a contagem de turnos.

```cs
namespace BasicEcho
{
    /// <summary>
    /// Class for storing conversation state.
    /// </summary>
    public class EchoState
    {
        public int TurnCount { get; set; } = 0;
    }
}
```

### <a name="defaulthtm"></a>default.htm

**default.htm** é a página da Web que é exibida quando você executa seu bot, gravado em `html`. Ele exibe informações úteis para a conexão do seu bot e como interagir com ele, mas o conteúdo da página não afeta o comportamento do bot. Você verá o código pop-up quando executar o bot.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="required-libraries"></a>Bibliotecas necessárias

Na parte superior do seu arquivo `app.js`, você encontrará uma série de módulos ou bibliotecas que estão sendo solicitados. Esses módulos oferecerão a você acesso a um conjunto de funções que talvez você queira incluir em seu aplicativo. 

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const restify = require('restify');
```

---

## <a name="core-of-the-bot"></a>Núcleo do bot

Por fim, o que realmente interessa a você! O núcleo do bot define como o bot interage com o usuário, incluindo middleware e a lógica do bot.

# <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="middleware"></a>Middleware

Em **Startup.cs**, há um método chamado `ConfigureServices`. Esse método configura seu provedor de credenciais e adiciona o middleware.

O [middleware](bot-builder-concept-middleware.md) adicionado aqui faz duas coisas. A primeira é a manipulação de exceções, que permite que seu bot falhe normalmente. Ele é definido em linha como uma expressão lambda, simplesmente imprimindo a exceção no terminal e informando ao usuário que algo deu errado.

O segundo middleware mantém seu estado, usando `MemoryStorage` que foi definido logo antes dele. Esse estado é definido como `ConversationState`, o que significa apenas que ele está mantendo o estado da conversa. Ele usa `EchoState`, a classe que definimos com seu contador de turnos, para armazenar as informações que interessam a você.

``` cs
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);

        // The CatchExceptionMiddleware provides a top-level exception handler for your bot.
        // Any exceptions thrown by other Middleware, or by your OnTurn method, will be 
        // caught here. To facilitate debugging, the exception is sent out, via Trace, 
        // to the emulator. Trace activities are NOT displayed to users, so in addition
        // an "Ooops" message is sent.
        options.Middleware.Add(new CatchExceptionMiddleware<Exception>(async (context, exception) =>
        {
            await context.TraceActivity("EchoBot Exception", exception);
            await context.SendActivity("Sorry, it looks like something went wrong!");
        }));

        // The Memory Storage used here is for local bot debugging only. When the bot
        // is restarted, anything stored in memory will be gone. 
        IStorage dataStore = new MemoryStorage();

        // The File data store, shown here, is suitable for bots that run on 
        // a single machine and need durable state across application restarts.

        // IStorage dataStore = new FileStorage(System.IO.Path.GetTempPath());

        // For production bots use the Azure Table Store, Azure Blob, or 
        // Azure CosmosDB storage provides, as seen below. To include any of 
        // the Azure based storage providers, add the Microsoft.Bot.Builder.Azure 
        // Nuget package to your solution. That package is found at:
        //      https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/

        // IStorage dataStore = new Microsoft.Bot.Builder.Azure.AzureTableStorage("AzureTablesConnectionString", "TableName");
        // IStorage dataStore = new Microsoft.Bot.Builder.Azure.AzureBlobStorage("AzureBlobConnectionString", "containerName");

        options.Middleware.Add(new ConversationState<EchoState>(dataStore));
    });
}
```

### <a name="bot-logic"></a>Lógica do bot

A lógica principal do bot é definida no manipulador `OnTurn` do bot. `OnTurn` é fornecido como uma variável [context](./bot-builder-concept-activity-processing.md#turn-context) como um argumento, que fornece informações sobre a [atividade](bot-builder-concept-activity-processing.md) de entrada, a conversa etc.

As atividades de entrada podem ser de [vários tipos](../bot-service-activities-entities.md#activity-types), portanto, primeiro verificamos se seu bot recebeu uma mensagem. Se for uma mensagem, podemos criar uma variável de estado que contém as informações atuais de conversa do seu bot. Em seguida, incrementamos o `int` que mantém o controle da sua contagem de turnos antes de ecoar de volta para o usuário a contagem junto com a mensagem enviada por eles.

```cs
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace BasicEcho
{
    public class EchoBot : IBot
    {
        /// <summary>
        /// Every Conversation turn for our EchoBot will call this method. In here
        /// the bot checks the Activty type to verify it's a message, bumps the 
        /// turn conversation 'Turn' count, and then echoes the users typing
        /// back to them. 
        /// </summary>
        /// <param name="context">Turn scoped context containing all the data needed
        /// for processing this conversation turn. </param>        
        public async Task OnTurn(ITurnContext context)
        {
            // This bot is only handling Messages
            if (context.Activity.Type == ActivityTypes.Message)
            {
                // Get the conversation state from the turn context
                var state = context.GetConversationState<EchoState>();

                // Bump the turn count. 
                state.TurnCount++;

                // Echo back to the user whatever they typed.
                await context.SendActivity($"Turn {state.TurnCount}: You sent '{context.Activity.Text}'");
            }
        }
    }    
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="middleware"></a>Middleware

Em **App.js**, usamos [middleware](bot-builder-concept-middleware.md) neste bot para manter seu estado, usando `MemoryStorage` que foi definido como um dos nossos módulos necessários na parte superior do `botbuilder`. Esse estado é definido como `ConversationState`, o que significa apenas que ele está mantendo o estado da conversa. `ConversationState` armazenará as informações que interessam a você, o que neste caso é simplesmente um contador de turnos, na memória.

```javascript
// Add conversation state middleware
const conversationState = new ConversationState(new MemoryStorage());
adapter.use(conversationState);
```

### <a name="bot-logic"></a>Lógica do bot

O terceiro parâmetro em *processActivity* é um manipulador de funções que será chamado para executar a lógica do bot depois que a [atividade](bot-builder-concept-activity-processing.md) recebida tiver sido pré-processada pelo adaptador e roteada por meio de qualquer middleware. A variável [context](./bot-builder-concept-activity-processing.md#turn-context), passada como um argumento para o manipulador de funções, pode ser usada para fornecer informações sobre a atividade de entrada, o remetente e o destinatário, o canal, a conversa etc.

Primeiro, verificamos se o bot recebeu uma mensagem. Se o bot não tiver recebido uma mensagem, repetiremos o tipo de atividade recebido. Em seguida, criamos uma variável de estado que armazena as informações de conversa do seu bot. Então, definimos a variável de contagem como 0 se esta for indefinida (o que ocorrerá quando você inicia o bot) ou a incrementaremos a cada nova mensagem. Por fim, enviamos a contagem de volta ao usuário junto com a mensagem enviada por ele.

```javascript
// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        // This bot is only handling Messages
        if (context.activity.type === 'message') {

            // Get the conversation state
            const state = conversationState.get(context);

            // If state.count is undefined set it to 0, otherwise increment it by 1
            const count = state.count === undefined ? state.count = 0 : ++state.count;

            // Echo back to the user whatever they typed.
            return context.sendActivity(`${count}: You said "${context.activity.text}"`);
        } else {
            // Echo back the type of activity the bot detected if not of type message
            return context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

---
