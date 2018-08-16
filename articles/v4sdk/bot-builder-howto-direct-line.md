---
title: Como criar um cliente e um bot de Linha Direta | Microsoft Docs
description: Saiba como criar um cliente e um bot de Linha Direta com V4 do SDK do Construtor de Bot para .NET.
keywords: bot de linha direta, cliente de linha direta, canal personalizado, com base no console, publicação
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/16/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ac96e35763d690c91e6584ff9a840b490e0a32cd
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296603"
---
# <a name="how-to-create-a-direct-line-bot-and-client"></a>Como criar um bot e um cliente de Linha Direta

Bots de Linha Direta do Microsoft Bot Framework são bots que podem funcionar com um cliente personalizado criado por você. Os bots de Linha Direta são incrivelmente semelhantes aos bots normais. Eles apenas não precisam usar os canais fornecidos.

Os clientes de Linha Direta podem ser escritos para ser tudo o que você quiser. Você pode escrever um cliente para Android, um cliente para iOS ou até mesmo um aplicativo cliente baseado em console.

Neste tópico, você aprenderá a criar e implantar um bot de Linha Direta e a criar e executar um aplicativo de cliente de Linha Direta baseado em console.

## <a name="create-your-bot"></a>Criar seu bot

Cada linguagem segue um caminho diferente para a criação do bot. O JavaScript cria o bot no Azure, depois modifica o código, enquanto C# cria o bot localmente e o publica no Azure, mas ambos são métodos válidos e podem ser usados com qualquer uma dessas linguagens. Se você quiser detalhes sobre como publicar seu bot no Azure, dê uma olhada em [implantar seu bot no Azure](../bot-builder-howto-deploy-azure.md).

# <a name="ctabcscreatebot"></a>[C#](#tab/cscreatebot)

### <a name="create-the-solution-in-visual-studio"></a>Criar a solução no Visual Studio

Para criar a solução para o bot de Linha Direta no Visual Studio 2015 ou posterior:

1. Crie um novo **aplicativo Web ASP.NET Core**, no **Visual C#** > **.NET Core**.

1. Para o nome, insira **DirectLineBotSample** e clique em **OK**.

1. Selecione **.NET Core** e **ASP.NET Core 2.0**, escolha o modelo de projeto **Vazio** e, em seguida, clique em **OK**.

#### <a name="add-dependencies"></a>Adicionar dependências

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Dependências**, depois escolha **Gerenciar Pacotes NuGet**.

1. Clique em **Procurar** e verifique se a caixa de seleção **Incluir pré-lançamento** está marcada.

1. Procure e instale os seguintes pacotes NuGet:
    - Microsoft.Bot.Builder
    - Microsoft.Bot.Builder.Core.Extensions
    - Microsoft.Bot.Builder.Integration.AspNet.Core
    - Newtonsoft.Json

### <a name="create-the-appsettingsjson-file"></a>Criar o arquivo appsettings.json

O arquivo appSettings.json contém a ID de Aplicativo da Microsoft, a Senha de Aplicativo e a cadeia de caracteres de Conexão de Dados. Como esse bot não armazenará as informações de estado, a cadeia de caracteres de Conexão de Dados permanecerá vazia. E, se você estiver usando apenas o Emulador do Bot Framework, todos poderão permanecer vazios.

Para criar um arquivo **appsettings.json**:

1. Clique com o botão direito do mouse no projeto **DirectLineBotSample**, escolha **Adicionar** > **Novo Item**.

1. Em **ASP.NET Core**, clique em **Arquivo de Configuração ASP.NET**.

1. Digite **appsettings.json** no nome e clique em **Adicionar**.

Substitua o conteúdo do arquivo appsettings.json pelo código a seguir:

```json
{
    "Logging": {
        "IncludeScopes": false,
        "Debug": {
            "LogLevel": {
                "Default": "Warning"
            }
        },
        "Console": {
            "LogLevel": {
                "Default": "Warning"
            }
        }
    },

    "MicrosoftAppId": "",
    "MicrosoftAppPassword": "",
    "DataConnectionString": ""
}
```

### <a name="edit-startupcs"></a>Editar Startup.cs

