---
title: Traduzir a entrada do usuário para tornar o bot multilíngue | Microsoft Docs
description: Como traduzir a entrada do usuário automaticamente para o idioma nativo do bot e traduzi-la de volta para o idioma do usuário.
keywords: tradução, traduzir, multilíngue, Microsoft Translator
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/06/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 13139755989afccd85b2e09267dc42619ec1f83c
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296730"
---
# <a name="translate-user-input-to-make-your-bot-multilingual"></a>Traduzir a entrada do usuário para tornar o bot multilíngue

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O bot pode usar o [Microsoft Translator](https://www.microsoft.com/en-us/translator/) para traduzir mensagens automaticamente para o idioma que o bot entende e, opcionalmente, traduzir as respostas do bot para o idioma do usuário. A adição de tradução ao bot permite que ele alcance um público-alvo maior sem alterar partes significativas da programação básica do bot.
<!-- 
- [Get a Text Services key](#get-a-text-services-key)
- [Installing Packages](#installing-packages)
- [Combine translation with LUIS or QnA Maker](#using-luis)
- [Combine translation with QnA Maker](#using-qna-maker)
-->

Neste tutorial, usaremos o serviço Microsoft Translator para a tradução e, em seguida, o adicionaremos a um bot simples para mostrar como ele funciona.

## <a name="get-a-text-services-key"></a>Obter uma chave de Serviços de Texto

Primeiro, você precisará de uma chave para usar o serviço Tradução de Texto. Obtenha uma [chave de avaliação gratuita](https://www.microsoft.com/en-us/translator/trial.aspx#get-started) no portal do Azure.

## <a name="installing-packages"></a>Instalando pacotes

Verifique se você tem os pacotes necessários para adicionar a tradução ao bot.

# <a name="ctabcs"></a>[C#](#tab/cs)

[Adicione uma referência](https://docs.microsoft.com/en-us/nuget/tools/package-manager-ui) à versão de pré-lançamento dos seguintes pacotes NuGet:

* `Microsoft.Bot.Builder.Integration.AspNet.Core`
* `Microsoft.Bot.Builder.Ai.Translation` (necessário para tradução)

Se você pretende combinar a tradução com o LUIS (Reconhecimento vocal), adicione também uma referência a:

* `Microsoft.Bot.Builder.Ai.Luis` (necessário para o LUIS)

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Qualquer um desses serviços pode ser adicionado ao bot usando o pacote botbuilder-ai. Adicione este pacote ao projeto por meio do npm:
* `npm install --save botbuilder@preview`
* `npm install --save botbuilder-ai@preview`

---

## <a name="configure-translation"></a>Configurar a tradução

Em seguida, você pode configurar o bot para chamar o tradutor para cada mensagem recebida de um usuário, apenas adicionando-o à pilha de middleware do bot. O middleware usa o resultado da tradução para modificar a mensagem do usuário usando o objeto de contexto.


# <a name="ctabcs"></a>[C#](#tab/cs)

Comece com a amostra EchoBot no SDK v4 e atualize o método `ConfigureServices` no arquivo `Startup.cs` para adicionar `TranslationMiddleware` ao bot. Isso configura o bot para traduzir cada mensagem recebida de um usuário. <!--, by simply adding it to your bot's middleware set. The middleware stores the translation results on the context object. -->
-   Atualize as instruções using.
-   Atualize o método `ConfigureServices` para incluir o middleware de tradução.

    O trecho aqui foi simplificado com a remoção da maioria dos comentários e a remoção do middleware de tratamento de exceções.

**Startup.cs**
```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Bot.Builder.Ai.Translation;
using Microsoft.Bot.Builder.BotFramework;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
```
```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddBot<EchoBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        var middleware = options.Middleware;
        // Add translation middleware
        // The first parameter is a list of languages the bot recognizes
        middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));

    });
}


```

> [!TIP] 
> O SDK do Construtor de Bot detecta automaticamente o idioma do usuário com base na mensagem recém-enviada. Para substituir essa funcionalidade, você pode fornecer parâmetros adicionais de retorno de chamada para adicionar sua própria lógica para detectar e alterar o idioma do usuário.  



Dê uma olhada no código em `EchoBot.cs`, no qual ele envia "Você enviou" seguido pelo que o usuário diz:

```cs
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using System.Threading.Tasks;

namespace Microsoft.Bot.Samples
{
    public class EchoBot : IBot
    {
        public EchoBot() { }

        public async Task OnTurn(ITurnContext context)
        {
            switch (context.Activity.Type)
            {
                case ActivityTypes.Message:
                // echo back the user's input.
                    await context.SendActivity($"You sent '{context.Activity.Text}'");


                case ActivityTypes.ConversationUpdate:
                    foreach (var newMember in context.Activity.MembersAdded)
                    {
                        if (newMember.Id != context.Activity.Recipient.Id)
                        {
                            await context.SendActivity("Hello and welcome to the echo bot.");
                        }
                    }
                    break;
            }
        }
        
    }
}
```

Quando você adiciona o middleware de tradução, um parâmetro opcional especifica se as respostas devem ser traduzidas novamente para o idioma do usuário. Em `Startup.cs`, especificamos `false` para apenas traduzir as mensagens do usuário para o idioma do bot.

```cs
// The first parameter is a list of languages the bot recognizes
// The second parameter is your API key
// The third parameter specifies whether to translate the bot's replies back to the user's language
middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Para configurar o middleware de tradução com um bot de eco, cole o código a seguir em app.js.

```javascript
const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');
const { LanguageTranslator, LocaleConverter } = require('botbuilder-ai');
const restify = require('restify');

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

// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'] 
});
adapter.use(languageTranslator);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type === 'message') {
            await context.sendActivity(`You said "${context.activity.text}"`);
        } else {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        }
    });
});
```

---

## <a name="run-the-bot-and-see-translated-input"></a>Executar o bot e ver a entrada traduzida

Execute o bot e digite algumas mensagens em outros idiomas. Você verá que o bot traduziu a mensagem do usuário e indica a tradução em sua resposta.

![o bot detecta o idioma e traduz a entrada](./media/how-to-bot-translate/bot-detects-language-translates-input.png)




## <a name="invoke-logic-in-the-bots-native-language"></a>Invocar a lógica no idioma nativo do bot

Agora, adicione uma lógica que verifica as palavras em inglês. Se o usuário disser "ajuda" ou "cancelar" em outro idioma, o bot traduz isso para o inglês e a lógica que verifica as palavras em inglês "help" ou "cancel" é invocada.

# <a name="ctabcs"></a>[C#](#tab/cs)
Em `EchoBot.cs`, atualize a instrução `case` para as atividades de mensagem no método `OnTurn` do bot.
```cs
case ActivityTypes.Message:
    // check the message text before calling context.SendActivity
    var messagetext = context.Activity.Text.Trim().ToLower();
    if (string.Equals("help", messagetext))
    {
        await context.SendActivity("You asked for help.");
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
if (context.activity.type === 'message') {
    // check the message text before calling context.sendActivity
    var messagetext = context.activity.text.trim().toLowerCase();
    if(messagetext === "help"){
        await context.sendActivity("you said help");
    }
}
```

---

![o bot detecta ajuda em francês](./media/how-to-bot-translate/bot-detects-help-french.png)



## <a name="translate-replies-back-to-the-users-language"></a>Traduzir as respostas novamente para o idioma do usuário

Você também pode traduzir as respostas novamente para o idioma do usuário definindo o último parâmetro de construtor como `true`.

# <a name="ctabcs"></a>[C#](#tab/cs)
Em `Startup.cs`, atualize a linha a seguir do método `ConfigureServices`.
```cs
// Use language recognition to detect the user's language from their message, instead of providing helper callbacks.
// Last parameter indicates that we'll translate replies back to the user's language
middleware.Add(new TranslationMiddleware(new string[] { "en" }, "TRANSLATION-SUBSCRIPTION-KEY", true));
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Use language recognition to detect the user's language from their message, instead of providing helper callbacks.
// Last parameter indicates that we'll translate replies back to the user's language
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: true
});
adapter.use(languageTranslator);
```

---

## <a name="run-the-bot-to-see-replies-in-the-users-language"></a>Executar o bot para ver as respostas no idioma do usuário

Execute o bot e digite algumas mensagens em outros idiomas. Você verá que ele detecta o idioma do usuário e traduz a resposta.

![o bot detecta o idioma e traduz a resposta](./media/how-to-bot-translate/bot-detects-language-translates-response.png)


## <a name="adding-logic-for-detecting-or-changing-the-user-language"></a>Adicionar uma lógica para detectar ou alterar o idioma do usuário

Em vez de deixar o SDK do Construtor de Bot detectar automaticamente o idioma do usuário, você pode fornecer um retorno de chamada para adicionar sua própria lógica, a fim de determinar o idioma do usuário ou determinar quando o idioma do usuário foi alterado.


No exemplo a seguir, o retorno de chamada `CheckUserChangedLanguage` verifica uma mensagem específica do usuário para alterar o idioma. 

# <a name="ctabcs"></a>[C#](#tab/cs)
Em `Startup.cs`, adicione um retorno de chamada ao middleware de tradução.
```csharp
middleware.Add(new TranslationMiddleware(new string[] { "en" },
     "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", null,
     TranslatorLocaleHelper.GetActiveLanguage,
     TranslatorLocaleHelper.CheckUserChangedLanguage));
