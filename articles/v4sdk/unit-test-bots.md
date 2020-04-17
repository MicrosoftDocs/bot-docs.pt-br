---
title: Bots de teste de unidade – Serviço de Bot
description: Descreve como realizar teste de unidade em bots usando estruturas de teste.
keywords: bot, testar bots, estrutura de teste de bot
author: gabog
ms.author: ggilaber
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: da99aa82c235bc8f530c9c2ad8d3ea1fa592b001
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75798123"
---
# <a name="how-to-unit-test-bots"></a>Como realizar testes de unidade em bots

[!INCLUDE[applies-to](../includes/applies-to.md)]

Neste tópico, mostraremos como:

- Criar testes de unidade para bots
- Usar o assert para verificar as atividades retornadas por um turno de caixa de diálogo, comparando-as aos valores esperados
- Usar o assert para verificar os resultados retornados por uma caixa de diálogo
- Criar diferentes tipos de testes controlados por dados
- Criar objetos fictícios para as diversas dependências de uma caixa de diálogo (ou seja, reconhecedores do LUIS, etc.)

## <a name="prerequisites"></a>Pré-requisitos

## <a name="c"></a>[C#](#tab/csharp)

O exemplo de [Testes de CoreBot](https://aka.ms/cs-core-test-sample) usado neste tópico faz referência ao pacote [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/), ao [XUnit](https://xunit.net/) e ao [Moq](https://github.com/moq/moq) para criar testes de unidade.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo de [Testes de CoreBot](https://aka.ms/js-core-test-sample) usado neste tópico faz referência ao pacote [botbuilder-testing](https://www.npmjs.com/package/botbuilder-testing), [Mocha](https://mochajs.org/) para criar testes de unidade e ao [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter) para visualizar os resultados de teste no VS Code.

---

## <a name="testing-dialogs"></a>Como testar caixas de diálogo

No exemplo do CoreBot, as caixas de diálogo são testadas por meio da classe `DialogTestClient`, que fornece um mecanismo para testá-las em isolamento fora de um bot e sem a necessidade de implantar seu código em um serviço Web.

Usando essa classe, você pode escrever testes de unidade que validam as respostas das caixas de diálogo, turno a turno. Os testes de unidade usando a classe `DialogTestClient` devem funcionar com outras caixas de diálogo criadas usando a biblioteca de caixas de diálogo botbuilder.

O exemplo a seguir demonstra os testes derivados de `DialogTestClient`:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut);