Substitua o conteúdo do arquivo Startup.cs pelo seguinte:

**Observação**: **DirectBot** será definido na próxima etapa, portanto, ignore o sublinhado vermelho sobre ele.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace DirectLineBotSample
{
    public class Startup
    {
        public IConfiguration Configuration { get; }

        public Startup(IHostingEnvironment env)
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(env.ContentRootPath)
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                .AddEnvironmentVariables();
            Configuration = builder.Build();
        }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton(_ => Configuration);
            services.AddBot<DirectBot>(options =>
            {
                options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
            });
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseDefaultFiles();
            app.UseStaticFiles();
            app.UseBotFramework();
        }
    }
}
```

### <a name="create-the-directbot-class"></a>Criar a classe DirectBot

A classe DirectBot contém grande parte da lógica para esse bot.

1. Clique com o botão direito do mouse em **DirectLineBotSample** > **Adicionar** > **Novo Item**.

1. No **ASP.NET Core**, clique em **Classe**.

1. Digite **DirectBot.cs** para o nome e clique em **Adicionar**.

Substitua o conteúdo do arquivo DirectBot.cs pelo seguinte:

```csharp
using System.Threading.Tasks;
using Microsoft.Bot;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

namespace DirectLineBotSample
{
    public class DirectBot : IBot
    {
        public Task OnTurn(ITurnContext context)
        {
            // Respond to the various activity types.
            switch (context.Activity.Type)
            {
                case ActivityTypes.Message:
                    // Respond to the incoming text message.
                    RespondToMessage(context);
                    break;

                case ActivityTypes.ConversationUpdate:
                    break;

                case ActivityTypes.ContactRelationUpdate:
                    break;

                case ActivityTypes.Typing:
                    break;

                case ActivityTypes.Ping:
                    break;

                case ActivityTypes.DeleteUserData:
                    break;
            }

            return Task.CompletedTask;
        }

