---
title: Adicionar mídia às mensagens | Microsoft Docs
description: Saiba como adicionar mídia às mensagens usando o SDK do Bot Framework.
keywords: mídia, mensagens, imagens, áudio, vídeo, arquivos, MessageFactory, rich cards, mensagens, cartões adaptáveis, cartão hero e ações sugeridas
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4caa11221ed930a627b53d7f46107adb226a816d
ms.sourcegitcommit: e815e786413296deea0bd78e5a495df329a9a7cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876171"
---
# <a name="add-media-to-messages"></a>Adicionar mídia às mensagens

[!INCLUDE[applies-to](../includes/applies-to.md)]

A troca de mensagens entre usuário e bot pode conter anexos de mídia como imagens, vídeo, áudio e arquivos. O SDK do Bot Framework oferece suporte à tarefa de envio de mensagens avançadas para o usuário. Para determinar o tipo de mensagens avançadas com suporte de um canal (Facebook, Skype, Slack etc), consulte a documentação do canal para obter informações sobre as limitações.

## <a name="prerequisites"></a>Pré-requisitos
- Conhecimento sobre os [conceitos básicos do bot](bot-builder-basics.md).
- O código deste artigo baseia-se nas seguintes amostras:

  | Exemplo de código | C# | JS |
  | :------ | :----- | :---|
  | Cartões | [Exemplo de C#](https://aka.ms/bot-cards-sample-code) | [Exemplo de JS](https://aka.ms/bot-cards-js-sample-code) |
  | Anexos | [Exemplo de C#](https://aka.ms/bot-attachments-sample-code) | [Exemplo de JS](https://aka.ms/bot-attachments-sample-code-js) |
  | Ações sugeridas | [Exemplo de C#](https://aka.ms/SuggestedActionsCSharp) | [Exemplo de JS](https://aka.ms/SuggestedActionsJS) |

## <a name="send-attachments"></a>Enviar anexos

Para enviar o conteúdo do usuário, como uma imagem ou um vídeo, é possível adicionar um anexo ou lista de anexos a uma mensagem.

Confira a [experiência de usuário de design](../bot-service-design-user-experience.md) para obter exemplos dos cartões disponíveis.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

A propriedade `Attachments` do objeto `Activity` contém uma matriz de objetos `Attachment` que representam os anexos de mídia e os cartões avançados na mensagem. Para adicionar um anexo de mídia a uma mensagem, crie um objeto `Attachment` para a atividade `reply` (que é criado fora dela com `CreateReply()`) e defina as propriedades `ContentType`, `ContentUrl` e `Name`.

O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos](https://aka.ms/bot-attachments-sample-code).

Para criar a mensagem de resposta, defina o texto e, em seguida, configure os anexos. A atribuição dos anexos à resposta é a mesma para cada tipo de anexo, no entanto vários anexos são configurados e definidos de forma diferente, como visto nos snippets de código a seguir. O código a seguir é a configuração da resposta para um anexo embutido:

**Bots/AttachmentsBot.cs**  
[!code-csharp[inline attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=105-106)]

Em seguida, vamos examinar os tipos de anexos. O primeiro é um anexo embutido:

**Bots/AttachmentsBot.cs**  
[!code-csharp[inline attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=167-178)]

Em seguida, um anexo carregado:

**Bots/AttachmentsBot.cs**  
[!code-csharp[uploaded attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=181-214)]

Por fim, um anexo de internet:

**Bots/AttachmentsBot.cs**  
[!code-csharp[online attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=217-226)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos JS](https://aka.ms/bot-attachments-sample-code-js).

Para usar anexos, inclua as seguintes bibliotecas em seu bot:

**bots/attachmentsBot.js**  
[!code-javascript[attachments libraries](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=4)]

Para criar a mensagem de resposta, defina o texto e, em seguida, configure os anexos. A atribuição dos anexos à resposta é a mesma para cada tipo de anexo, no entanto vários anexos são configurados e definidos de forma diferente, como visto nos snippets de código a seguir. O código a seguir é a configuração da resposta para um anexo embutido:

**bots/attachmentsBot.js**  
[!code-javascript[attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=119,128-129)]

Para enviar ao usuário uma única parte do conteúdo, como uma imagem ou um vídeo, é possível enviar mídia de algumas maneiras diferentes. Primeiro, como um anexo embutido:

**bots/attachmentsBot.js**  
[!code-javascript[inline attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=170-179)]

Em seguida, um anexo carregado:

**bots/attachmentsBot.js**  
[!code-javascript[uploaded attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=197-215)]

Por fim, um anexo de internet contido em uma URL:

**bots/attachmentsBot.js**  
[!code-javascript[internet attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=184-191)]

---

Se um anexo for uma imagem, áudio ou vídeo, o serviço do Connector comunicará os dados do anexo ao canal de modo a permitir que o [canal](bot-builder-channeldata.md) renderize esse anexo na conversa. Se o anexo for um arquivo, a URL do arquivo será renderizada como um hiperlink na conversa.

## <a name="send-a-hero-card"></a>Enviar um cartão hero

Além de anexos de vídeo ou imagem simples, é possível anexar um **cartão hero**, o que permite a você combinar imagens e botões em um objeto e depois enviá-los ao usuário. O markdown tem suporte na maioria dos campos de texto, mas o suporte pode variar por canal.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para redigir uma mensagem com um cartão hero e um botão, anexe um `HeroCard` a uma mensagem. 

O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos](https://aka.ms/bot-attachments-sample-code).

**Bots/AttachmentsBot.cs**  
[!code-csharp[Hero card](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=39-58)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para redigir uma mensagem com um cartão hero e um botão, anexe um `HeroCard` a uma mensagem. 

O código-fonte mostrado aqui se baseia no exemplo [Tratamento de Anexos JS](https://aka.ms/bot-attachments-sample-code-js).

**bots/attachmentsBot.js**  
[!code-javascript[hero card](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=148-164)]

---

## <a name="process-events-within-rich-cards"></a>Processar eventos em cartões avançados

Para processar eventos em cartões avançados, use objetos  _ação do cartão_ para especificar o que deve acontecer quando o usuário clica em um botão ou toca em uma seção do cartão. Cada ação de cartão tem um _tipo_ e _valor_.

Para funcionar corretamente, atribua um tipo de ação para cada item clicável no cartão. Esta tabela lista e descreve os tipos de ação disponíveis e quais devem estar na propriedade de valor associada.

| Type | DESCRIÇÃO | Valor |
| :---- | :---- | :---- |
| openUrl | Abre uma URL no navegador interno. | A URL para abrir. |
| imBack | Envia uma mensagem para o bot e posta uma resposta visível no bate-papo. | Texto da mensagem a ser enviada. |
| postBack | Envia uma mensagem para o bot e não pode postar uma resposta visível no bate-papo. | Texto da mensagem a ser enviada. |
| chamada | Inicia uma chamada telefônica. | Destino de uma chamada telefônica nesse formato: `tel:123123123123`. |
| playAudio | Reproduz áudio. | A URL do áudio para reproduzir. |
| playVideo | Reproduz um vídeo. | A URL do vídeo para reproduzir. |
| showImage | Exibe uma imagem. | A URL da imagem para exibir. |
| downloadFile | Baixa um arquivo. | A URL do arquivo para download. |
| signin | Inicia um processo de entrada do OAuth. | A URL do fluxo de OAuth para iniciar. |

## <a name="hero-card-using-various-event-types"></a>Cartão hero usando vários tipos de evento

O código a seguir mostra exemplos que usam diversos eventos de cartão avançados.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para obter exemplos de todos os cartões disponíveis, confira o [exemplo de cartões C#](https://aka.ms/bot-cards-sample-code).

**Cards.cs**  
[!code-csharp[hero cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=27-40)]

**Cards.cs**  
[!code-csharp[cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=91-100)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para obter exemplos de todos os cartões disponíveis, confira o [exemplo de cartões JS](https://aka.ms/bot-cards-js-sample-code).

**dialogs/mainDialog.js**  
[!code-javascript[hero cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=213-225)]

**dialogs/mainDialog.js**  
[!code-javascript[sign in cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=266-272)]

---

## <a name="send-an-adaptive-card"></a>Enviar um cartão adaptável
O Cartão Adaptável e MessageFactory são usados para enviar mensagens avançadas incluindo textos, imagens, vídeo, áudio e arquivos para se comunicar com os usuários. No entanto, há algumas diferenças entre eles. 

Primeiro, somente alguns canais dão suporte aos Cartões Adaptáveis, e os canais que dão suporte podem dar suporte parcial aos Cartões Adaptáveis. Por exemplo, se você enviar um Cartão Adaptável no Facebook, os botões não funcionarão se textos e imagens funcionarem bem. MessageFactory é simplesmente uma classe auxiliar dentro do SDK do Bot Framework para automatizar as etapas de criação para você e oferece suporte para a maioria dos canais. 

Em segundo lugar, o Cartão Adaptável entrega as mensagens no formato de cartão e o canal determina o layout do cartão. O formato das mensagens que o MessageFactory entrega depende do canal e não é necessariamente no formato de cartão. a menos que o Cartão Adaptável faça parte do anexo. 

Para localizar as informações mais recentes sobre o suporte de canal do Cartão Adaptável, confira o <a href="http://adaptivecards.io/designer/">Designer de Cartões Adaptáveis</a>.

Para usar os cartões adaptáveis, certifique-se de adicionar o pacote NuGet `AdaptiveCards`. 


> [!NOTE]
> Você deve testar esse recurso com os canais de que bot será usado para determinar se esses canais dão suporte a cartões adaptáveis.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para usar Cartões Adaptáveis, certifique-se de adicionar o pacote NuGet `AdaptiveCards`.

O código-fonte mostrado aqui se baseia no exemplo [Usando Cartões](https://aka.ms/bot-cards-sample-code):

**Cards.cs**  
[!code-csharp[adaptive cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=13-25)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar Cartões Adaptáveis, certifique-se de adicionar o pacote npm `adaptivecards`.

O código-fonte mostrado aqui se baseia no exemplo [Usando Cartões JS](https://aka.ms/bot-cards-js-sample-code). 

Aqui, o cartão Adaptável é armazenado em seu próprio arquivo e incluído em nosso bot:

**resources/adaptiveCard.json**  
[!code-json[adaptive cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/resources/adaptiveCard.json)]

Em seguida, ele é criado com o CardFactory:

**dialogs/mainDialog.js**  
[!code-javascript[adaptive cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=177-179)]

---

## <a name="send-a-carousel-of-cards"></a>Enviar um carrossel de cartões

As mensagens também podem incluir vários anexos em um layout de carrossel, que coloca os anexos lado a lado e permite ao usuário rolar para deslocar.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

O código-fonte mostrado aqui se baseia no [exemplo Cartões](https://aka.ms/bot-cards-sample-code).

Primeiro, crie a resposta e defina os anexos como uma lista.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=61-66)]

Em seguida, adicione os anexos. Aqui estamos adicionando-os um de cada vez, mas sinta-se à vontade para manipular a lista e adicionar os cartões como preferir.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=104-113)]

Depois que os anexos forem adicionados, você poderá enviar a resposta como faria com qualquer outra.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=117-118)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O código-fonte mostrado aqui se baseia no [exemplo de cartões JS](https://aka.ms/bot-cards-js-sample-code).

Para enviar um carrossel de cartões, envie uma resposta com os anexos como uma matriz e o tipo de layout definido como `Carousel`:

**dialogs/mainDialog.js**  
[!code-javascript[carousel of cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=104-116)]

---

<!-- TODO: Add a media card, such as video or audion. Revisit which examples we put here and link to the 06 through 08 samples. -->

## <a name="additional-resources"></a>Recursos adicionais

Confira a [experiência de usuário de design](../bot-service-design-user-experience.md) para obter exemplos dos cartões disponíveis.

Para obter informações detalhadas sobre o esquema, confira o [esquema de cartão do Bot Framework](https://aka.ms/botSpecs-cardSchema) e a [seção de atividades de mensagem](https://aka.ms/botSpecs-activitySchema#message-activity) do esquema de Atividade do Bot Framework.

### <a name="code-sample-for-processing-adaptive-card-input"></a>Exemplo de código para processar entrada do Cartão Adaptável

Este exemplo de código mostra uma maneira de usar entradas de Cartão Adaptável dentro de uma classe de caixa de diálogo de bot.
Ele estende o atual exemplo 06.using-cards validando a entrada recebida no campo de texto do cliente está respondendo.
Primeiro, adicionamos funcionalidades de botão e de entrada de texto ao cartão adaptável existente adicionando o seguinte código antes do colchete final de adaptiveCard.json, encontrado na pasta de recursos:

```json
  ,
  "actions": [
    {
      "type": "Action.ShowCard",
      "title": "Text",
      "card": {
      "type": "AdaptiveCard",
      "body": [
        {
          "type": "Input.Text",
          "id": "text",
          "isMultiline": true,
          "placeholder": "Enter your comment"
        }
      ],
      "actions": [
        {
          "type": "Action.Submit",
          "title": "OK"
        }
      ]
    }
  }
]

```

Observe que o campo de entrada é rotulado "text", de modo que nosso cartão adaptável anexará os dados de texto do comentário como Valor.[texto].

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
nosso validador usa Newtonsoft.json para, primeiro, converter isso em um JObject e, em seguida, criar uma cadeia de caracteres de texto aparada para comparação. Portanto, adicione:
  ```csharp
  using Newtonsoft.Json.Linq;
  ```
a MainDialog.cs e instale o pacote NuGet estável mais recente de Newtonsoft.Json.
No código do validador, adicionamos o fluxo de lógica aos comentários do código. Esse código ChoiceValidator() é colocado no exemplo 06.using-cards logo após o colchete fechado de forma pública para a declaração de MainDialog:

```csharp
private async Task ChoiceValidator(
  PromptValidatorContext promptContext,
  CancellationToken cancellationToken)
  {
    // Retrieves Adaptive Card comment text as JObject.
    // looks for JObject field "text" and converts that input into a trimmed text string.
    var jobject = promptContext.Context.Activity.Value as JObject;
    var jtoken = jobject?["text"];
    var text = jtoken?.Value().Trim();
    // Logic: 1. if succeeded = true, just return promptContext
    //        2. if false, see if JObject contained Adaptive Card input.
    //               No = (bad input) return promptContext
    //               Yes = update Value field with JObject text string, return "true".
    if (!promptContext.Recognized.Succeeded && text != null)
    {
       var choice = promptContext.Options.Choices.FirstOrDefault(
       c => c.Value.Equals(text, StringComparison.InvariantCultureIgnoreCase));
       if (choice != null)
       {
           promptContext.Recognized.Value = new FoundChoice
            {
               Value = choice.Value,
             };
            return true;
       }
    }
    return promptContext.Recognized.Succeeded;
  }
```

Agora, na declaração MainDialog, altere:
  ```csharp
  // Define the main dialog and its related components.
  AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));
  ```
para:
  ```csharp
  // Define the main dialog and its related components.
  AddDialog(new ChoicePrompt(nameof(ChoicePrompt), ChoiceValidator));
  ```
Isso invocará seu validador para procurar pela entrada do Cartão Adaptável sempre que um novo ChoicePrompt for criado.

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Abra mainDialog.js e localize o método de execução _async run(turnContext, accessor)_ . Esse método manipula a atividade de entrada.
Logo após a chamada _dialogSet.add(this);_ , adicione o seguinte:
```JavaScript
  // The following check looks for a non-existant text input
  // plus Adaptive Card input in _activity.value.text
  // If both conditions exist, the Activity Card text 
  // is copied into the text input field.
  if(turnContext._activity.text == null
      && turnContext._activity.value.text != null)
   {
      this.logger.log('replacing null text with Activity Card text input');
      turnContext._activity.text = turnContext._activity.value.text;
   }
```
Se essa verificação encontrar uma entrada de texto inexistente do cliente, ela verificará se há entrada de um Cartão Adaptável.
Se houver uma entrada de Cartão Adaptável em \_activity.value.text, ela a copiará para o campo de entrada de texto normal.

---

Para testar seu código, depois que um Cartão Adaptável for exibido, clique no botão "Texto", insira uma seleção válida, como "Cartão Hero", e clique no botão "OK".

![Testar Cartão Adaptável](media/adaptive-card-input.png)

1. A primeira entrada será ser usada para iniciar uma nova caixa de diálogo.
2. Clique no botão "OK" novamente e essa entrada será usada para selecionar um novo cartão.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar botões para orientar a ação do usuário](./bot-builder-howto-add-suggested-actions.md)