var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("Seattle");
Assert.Equal("Where are you traveling from?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("New York");
Assert.Equal("When would you like to travel?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("tomorrow");
Assert.Equal("OK, I will book a flight from Seattle to New York for tomorrow, Is this Correct?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("yes");
Assert.Equal("Sure thing, wait while I finalize your reservation...", reply.Text);

reply = testClient.GetNextReply<IMessageActivity>();
Assert.Equal("All set, I have booked your flight to Seattle for tomorrow", reply.Text);
```

A classe `DialogTestClient` é definida no namespace `Microsoft.Bot.Builder.Testing` e incluída no pacote NuGet [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut);

let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');

reply = await testClient.sendActivity('Seattle');
assert.strictEqual(reply.text, 'Where are you traveling from?');

reply = await testClient.sendActivity('New York');
assert.strictEqual(reply.text, 'When would you like to travel?');

reply = await testClient.sendActivity('tomorrow');
assert.strictEqual(reply.text, 'OK, I will book a flight from Seattle to New York for tomorrow, Is this Correct?');

reply = await testClient.sendActivity('yes');
assert.strictEqual(reply.text, 'Sure thing, wait while I finalize your reservation...');

reply = testClient.getNextReply();
assert.strictEqual(reply.text, 'All set, I have booked your flight to Seattle for tomorrow');
```

A classe `DialogTestClient` está incluída no pacote npm [botbuilder-testing]().

---

### <a name="dialogtestclient"></a>DialogTestClient

O primeiro parâmetro de `DialogTestClient` é o canal de destino. Isso permite que você teste uma lógica de renderização diferente com base no canal de destino para o bot (Teams, Slack, Cortana, etc.). Se você não tiver certeza sobre o canal de destino, poderá usar as IDs de canal `Emulator` ou `Test`, mas tenha em mente que alguns componentes podem se comportar de forma diferente dependendo do canal atual, por exemplo, `ConfirmPrompt` renderiza as opções Sim/Não diferentemente para os canais `Test` e `Emulator`. Você também pode usar esse parâmetro para testar a lógica de renderização condicional na caixa de diálogo com base na ID do canal.

O segundo parâmetro é uma instância da caixa de diálogo que está sendo testada (observação: **"sut"** significa "sistema em teste", usamos esse acrônimo em snippets de código neste artigo).

O construtor `DialogTestClient` fornece parâmetros adicionais que permitem personalizar ainda mais o comportamento do cliente ou passar parâmetros para a caixa de diálogo que está sendo testada, se necessário. Você pode passar dados de inicialização para a caixa de diálogo, adicionar um middleware personalizado ou usar seu próprio TestAdapter e instância de `ConversationState`.

### <a name="sending-and-receiving-messages"></a>Enviar e receber mensagens

## <a name="c"></a>[C#](#tab/csharp)

O método `SendActivityAsync<IActivity>` permite que você envie um enunciado de texto ou `IActivity` um para a caixa de diálogo e retorna a primeira mensagem recebida. O parâmetro `<T>` é usado para retornar uma instância fortemente tipada da resposta para que você possa declará-la sem precisar convertê-la.

```csharp
var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);
```

Em alguns cenários, o bot pode enviar várias mensagens em resposta a uma única atividade. Nesses casos, `DialogTestClient` enfileirará as respostas e você poderá usar o método `GetNextReply<IActivity>` para exibir a próxima mensagem da fila de respostas.

```csharp
reply = testClient.GetNextReply<IMessageActivity>();
Assert.Equal("All set, I have booked your flight to Seattle for tomorrow", reply.Text);
```

`GetNextReply<IActivity>` retornará null se não houver mais mensagens na fila de resposta.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

O método `sendActivity` permite que você envie um enunciado de texto ou `Activity` um para a caixa de diálogo e retorna a primeira mensagem recebida.

```javascript
let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');
```

Em alguns cenários, o bot pode enviar várias mensagens em resposta a uma única atividade. Nesses casos, `DialogTestClient` enfileirará as respostas e você poderá usar o método `getNextReply` para exibir a próxima mensagem da fila de respostas.

```javascript
reply = testClient.getNextReply();
assert.strictEqual(reply.text, 'All set, I have booked your flight to Seattle for tomorrow');
```

`getNextReply` retornará null se não houver mais mensagens na fila de resposta.

---

### <a name="asserting-activities"></a>Declaração de atividades

## <a name="c"></a>[C#](#tab/csharp)

O código no exemplo CoreBot declara apenas a propriedade `Text` das atividades retornadas. Em bots mais complexos, talvez você queira declarar outras propriedades como `Speak`, `InputHint`, `ChannelData`, etc.

```csharp
Assert.Equal("Sure thing, wait while I finalize your reservation...", reply.Text);
Assert.Equal("One moment please...", reply.Speak);
Assert.Equal(InputHints.IgnoringInput, reply.InputHint);
```

Você pode fazer isso verificando cada propriedade individualmente conforme mostrado acima, pode escrever seus próprios utilitários auxiliares para declaração de atividades ou pode usar outras estruturas como [FluentAssertions](https://fluentassertions.com/) para escrever declarações personalizadas e simplificar seu código de teste.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

O código no exemplo CoreBot declara apenas a propriedade `text` das atividades retornadas. Em bots mais complexos, talvez você queira declarar outras propriedades como `speak`, `inputHint`, `channelData`, etc.

```javascript
assert.strictEqual(reply.text, 'Sure thing, wait while I finalize your reservation...');
assert.strictEqual(reply.speak, 'One moment please...');
assert.strictEqual(reply.inputHint, InputHints.IgnoringInput);
```

Você pode fazer isso verificando cada propriedade individualmente conforme mostrado acima, pode escrever seus próprios utilitários auxiliares para declaração de atividades ou pode usar outras bibliotecas como [Chai](https://www.chaijs.com/) para escrever declarações personalizadas e simplificar seu código de teste.

---

### <a name="passing-parameters-to-your-dialogs"></a>Passando parâmetros para a caixa de diálogo

O construtor `DialogTestClient` tem um `initialDialogOptions` que pode ser usado para passar parâmetros para a caixa de diálogo. Por exemplo, o `MainDialog` neste exemplo inicializa um objeto `BookingDetails` dos resultados do LUIS com as entidades que ele resolve do enunciado do usuário e passa esse objeto na chamada para invocar `BookingDialog`.

Você pode implementar isso em um teste da seguinte maneira:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
var inputDialogParams = new BookingDetails()
{
    Destination = "Seattle",
    TravelDate = $"{DateTime.UtcNow.AddDays(1):yyyy-MM-dd}"
};

var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut, inputDialogParams);

```

`BookingDialog` recebe esse parâmetro e o acessa no teste da mesma maneira que ocorreria quando invocado de `MainDialog`.

```csharp
private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    var bookingDetails = (BookingDetails)stepContext.Options;
    ...
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const inputDialogParams = {
    destination: 'Seattle',
    travelDate: formatDate(new Date().setDate(now.getDate() + 1))
};

const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut, inputDialogParams);
```

`BookingDialog` receberá esse parâmetro e poderá acessá-lo no teste da mesma maneira que ocorreria se tivesse sido invocado de `MainDialog`.

```javascript
async destinationStep(stepContext) {
    const bookingDetails = stepContext.options;
    ...
}
```

---

### <a name="asserting-dialog-turn-results"></a>Declarando resultados em turnos de caixa de diálogo

## <a name="c"></a>[C#](#tab/csharp)

Algumas caixas de diálogo como `BookingDialog` ou `DateResolverDialog` retornam um valor para a caixa de diálogo que faz a chamada. O objeto `DialogTestClient` expõe uma propriedade `DialogTurnResult` que pode ser usada para analisar e declarar os resultados retornados pela caixa de diálogo.

Por exemplo:

```csharp
var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut);

var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);

...

var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
Assert.Equal("New York", bookingResults?.Origin);
Assert.Equal("Seattle", bookingResults?.Destination);
Assert.Equal("2019-06-21", bookingResults?.TravelDate);
```

A propriedade `DialogTurnResult` também pode ser usada para inspecionar e declarar resultados intermediários retornados pelas etapas em uma cascata.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Algumas caixas de diálogo como `BookingDialog` ou `DateResolverDialog` retornam um valor para a caixa de diálogo que faz a chamada. O objeto `DialogTestClient` expõe uma propriedade `dialogTurnResult` que pode ser usada para analisar e declarar os resultados retornados pela caixa de diálogo.

Por exemplo:

```javascript
const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut);