        /// <summary>
        /// Responds to the incoming message by either sending a hero card, an image, 
        /// or echoing the user's message.
        /// </summary>
        /// <param name="context">The context of this conversation.</param>
        private void RespondToMessage(ITurnContext context)
        {
            switch (context.Activity.Text.Trim().ToLower())
            {
                case "hi":
                case "hello":
                case "help":
                    // Send the user an instruction message.
                    context.SendActivity("Welcome to the Bot to showcase the DirectLine API. " +
                    "Send \"Show me a hero card\" or \"Send me a BotFramework image\" to see how the " +
                    "DirectLine client supports custom channel data. Any other message will be echoed.");
                    break;

                case "show me a hero card":
                    // Create the hero card.
                    HeroCard heroCard = new HeroCard()
                    {
                        Title = "Sample Hero Card",
                        Text = "Displayed in the DirectLine client"
                    };

                    // Attach the hero card to a new activity.
                    context.SendActivity(MessageFactory.Attachment(heroCard.ToAttachment()));
                    break;

                case "send me a botframework image":
                    // Create the image attachment.
                    Attachment imageAttachment = new Attachment()
                    {
                        ContentType = "image/png",
                        ContentUrl = "https://docs.microsoft.com/en-us/bot-framework/media/how-it-works/architecture-resize.png",
                    };

                    // Attach the image attachment to a new activity.
                    context.SendActivity(MessageFactory.Attachment(imageAttachment));
                    break;

                default:
                    // No command was encountered. Echo the user's message.
                    context.SendActivity($"You said \"{context.Activity.Text}\"");
                    break;
            }
        }
    }
}
```

**Observação:** o arquivo Program.cs existente não precisa ser alterado.

Para verificar se as coisas estão em ordem, pressione **F6** para compilar o projeto. Não deve haver nenhum aviso ou erro.

### <a name="publish-the-bot-from-visual-studio"></a>Publicar o bot do Visual Studio

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto **DirectLineBotSample** e clique em **Configurar como Projeto de Inicialização**.

    ![Página de publicação do visual studio](media/bot-builder-howto-direct-line/visual-studio-publish-page.png)

1. Clique com o botão direito do mouse em **DirectLineBotSample** novamente e clique em **Publicar**. A página **Publicar** do Visual Studio é exibida.

1. Se você já tiver um perfil de publicação para este bot, selecione-o e clique no botão **Publicar**, depois vá para a próxima seção.

1. Para criar um novo perfil de publicação, selecione o ícone **Serviço de Aplicativo do Microsoft Azure**.

1. Selecione **Criar novo**.

1. Clique no botão **Publicar** . A caixa de diálogo **Criar Serviço de Aplicativo** é exibida.

    ![A caixa de diálogo criar serviço de aplicativo](media/bot-builder-howto-direct-line/create-app-service-dialog.png)

    - Para o Nome do Aplicativo, dê um nome que você consiga encontrar depois. Por exemplo, você pode adicionar seu nome de email no início do Nome do Aplicativo.

    - Verifique se você está usando a assinatura correta.

    - Para o Grupo de Recursos, verifique se você está usando o grupo de recursos correto. O grupo de recursos pode ser encontrado na folha **Visão geral** do seu bot. **Observação:** um grupo de recursos incorreto é difícil de corrigir.

    - Verifique se você está usando o Plano do Serviço de Aplicativo correto.
    
1. Selecione o botão **Criar**. Visual Studio começará a implantar seu bot.

Após a publicação do seu bot, um navegador será exibido com o ponto de extremidade da URL do seu bot.

### <a name="create-bot-channels-registration-bot-on-microsoft-azure"></a>Criar um bot de Registro de Canais de Bot no Microsoft Azure

O bot de Linha Direta pode ser hospedado em qualquer plataforma. Neste exemplo, o bot será hospedado no Microsoft Azure. 

Para criar o bot no Microsoft Azure:

1. No Portal do Microsoft Azure, clique em **Criar um recurso**, depois procure "Registro de Canais de Bot".

1. Clique em **Criar**. A folha Registro de Canais de Bot é exibida.

    ![A folha de registro de canais de bot, com campos para o nome do bot, assinatura, grupo de recursos, local, tipo de preço, ponto de extremidade de mensagens e outros campos.](media/bot-builder-howto-direct-line/bot-service-registration-blade.png)

1. Na folha Registro de Canais de Bot, insira o **Nome do bot**, a **Assinatura**, o **Grupo de recursos**, o **local** e o **Tipo de preço**.

1. Deixe **Ponto de extremidade de mensagens** em branco. Esse valor será preenchido posteriormente.

1. Clique em **ID e senha do Aplicativo Microsoft**, depois clique em **Criar ID do Aplicativo e senha automaticamente**.

1. Marque a caixa de seleção **Fixar no painel**.

1. Selecione o botão **Criar**.

Implantação demorará alguns minutos, mas quando terminar deverá aparecer em seu painel.

### <a name="update-the-appsettingsjson-file"></a>Atualizar o arquivo appsettings.json

1. Clique no bot em seu painel, ou acesse seu novo recurso clicando em **Todos os recursos** e procurando o nome do seu Registro de Canais de Bot.

1. Clique em **Visão Geral**.

1. Copie o nome do Grupo de recursos na cadeia de caracteres **botId** em **Program.cs** do projeto **DirectLineClientSample**.

1. Clique em **Configurações**, depois clique em **Gerenciar** perto do campo **ID do Aplicativo Microsoft**.

1. Copie a ID do Aplicativo e cole-a no campo **"MicrosoftAppId"** do arquivo **appsettings.json**.

1. Clique no botão **Gerar Nova Senha**.

1. Copie a nova senha e cole-a no campo **"MicrosoftAppPassword"** do arquivo **appsettings.json**.

### <a name="set-the-messaging-endpoint"></a>Definir o ponto de extremidade de mensagens

Para adicionar o ponto de extremidade ao seu bot:

1. Copie o endereço da URL do navegador que apareceu após a publicação do seu bot.

    ![A folha de configurações do registro de canais do bot, realçando o ponto de extremidade de mensagens](media/bot-builder-howto-direct-line/bot-channels-registration-settings.png)

1. Abra a folha **Configurações** do seu bot.

1. Cole o endereço no **Ponto de extremidade de mensagens**.

1. Edite o endereço para começar com "https://" e terminar com "/ api/mensagens". Por exemplo, se o endereço copiado do navegador for "http://v-royhar-dlbot-directlinebotsample20180329044602.azurewebsites.net", edite-o para "https://v-royhar-dlbot-directlinebotsample20180329044602.azurewebsites.net/api/messages".

1. Clique no botão **Salvar** na folha Configurações.

**OBSERVAÇÃO:** se a publicação falhar, talvez seja necessário parar seu bot no Azure antes de publicar as alterações no bot.

1. Localize o nome do seu Serviço de Aplicativo (está entre "https://" e ".azurewebsites.net".

1. Pesquise esse recurso em "Todos os recursos" do Portal do Azure.

1. Clique no recurso Serviço de Aplicativo. A folha Serviço de Aplicativo é exibida.

1. Clique no botão **Parar** na folha do Serviço de Aplicativo.

1. No Visual Studio, publique seu bot novamente.

1. Clique no botão **Iniciar** na folha do Serviço de Aplicativo.

### <a name="test-your-bot-in-webchat"></a>Teste seu bot no chat na Web

Para verificar se o seu bot está funcionando, verifique-o no chat na Web:

1. Na folha Registro de Canais do Bot do seu bot, clique em **Configurações**, depois **Teste no Chat da Web**.

1. Digite "Oi". O bot deve responder com uma mensagem de boas-vindas.

1. Digite "mostre-me um cartão hero". O bot deve exibir um cartão hero.

1. Digite "enviar uma imagem do botframework". O bot deve exibir uma imagem da documentação do Bot Framework.

1. Digite qualquer outra coisa, e o bot deve responder com, "Você disse" e a mensagem entre aspas.

### <a name="troubleshooting"></a>solução de problemas

Se seu bot não estiver funcionando, verifique ou insira novamente sua ID de Aplicativo e senha da Microsoft. Mesmo se você tiver copiado antes, compare a ID de Aplicativo da Microsoft na folha Configurações do seu Registro de Canais do Bot com o valor do campo **"MicrosoftAppId"** no arquivo appsettings.json.

Verifique sua senha ou crie e use uma nova senha: 

1. Clique em **Gerenciar** ao lado do campo **ID do Aplicativo da Microsoft** na folha do seu Registro de Canais do Bot.

1. Faça logon no Portal de Registro de Aplicativos.

1. Verifique se as três primeiras letras da sua senha correspondem ao campo **"MicrosoftAppPassword"** no arquivo **appsettings.json**. 

1. Se os valores não corresponderem, gere uma nova senha e armazene esse valor no campo **"MicrosoftAppPassword"** no arquivo **appsettings.json**.

# <a name="javascripttabjscreatebot"></a>[JavaScript](#tab/jscreatebot)

### <a name="create-web-app-bot-on-microsoft-azure"></a>Criar bot de aplicativo Web no Microsoft Azure

Para criar o bot no Microsoft Azure: 

1. No Portal do Microsoft Azure, clique em **Criar um recurso**, depois procure "Bot de Aplicativo Web".

1. Clique em **Criar**. A folha Bot de Aplicativo Web é exibida.

![Registro de bot de aplicativo Web](media/bot-builder-howto-direct-line/web-app-bot-registration.png)

1. Na folha Bot de Aplicativo Web, insira o **Nome do bot**, a **Assinatura**, o **Grupo de recursos**, o **local**, o **Tipo de preço**, o **Nome do aplicativo**.

1. Selecione o modelo de bot que você quer usar. **Versão do SDK: SDK v4** e **Linguagem do SDK: Node.js** 

1. Selecione o modelo de visualização v4 básico. 

1. Escolha seu **Plano do Serviço de Aplicativo/Local**, **Armazenamento do Azure** e **Local do Application Insights**.

1. Marque a caixa de seleção Fixar no painel.

1. Clique no botão Criar.

1. Aguarde a implantação de seu bot. Como você marcou a caixa de seleção Fixar no painel, seu bot será exibido em seu painel.

### <a name="edit-your-direct-line-bot"></a>Editar seu Bot de Linha Direta

Agora vamos adicionar outros recursos ao bot de eco.

1. Na folha Bot do Aplicativo Web, clique em Compilar. 

1. Clique em Baixar arquivo zip. 

1. Extraia o arquivo .zip para um diretório local.

1. Navegue até a pasta extraída e abra os arquivos de código-fonte em seu IDE favorito.

1. No arquivo app.js, copie e cole o código abaixo.

```javascript
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License.

