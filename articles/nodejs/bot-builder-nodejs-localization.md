---
title: Suporte à localização | Microsoft Docs
description: Saiba como determinar onde o usuário está e habilitar a funcionalidade de localização usando o SDK do Bot Framework para Node.js.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ce1b3f073c932cd4042b91ae9afc1e332a7443f2
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67404899"
---
# <a name="support-localization"></a>Suporte à localização

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O Construtor de Bot inclui um sistema de localização avançado para criar bots que podem se comunicar com o usuário em vários idiomas. Todos os prompts do seu bot podem ser traduzidos usando arquivos JSON armazenados na estrutura de diretório do bot. Se você estiver usando um sistema como o LUIS para executar o processamento natural de idiomas, configure seu [LuisRecognizer][LUISRecognizer] com um modelo separado para cada idioma ao qual o bot dá suporte, e o SDK automaticamente escolherá o modelo que corresponde à localidade de preferência do usuário.

## <a name="determine-the-locale-by-prompting-the-user"></a>Determinar o local solicitando-o ao usuário
O primeiro passo para localizar seu bot é adicionando a capacidade de identificar o idioma preferido do usuário. O SDK fornece um método [session.preferredLocale()][preferredLocal] para salvar e recuperar essa preferência por usuário. O exemplo a seguir é uma caixa de diálogo para solicitar o idioma de preferência do usuário e, em seguida, salvar essa preferência.

``` javascript
bot.dialog('/localePicker', [
    function (session) {
        // Prompt the user to select their preferred locale
        builder.Prompts.choice(session, "What's your preferred language?", 'English|Español|Italiano');
    },
    function (session, results) {
        // Update preferred locale
        var locale;
        switch (results.response.entity) {
            case 'English':
                locale = 'en';
                break;
            case 'Español':
                locale = 'es';
                break;
            case 'Italiano':
                locale = 'it';
                break;
        }
        session.preferredLocale(locale, function (err) {
            if (!err) {
                // Locale files loaded
                session.endDialog(`Your preferred language is now ${results.response.entity}`);
            } else {
                // Problem loading the selected locale
                session.error(err);
            }
        });
    }
]);
```

## <a name="determine-the-locale-by-using-analytics"></a>Determinar o local usando análise
Uma outra forma de determinar o local do usuário é usando um serviço como a [API de Análise de Texto](/azure/cognitive-services/cognitive-services-text-analytics-quick-start) para detectar automaticamente o idioma do usuário com base no texto da mensagem que ele enviou.

O snippet de código a seguir ilustra como você pode incorporar esse serviço ao seu próprio bot.
``` javascript
var request = require('request');

bot.use({
    receive: function (event, next) {
        if (event.text && !event.textLocale) {
            var options = {
                method: 'POST',
                url: 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages?numberOfLanguagesToDetect=1',
                body: { documents: [{ id: 'message', text: event.text }]},
                json: true,
                headers: {
                    'Ocp-Apim-Subscription-Key': '<YOUR API KEY>'
                }
            };
            request(options, function (error, response, body) {
                if (!error && body) {
                    if (body.documents && body.documents.length > 0) {
                        var languages = body.documents[0].detectedLanguages;
                        if (languages && languages.length > 0) {
                            event.textLocale = languages[0].iso6391Name;
                        }
                    }
                }
                next();
            });
        } else {
            next();
        }
    }
});
```

Depois de adicionar o snippet de código acima ao seu bot, chamar [session.preferredLocale()][preferredLocal] retornará automaticamente o idioma detectado. A ordem da pesquisa para `preferredLocale()` é a seguinte:
1. Local salvo chamando `session.preferredLocale()`. Esse valor é armazenado em `session.userData['BotBuilder.Data.PreferredLocale']`.
2. Local detectado atribuído a `session.message.textLocale`.
3. O local padrão configurado para o bot (por exemplo: inglês ('en')).

Você pode configurar o código de idioma padrão do bot usando seu construtor:

```javascript
var bot = new builder.UniversalBot(connector, {
    localizerSettings: { 
        defaultLocale: "es" 
    }
});
```