```
Adicione um arquivo `TranslatorLocalHelper.cs` e adicione o código a seguir à definição de classe TranslatorLocalHelper.
```csharp
    class CurrentUserState
    {
        public string Language { get; set; }
    }

    public static void SetLanguage(ITurnContext context, string language) =>
        context.GetConversationState<CurrentUserState>().Language = language;

    public static bool IsSupportedLanguage(string language) =>
        _supportedLanguages.Contains(language);

    public static async Task<bool> CheckUserChangedLanguage(ITurnContext context)
    {
        bool changeLang = false;
        
        // use a specific message from user to change language
        if (context.Activity.Type == ActivityTypes.Message)
        {
            var messageActivity = context.Activity.AsMessageActivity();
            if (messageActivity.Text.ToLower().StartsWith("set my language to"))
            {
                changeLang = true;
            }
            if (changeLang)
            {
                var newLang = messageActivity.Text.ToLower().Replace("set my language to", "").Trim();
                if (!string.IsNullOrWhiteSpace(newLang)
                        && IsSupportedLanguage(newLang))
                {
                    SetLanguage(context, newLang);
                    await context.SendActivity($@"Changing your language to {newLang}");
                }
                else
                {
                    await context.SendActivity($@"{newLang} is not a supported language.");
                }
                // return true to intercept message from further processesing
                return true;
            }
        }

        return false;
    }

    public static string GetActiveLanguage(ITurnContext context)
    {

        if (context.Activity.Type == ActivityTypes.Message
            && context.GetConversationState<CurrentUserState>() != null
            && context.GetConversationState<CurrentUserState>().Language != null)
        {
            return context.GetConversationState<CurrentUserState>().Language;
        }

        return "en";
    }