const { BotFrameworkAdapter, MessageFactory, CardFactory } = require('botbuilder');
const restify = require('restify');

// Create server
let server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
    console.log(`${server.name} listening to ${server.url}`);
});

// Create adapter
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

// Responds to the incoming message by either sending a hero card, an image, 
// or echoing the user's message.

// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, (context) => {
        if (context.activity.type === 'message') {
            const text = (context.activity.text || '').trim().toLowerCase()

            switch(text){
                case 'hi':
                case "hello":
                case "help":
                    // Send the user an instruction message.
                    return context.sendActivity("Welcome to the Bot to showcase the DirectLine API. " +
                    "Send \"Show me a hero card\" or \"Send me a BotFramework image\" to see how the " +
                    "DirectLine client supports custom channel data. Any other message will be echoed.");
                    break;

                case 'show me a hero card':
                    // Create the hero card.
                    const message = MessageFactory.attachment(
                        CardFactory.heroCard(   
                        'Sample Hero Card', //cards title
                        'Displayed in the DirectLine client' //cards text
                        )
                    );
                    return context.sendActivity(message);
                    break;
                    
                case 'send me a botframework image':
                    // Create the image attachment.
                    const imageOrVideoMessage = MessageFactory.contentUrl('https://docs.microsoft.com/en-us/azure/bot-service/media/how-it-works/architecture-resize.png', 'image/png')
                    return context.sendActivity(imageOrVideoMessage);
                    break;
                
                default:
                    // No command was encountered. Echo the user's message.
                    return context.sendActivity(`You said ${context.activity.text}`);
                    break;
                    
            }
        }
    });
});