let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');

...

const bookingResults = client.dialogTurnResult.result;
assert.strictEqual('New York', bookingResults.destination);
assert.strictEqual('Seattle', bookingResults.origin);
assert.strictEqual('2019-06-21', bookingResults.travelDate);
```

A propriedade `dialogTurnResult` também pode ser usada para inspecionar e declarar resultados intermediários retornados pelas etapas em uma cascata.

---

### <a name="analyzing-test-output"></a>Analisando a saída de teste

Às vezes, é necessário ler uma transcrição de teste de unidade para analisar a execução do teste sem a necessidade de depurá-lo.

## <a name="c"></a>[C#](#tab/csharp)

O pacote [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/) inclui um `XUnitDialogTestLogger` que registra em log as mensagens enviadas e recebidas pela caixa de diálogo no console.

Para usar esse middleware, seu teste precisa expor um construtor que recebe um objeto `ITestOutputHelper` que é fornecido pelo executor de teste do xUnit e criar um `XUnitDialogTestLogger` que será passado para `DialogTestClient` por meio do parâmetro `middlewares`.

```csharp
public class BookingDialogTests
{
    private readonly IMiddleware[] _middlewares;

    public BookingDialogTests(ITestOutputHelper output)
        : base(output)
    {
        _middlewares = new[] { new XUnitDialogTestLogger(output) };
    }