```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// When the user inputs 'set my language to fr'
// The bot will automatically change all text to French
async function setUserLanguage(context) {
    let state = conversationState.get(context)
    if (context.activity.text.toLowerCase().startsWith('set my language to')) {
        state.language = context.activity.text.toLowerCase().replace('set my language to', '').trim();
        await context.sendActivity(`Setting your language to ${state.language}`);
        return Promise.resolve(true);
    } else {
        return Promise.resolve(false);
    }
}

// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    setUserLanguage: setUserLanguage,
    translateBackToUserLanguage: true
});
adapter.use(languageTranslator);

// Listen for incoming activity 
server.post('/api/messages', (req, res) => {
    // Route received activity to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            await context.sendActivity(`You said: ${context.activity.text}`)
        }
    });
});

```

---

## <a name="combining-luis-or-qna-with-translation"></a>Combinando o LUIS ou o QnA com a tradução

Se você estiver combinando a tradução com outros serviços no bot, como o LUIS ou o QnA Maker, adicione o middleware de tradução primeiro, de modo que as mensagens sejam traduzidas antes de serem passadas para outro middleware que espera o idioma nativo do bot.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton(_ => Configuration);
    services.AddBot<AiBot>(options =>
    {
        options.CredentialProvider = new ConfigurationCredentialProvider(Configuration);
        var middleware = options.Middleware;

        // Add translation middleware
        // The first parameter is a list of languages the bot recognizes. Input in these language won't be translated.
        middleware.Add(new TranslationMiddleware(new string[] { "en" }, "<YOUR MICROSOFT TRANSLATOR TEXT API KEY>", false));

        // Add LUIS middleware after translation middleware to pass translated messages to LUIS
        middleware.Add(
            new LuisRecognizerMiddleware(
                new LuisModel("luisAppId", "subscriptionId", new Uri("luisModelBaseUrl"))));
    });
}
```

No código do bot, os resultados do LUIS baseiam-se na entrada que já foi traduzida para o idioma nativo do bot. Tente modificar o código do bot para verificar os resultados de um aplicativo LUIS:

```cs
public async Task OnTurn(ITurnContext context)
{
    switch (context.Activity.Type)
    {
        case ActivityTypes.Message:
            // check LUIS results
            var luisResult = context.Services.Get<RecognizerResult>(LuisRecognizerMiddleware.LuisRecognizerResultKey);
            (string key, double score) topItem = luisResult.GetTopScoringIntent();
            await context.SendActivity($"The top intent was: '{topItem.key}', with score {topItem.score}");

            await context.SendActivity($"You sent '{context.Activity.Text}'");

        case ActivityTypes.ConversationUpdate:
            foreach (var newMember in context.Activity.MembersAdded)
            {
                if (newMember.Id != context.Activity.Recipient.Id)
                {
                    await context.SendActivity("Hello and welcome to the echo bot.");
                }
            }
            break;
        }
    }  
}          
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "xxxxxx",
    noTranslatePatterns: new Set(),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: false
});
adapter.use(languageTranslator);

