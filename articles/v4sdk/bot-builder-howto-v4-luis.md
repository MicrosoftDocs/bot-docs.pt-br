---
title: Adicionar reconhecimento de idioma natural ao seu bot | Microsoft Docs
description: Aprenda a usar o LUIS para reconhecimento de linguagem natural com o SDK do Bot Framework.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b20670b85b876d7cf10dea7f031e4d24221071a8
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70167149"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Adicionar reconhecimento de idioma natural ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]
A capacidade de entender o que seu usuário quer dizer contextualmente e em conversas pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O Language Understanding, chamado LUIS, permite que você faça exatamente isso para que seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação. Este tópico explica como adicionar LUIS a um aplicativo de reservas de voo para reconhecer intenções e entidades diferentes contidas na entrada do usuário. 

## <a name="prerequisites"></a>Pré-requisitos
- Conta [LUIS](https://www.luis.ai)
- O código neste artigo baseia-se no exemplo de **Core Bot**. Você precisará de uma cópia do exemplo em **[C#](https://aka.ms/cs-core-sample) ou [JavaScript](https://aka.ms/js-core-sample)** . 
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), do [processamento de idioma natural](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis) e do [gerenciamento de recursos do bot](bot-file-basics.md).

## <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo de codificação de core bot mostra um exemplo de um aplicativo de reservas de voo do aeroporto. Ele usa um serviço do LUIS para reconhecer a entrada do usuário e retornar a melhor intenção reconhecida pelo LUIS. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Após cada processamento da entrada do usuário, `DialogBot` salva o estado atual de `UserState` e `ConversationState`. Após a coleta de todas as informações necessárias, o exemplo de codificação cria uma reserva de reserva de voo de demonstração. Neste artigo, abordaremos os aspectos LUIS deste exemplo. No entanto, o fluxo geral do exemplo é mostrado a seguir:

- `OnMembersAddedAsync` é chamado quando um novo usuário é conectado e exibe um cartão de boas-vindas. 
- `OnMessageActivityAsync` é chamado em cada entrada do usuário recebida.

![Fluxo lógico do exemplo do LUIS](./media/how-to-luis/luis-logic-flow.png)

O módulo `OnMessageActivityAsync` executa o diálogo apropriado por meio do método de extensão de diálogo `Run`. Em seguida, a caixa de diálogo principal chama o auxiliar do LUIS para localizar a intenção do usuário com melhor pontuação. Se a intenção principal para a entrada do usuário retornar "BookFlight", o auxiliar preencherá as informações do usuário retornadas pelo LUIS. Depois disso, a caixa de diálogo principal inicia o `BookingDialog`, que adquire informações adicionais do usuário conforme necessário, tais como:

- `Origin` a cidade de origem
- `TravelDate` a data da reserva do voo
- `Destination` a cidade de destino

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Após cada processamento da entrada do usuário, `dialogBot` salva o estado atual de `userState` e `conversationState`. Após a coleta de todas as informações necessárias, o exemplo de codificação cria uma reserva de reserva de voo de demonstração. Neste artigo, abordaremos os aspectos LUIS deste exemplo. No entanto, o fluxo geral do exemplo é mostrado a seguir:

- `onMembersAdded` é chamado quando um novo usuário é conectado e exibe um cartão de boas-vindas. 
- `OnMessage` é chamado em cada entrada do usuário recebida.

![Fluxo lógico do JavaScript de exemplo do LUIS](./media/how-to-luis/luis-logic-flow-js.png)

O módulo `onMessage` executa o `mainDialog` que reúne a entrada do usuário.
Em seguida, a caixa de diálogo principal chama o auxiliar do LUIS `FlightBookingRecognizer` para localizar a intenção do usuário com melhor pontuação. Se a intenção principal para a entrada do usuário retornar "BookFlight", o auxiliar preencherá as informações do usuário retornadas pelo LUIS.
Quando recebe uma resposta de volta, `mainDialog` preserva as informações do usuário retornadas pelo LUIS e inicia `bookingDialog`. `bookingDialog` obtém informações adicionais conforme a necessidade do usuário, como

- `destination` A cidade de destino.
- `origin` A cidade de origem.
- `travelDate` A data da reserva do voo.

---

Para obter detalhes sobre outros aspectos do exemplo, como diálogos ou estado, confira [Coletar entradas do usuário usando um prompt de diálogo](bot-builder-prompts.md) ou [Salvar dados do usuário e da conversa](bot-builder-howto-v4-state.md).

## <a name="create-a-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS
Entrar no portal do LUIS para criar sua própria versão do exemplo de aplicativo LUIS. Você pode criar e gerenciar seus aplicativos em **Meus Aplicativos**.

1. Selecione **Importar novos aplicativos**. 
1. Clique em **Escolher arquivo do aplicativo (formato JSON)...** 
1. Selecione o arquivo `FlightBooking.json` localizado na parta `CognitiveModels` do exemplo. No **Nome opcional**, insira **FlightBooking**. Esse arquivo contém três intenções: “Reservar voo”, “Cancelar” e “Nenhum”. Vamos usar essas intenções para entender o que o usuário pretende ao enviar uma mensagem ao bot.
1. [Treine](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-train) o aplicativo.
1. [Publique](https://docs.microsoft.com/azure/cognitive-services/LUIS/publishapp) o aplicativo no ambiente de *produção*.

### <a name="why-use-entities"></a>Por que usar entidades?
As entidades LUIS permitem que seu bot entenda determinadas coisas ou eventos diferentes das intenções padrão. Assim, você pode coletar mais informações do usuário, o que permite que seu bot responda de forma mais inteligente ou ignore algumas perguntas nas quais ele pede essas informações ao usuário. Além das definições das três intenções do LUIS (“Reservar voo, “Cancelar” e “Nenhum”), o arquivo FlightBooking.json também contém um conjunto de entidades, como “From.Airport” e “To.Airport”. Essas entidades permitem que o LUIS detecte e retorne informações adicionais contidas na entrada original do usuário, quando ele solicitar uma nova reserva de viagem.

Para obter informações de como as informações de entidade aparecem no resultado do LUIS, confira [Extrair dados de texto de enunciado com intenções e entidades](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-data-extraction).

## <a name="obtain-values-to-connect-to-your-luis-app"></a>Obter valores para conectar ao seu aplicativo LUIS
Após a publicação de seu aplicativo LUIS, acesse-o no bot. Será necessário registrar vários valores para acessar seu aplicativo LUIS no bot. Você pode recuperar essas informações usando o portal do LUIS.

### <a name="retrieve-application-information-from-the-luisai-portal"></a>Recupere as informações do aplicativo no portal LUIS.ai
O arquivo de configurações (`appsettings.json` ou `.env`) age como um único local para reunir todas as referências de serviço. As informações que você recupera serão adicionadas a esse arquivo na próxima seção. 
1. Marque seu aplicativo LUIS publicado em [luis.ai](https://www.luis.ai).
1. Com o aplicativo LUIS publicado aberto, escolha a guia **GERENCIAR**. ![Gerenciar aplicativo LUIS](./media/how-to-luis/manage-luis-app.png)
1. Escolha a guia **Informações do Aplicativo** no lado esquerdo, registre o valor mostrado para _ID do Aplicativo_ como <ID_DO_SEU_APLICATIVO>.
1. Escolha a guia **Chaves e Pontos de Extremidade** no lado esquerdo, registre o valor mostrado para _Chave de Criação_ como <SUA_CHAVE_DE_CRIAÇÃO>.
1. Role para baixo até o final da página, registre o valor mostrado para _Região_ como <YOUR_REGION>.

### <a name="update-the-settings-file"></a>Atualizar o arquivo de configurações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Adicione as informações necessárias para acessar o aplicativo LUIS, incluindo ID do aplicativo, chave de criação e região, ao arquivo `appsettings.json`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado. Lembre-se de que o nome do host da API deve estar no formato `<your region>.api.cognitive.microsoft.com`.

**appsetting.json**  
[!code-json[appsettings](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/appsettings.json?range=1-7)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Adicione as informações necessárias para acessar o aplicativo LUIS, incluindo ID do aplicativo, chave de criação e região, ao arquivo `.env`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado. Lembre-se de que o nome do host da API deve estar no formato `<your region>.api.cognitive.microsoft.com`.

**.env**  
[!code[env](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/.env?range=1-5)]

---

## <a name="configure-your-bot-to-use-your-luis-app"></a>Configure seu bot para usar seu aplicativo LUIS

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verifique se o pacote do NuGet **Microsoft.Bot.Builder.AI.Luis** está instalado para o seu projeto.

Para se conectar ao serviço do LUIS, o bot extrai as informações adicionadas acima do arquivo appsetting.json. A classe `FlightBookingRecognizer` contém código com as configurações do arquivo appsetting.json e consulta o serviço do LUIS chamando o método `RecognizeAsync`.

**FlightBookingRecognizer.cs**  

[!code-csharp[luisHelper](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/FlightBookingRecognizer.cs?range=12-39)]

O `FlightBookingEx.cs` contém a lógica para extrair *From*, *To* e *TravelDate*; ela estende a classe parcial `FlightBooking.cs` usada para armazenar os resultados do LUIS ao chamar `FlightBookingRecognizer.RecognizeAsync<FlightBooking>` do `MainDialog.cs`.

**CognitiveModels\FlightBookingEx.cs**  

[!code-csharp[luis helper](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/CognitiveModels/FlightBookingEx.cs?range=8-35)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Para usar o LUIS, seu projeto precisa instalar o pacote do npm **botbuilder-ai**.

Para se conectar ao serviço do LUIS, o bot usa as informações adicionadas acima do arquivo `.env`. A classe `flightBookingRecognizer.js` contém o código que importa as configurações do arquivo `.env` e consulta o serviço do LUIS chamando o método `recognize()`.

**dialogs/flightBookingRecognizer.js**

[!code-javascript[luis helper](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/dialogs/flightBookingRecognizer.js?range=6-64)]

A lógica para extrair From, To e TravelDate é implementada como métodos auxiliares dentro de `flightBookingRecognizer.js`. Esses métodos são usados após chamar `flightBookingRecognizer.executeLuisQuery()` de `mainDialog.js`

---

Agora, o LUIS está configurado e conectado ao seu bot.

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, veja [Exemplo de C#](https://aka.ms/cs-core-sample) ou [Exemplo de JS](https://aka.ms/js-core-sample) no arquivo LEIAME.

1. No emulador, digite uma mensagem, como "viagem para paris" ou "ir de paris para berlim". Use qualquer enunciado encontrado no arquivo FlightBooking.json para treinar a intenção "Reservar voo".

![Entrada de reserva do LUIS](./media/how-to-luis/luis-user-travel-input.png)

Se a melhor intenção retornada pelo LUIS for "Reservar voo", seu bot fará perguntas adicionais até que tenha informações suficientes armazenadas para criar uma reserva de viagem. Nesse ponto, ele retornará essas informações de reserva para o usuário. 

![Resultado da reserva do LUIS](./media/how-to-luis/luis-travel-result.png)

Nesse momento, a lógica de bot do código reiniciará e você poderá continuar a criar reservas adicionais. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o QnA Maker para responder a perguntas](./bot-builder-howto-qna.md)