    [Fact]
    public async Task SomeBookingDialogTest()
    {
        // Arrange
        var sut = new BookingDialog();
        var testClient = new DialogTestClient(Channels.Msteams, sut, middlewares: _middlewares);

        ...
    }
}
```

Aqui está um exemplo do que o `XUnitDialogTestLogger` registra na janela de saída quando é configurado:

![XUnitMiddlewareOutput](media/how-to-unit-test/cs/XUnitMiddlewareOutput.png)

Para obter informações adicionais sobre como enviar a saída de teste para o console ao usar o XUnit, confira [Capturar a saída](https://xunit.net/docs/capturing-output.html) na documentação do XUnit.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

O pacote [botbuilder-testing](https://www.npmjs.com/package/botbuilder-testing) inclui um `DialogTestLogger` que registra em log as mensagens enviadas e recebidas pela caixa de diálogo no console.

Para usar esse middleware, basta passá-lo para `DialogTestClient` por meio do parâmetro `middlewares`.

```javascript
const client = new DialogTestClient('msteams', sut, testData.initialData, [new DialogTestLogger()]);
```

Aqui está um exemplo do que o `DialogTestLogger` registra na janela de saída quando é configurado:

![DialogTestLoggerOutput](media/how-to-unit-test/js/DialogTestLoggerOutput.png)

---

Essa saída também será registrada no servidor de build durante os builds de integração contínua e ajudará você a analisar falhas de build.

## <a name="data-driven-tests"></a>Testes controlados por dados

Na maioria dos casos, a lógica da caixa de diálogo não é alterada e os caminhos de execução diferentes em uma conversa são baseados nos enunciados do usuário. Em vez de escrever um único teste de unidade para cada variante na conversa, é mais fácil usar testes controlados por dados (também conhecidos como testes com parâmetros).

Por exemplo, o exemplo de teste na seção de visão geral deste documento mostra como testar um fluxo de execução, mas o que acontece se o usuário disser não para a confirmação? E se ele usar uma data diferente? E assim por diante.

Os testes controlados por dados nos permitem testar todas essas permutações sem precisar reescrever os testes.

## <a name="c"></a>[C#](#tab/csharp)

No exemplo de CoreBot, usamos testes `Theory` do XUnit para parametrizar testes.

### <a name="theory-tests-using-inlinedata"></a>Testes de teoria usando InlineData

O teste a seguir verifica se uma caixa de diálogo é cancelada quando o usuário diz "cancelar".

```csharp
[Fact]
public async Task ShouldBeAbleToCancel()
{
    var sut = new TestCancelAndHelpDialog();
    var testClient = new DialogTestClient(Channels.Test, sut);

    var reply = await testClient.SendActivityAsync<IMessageActivity>("Hi");
    Assert.Equal("Hi there", reply.Text);
    Assert.Equal(DialogTurnStatus.Waiting, testClient.DialogTurnResult.Status);

    reply = await testClient.SendActivityAsync<IMessageActivity>("cancel");
    Assert.Equal("Cancelling...", reply.Text);
}
```

Para cancelar uma caixa de diálogo, os usuários podem digitar "sair", "desistir" e "parar". Em vez de escrever um novo caso de teste para cada palavra possível, escreva um único método de teste `Theory` que aceite parâmetros por meio de uma lista de valores `InlineData` para definir os parâmetros para cada caso de teste:

```csharp
[Theory]
[InlineData("cancel")]
[InlineData("quit")]
[InlineData("never mind")]
[InlineData("stop it")]
public async Task ShouldBeAbleToCancel(string cancelUtterance)
{
    var sut = new TestCancelAndHelpDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, middlewares: _middlewares);

    var reply = await testClient.SendActivityAsync<IMessageActivity>("Hi");
    Assert.Equal("Hi there", reply.Text);
    Assert.Equal(DialogTurnStatus.Waiting, testClient.DialogTurnResult.Status);

    reply = await testClient.SendActivityAsync<IMessageActivity>(cancelUtterance);
    Assert.Equal("Cancelling...", reply.Text);
}
```

O novo teste será executado quatro vezes com os parâmetros diferentes e cada caso será exibido como um item filho sob o teste `ShouldBeAbleToCancel` no Gerenciador de Testes do Visual Studio. Se qualquer um deles falhar, conforme mostrado abaixo, você poderá clicar com o botão direito do mouse e depurar o cenário que falhou em vez de executar novamente o conjunto de testes inteiro.

![InlineDataTestResults](media/how-to-unit-test/cs/InlineDataTestResults.png)

### <a name="theory-tests-using-memberdata-and-complex-types"></a>Testes de teoria usando MemberData e tipos complexos

`InlineData` é útil para testes pequenos controlados por dados que recebem parâmetros de tipo de valor simples (string, int, etc.).

O `BookingDialog` recebe um objeto `BookingDetails` e retorna um novo objeto `BookingDetails`. Uma versão não parametrizada de um teste para esta caixa de diálogo teria a seguinte aparência:

```csharp
[Fact]
public async Task DialogFlow()
{
    // Initial parameters
    var initialBookingDetails = new BookingDetails
    {
        Origin = "Seattle",
        Destination = null,
        TravelDate = null,
    };

    // Expected booking details
    var expectedBookingDetails = new BookingDetails
    {
        Origin = "Seattle",
        Destination = "New York",
        TravelDate = "2019-06-25",
    };

    var sut = new BookingDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, initialBookingDetails);

    // Act/Assert
    var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
    ...

    var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
    Assert.Equal(expectedBookingDetails.Origin, bookingResults?.Origin);
    Assert.Equal(expectedBookingDetails.Destination, bookingResults?.Destination);
    Assert.Equal(expectedBookingDetails.TravelDate, bookingResults?.TravelDate);
}
```

Para parametrizar esse teste, criamos uma classe `BookingDialogTestCase` que contém os dados do nosso caso de teste. Ele contém o objeto inicial `BookingDetails`, o `BookingDetails` esperado e uma matriz de cadeias de caracteres contendo os enunciados enviados pelo usuário e as respostas esperadas da caixa de diálogo para cada turno.

```csharp
public class BookingDialogTestCase
{
    public BookingDetails InitialBookingDetails { get; set; }

    public string[,] UtterancesAndReplies { get; set; }