## <a name="localize-prompts"></a>Localizar prompts
O sistema de localização padrão do SDK do Bot Framework é baseado em arquivos e permite que um bot ofereça suporte a vários idiomas usando os arquivos JSON armazenados em disco. Por padrão, o sistema de localização pesquisará os prompts do bot no arquivo **./locale/<IETF TAG>/index.json** em que <IETF TAG> é uma [marca de idioma da IETF][IEFT] válida que representa a localidade preferencial para encontrar prompts. 

A captura de tela a seguir mostra a estrutura de diretório de um bot compatível com três idiomas: inglês, italiano e espanhol.

![Estrutura de diretório para três locais](../media/locale-dir.png)

A estrutura do arquivo é um mapa JSON simples de IDs de mensagens em cadeias de caracteres de texto traduzido. Se o valor for uma matriz em vez de uma cadeia de caracteres, um prompt da matriz será escolhido aleatoriamente quando esse valor for recuperado usando [session.localizer.gettext()][GetText]. 

O bot recupera automaticamente a versão traduzida de uma mensagem se você passar a ID da mensagem em uma chamada para [session.send()](http://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#send) em vez de um texto específico do idioma:

```javascript
var bot = new builder.UniversalBot(connector, [
    function (session) {
        session.send("greeting");
        session.send("instructions");
        session.beginDialog('/localePicker');
    },
    function (session) {
        builder.Prompts.text(session, "text_prompt");
    }
]);
```

Internamente, o SDK chama [`session.preferredLocale()`][preferredLocale] to get the user's preferred locale and then uses that in a call to [`session.localizer.gettext()`][GetText] para mapear a ID da mensagem para sua cadeia de caracteres de texto localizada.  Há momentos em que você pode precisa chamar manualmente o localizador. Por exemplo, os valores de enumeração passados para [`Prompts.choice()`][promptsChoice] nunca são traduzidos automaticamente, portanto, é provável que seja preciso recuperar manualmente uma lista traduzida antes de chamar o prompt:

```javascript
var options = session.localizer.gettext(session.preferredLocale(), "choice_options");
builder.Prompts.choice(session, "choice_prompt", options);
```

O localizador padrão pesquisa a ID de uma mensagem em vários arquivos e, se não conseguir encontrar uma ID (ou se nenhum arquivo de localização tiver sido fornecido), ele simplesmente retornará o texto da ID, fazendo com que o uso de arquivos de localização seja transparente e opcional.  Os arquivos são pesquisados na seguinte ordem:

1. O arquivo **index.json** na localidade retornada por [`session.preferredLocale()`][preferredLocale] é pesquisado.
2. Se o local incluir uma submarca opcional, como **en-US**, em seguida, a marca raiz de **en** será pesquisada.
3. O local padrão configurado do bot é pesquisado.

## <a name="use-namespaces-to-customize-and-localize-prompts"></a>Usar namespaces para personalizar e localizar prompts
O localizador padrão oferece suporte a namespacing de prompts para evitar colisões entre IDs de mensagem.  Seu bot pode substituir os prompts de namespaces para personalizar ou reformular os prompts de outro namespace.  Você pode aproveitar esse recurso para personalizar mensagens internas do SDK para permitir a adição de suporte para outros idiomas ou simplesmente para reformular mensagens atuais do SDK.  Por exemplo, para alterar a mensagem de erro padrão do SDK, basta adicionar um arquivo chamado **BotBuilder.json** ao diretório de local do bot e, em seguida, adicionar uma entrada `default_error` ao ID da mensagem:

![BotBuilder.json para namespacing de local](../media/locale-namespacing.png)


## <a name="additional-resources"></a>Recursos adicionais

Para saber mais sobre como localizar um reconhecedor, confira [Reconhecimento da intenção](bot-builder-nodejs-recognize-intent-messages.md).


[LUIS]: https://www.luis.ai/
[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html
[LUISRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer
[LUISSample]: https://aka.ms/v3-js-luisSample
[DisambiguationSample]: https://aka.ms/v3-js-onDisambiguateRoute
[preferredLocal]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#preferredlocale
[preferredLocale]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#preferredlocale
[promptsChoice]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.__global.iprompts.html#choice
[GetText]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.ilocalizer.html#gettext
[IEFT]: https://en.wikipedia.org/wiki/IETF_language_tag

