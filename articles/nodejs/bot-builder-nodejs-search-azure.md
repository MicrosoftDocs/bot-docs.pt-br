---
title: Criar experiências controladas por dados com o Azure Search | Microsoft Docs
description: Saiba como criar experiências controladas por dados com o Azure Search e ajudar os usuários a navegar em grandes quantidades de conteúdo em um bot com o SDK do Bot Framework para Node.js e Azure Search.
author: matthewshim-ms
ms.author: v-shimma
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 259f709ae460fde13cdf25ce6d7cbf5dd44a333d
ms.sourcegitcommit: cf3786c6e092adec5409d852849927dc1428e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57224854"
---
# <a name="create-data-driven-experiences-with-azure-search"></a>Criar experiências controladas por dados com o Azure Search 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-search-azure.md)
> - [Node.js](../nodejs/bot-builder-nodejs-search-azure.md)

Você pode adicionar o [ Azure Search][search] ao bot para ajudar o usuário a navegar em grandes quantidades de conteúdo e criar uma experiência de exploração controlada por dados para os usuários do bot.

Azure Search é um serviço do Azure que oferece pesquisa por palavra-chave, linguística interna, a pontuação personalizada, faceted navigation e mais. O Azure Search também pode indexar o conteúdo de várias fontes, incluindo o BD SQL do Azure, DocumentDB, Armazenamento de Blobs e Armazenamento de Tabelas. Ele dá suporte à indexação "push" para outras fontes de dados e pode abrir PDFs, documentos do Office e outros formatos que contém dados não estruturados. Depois de coletado, o conteúdo entra em um índice do Azure Search, que o bot pode então consultar.

## <a name="install-dependencies"></a>Instalar dependências

Em um prompt de comando, navegue até o diretório do projeto do bot e instale os seguintes módulos com NPM (Node Package Manager):