    public BookingDetails ExpectedBookingDetails { get; set; }
}
```

Também criamos uma classe auxiliar `BookingDialogTestsDataGenerator` que expõe um método `IEnumerable<object[]> BookingFlows()`, que retorna uma coleção dos casos de teste a serem usados pelo teste.

Para exibir cada caso de teste como um item separado no Gerenciador de Testes do Visual Studio, o executor de teste XUnit requer que tipos complexos, tais como `BookingDialogTestCase`, implementem `IXunitSerializable`. Para simplificar isso, a estrutura Bot.Builder.Testing fornece uma classe `TestDataObject` que implementa essa interface e pode ser usada para encapsular os dados do caso de teste sem necessidade de implementar `IXunitSerializable`. 

Aqui está um fragmento do `IEnumerable<object[]> BookingFlows()` que mostra como as duas classes são usadas:

```csharp
public static class BookingDialogTestsDataGenerator
{
    public static IEnumerable<object[]> BookingFlows()
    {
        // Create the first test case object
        var testCaseData = new BookingDialogTestCase
        {
            InitialBookingDetails = new BookingDetails(),
            UtterancesAndReplies = new[,]
            {
                { "hi", "Where would you like to travel to?" },
                { "Seattle", "Where are you traveling from?" },
                { "New York", "When would you like to travel?" },
                { "tomorrow", $"Please confirm, I have you traveling to: Seattle from: New York on: {DateTime.Now.AddDays(1):yyyy-MM-dd}. Is this correct? (1) Yes or (2) No" },
                { "yes", null },
            },
            ExpectedBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = $"{DateTime.Now.AddDays(1):yyyy-MM-dd}",
            }, 
        };
        // wrap the test case object into TestDataObject and return it.
        yield return new object[] { new TestDataObject(testCaseData) };