// Add Luis recognizer middleware
const luisRecognizer = new LuisRecognizer({
    appId: "xxxxxx",
    subscriptionKey: "xxxxxxx"
});
adapter.use(luisRecognizer);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            let results = luisRecognizer.get(context);
            for (const intent in results.intents) {
                await context.sendActivity(`intent: ${intent.toString()} : ${results.intents[intent.toString()]}`)
            }
        }
    });
});
```

---

## <a name="bypass-translation-for-specified-patterns"></a>Ignorar a tradução de padrões especificados
Pode haver determinadas palavras que você não deseja que o bot traduza, como nomes próprios. Forneça expressões regulares para indicar os padrões que não devem ser traduzidos. Por exemplo, se o usuário disser "Meu nome é..." em um idioma não nativo do bot e você desejar impedir a tradução de seu nome, use um padrão para especificar isso.

# <a name="ctabcs"></a>[C#](#tab/cs)
Em Startup.cs
```cs
// Pattern representing input to not translate
Dictionary<string, List<string>> patterns = new Dictionary<string, List<string>>();
// single pattern for fr language, to avoid translating what follows "my name is"
patterns.Add("fr", new List<string> { "mon nom est (.+)" });

middleware.Add(new TranslationMiddleware(new string[] { "en" },
    "<YOUR API KEY>", patterns,
    TranslatorLocaleHelper.GetActiveLanguage,
    TranslatorLocaleHelper.CheckUserChangedLanguage));

```
<!-- TODO: ADD more explanation (both of these callbacks are run every time), fix image by debugging regex for l'etat -->

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
// Add language translator middleware
const languageTranslator = new LanguageTranslator({
    translatorKey: "<YOUR API KEY>",
    // single pattern for fr language, to avoid translating what follows "my name is"
    noTranslatePatterns: new Set("fr", "/mon nom est (.+)/"),
    nativeLanguages: ['en'],
    translateBackToUserLanguage: false,
    
});
adapter.use(languageTranslator);

```

---

![o bot ignora a tradução de padrão](./media/how-to-bot-translate/bot-no-translate-name-fr.png)

## <a name="localize-dates"></a>Localizar datas

Caso precise localizar datas, adicione `LocaleConverterMiddleware`. Por exemplo, se você souber que o bot espera receber as datas no formato `MM/DD/YYYY` e os usuários em outras localidades inserirem datas no formato `DD/MM/YYYY`, o middleware de conversor de localidade poderá converter automaticamente as datas no formato esperado pelo bot.

> [!NOTE]
> O middleware de conversor de localidade destina-se apenas à conversão de datas. Ele não tem conhecimento dos resultados do middleware de tradução. Se estiver usando o middleware de tradução, tenha cuidado com a forma como você o combinará com o conversor de localidade. O middleware de tradução converterá algumas datas que estão no formato textual junto com outras entradas de texto, mas ele não converte datas