```

Quando estiver pronto, publique o código-fonte de volta no Azure. [Execute essas etapas para saber como publicar seu bot de volta no Azure](../bot-service-build-download-source-code.md#publish-node-bot-source-code-to-azure)

---


## <a name="create-the-console-client-app"></a>Criar o aplicativo de cliente do console

# <a name="ctabcsclientapp"></a>[C#](#tab/csclientapp)

O aplicativo de cliente do console opera em dois threads. O thread primário aceita entradas do usuário e envia mensagens ao bot. O thread secundário sonda o bot uma vez por segundo para recuperar todas as mensagens do bot e, depois, exibe as mensagens recebidas.

Para criar um projeto de console:

1. Clique com o botão direito do mouse em **Solução 'DirectLineBotSample'** no Gerenciador de Soluções.

1. Escolha **Adicionar** > **Novo Projeto**.

1. Em **Visual C#** > **Área de Trabalho Clássica do Windows*, escolha **Aplicativo de Console (.NET Framework)**.
 
    **Observação:** não escolha **Aplicativo de Console (.NET Core)**.

1. Para o nome, insira **DirectLineClientSample**.

1. Clique em **OK**.

### <a name="add-the-nuget-packages-to-the-console-app"></a>Adicionar os pacotes NuGet ao aplicativo de console

1. Clique com o botão direito do mouse em **Referências**.

1. Clique em **Gerenciar Pacotes NuGet**

1. Clique em **Procurar**. Verifique se a caixa de seleção **Incluir pré-lançamento** está marcada.

1. Procure e instale os seguintes pacotes NuGet:
    - Microsoft.Bot.Connector.DirectLine (v3.0.2)
    - Newtonsoft.Json

### <a name="edit-the-programcs-file"></a>Editar o arquivo Program.cs

Substitua o conteúdo do arquivo **Program.cs** de DirectLineClientSample pelo código a seguir:

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Bot.Connector.DirectLine;
using Newtonsoft.Json;

namespace DirectLineClientSample
{
    class Program
    {
        // ************
        // Replace the following values with your Direct Line secret and the name of your bot resource ID.
        //*************
        private static string directLineSecret = "*** Replace with Direct Line secret ***";
        private static string botId = "*** Replace with the resource name of your bot ***";

        // This gives a name to the bot user.
        private static string fromUser = "DirectLineClientSampleUser";

        static void Main(string[] args)
        {
            StartBotConversation().Wait();
        }


        /// <summary>
        /// Drives the user's conversation with the bot.
        /// </summary>
        /// <returns></returns>
        private static async Task StartBotConversation()
        {
            // Create a new Direct Line client.
            DirectLineClient client = new DirectLineClient(directLineSecret);

            // Start the conversation.
            var conversation = await client.Conversations.StartConversationAsync();

            // Start the bot message reader in a separate thread.
            new System.Threading.Thread(async () => await ReadBotMessagesAsync(client, conversation.ConversationId)).Start();

            // Prompt the user to start talking to the bot.
            Console.Write("Type your message (or \"exit\" to end): ");

            // Loop until the user chooses to exit this loop.
            while (true)
            {
                // Accept the input from the user.
                string input = Console.ReadLine().Trim();

                // Check to see if the user wants to exit.
                if (input.ToLower() == "exit")
                {
                    // Exit the app if the user requests it.
                    break;
                }
                else
                {
                    if (input.Length > 0)
                    {
                        // Create a message activity with the text the user entered.
                        Activity userMessage = new Activity
                        {
                            From = new ChannelAccount(fromUser),
                            Text = input,
                            Type = ActivityTypes.Message
                        };

                        // Send the message activity to the bot.
                        await client.Conversations.PostActivityAsync(conversation.ConversationId, userMessage);
                    }
                }
            }
        }


        /// <summary>
        /// Polls the bot continuously and retrieves messages sent by the bot to the client.
        /// </summary>
        /// <param name="client">The Direct Line client.</param>
        /// <param name="conversationId">The conversation ID.</param>
        /// <returns></returns>
        private static async Task ReadBotMessagesAsync(DirectLineClient client, string conversationId)
        {
            string watermark = null;

            // Poll the bot for replies once per second.
            while (true)
            {
                // Retrieve the activity set from the bot.
                var activitySet = await client.Conversations.GetActivitiesAsync(conversationId, watermark);
                watermark = activitySet?.Watermark;

                // Extract the activies sent from our bot.
                var activities = from x in activitySet.Activities
                                 where x.From.Id == botId
                                 select x;

                // Analyze each activity in the activity set.
                foreach (Activity activity in activities)
                {
                    // Display the text of the activity.
                    Console.WriteLine(activity.Text);

                    // Are there any attachments?
                    if (activity.Attachments != null)
                    {
                        // Extract each attachment from the activity.
                        foreach (Attachment attachment in activity.Attachments)
                        {
                            switch (attachment.ContentType)
                            {
                                // Display a hero card.
                                case "application/vnd.microsoft.card.hero":
                                    RenderHeroCard(attachment);
                                    break;

                                // Display the image in a browser.
                                case "image/png":
                                    Console.WriteLine($"Opening the requested image '{attachment.ContentUrl}'");
                                    Process.Start(attachment.ContentUrl);
                                    break;
                            }
                        }
                    }

                    // Redisplay the user prompt.
                    Console.Write("\nType your message (\"exit\" to end): ");
                }

                // Wait for one second before polling the bot again.
                await Task.Delay(TimeSpan.FromSeconds(1)).ConfigureAwait(false);
            }
        }


        /// <summary>
        /// Displays the hero card on the console.
        /// </summary>
        /// <param name="attachment">The attachment that contains the hero card.</param>
        private static void RenderHeroCard(Attachment attachment)
        {
            const int Width = 70;
            // Function to center a string between asterisks.
            Func<string, string> contentLine = (content) => string.Format($"{{0, -{Width}}}", string.Format("{0," + ((Width + content.Length) / 2).ToString() + "}", content));

            // Extract the hero card data.
            var heroCard = JsonConvert.DeserializeObject<HeroCard>(attachment.Content.ToString());

            // Display the hero card.
            if (heroCard != null)
            {
                Console.WriteLine("/{0}", new string('*', Width + 1));
                Console.WriteLine("*{0}*", contentLine(heroCard.Title));
                Console.WriteLine("*{0}*", new string(' ', Width));
                Console.WriteLine("*{0}*", contentLine(heroCard.Text));
                Console.WriteLine("{0}/", new string('*', Width + 1));
            }
        }
    }
}
```

