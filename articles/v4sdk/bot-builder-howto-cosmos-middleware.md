---
title: Criar middleware usando o Cosmos DB | Microsoft Docs
description: Saiba como criar um middleware que efetua login no Cosmos DB.
keywords: middleware, cosmos db, database, azure, onTurn
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 29edff2db0e513a37b8ca8ac0f97e0647282567d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296894"
---
# <a name="create-middleware-that-logs-in-cosmos-db"></a>Criar um middleware que efetua login no Cosmos DB

Apesar de um ótimo middleware ser fornecido com o SDK, há situações em que você precisará implementar seu próprio middleware para alcançar a meta desejada.

Neste exemplo, criaremos uma camada de middleware que se conecta ao Cosmos DB para registrar todas as mensagens recebidas e as respostas que enviamos de volta. O código que você verá aqui está disponível como código-fonte completo fornecido com nossos [exemplos](../dotnet/bot-builder-dotnet-samples.md).

## <a name="set-up"></a>Configurar

Precisamos definir algumas configurações antes de entrar no código.

### <a name="create-your-database"></a>Crie seu banco de dados

Primeiro, precisamos ter um local para armazenar nosso banco de dados.  Isso pode ser feito com uma conta do Azure ou, para fins de teste, você pode usar o [emulador do Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator). Dependendo do método escolhido, precisaremos de um URI de ponto de extremidade e a chave do banco de dados.

O exemplo, e o código fornecido aqui, está usando o emulador. O ponto de extremidade e a chave são aqueles fornecidos para a conta de testes do Cosmos DB que são valores comuns fornecidos com a documentação do emulador e que não podem ser usados para produção.

Se você quiser usar um Azure Cosmos DB real, siga a etapa 1 do tópico [Introdução ao Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started). Depois disso, estão disponíveis no seu ponto de extremidade de URI e a chave de **chaves** guia em suas configurações de banco de dados. Esses valores serão necessários para o arquivo de configuração a seguir.

### <a name="build-a-basic-bot"></a>Crie um bot básico

O restante deste tópico é criado a partir de um bot básico, como o HelloBot criado na visão geral. Você pode usar o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator) para se conectar ao bot, conversar com ele e testá-lo.

Além das referências necessárias para bots padrão que usam o Bot Framework,será preciso adicionar referências para os seguintes pacotes do NuGet:

* Microsoft.Azure.Documentdb.Core
* System.Configuration.ConfigurationManager

Essas bibliotecas são usadas na comunicação com o banco de dados e para habilitar o uso de um arquivo de configuração, respectivamente.

### <a name="add-configuration-file"></a>Adicionar arquivo de configuração

É recomendável usar um arquivo de configuração por diversos motivos, portanto, usaremos um aqui. Nossos dados de configuração são curtos e simples, no entanto, você pode adicionar outras definições de configuração à medida que seu bot torna-se mais complexo.

# <a name="ctabcs"></a>[C#](#tab/cs)
1. Adicione ao projeto um arquivo chamado `app.config`.
2. Salve as seguintes informações nele. O ponto de extremidade e a chave são definidos para o emulador local, mas podem ser atualizados para sua instância do Cosmos DB.
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
        <appSettings>
            <add key="EmulatorDbUrl" value="https://localhost:8081"/>
            <add key="EmulatorDbKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="/>
        </appSettings>
    </configuration>
    ```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
1. Adicione ao projeto um arquivo chamado `config.js`.
2. Salve as seguintes informações nele. O ponto de extremidade e a chave são definidos para o emulador local, mas podem ser atualizados para sua instância do Cosmos DB.
    ```javascript
        var config = {}

        config.EmulatorServiceEndpoint = "https://localhost:8081";
        config.EmulatorAuthKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWE+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";
        config.database = {
            "id": "Tasks"
        };
        config.collection = {
            "id": "Items"
        };

        module.exports = config;
    ```

---

Se você estiver usando um Cosmos DB real, poderá substituir os valores das duas chaves acima pelos valores de suas configurações do Cosmos DB. Como alternativa, você pode adicionar mais duas chaves à configuração e isso permitirá alternar entre o emulador para testes e o Cosmos DB real da seguinte forma:

# <a name="ctabcs"></a>[C#](#tab/cs)
```
    <add key="ActualDbUrl" value="<your database URI>"/>
    <add key="ActualDbKey" value="<your database key>"/>