Por exemplo, a imagem a seguir mostra um bot que ecoa a entrada do usuário, após a tradução do inglês para o francês. Ele usa `TranslationMiddleware` sem usar `LocaleConverterMiddleware`.

![bot traduzindo datas sem a conversão delas](./media/how-to-bot-translate/locale-date-before.png)

Veja a seguir o mesmo bot se o `LocaleConverterMiddleware` é adicionado.

![bot traduzindo datas sem a conversão delas](./media/how-to-bot-translate/locale-date-after.png)

Os conversores de localidade podem dar suporte a localidades do inglês, francês, alemão e chinês. <!-- TODO: ADD DETAIL ABOUT SUPPORTED LOCALES -->

# <a name="ctabcs"></a>[C#](#tab/cs)
Em Startup.cs
```cs
// Add locale converter middleware
middleware.Add(new LocaleConverterMiddleware(
    TranslatorLocaleHelper.GetActiveLocale,
    TranslatorLocaleHelper.CheckUserChangedLocale,
     "en-us", LocaleConverter.Converter));
```
Em TranslatorLocaleHelper.cs
```cs
// TranslatorLocaleHelper.cs
public static async Task<bool> CheckUserChangedLocale(ITurnContext context)
{
    bool changeLocale = false;//logic implemented by developper to make a signal for language changing 
    //use a specific message from user to change language
    if (context.Activity.Type == ActivityTypes.Message)
    {
        var messageActivity = context.Activity.AsMessageActivity();
        if (messageActivity.Text.ToLower().StartsWith("set my locale to"))
        {
            changeLocale = true;
        }
        if (changeLocale)
        {
            var newLocale = messageActivity.Text.ToLower().Replace("set my locale to", "").Trim(); //extracted by the user using user state 
            if (!string.IsNullOrWhiteSpace(newLocale)
                    && IsSupportedLanguage(newLocale))
            {
                SetLocale(context, newLocale);
                await context.SendActivity($@"Changing your language to {newLocale}");
            }
            else
            {
                await context.SendActivity($@"{newLocale} is not a supported locale.");
            }
            //intercepts message
            return true;
        }
    }

    return false;
}
public static string GetActiveLocale(ITurnContext context)
{
    if (currentLocale != null)
    {
        //the user has specified a different locale so update the bot state
        if (context.GetConversationState<CurrentUserState>() != null
            && currentLocale != context.GetConversationState<CurrentUserState>().Locale)
        {
            SetLocale(context, currentLocale);
        }
    }
    if (context.Activity.Type == ActivityTypes.Message
        && context.GetConversationState<CurrentUserState>() != null && context.GetConversationState<CurrentUserState>().Locale != null)
    {
        return context.GetConversationState<CurrentUserState>().Locale;
    }

    return "en-us";
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

<!-- this snippet only works if the user doesn't actually try to change their locale.  Emailed Mostafa about the issue 
It should change the locale after you type in 'set my locale to....' -->

```javascript
// Delegates for getting and setting user locale
function getUserLocale(context) {
    const state = conversationState.get(context)
    if (state.locale == undefined) {
        return 'en-us';
    } else {
        return state.locale;
    }
}

// Function to change the user's locale.
async function setUserLocale(context) {
    let state = conversationState.get(context)
    if (context.activity.text.toLowerCase().startsWith('set my locale to')) {        
        state.locale = context.activity.text.toLowerCase().replace('set my locale to', '').trim();
        await context.sendActivity(`Setting your locale to ${state.locale}`);
        return Promise.resolve(true);
    } else {
        return Promise.resolve(false);
    }
}

// Add locale converter middleware
// Will convert input to fr-fr
const localeConverter = new LocaleConverter({
    toLocale: 'fr-fr',
    setUserLocale: setUserLocale,
    getUserLocale: getUserLocale
});
adapter.use(localeConverter);


// Listen for incoming requests 
server.post('/api/messages', (req, res) => {
    // Route received request to adapter for processing
    adapter.processActivity(req, res, async (context) => {
        if (context.activity.type != 'message') {
            await context.sendActivity(`[${context.activity.type} event detected]`);
        } else {
            await context.sendActivity(`The message that you sent was:  ${context.activity.text}`)
        }
    });
});

```

---