        // Create the second test case object
        testCaseData = new BookingDialogTestCase
        {
            InitialBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = null,
            },
            UtterancesAndReplies = new[,]
            {
                { "hi", "When would you like to travel?" },
                { "tomorrow", $"Please confirm, I have you traveling to: Seattle from: New York on: {DateTime.Now.AddDays(1):yyyy-MM-dd}. Is this correct? (1) Yes or (2) No" },
                { "yes", null },
            },
            ExpectedBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = $"{DateTime.Now.AddDays(1):yyyy-MM-dd}",
            },
        };
        // wrap the test case object into TestDataObject and return it.
        yield return new object[] { new TestDataObject(testCaseData) };
    }
}
```

Depois de criarmos um objeto para armazenar os dados de teste e uma classe que expõe uma coleção de casos de teste, usamos o atributo `MemberData` do XUnit em vez de `InlineData` para alimentar os dados no teste, o primeiro parâmetro `MemberData` para é o nome da função estática que retorna a coleção de casos de teste e o segundo parâmetro é o tipo da classe que expõe esse método.

```csharp
[Theory]
[MemberData(nameof(BookingDialogTestsDataGenerator.BookingFlows), MemberType = typeof(BookingDialogTestsDataGenerator))]
public async Task DialogFlowUseCases(TestDataObject testData)
{
    // Get the test data instance from TestDataObject
    var bookingTestData = testData.GetObject<BookingDialogTestCase>();
    var sut = new BookingDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, bookingTestData.InitialBookingDetails);

    // Iterate over the utterances and replies array.
    for (var i = 0; i < bookingTestData.UtterancesAndReplies.GetLength(0); i++)
    {
        var reply = await testClient.SendActivityAsync<IMessageActivity>(bookingTestData.UtterancesAndReplies[i, 0]);
        Assert.Equal(bookingTestData.UtterancesAndReplies[i, 1], reply?.Text);
    }

    // Assert the resulting BookingDetails object
    var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.Origin, bookingResults?.Origin);
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.Destination, bookingResults?.Destination);
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.TravelDate, bookingResults?.TravelDate);
}
```

Aqui está um exemplo dos resultados para os testes de `DialogFlowUseCases` no Gerenciador de Testes do Visual Studio quando o teste é executado:

![BookingDialogTests](media/how-to-unit-test/cs/BookingDialogTestsResults.png)

## <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="simple-data-driven-tests"></a>Testes simples controlados por dados

O teste a seguir verifica se uma caixa de diálogo é cancelada quando o usuário diz "cancelar".

```javascript
describe('ShouldBeAbleToCancel', () => {
    it('Should cancel', async () => {
        const sut = new TestCancelAndHelpDialog();
        const client = new DialogTestClient('test', sut, null, [new DialogTestLogger()]);

        // Execute the test case
        let reply = await client.sendActivity('Hi');
        assert.strictEqual(reply.text, 'Hi there');
        assert.strictEqual(client.dialogTurnResult.status, 'waiting');

        reply = await client.sendActivity('cancel');
        assert.strictEqual(reply.text, 'Cancelling...');
    });
});
```

Considere que, mais tarde, precisamos ser capazes de lidar com outros enunciados para cancelamento como "sair", "desistir" e "parar". Em vez de escrever mais três testes repetitivos para cada novo enunciado, podemos refatorar o teste para usar uma lista de enunciados, a fim de definir os parâmetros para cada caso de teste:

```javascript
describe('ShouldBeAbleToCancel', () => {
    const testCases = ['cancel', 'quit', 'never mind', 'stop it'];

    testCases.map(testData => {
        it(testData, async () => {
            const sut = new TestCancelAndHelpDialog();
            const client = new DialogTestClient('test', sut, null, [new DialogTestLogger()]);

            // Execute the test case
            let reply = await client.sendActivity('Hi');
            assert.strictEqual(reply.text, 'Hi there');
            assert.strictEqual(client.dialogTurnResult.status, 'waiting');

            reply = await client.sendActivity(testData);
            assert.strictEqual(reply.text, 'Cancelling...');
        });
    });
});
```

O novo teste será executado quatro vezes com os parâmetros diferentes e cada caso será exibido como um item filho sob o conjunto de testes `ShouldBeAbleToCancel` no [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter). Se qualquer um deles falhar, conforme mostrado abaixo, você poderá executar e depurar o cenário que falhou em vez de executar novamente o conjunto de testes inteiro.

![SimpleCancelTestResults](media/how-to-unit-test/js/SimpleCancelTestResults.png)

### <a name="data-driven-tests-with-complex-types"></a>Testes controlados por dados com tipos complexos

Usar uma lista simples de enunciados é útil para testes pequenos controlados por dados que recebem parâmetros de tipo de valor simples (string, int, etc.) ou objetos pequenos.

O `BookingDialog` recebe um objeto `BookingDetails` e retorna um novo objeto `BookingDetails`. Uma versão não parametrizada de um teste para esta caixa de diálogo teria a seguinte aparência:

```javascript
describe('BookingDialog', () => {
    it('Returns expected booking details', async () => {
        // Initial parameters
        const initialBookingDetails = {
            origin: 'Seattle',
            destination: undefined,
            travelDate: undefined
        };

        // Expected booking details
        const expectedBookingDetails = {
            origin: 'Seattle',
            destination: 'New York',
            travelDate: '2019-06-25'
        };

        const sut = new BookingDialog('bookingDialog');
        const client = new DialogTestClient('test', sut, initialBookingDetails, [new DialogTestLogger()]);

        // Execute the test case
        const reply = await client.sendActivity('Hi');
        ...

        // Check dialog results
        const result = client.dialogTurnResult.result;
        assert.strictEqual(result.destination, expectedBookingDetails.destination);
        assert.strictEqual(result.origin, expectedBookingDetails.origin);
        assert.strictEqual(result.travelDate, expectedBookingDetails.travelDate);
    });
});
```

Para parametrizar esse teste, criamos um módulo `bookingDialogTestCases` que retorna os dados do caso de teste. Cada item contém um nome de caso de teste, o objeto 'BookingDetails' inicial, o 'BookingDetails' esperado e uma matriz de cadeias de caracteres com os enunciados enviados pelo usuário e as respostas esperadas da caixa de diálogo para cada turno.

```javascript
module.exports = [
    // Create the first test case object
    {
        name: 'Full flow',
        initialData: {},
        steps: [
            ['hi', 'To what city would you like to travel?'],
            ['Seattle', 'From what city will you be travelling?'],
            ['New York', 'On what date would you like to travel?'],
            ['tomorrow', `Please confirm, I have you traveling to: Seattle from: New York on: ${ tomorrow }. Is this correct? (1) Yes or (2) No`],
            ['yes', null]
        ],
        expectedResult: {
            destination: 'Seattle',
            origin: 'New York',
            travelDate: tomorrow
        }
    },
    // Create the second test case object
    {
        name: 'Destination and Origin provided',
        initialData: {
            destination: 'Seattle',
            origin: 'New York'
        },
        steps: [
            ['hi', 'On what date would you like to travel?'],
            ['tomorrow', `Please confirm, I have you traveling to: Seattle from: New York on: ${ tomorrow }. Is this correct? (1) Yes or (2) No`],
            ['yes', null]
        ],
        expectedStatus: 'complete',
        expectedResult: {
            destination: 'Seattle',
            origin: 'New York',
            travelDate: tomorrow
        }
    }
];
```

Depois de criarmos a lista que contém os dados de teste, podemos refatorar nosso teste para mapear essa lista em casos de teste individuais.

```javascript
describe('DialogFlowUseCases', () => {
    const testCases = require('./testData/bookingDialogTestCases.js');

    testCases.map(testData => {
        it(testData.name, async () => {
            const sut = new BookingDialog('bookingDialog');
            const client = new DialogTestClient('test', sut, testData.initialData, [new DialogTestLogger()]);

            // Execute the test case
            for (let i = 0; i < testData.steps.length; i++) {
                const reply = await client.sendActivity(testData.steps[i][0]);
                assert.strictEqual((reply ? reply.text : null), testData.steps[i][1]);
            }

            // Check dialog results
            const actualResult = client.dialogTurnResult.result;
            assert.strictEqual(actualResult.destination, testData.expectedResult.destination);
            assert.strictEqual(actualResult.origin, testData.expectedResult.origin);
            assert.strictEqual(actualResult.travelDate, testData.expectedResult.travelDate);
        });
    });
});
```

Aqui está um exemplo dos resultados para o conjunto de testes `DialogFlowUseCases` Mocha Test Explorer quando o conjunto de testes é executado:

![BookingDialogTests](media/how-to-unit-test/js/BookingDialogTestsResults.png)

---

## <a name="using-mocks"></a>Usando elementos fictícios

Você pode usar elementos fictícios para as coisas que não estão testadas no momento. Para referência, esse nível pode geralmente ser considerado como testes de unidade e de integração.

A simulação do máximo de elementos possível permite um melhor isolamento da parte que você está testando. Os candidatos a elementos fictícios incluem armazenamento, adaptador, middleware, pipeline de atividade, canais e qualquer outra coisa que não faça parte diretamente do bot. Isso pode também envolver a remoção de alguns aspectos temporariamente, como o middleware não envolvido na parte do bot que está sendo testada, a fim de isolar cada parte. No entanto, se estiver testando o middleware, talvez você deseje simular o bot.

Os elementos fictícios podem assumir várias formas, da substituição de um elemento com um objeto conhecido diferente à implementação de uma funcionalidade de funções básicas do Olá, Mundo. Isso poderá também assumir a forma de apenas remover o elemento, se ele não for necessário, ou simplesmente forçá-lo a não executar nenhuma ação.

As simulações nos permitem configurar as dependências de uma caixa de diálogo e garantir que elas estejam em um estado conhecido durante a execução do teste, sem precisar contar com recursos externos como bancos de dados, modelos do LUIS ou outros objetos.

Para tornar sua caixa de diálogo mais fácil de testar e reduzir suas dependências de objetos externos, talvez seja necessário injetar as dependências externas no construtor da caixa de diálogo.

Por exemplo, em vez de instanciar `BookingDialog` em `MainDialog`:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
public MainDialog()
    : base(nameof(MainDialog))
{
    ...
    AddDialog(new BookingDialog());
    ...
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor() {
    super('MainDialog');
    ...
    this.addDialog(new BookingDialog('bookingDialog'));
    ...
}
```