```
 Se você adicionar mais duas chaves e quiser usar o banco de dados real, certifique-se de especificar a chave correta no construtor abaixo em vez de `EmulatorDbUrl` e `EmulatorDbKey`.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```
    config.ActualServiceEndpoint = "your database URI;
    config.ActualAuthKey = "your database key";
```

Se você adicionar mais duas chaves e quiser usar o banco de dados real, certifique-se de especificar a chave correta no construtor abaixo em vez de `EmulatorServiceEndpoint` e `EmulatorAuthKey`.

---



## <a name="creating-your-middleware"></a>Crie seu middleware

# <a name="ctabcs"></a>[C#](#tab/cs)
Antes de começar a gravar a lógica de middleware real, crie uma nova classe no projeto de bot para o middleware. Após obter essa classe, altere o namespace para o que usamos para o resto do bot, `Microsoft.Bot.Samples`. Aqui você verá que adicionamos referências a algumas novas bibliotecas:

* `Newtonsoft.Json;`
* `Microsoft.Azure.Documents;`
* `Microsoft.Azure.Documents.Client;`
* `Microsoft.Azure.Documents.Linq;`

Os vários `Microsoft.Azure.Documents.*` destinam-se a diferentes partes da comunicação com nosso banco de dados e o `Newtonsoft.Json` nos ajuda a analisar o JSON indo e voltando ao banco de dados.

Por fim, cada parte do middleware implementa `IMiddleware` e exige a definição dos manipuladores apropriados para que o middleware funcione corretamente.

```cs
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Configuration;
using Newtonsoft.Json;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

namespace Microsoft.Bot.Samples
{
    public class CosmosMiddleware : IMiddleware
    {
        ...
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Antes de começar a gravar a lógica de middleware real, precisamos criar nosso Middleware personalizado que começará com `onTurn()`. 

```javascript
adapter.use({onTurn: async (context, next) =>{
    // Middleware logic here...
}})
    
```

---

#### <a name="defining-local-variables"></a>Defina variáveis de locais

# <a name="ctabcs"></a>[C#](#tab/cs)
Em seguida, precisamos de variáveis locais para manipular nosso banco de dados e uma classe para armazenar as informações que queremos registrar. Essa classe de informações, que chamamos de `Log`, define quais propriedades JSON associadas a cada membro serão nomeadas. Retornaremos a esse tópico mais adiante.

```cs
    private string Endpoint;
    private string Key;
    private static readonly string Database = "BotData";
    private static readonly string Collection = "BotCollection";
    public DocumentClient docClient;

    public class Log
    {
        [JsonProperty("Time")]
        public string Time;

        [JsonProperty("MessageReceived")]
        public string Message;

        [JsonProperty("ReplySent")]
        public string Reply;
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Em seguida, precisamos de uma variável local para armazenar as informações que queremos registrar. No middleware, precisamos acessar o conversationState que tem o Cosmos DB como provedor de armazenamento. A variável `info` será o objeto do estado que vamos ler e no qual poderemos gravar. 

```javascript
// Add conversation state middleware
const conversationState = new ConversationState(storage);
adapter.use(conversationState);

adapter.use({onTurn: async (context, next) =>{

    const info = conversationState.get(context);
    // More middleware logic 
}})
```

--- 


#### <a name="initialize-database-connection"></a>Inicialize a conexão do banco de dados

# <a name="ctabcs"></a>[C#](#tab/cs)
O construtor deste middleware se encarrega de extrair as informações necessárias do nosso arquivo de configuração definido acima e criar o `DocumentClient`, que nos permite ler e gravar em nosso banco de dados. Também verificamos se nosso banco de dados e coleta estão configurados.

O uso adequado de um novo `DocumentClient` requer que ele seja descartado, então nosso destruidor faz exatamente isso.

A criação do banco de dados depende da tentativa de fazer uma leitura básica do primeiro banco de dados e, em seguida, da coleta dentro desse banco de dados. Se chegarmos a uma exceção `NotFound`, vamos capturá-la e criar o banco de dados ou coleta em vez de jogá-la na pilha. Na maioria dos casos, eles já serão criados, mas, para fins deste exemplo, isso nos permitirá não nos preocupar com a criação desse banco de dados antes da primeira execução. No código de exemplo, o banco de dados e a coleta são divididos em duas funções para simplificar, mas eles foram combinados no trecho a seguir.

Para saber mais informações sobre o CosmosDB, dê uma olhada no [site](https://docs.microsoft.com/en-us/azure/cosmos-db/). No restante deste tópico, veremos detalhes do próprio Cosmos DB e nos concentraremos no que seu bot precisa para usá-lo.

A definição de `Key`, `Endpoint` e `docClient` foi incluída em um trecho de código acima e só foi copiada aqui para maior clareza.

```cs
    private string Endpoint;
    private string Key;
    // ...
    public DocumentClient docClient;
    // ...