* [bluebird](https://www.npmjs.com/package/bluebird)
* [lodash](https://www.npmjs.com/package/lodash)
* [solicitação](https://www.npmjs.com/package/request)

## <a name="prerequisites"></a>Pré-requisitos

Os argumentos a seguir são **exigidos**: 
- Ter uma assinatura do Azure e uma chave primária do Azure Search. Você pode encontrar isso no Portal do Azure.
- Copie a biblioteca [SearchDialogLibrary](https://github.com/Microsoft/botBuilder-Samples/tree/master/Node/demo-Search/SearchDialogLibrary) para o diretório do projeto do bot. Essa biblioteca contém diálogos gerais para o usuário pesquisar, mas pode ser personalizada conforme necessário para adequar-se ao bot. 

- Copie a biblioteca [SearchProviders](https://github.com/Microsoft/botBuilder-Samples/tree/master/Node/demo-Search/SearchProviders) para o diretório do projeto do bot. Esta biblioteca contém todos os componentes necessários para criar uma solicitação e enviá-la ao Azure Search.

## <a name="connect-to-the-azure-service"></a>Conectar-se ao Serviço do Azure 

No arquivo de programa principal do bot (por exemplo: app.js), crie os caminhos de referência para as duas bibliotecas que você instalou anteriormente. 

```javascript
var SearchLibrary = require('./SearchDialogLibrary');
var AzureSearch = require('./SearchProviders/azure-search');
```

Adicione o seguinte código de exemplo ao bot. No objeto `AzureSearch`, passe suas próprias configurações do Azure Search para o método `.create`. Em tempo de execução, isso associará o bot ao serviço do Azure Search e aguardará até que uma consulta de usuário seja concluída na forma de um `Promise`.  

```javascript
// Azure Search
var azureSearchClient = AzureSearch.create('Your-Azure-Search-Service-Name', 'Your-Azure-Search-Primary-Key', 'Your-Azure-Search-Service-Index');
var ResultsMapper = SearchLibrary.defaultResultsMapper(ToSearchHit);
```

 A referência `azureSearchClient` cria o modelo do Azure Search, que passa as configurações de autorização do Serviço do Azure das configurações `.env` do bot. 
 `ResultsMapper` analisa o objeto de resposta do Azure e mapeia os dados, conforme definimos no método `ToSearchHit`. Para obter um exemplo de implementação desse método, consulte [Após o Azure Search responder](#after-azure-search-responds).

## <a name="register-the-search-library"></a>Registrar a biblioteca de pesquisa
Você pode personalizar os diálogos de pesquisa diretamente no próprio módulo `SearchLibrary`. O `SearchLibrary` executa a maioria do carregamento pesado, incluindo fazer a chamada para o Azure Search. 

Adicione o seguinte código no arquivo de programa principal do bot para registrar a biblioteca dos diálogos de pesquisa com o bot. 

```javascript
bot.library(SearchLibrary.create({
    multipleSelection: true,
    search: function (query) { return azureSearchClient.search(query).then(ResultsMapper); },
    refiners: ['refiner1', 'refiner2', 'refiner3'], // customize your own refiners 
    refineFormatter: function (refiners) {
        return _.zipObject(
            refiners.map(function (r) { return 'By ' + _.capitalize(r); }),
            refiners);
    }
}));
```
O `SearchLibrary` não apenas armazena todos os diálogos relacionados à pesquisa como também encaminha a consulta do usuário para envio ao Azure Search. Será necessário definir seus próprios refinadores na matriz `refiners` para especificar as entidades que você quer permitir ao usuário restringir ou filtrar os resultados da pesquisa dele.  

## <a name="create-a-search-dialog"></a>Criar um diálogo de pesquisa

Você pode optar por estruturar seus diálogos da maneira que quiser. O único requisito para a configuração de um diálogo do Azure Search é chamar o método `.begin` a partir do objeto `SearchLibrary`, passando o objeto `session` gerado pelo SDK do Bot Framework. 

```javascript
function (session) {
        // Trigger Azure Search dialogs 
        SearchLibrary.begin(session);
    },
    function (session, args) {
        // Process selected search results
        session.send(
            'Search Completed!',
            args.selection.map(  ); // format your response 
    }
```
Para obter mais informações sobre diálogos, consulte [Gerenciar uma conversa com diálogos](bot-builder-nodejs-dialog-manage-conversation.md).

## <a name="after-azure-search-responds"></a>Após o Azure Search responder 

Após o Azure Search resolver com êxito, será necessário armazenar os dados selecionados do objeto de resposta e exibi-los de forma significativa para o usuário.

> [!TIP]
> Considere a inclusão do [módulo util][NodeUtil]. Ele ajudará a formatar e mapear a resposta do Azure Search.

No arquivo de programa principal do bot, crie um método `ToSearchHit`. Esse método retorna um objeto que formata os dados relevantes que você precisa da resposta do Azure. O código a seguir mostra como é possível definir seus próprios parâmetros no método `ToSearchHit`. 
 
 ```javascript
 function ToSearchHit(azureResponse) {
     return {
         // define your own parameters 
         key: azureResponse.id,
         title: azureResponse.title,
         description: azureResponse.description,
         imageUrl: azureResponse.thumbnail
     };
 }
```
Depois que isso é feito, tudo o que você precisa fazer é exibir os dados para o usuário. 

 No arquivo **index.js** do projeto **SearchDialogLibrary**, o método `searchHitAsCard` analisa cada resposta do Azure Search e cria um novo objeto de cartão para ser exibido ao usuário. Os campos definidos no método `ToSearchHit` do arquivo de programa principal do bot devem ser sincronizados com as propriedades no método `searchHitAsCard`. 

O exemplo a seguir mostra como e onde os parâmetros do método `ToSearchHit` definidos por você são usados para criar uma interface do usuário do anexo de cartão para processar para o usuário. 

```javascript
function searchHitAsCard(showSave, searchHit) {
    var buttons = showSave
        ? [new builder.CardAction().type('imBack').title('Save').value(searchHit.key)]
        : [];

    var card = new builder.HeroCard()
        .title(searchHit.title) 
        .buttons(buttons);

    if (searchHit.description) {
        card.subtitle(searchHit.description);
    }

    if (searchHit.imageUrl) {
        card.images([new builder.CardImage().url(searchHit.imageUrl)]);
    }

    return card;
}
```

## <a name="sample-code"></a>Exemplo de código

Veja dois exemplos completos que mostram como dar suporte ao Azure Search com os bots usando o SDK do Bot Framework para Node.js em [Exemplo de Bot de Imóveis](https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-Search/RealEstateBot) ou [Exemplo de Bot de Listagem de Trabalhos](https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-Search/JobListingBot) no GitHub. 

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Search][search]
* [Nó Util][NodeUtil]
* [Diálogos](bot-builder-nodejs-dialog-manage-conversation.md)

[NodeUtil]: https://nodejs.org/api/util.html
[search]: /azure/search/search-what-is-azure-search
