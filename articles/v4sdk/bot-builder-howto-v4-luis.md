---
title: Adicionar o reconhecimento vocal natural ao seu bot – Serviço de Bot
description: Aprenda a usar o LUIS para reconhecimento de linguagem natural com o SDK do Bot Framework.
keywords: Entendimento de Linguagem, LUIS, intenção, reconhecedor, entidades, middleware
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8d4886041fba86fbdd9c0e36e3a9c882782a7242
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073935"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Adicionar reconhecimento de idioma natural ao seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

A capacidade de entender o que seu usuário quer dizer contextualmente e em conversas pode ser uma tarefa difícil, mas pode dar ao seu bot uma sensação de conversa mais natural. O _LUIS (Reconhecimento vocal)_ é um serviço de API baseado em nuvem que permite que você faça exatamente isso, para que o seu bot possa reconhecer a intenção das mensagens do usuário, permitir uma linguagem mais natural do seu usuário e direcionar melhor o fluxo de conversação.

Este tópico explica como adicionar LUIS a um aplicativo de reservas de voo para reconhecer intenções e entidades diferentes contidas na entrada do usuário.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do [LUIS](https://www.luis.ai).
- Uma cópia da amostra de **Bot Básico** em [**C#** ](https://aka.ms/cs-core-sample), [**JavaScript**](https://aka.ms/js-core-sample) ou [**Python**](https://aka.ms/python-core-sample).
- Conhecimento das [Noções básicas do bot](bot-builder-basics.md), do [processamento de idioma natural](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis) e do [gerenciamento de recursos do bot](bot-file-basics.md).

## <a name="about-this-sample"></a>Sobre este exemplo

Essa amostra de bot básico mostra um exemplo de um aplicativo de reservas de voo em aeroportos. Ele usa um serviço do LUIS para reconhecer a entrada do usuário e retornar a melhor intenção reconhecida pelo LUIS.

O modelo de linguagem contém três intenções: `Book Flight`, `Cancel` e `None`. O LUIS usará essas intenções para entender o que o usuário pretendia ao enviar uma mensagem ao bot. O modelo de linguagem também define as entidades que o LUIS pode extrair da entrada do usuário, como o aeroporto de origem ou de destino.

# <a name="c"></a>[C#](#tab/csharp)

Após cada processamento da entrada do usuário, `DialogBot` salva o estado atual de `UserState` e `ConversationState`. Após a coleta de todas as informações necessárias, o exemplo de codificação cria uma reserva de reserva de voo de demonstração. Neste artigo, abordaremos os aspectos LUIS deste exemplo. No entanto, o fluxo geral do exemplo é mostrado a seguir:

- `OnMembersAddedAsync` é chamado quando um novo usuário é conectado e exibe um cartão de boas-vindas.
- `OnMessageActivityAsync` é chamado em cada entrada do usuário recebida.

![Fluxo lógico do exemplo do LUIS](./media/how-to-luis/luis-logic-flow.png)

O módulo `OnMessageActivityAsync` executa o diálogo apropriado por meio do método de extensão de diálogo `Run`. Em seguida, a caixa de diálogo principal chama o auxiliar do LUIS para localizar a intenção do usuário com melhor pontuação. Se a intenção principal para a entrada do usuário retornar "BookFlight", o auxiliar preencherá as informações do usuário retornadas pelo LUIS. Depois disso, a caixa de diálogo principal inicia o `BookingDialog`, que adquire informações adicionais do usuário conforme necessário, tais como:

- `Origin` a cidade de origem
- `TravelDate` a data da reserva do voo
- `Destination` a cidade de destino

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

Após cada processamento da entrada do usuário, `DialogBot` salva o estado atual de `user_state` e `conversation_state`. Após a coleta de todas as informações necessárias, o exemplo de codificação cria uma reserva de reserva de voo de demonstração. Neste artigo, abordaremos os aspectos LUIS deste exemplo. No entanto, o fluxo geral do exemplo é mostrado a seguir:

- `on_members_added_activity` é chamado quando um novo usuário é conectado e exibe um cartão de boas-vindas.
- `on_message_activity` é chamado em cada entrada do usuário recebida.

![Fluxo lógico do Python de exemplo do LUIS](./media/how-to-luis/luis-logic-flow-python.png)

O módulo `on_message_activity` executa o diálogo apropriado por meio do método de extensão de diálogo `run_dialog`. Em seguida, a caixa de diálogo principal chama o `LuisHelper` para localizar a intenção do usuário com melhor pontuação. Se a intenção principal para a entrada do usuário retornar "BookFlight", a função auxiliar preencherá as informações do usuário retornadas pelo LUIS. Depois disso, a caixa de diálogo principal inicia o `BookingDialog`, que adquire informações adicionais do usuário conforme necessário, tais como:

- `destination` A cidade de destino.
- `origin` A cidade de origem.
- `travel_date` A data da reserva do voo.

---

Este artigo aborda como adicionar o LUIS a um bot. Para obter informações sobre como usar diálogos ou estado, consulte como [coletar entradas do usuário usando um prompt de diálogo](bot-builder-prompts.md) ou como [salvar dados do usuário e da conversa](bot-builder-howto-v4-state.md), respectivamente.

## <a name="create-a-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS

1. Entre no portal do [LUIS](https://www.luis.ai).
    - Se você ainda não tem uma conta, crie uma.
    - Se você ainda não tem um recurso de criação, crie um.
    - Para obter mais informações, consulte a documentação do LUIS sobre como [entrar no portal do LUIS](/azure/cognitive-services/luis/luis-how-to-start-new-app#sign-in-to-luis-portal).
1. Na página **Meus Aplicativos**, clique em **Novo aplicativo para conversa** e selecione **Importar como JSON**.
1. No diálogo **Importar novo aplicativo**:
    1. Escolha o arquivo **FlightBooking.json** na pasta **CognitiveModels** da amostra.
    1. Insira `FlightBooking` como o nome opcional do aplicativo e clique em **Concluído**.
1. Treine e publique o seu aplicativo.
    Para obter mais informações, consulte a documentação do LUIS sobre como [treinar](/azure/cognitive-services/LUIS/luis-how-to-train) e [publicar](/azure/cognitive-services/LUIS/publishapp) um aplicativo no ambiente de produção.

### <a name="why-use-entities"></a>Por que usar entidades?

As entidades LUIS permitem que seu bot entenda determinadas coisas ou eventos diferentes das intenções padrão. Assim, você pode coletar mais informações do usuário, o que permite que seu bot responda de forma mais inteligente ou ignore algumas perguntas nas quais ele pede essas informações ao usuário. Além das definições das três intenções do LUIS (“Reservar voo, “Cancelar” e “Nenhum”), o arquivo FlightBooking.json também contém um conjunto de entidades, como “From.Airport” e “To.Airport”. Essas entidades permitem que o LUIS detecte e retorne informações adicionais contidas na entrada original do usuário, quando ele solicitar uma nova reserva de viagem.

## <a name="obtain-values-to-connect-to-your-luis-app"></a>Obter valores para conectar ao seu aplicativo LUIS

Após a publicação de seu aplicativo LUIS, acesse-o no bot. Será necessário registrar vários valores para acessar seu aplicativo LUIS no bot. Você pode recuperar essas informações usando o portal do LUIS.

### <a name="retrieve-application-information-from-the-luisai-portal"></a>Recupere as informações do aplicativo no portal LUIS.ai

O arquivo de configurações (`appsettings.json`, `.env` ou `config.py`) age como um único local para reunir todas as referências de serviço. As informações que você recupera serão adicionadas a esse arquivo na próxima seção.

1. Marque seu aplicativo LUIS publicado em [luis.ai](https://www.luis.ai).
1. Com o aplicativo LUIS publicado aberto, escolha a guia **GERENCIAR**.
1. Selecione a guia **Configurações** no lado esquerdo.
    Registre o valor mostrado para a _ID do Aplicativo_ como \<YOUR_APP_ID>.
    ![Gerenciar o Aplicativo LUIS – Informações do Aplicativo](./media/how-to-luis/manage-luis-app-app-info.png)
1. Selecione a guia **Recursos do Azure** no lado esquerdo e selecione o grupo **Recurso de Criação**.
    Registre o valor mostrado para _Localização_ como \<YOUR_REGION> e _Chave Primária_ como \<YOUR_AUTHORING_KEY>.
    ![Gerenciar o Aplicativo LUIS – Informações do Aplicativo](./media/how-to-luis/manage-luis-app-azure-resources.png)

### <a name="update-the-settings-file"></a>Atualizar o arquivo de configurações

# <a name="c"></a>[C#](#tab/csharp)

Adicione as informações necessárias para acessar o aplicativo LUIS, incluindo ID do aplicativo, chave de criação e região, ao arquivo `appsettings.json`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado. Lembre-se de que o nome do host da API deve estar no formato `<your region>.api.cognitive.microsoft.com`.

**appsetting.json**

[!code-json[appsettings](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/appsettings.json?range=1-7)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Adicione as informações necessárias para acessar o aplicativo LUIS, incluindo ID do aplicativo, chave de criação e região, ao arquivo `.env`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado. Lembre-se de que o nome do host da API deve estar no formato `<your region>.api.cognitive.microsoft.com`.

**.env**

[!code[env](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/.env?range=1-5)]

# <a name="python"></a>[Python](#tab/python)

Adicione as informações necessárias para acessar o aplicativo LUIS, incluindo ID do aplicativo, chave de criação e região, ao arquivo `config.py`. Estes são os valores que você salvou anteriormente de seu aplicativo LUIS publicado. Lembre-se de que o nome do host da API deve estar no formato `<your region>.api.cognitive.microsoft.com`.

**config.py**

[!code-python[config.py](~/../botbuilder-samples/samples/python/13.core-bot/config.py?range=14-19)]

---

## <a name="configure-your-bot-to-use-your-luis-app"></a>Configure seu bot para usar seu aplicativo LUIS

# <a name="c"></a>[C#](#tab/csharp)

Verifique se o pacote do NuGet **Microsoft.Bot.Builder.AI.Luis** está instalado para o seu projeto.

Para se conectar ao serviço do LUIS, o bot extrai as informações adicionadas acima do arquivo appsetting.json. A classe `FlightBookingRecognizer` contém código com as configurações do arquivo appsetting.json e consulta o serviço do LUIS chamando o método `RecognizeAsync`.

**FlightBookingRecognizer.cs**

[!code-csharp[luisHelper](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/FlightBookingRecognizer.cs?range=12-48)]

O `FlightBookingEx.cs` contém a lógica para extrair *From*, *To* e *TravelDate*; ela estende a classe parcial `FlightBooking.cs` usada para armazenar os resultados do LUIS ao chamar `FlightBookingRecognizer.RecognizeAsync<FlightBooking>` do `MainDialog.cs`.

**CognitiveModels\FlightBookingEx.cs**

[!code-csharp[luis helper](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/CognitiveModels/FlightBookingEx.cs?range=8-35)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar o LUIS, seu projeto precisa instalar o pacote do npm **botbuilder-ai**.

Para se conectar ao serviço do LUIS, o bot usa as informações adicionadas acima do arquivo `.env`. A classe `flightBookingRecognizer.js` contém o código que importa as configurações do arquivo `.env` e consulta o serviço do LUIS chamando o método `recognize()`.

**dialogs/flightBookingRecognizer.js**

[!code-javascript[luis helper](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/dialogs/flightBookingRecognizer.js?range=6-70)]

A lógica para extrair From, To e TravelDate é implementada como métodos auxiliares dentro de `flightBookingRecognizer.js`. Esses métodos são usados após chamar `flightBookingRecognizer.executeLuisQuery()` de `mainDialog.js`

# <a name="python"></a>[Python](#tab/python)

Certifique-se de que o pacote PyPI **botbuilder-ai** esteja instalado para seu projeto.

Para se conectar ao serviço do LUIS, o bot usa as informações adicionadas acima do arquivo `config.py`. A classe `FlightBookingRecognizer` contém o código que importa as configurações do arquivo `config.py` e consulta o serviço do LUIS chamando o método `recognize()`.

**flight_booking_recognizer.py**

[!code-python[config.py](~/../botbuilder-samples/samples/python/13.core-bot/flight_booking_recognizer.py?range=10-36&highlight=26)]

A lógica para extrair *From*, *To* e *travel_date* é implementada como métodos auxiliares da classe `LuisHelper` dentro de `luis_helper.py`. Esses métodos são usados após chamar `LuisHelper.execute_luis_query()` de `main_dialog.py`

**helpers/luis_helper.py**

[!code-python[luis helper](~/../botbuilder-samples/samples/python/13.core-bot/helpers/luis_helper.py?range=30-102)]

---

Agora, o LUIS está configurado e conectado ao seu bot.

## <a name="test-the-bot"></a>Testar o bot

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o exemplo localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para o [Exemplo de C#](https://aka.ms/cs-core-sample), [Exemplo de JS](https://aka.ms/js-core-sample) ou [Exemplo de Python](https://aka.ms/python-core-sample).

1. No emulador, digite uma mensagem, como "viagem para paris" ou "ir de paris para berlim". Use qualquer enunciado encontrado no arquivo FlightBooking.json para treinar a intenção "Reservar voo".

![Entrada de reserva do LUIS](./media/how-to-luis/luis-user-travel-input.png)

Se a melhor intenção retornada pelo LUIS for "Reservar voo", seu bot fará perguntas adicionais até que tenha informações suficientes armazenadas para criar uma reserva de viagem. Nesse ponto, ele retornará essas informações de reserva para o usuário.

![Resultado da reserva do LUIS](./media/how-to-luis/luis-travel-result.png)

Nesse momento, a lógica de bot do código reiniciará e você poderá continuar a criar reservas adicionais.

## <a name="additional-information"></a>Informações adicionais

Para obter mais informações sobre o LUIS, consulte a documentação dele:

- [O que é o LUIS (Reconhecimento vocal)?](/azure/cognitive-services/LUIS/what-is-luis)
- [Criar um aplicativo LUIS no portal do LUIS](/azure/cognitive-services/LUIS/luis-how-to-start-new-app)
- [Design com intenção e modelos de entidade](/azure/cognitive-services/LUIS/luis-concept-model)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o QnA Maker para responder a perguntas](./bot-builder-howto-qna.md)