    public CosmosMiddleware()
    {
        Endpoint = ConfigurationManager.AppSettings["EmulatorDbUrl"];
        Key = ConfigurationManager.AppSettings["EmulatorDbKey"];
        docClient = new DocumentClient(new Uri(Endpoint), Key);
        CreateDatabaseAndCollection().ConfigureAwait(false);
    }

    ~CosmosMiddleware()
    {
        docClient.Dispose();
    }

    private async Task CreateDatabaseAndCollection()
    {
        try
        {
            await docClient.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(Database));
        }
        catch (DocumentClientException e)
        {
            if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                await docClient.CreateDatabaseAsync(new Database { Id = Database });
            }
            else
            {
                throw;
            }
        }

        try
        {
            await docClient.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri
                (Database, Collection));
        }
        catch (DocumentClientException e)
        {
            if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                await docClient.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(Database),
                    new DocumentCollection { Id = Collection });
            }
            else
            {
                throw;
            }
        }
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
A criação do banco de dados depende da tentativa de fazer uma leitura básica do primeiro banco de dados e, em seguida, da coleta dentro desse banco de dados. Se chegarmos a uma exceção `undefined`, vamos capturá-la e criar um objeto chamado `log` que será definido como uma matriz vazia. Na maioria dos casos, `log` já será criado, mas, para fins deste exemplo, isso nos permitirá não nos preocupar com a criação desse banco de dados antes da primeira execução. No código de exemplo, verificamos para ver se `info.log` está indefinido. Caso esteja, defina-o como uma matriz vazia.

```javascript
adapter.use({onTurn: async (context, next) =>{

    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    // More bot logic below
}})
```
---

#### <a name="read-from-database-logic"></a>Ler a lógica do banco de dados