Para verificar se as coisas estão em ordem, pressione **F6** para compilar o projeto. Não deve haver nenhum aviso ou erro.

# <a name="javascripttabjsclientapp"></a>[JavaScript](#tab/jsclientapp)

### <a name="create-a-direct-line-client"></a>Criar um Cliente de Linha Direta 

Agora que você implantou seu bot de aplicativo Web, podemos criar um Cliente de Linha Direta.

```
    md DirectLineClient
    cd DirectLineClient
    npm init
```

Em seguida, instale esses pacotes a partir do npm

```
    npm install --save open
    npm install --save request
    npm install --save request-promise
    npm install --save swagger-client
```

Crie um arquivo **app.js**. Copie e cole o código abaixo nesse arquivo.

Obteremos o directLineSecret na próxima etapa.
```javascript
var Swagger = require('swagger-client');
var open = require('open');
var rp = require('request-promise');

// config items
var pollInterval = 1000;
// Change the Direct Line Secret to your own 
var directLineSecret = 'your secret here';
var directLineClientName = 'DirectLineClient';
var directLineSpecUrl = 'https://docs.botframework.com/en-us/restapi/directline3/swagger.json';

var directLineClient = rp(directLineSpecUrl)
    .then(function (spec) {
        // client
        return new Swagger({
            spec: JSON.parse(spec.trim()),
            usePromise: true
        });
    })
    .then(function (client) {
        // add authorization header to client
        client.clientAuthorizations.add('AuthorizationBotConnector', new Swagger.ApiKeyAuthorization('Authorization', 'Bearer ' + directLineSecret, 'header'));
        return client;
    })
    .catch(function (err) {
        console.error('Error initializing DirectLine client', err);
    });

// once the client is ready, create a new conversation
directLineClient.then(function (client) {
    client.Conversations.Conversations_StartConversation()                          // create conversation
        .then(function (response) {
            return response.obj.conversationId;
        })                            // obtain id
        .then(function (conversationId) {
            sendMessagesFromConsole(client, conversationId);                        // start watching console input for sending new messages to bot
            pollMessages(client, conversationId);                                   // start polling messages from bot
        })
        .catch(function (err) {
            console.error('Error starting conversation', err);
        });
});

// Read from console (stdin) and send input to conversation using DirectLine client
function sendMessagesFromConsole(client, conversationId) {
    var stdin = process.openStdin();
    process.stdout.write('Command> ');
    stdin.addListener('data', function (e) {
        var input = e.toString().trim();
        if (input) {
            // exit
            if (input.toLowerCase() === 'exit') {
                return process.exit();
            }

            // send message
            client.Conversations.Conversations_PostActivity(
                {
                    conversationId: conversationId,
                    activity: {
                        textFormat: 'plain',
                        text: input,
                        type: 'message',
                        from: {
                            id: directLineClientName,
                            name: directLineClientName
                        }
                    }
                }).catch(function (err) {
                    console.error('Error sending message:', err);
                });

            process.stdout.write('Command> ');
        }
    });
}

// Poll Messages from conversation using DirectLine client
function pollMessages(client, conversationId) {
    console.log('Starting polling message for conversationId: ' + conversationId);
    var watermark = null;
    setInterval(function () {
        client.Conversations.Conversations_GetActivities({ conversationId: conversationId, watermark: watermark })
            .then(function (response) {
                watermark = response.obj.watermark;                                 // use watermark so subsequent requests skip old messages
                return response.obj.activities;
            })
            .then(printMessages);
    }, pollInterval);
}

// Helpers methods
function printMessages(activities) {
    if (activities && activities.length) {
        // ignore own messages
        activities = activities.filter(function (m) { return m.from.id !== directLineClientName });

        if (activities.length) {
            process.stdout.clearLine();
            process.stdout.cursorTo(0);

            // print other messages
            activities.forEach(printMessage);

            process.stdout.write('Command> ');
        }
    }
}

function printMessage(activity) {
    if (activity.text) {
        console.log(activity.text);
    }

    if (activity.attachments) {
        activity.attachments.forEach(function (attachment) {
            switch (attachment.contentType) {
                case "application/vnd.microsoft.card.hero":
                    renderHeroCard(attachment);
                    break;

                case "image/png":
                    console.log('Opening the requested image ' + attachment.contentUrl);
                    open(attachment.contentUrl);
                    break;
            }
        });
    }
}

function renderHeroCard(attachment) {
    var width = 70;
    var contentLine = function (content) {
        return ' '.repeat((width - content.length) / 2) +
            content +
            ' '.repeat((width - content.length) / 2);
    }

    console.log('/' + '*'.repeat(width + 1));
    console.log('*' + contentLine(attachment.content.title) + '*');
    console.log('*' + ' '.repeat(width) + '*');
    console.log('*' + contentLine(attachment.content.text) + '*');
    console.log('*'.repeat(width + 1) + '/');
}
```