---

Passamos uma instância de `BookingDialog` como um parâmetro do construtor:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
public MainDialog(BookingDialog bookingDialog)
    : base(nameof(MainDialog))
{
    ...
    AddDialog(bookingDialog);
    ...
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor(bookingDialog) {
    super('MainDialog');
    ...
    this.addDialog(bookingDialog);
    ...
}
```

---

Isso nos permite substituir a instância de `BookingDialog` por um objeto fictício e escrever testes de unidade para `MainDialog` sem precisar chamar a verdadeira classe `BookingDialog`.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the mock object
var mockDialog = new Mock<BookingDialog>();

// Use the mock object to instantiate MainDialog
var sut = new MainDialog(mockDialog.Object);

var testClient = new DialogTestClient(Channels.Test, sut);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Create the mock object
const mockDialog = new MockBookingDialog();

// Use the mock object to instantiate MainDialog
const sut = new MainDialog(mockDialog);

const testClient = new DialogTestClient('test', sut);
```

---

### <a name="mocking-dialogs"></a>Caixas de diálogo fictícias

Conforme descrito acima, `MainDialog` invoca `BookingDialog` para obter o objeto `BookingDetails`. Implementamos e configuramos uma instância fictícia de `BookingDialog` da seguinte maneira:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the mock object for BookingDialog.
var mockDialog = new Mock<BookingDialog>();
mockDialog
    .Setup(x => x.BeginDialogAsync(It.IsAny<DialogContext>(), It.IsAny<object>(), It.IsAny<CancellationToken>()))
    .Returns(async (DialogContext dialogContext, object options, CancellationToken cancellationToken) =>
    {
        // Send a generic activity so we can assert that the dialog was invoked.
        await dialogContext.Context.SendActivityAsync($"{mockDialogNameTypeName} mock invoked", cancellationToken: cancellationToken);

        // Create the BookingDetails instance we want the mock object to return.
        var expectedBookingDialogResult = new BookingDetails()
        {
            Destination = "Seattle",
            Origin = "New York",
            TravelDate = $"{DateTime.UtcNow.AddDays(1):yyyy-MM-dd}"
        };

        // Return the BookingDetails we need without executing the dialog logic.
        return await dialogContext.EndDialogAsync(expectedBookingDialogResult, cancellationToken);
    });

// Create the sut (System Under Test) using the mock booking dialog.
var sut = new MainDialog(mockDialog.Object);
```

Neste exemplo, usamos o [Moq](https://github.com/moq/moq) para criar a caixa de diálogo fictícia e os métodos `Setup` e `Returns` para configurar o comportamento dela.

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
class MockBookingDialog extends BookingDialog {
    constructor() {
        super('bookingDialog');
    }

    async beginDialog(dc, options) {
        // Send a generic activity so we can assert that the dialog was invoked.
        await dc.context.sendActivity(`${ this.id } mock invoked`);

        // Create the BookingDetails instance we want the mock object to return.
        const bookingDetails = {
            origin: 'New York',
            destination: 'Seattle',
            travelDate: '2025-07-08'
        };

        // Return the BookingDetails we need without executing the dialog logic.
        return await dc.endDialog(bookingDetails);
    }
}
...
// Create the sut (System Under Test) using the mock booking dialog.
const sut = new MainDialog(new MockBookingDialog());
...

```

Neste exemplo, implementamos a caixa de diálogo fictícia derivando de `BookingDialog` e substituindo o método `beginDialog` para ignorar a lógica de caixa de diálogo subjacente.

---

### <a name="mocking-luis-results"></a>Simulando resultados do LUIS

Em cenários simples é possível implementar resultados do LUIS fictícios por meio de código, da seguinte maneira:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
var mockRecognizer = new Mock<IRecognizer>();
mockRecognizer
    .Setup(x => x.RecognizeAsync<FlightBooking>(It.IsAny<ITurnContext>(), It.IsAny<CancellationToken>()))
    .Returns(() =>
    {
        var luisResult = new FlightBooking
        {
            Intents = new Dictionary<FlightBooking.Intent, IntentScore>
            {
                { FlightBooking.Intent.BookFlight, new IntentScore() { Score = 1 } },
            },
            Entities = new FlightBooking._Entities(),
        };
        return Task.FromResult(luisResult);
    });
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript

// Create a mock class for the recognizer that overrides executeLuisQuery.
class MockFlightBookingRecognizer extends FlightBookingRecognizer {
    constructor(mockResult) {
        super();
        this.mockResult = mockResult;
    }

    async executeLuisQuery(context) {
        return this.mockResult;
    }
}
...
// Create a mock result from a string
const mockLuisResult = JSON.parse(`{"intents": {"BookFlight": {"score": 1}}, "entities": {"$instance": {}}}`);
// Use the mock result with the mock recognizer.
const mockRecognizer = new MockFlightBookingRecognizer(mockLuisResult);
...
```

---

Mas os resultados do LUIS podem ser complexos, e quando o são, é mais simples capturar o resultado desejado em um arquivo JSON, adicioná-lo como um recurso ao projeto e desserializá-lo em um resultado do LUIS. Veja um exemplo:

## <a name="c"></a>[C#](#tab/csharp)

```csharp
var mockRecognizer = new Mock<IRecognizer>();
mockRecognizer
    .Setup(x => x.RecognizeAsync<FlightBooking>(It.IsAny<ITurnContext>(), It.IsAny<CancellationToken>()))
    .Returns(() =>
    {
        // Deserialize the LUIS result from embedded json file in the TestData folder.
        var bookingResult = GetEmbeddedTestData($"{GetType().Namespace}.TestData.FlightToMadrid.json");

        // Return the deserialized LUIS result.
        return Task.FromResult(bookingResult);
    });
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Create a mock result from a json file
const mockLuisResult = require(`./testData/FlightToMadrid.json`);
// Use the mock result with the mock recognizer.
const mockRecognizer = new MockFlightBookingRecognizer(mockLuisResult);
```

---

## <a name="additional-information"></a>Informações adicionais

- [Exemplo de teste do CoreBot (C#)](https://aka.ms/cs-core-test-sample)
- [Exemplo de teste do CoreBot (JavaScript)](https://aka.ms/js-core-test-sample)
- [Testes de bot](https://github.com/microsoft/botframework-sdk/blob/master/specs/testing/testing.md)