A função do último auxiliar lê nosso banco de dados e retorna o número de registros especificado mais recente. Convém observar que há melhores práticas de banco de dados para recuperar os dados que usamos aqui, especialmente quando seu armazenamento de dados é significativamente maior.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
    public async Task<string> ReadFromDatabase(int numberOfRecords)
    {
        var documents = docClient.CreateDocumentQuery<Log>(
                UriFactory.CreateDocumentCollectionUri(Database, Collection))
                .AsDocumentQuery();
        List<Log> messages = new List<Log>();
        while (documents.HasMoreResults)
        {
            messages.AddRange(await documents.ExecuteNextAsync<Log>());
        }

        // Create a sublist of messages containing the number of requested records.
        List<Log> messageSublist = messages.GetRange(messages.Count - numberOfRecords, numberOfRecords);

        string history = "";

        // Send the last 3 messages.
        foreach (Log logEntry in messageSublist)
        {
            history += ("Message was: " + logEntry.Message + " Reply was: " + logEntry.Reply + "  ");
        }

        return history;
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

O código abaixo ainda está na função `onTurn()` e será chamado se o usuário inserir a uma cadeia de caracteres de "history".

```javascript
adapter.use({onTurn: async (context, next) =>{

    const utterance = (context.activity.text || '').trim().toLowerCase();
    const isMessage = context.activity.type === 'message';
    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    if(isMessage && utterance == 'history'){
        // Loop through the info.log array
        var logHistory = "";
        for(var i = 0; i < info.log.length; i++){
            logHistory += info.log[i] + " ";
        }
        await context.sendActivity(logHistory);
        // Short circuit the middleware
        return
    }
    //...
}})

```

---

#### <a name="define-onturn"></a>Defina OnTurn()

Em seguida, o método de middleware padrão `OnTurn()` manipula o restante do trabalho. Só temos interesse em registrar se a atividade atual for uma mensagem, que verificamos antes e depois de chamar `next()`.

A primeira coisa que verificamos é se esta é nossa mensagem de caso especial, em que o usuário está solicitando o histórico mais recente. Se assim for, chamamos read no banco de dados para obtermos os registros mais recentes e enviamos essas informações à conversa. Como isso trata totalmente da atividade atual, diminuímos o circuito do pipeline em vez de transmitir a execução.

Para obter a resposta que o bot envia, criamos um manipulador toda vez que recebemos uma mensagem para reter essas respostas, que podem ser vistas no lambda que concedemos a `OnSendActivity()`. Ele cria uma cadeia de caracteres para coletar todas as mensagens enviadas através de `SendActivity()` para este objeto de contexto.

Quando a execução retorna ao pipeline de `next()`, reunimos nossos dados de log e gravamos esses dados no banco de dados. 

Examine o gerenciador de dados do Cosmos DB após algumas mensagens serem enviadas para esse bot e deverá ver seus dados em registros individuais. Ao examinar um desses registros, você deverá ver que os três primeiros itens são os três valores de nossos dados de registro, nomeados como a cadeia de caracteres que especificamos para a respectiva `JsonProperty`.

# <a name="ctabcs"></a>[C#](#tab/cs)
```cs
    public async Task OnTurn
        (ITurnContext context, MiddlewareSet.NextDelegate next)
    {
        string botReply = "";

        if (context.Activity.Type == ActivityTypes.Message)
        {
            if (context.Activity.Text == "history")
            {
                // Read last 3 responses from the database, and short circuit future execution.
                await context.SendActivity(await ReadFromDatabase(3));
                return;
            }

            // Create a send activity handler to grab all response activities 
            // from the activity list.
            context.OnSendActivity(async (activityContext, activityList, activityNext) =>
            {
                foreach (Activity activity in activityList)
                {
                    botReply += (activity.Text + " ");
                }
                await activityNext();
            });
        }

        // Pass execution on to the next layer in the pipeline.
        await next();

        // Save logs for each conversational exchange only.
        if (context.Activity.Type == ActivityTypes.Message)
        {
            // Build a log object to write to the database.
            var logData = new Log
            {
                Time = DateTime.Now.ToString(),
                Message = context.Activity.Text,
                Reply = botReply
            };

            // Write our log to the database.
            try
            {
                var document = await docClient.CreateDocumentAsync(UriFactory.
                    CreateDocumentCollectionUri(Database, Collection), logData);
            }
            catch (Exception ex)
            {
                // More logic for what to do on a failed write can be added here
                throw ex;
            }
        }
    }
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Criado a partir da função `onTurn` descrita acima.

```javascript
adapter.use({onTurn: async (context, next) =>{

    const utterance = (context.activity.text || '').trim().toLowerCase();
    const isMessage = context.activity.type === 'message';
    const info = conversationState.get(context);

    if(info.log == undefined){
        info.log = [];
    }

    if(isMessage && utterance == 'history'){
        // Loop through info.log 
        var logHistory = "";
        for(var i = 0; i < info.log.length; i++){
            logHistory += info.log[i] + " ";
        }
        await context.sendActivity(logHistory);
        // Short circuit the middleware
        return
    } else if(isMessage){
        // Store the users response
        info.log.push(`Message was: ${context.activity.text}`); 

        await context.onSendActivities(async (handlerContext, activities, handlerNext) => 
        {
            // Store the bot's reply
            info.log.push(`Reply was: ${activities[0].text}`);
            
            await handlerNext(); 
        });
        await next();
    }
}})

```

---

## <a name="sample-output"></a>Saída de exemplo

No exemplo completo, o bot usa a biblioteca de prompts para solicitar o nome do usuário e o número favorito. Os detalhes sobre a biblioteca de prompts podem ser encontrados no tópico sobre [enviar prompts aos usuários](~/v4sdk/bot-builder-prompts.md).

Veja um exemplo de conversa com nosso bot de exemplo que está executando o código descrito acima.

![Conversa de exemplo do middleware do Cosmos](./media/cosmos-middleware-conversation.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/)
* [Emulador do Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)