---

## <a name="configure-the-direct-line-channel"></a>Configurar o canal da Linha Direta

A adição do canal de Linha Direta torna esse bot um bot de Linha Direta.

Para configurar o canal da Linha Direta:

![A folha Conectar aos canais com o botão do canal de Linha Direta realçado.](media/bot-builder-howto-direct-line/direct-line-channel-button.png)

1. Na folha **Registro de Canais de Bot**, clique em **Canais**. A folha **Conectar-se aos Canais** será exibida.

    ![A folha Configurar Linha Direta com as duas chaves secretas é exibida.](media/bot-builder-howto-direct-line/configure-direct-line.png)

1. Na folha **Conectar aos Canais**, clique no botão **Configurar canal de Linha Direta**. 

1. Se **3.0** não estiver marcado, marque a caixa de seleção.

1. Clique em **Mostrar** em pelo menos uma chave secreta.

1. Copie uma das chaves secretas e cole-a em seu aplicativo cliente, na cadeia de caracteres **directLineSecret**.

1. Clique em Concluído.

## <a name="run-the-client-app"></a>Execute o aplicativo cliente

# <a name="ctabcsrunclient"></a>[C#](#tab/csrunclient)

Seu bot está pronto para se comunicar com o aplicativo de cliente do console de Linha Direta. Para executar o aplicativo de console, faça o seguinte:

