---
title: Criar experiências controladas por dados com o Azure Search (C# v3) – Serviço de Bot
description: Saiba como criar experiências controladas por dados com o Azure Search e ajudar os usuários a navegar em grandes quantidades de conteúdo em um bot com o SDK do Bot Framework para .NET e Azure Search.
author: matthewshim-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 1/28/2019
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 51c01cc950cf26bddc73a2c3e7e4da2cfb4e6b5f
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824066"
---
# <a name="create-data-driven-experiences-with-azure-search-in-the-v3-c-sdk"></a>Crie experiências controladas por dados com Azure Search no SDK do C v3 \#

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-search-azure.md)
> - [Node.js](../nodejs/bot-builder-nodejs-search-azure.md)

É possível adicionar o [Azure Search](https://azure.microsoft.com/services/search/) a um bot para ajudar os usuários a navegar em grandes quantidades de conteúdo e criar uma experiência de exploração controlada por dados.

O Azure Search é um serviço do Azure que oferece pesquisa de palavras-chave, linguística interna, pontuação personalizada, faceted navigation, e muito mais. O Azure Search também pode indexar conteúdo de várias fontes, incluindo o Azure SQL DB, DocumentDB, Armazenamento de Blobs e Armazenamento de Tabelas. Ele dá suporte à indexação "push" para outras fontes de dados e pode abrir PDFs, documentos do Office e outros formatos que contém dados não estruturados. Depois de coletado, o conteúdo entra em um índice do Azure Search, que o bot pode então consultar.

## <a name="prerequisites"></a>Pré-requisitos

Instale o [pacote Nuget Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/4.0.0-preview) no projeto de bot.

Os três projetos C# a seguir são necessários na solução do bot. Esses projetos fornecem funcionalidade adicional para bots e Azure Search. Bifurque os projetos do [GitHub](https://aka.ms/v3-cs-search-demo) ou baixe o código-fonte diretamente.

- O projeto **Search.Azure** define a chamada do Azure Service.
- O projeto **Search.Contracts** define interfaces genéricas e modelos de dados para tratar dados.
- O projeto **Search.Dialogs** inclui várias caixas de diálogo genéricas do Bot Builder usadas para consultar o Azure Search.

## <a name="configure-azure-search-settings"></a>Definir configurações do Azure Search

Defina as configurações do Azure Search no arquivo **Web.config** do projeto usando suas próprias credenciais do Azure Search nos campos de valor. O construtor na classe `AzureSearchClient` usará essas configurações para registrar e associar o bot ao Azure Service.

```xml
<appSettings>
    <add key="SearchDialogsServiceName" value="Azure-Search-Service-Name" /> <!-- replace value field with Azure Service Name --> 
    <add key="SearchDialogsServiceKey" value="Azure-Search-Service-Primary-Key" /> <!-- replace value field with Azure Service Key --> 
    <add key="SearchDialogsIndexName" value="Azure-Search-Service-Index" /> <!-- replace value field with your Azure Search Index --> 
</appSettings>
```

## <a name="create-a-search-dialog"></a>Criar um diálogo de pesquisa

No projeto do bot, crie uma nova classe `AzureSearchDialog` para chamar o Azure Service no bot. Essa nova classe deve herdar a classe `SearchDialog` do projeto **Search.Dialogs**, que trata a maior parte do trabalho pesado. A substituição `GetTopRefiners()` permite que os usuários restrinjam/filtrem os resultados de pesquisa sem a necessidade de iniciar a pesquisa sobre o formulário no início, mantendo o estado do objeto de pesquisa. É possível adicionar os próprios refinadores personalizados na matriz `TopRefiners` para permitir que os usuários filtrem ou restrinjam os resultados da pesquisa. 

```cs
[Serializable]
public class AzureSearchDialog : SearchDialog
{
    private static readonly string[] TopRefiners = { "refiner1", "refiner2", "refiner3" }; // define your own custom refiners 

    public AzureSearchDialog(ISearchClient searchClient) : base(searchClient, multipleSelection: true)
    {
    }

    protected override string[] GetTopRefiners()
    {
        return TopRefiners;
    }
}
```

## <a name="define-the-response-data-model"></a>Definir o modelo de dados de resposta

A classe **SearchHit.cs** no projeto `Search.Contracts` define os dados relevantes a serem analisados a partir da resposta do Azure Search. Para o bot, as únicas inclusões obrigatórias são a `PropertyBag` declaração IDictionary e criação no construtor. É possível definir todas as outras propriedades nessa classe relacionadas às necessidades do bot. 

```cs
[Serializable]
public class SearchHit
{
    public SearchHit()
    {
        this.PropertyBag = new Dictionary<string, object>();
    }

    public IDictionary<string, object> PropertyBag { get; set; }

    // customize the fields below as needed 
    public string Key { get; set; }

    public string Title { get; set; }

    public string PictureUrl { get; set; }

    public string Description { get; set; }
}
```

## <a name="after-azure-search-responds"></a>Após o Azure Search responder 

Em uma consulta com êxito ao Serviço do Azure, o resultado da pesquisa deverá ser analisado para recuperar os dados relevantes para o bot exibir ao usuário. Para habilitar isso, será necessário criar uma classe `SearchResultMapper`. O objeto `GenericSearchResult` criado no construtor define uma lista e dicionário para armazenar resultados e facetas respectivamente, após cada resultado ser analisado em relação aos modelos de dados do bot. 

Sincronize as propriedades no método `ToSearchHit` para corresponder ao modelo de dados em **SearchHit.cs**. O método `ToSearchHit` será executado e gerará um novo `SearchHit` para cada resultado encontrado na resposta.  

```cs
public class SearchResultMapper : IMapper<DocumentSearchResult, GenericSearchResult>
{
    public GenericSearchResult Map(DocumentSearchResult documentSearchResult)
    {
        var searchResult = new GenericSearchResult();

        searchResult.Results = documentSearchResult.Results.Select(r => ToSearchHit(r)).ToList();
        searchResult.Facets = documentSearchResult.Facets?.ToDictionary(kv => kv.Key, kv => kv.Value.Select(f => ToFacet(f)));

        return searchResult;
    }

    private static GenericFacet ToFacet(FacetResult facetResult)
    {
        return new GenericFacet
        {
            Value = facetResult.Value,
            Count = facetResult.Count.Value
        };
    }

    private static SearchHit ToSearchHit(SearchResult hit)
    {
        return new SearchHit
        {
            // custom properties defined in SearchHit.cs 
            Key = (string)hit.Document["id"],
            Title = (string)hit.Document["title"],
            PictureUrl = (string)hit.Document["thumbnail"],
            Description = (string)hit.Document["description"]
        };
    }
}
```
Depois que os resultados forem analisados e armazenados, as informações ainda precisarão ser exibidas para o usuário. A classe `SearchHitStyler` deverá ser gerenciada para acomodar o modelo de dados da classe `SearchHit`. Por exemplo, as propriedades `Title`, `PictureUrl` e `Description` da classe **SearchHit.cs** são usadas no código de exemplo para criar novos anexos de cartão. O código a seguir cria um novo anexo de cartão para cada objeto `SearchHit` na lista de Resultados `GenericSearchResult` para exibir ao usuário.   

```cs
[Serializable]
public class SearchHitStyler : PromptStyler
{
    public override void Apply<T>(ref IMessageActivity message, string prompt, IReadOnlyList<T> options, IReadOnlyList<string> descriptions = null)
    {
        var hits = options as IList<SearchHit>;
        if (hits != null)
        {
            var cards = hits.Select(h => new ThumbnailCard
            {
                Title = h.Title,
                Images = new[] { new CardImage(h.PictureUrl) },
                Buttons = new[] { new CardAction(ActionTypes.ImBack, "Pick this one", value: h.Key) },
                Text = h.Description
            });

            message.AttachmentLayout = AttachmentLayoutTypes.Carousel;
            message.Attachments = cards.Select(c => c.ToAttachment()).ToList();
            message.Text = prompt;
        }
        else
        {
            base.Apply<T>(ref message, prompt, options, descriptions);
        }
    }
}
```
Os resultados da pesquisa são exibidos ao usuário, e você adicionou com êxito o Azure Search ao bot.

## <a name="samples"></a>Exemplos

Veja dois exemplos completos que mostram como dar suporte ao Azure Search com bots usando o SDK do Bot Framework para .NET, em [Exemplo de bot Imobiliário](https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/demo-Search/RealEstateBot) ou [Exemplo de bot de Listagem de Trabalhos](https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/demo-Search/JobListingBot) no GitHub. 

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Search][search]
- [Visão geral de caixas de diálogo](bot-builder-dotnet-dialogs.md)

[search]: /azure/search/search-what-is-azure-search