1. No Visual Studio, clique com o botão direito do mouse no projeto **DirectLineClientSample** e selecione **Definir como Projeto de Inicialização**.

1. Examine o arquivo **Program.cs** no projeto **DirectLineClientSample**.

    - Verifique se o código secreto de Linha Direta está na cadeia de caracteres **directLineSecret**.

1. Se **directLineSecret** não estiver correto, acesse o Portal do Azure, clique em seu bot de Linha Direta, clique em **Canais**, em **Configurar canal de Linha Direta** (ou **Editar**), copie a chave exibida na cadeia de caracteres **directLineSecret**.

    - Verifique se o grupo de recursos está na cadeia de caracteres **botId**.

1. Se não estiver, na folha **Visão geral**, copie e cole o **Grupo de recursos** na cadeia de caracteres **botId**.

1. Pressione F5 para iniciar a depuração.

O aplicativo de cliente do console será iniciado. Para testar o aplicativo: 

1. Digite "oi". O bot deve exibir "Você disse 'oi'".

1. Insira "mostre-me um cartão hero". O bot deve exibir um cartão hero.

1. Insira "enviar uma imagem do botframework". O bot iniciará um navegador para exibir uma imagem da documentação do Bot Framework.

1. Insira qualquer outra coisa, e o bot deve responder com, "Você disse" e a mensagem entre aspas.

# <a name="javascripttabjsrunclient"></a>[JavaScript](#tab/jsrunclient)

Para executar o exemplo, você precisará executar seu aplicativo DirectLineClient.

1. Abra um console do CMD e execute CD no diretório DirectLineClient

1. Execute o `node app.js`

Para testar as mensagens personalizadas, digite mostre-me um cartão hero ou enviar uma imagem do botframework no console do Cliente, e você verá o resultado abaixo.

![resultado](media/bot-builder-howto-direct-line/outcome.png)

---


### <a name="next-steps"></a>Próximas etapas
