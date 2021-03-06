---
title: Depurar um bot com middleware de inspeção – Serviço de Bot
description: Saiba como usar o middleware de inspeção para depurar bots. Consulte como usar o emulador do bot Framework para inspecionar dados de estado e tráfego de mensagens que envolve bots.
author: zxyanliu
ms.author: v-liyan
keywords: SDK do Bot Framework, depurar bot, middleware de inspeção, bot emulator, registro de canais de bot do Azure
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/17/2020
ms.openlocfilehash: 3af164c740fb22f97d95adca7a2b053dca0ae52a
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576068"
---
# <a name="debug-a-bot-with-inspection-middleware"></a>Depurar um bot com middleware de inspeção

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como depurar um bot usando o middleware de inspeção. Esse recurso permite que o Bot Framework Emulator depure o tráfego dentro e fora do bot, além de examinar o estado atual dele. Você pode usar uma mensagem de rastreamento para enviar dados para o emulador e, em seguida, inspecionar o estado do bot em qualquer determinada rodada da conversa.

Usamos um EchoBot criado localmente usando o Bot Framework v4 ([C#](dotnet/bot-builder-dotnet-sdk-quickstart.md) |
[JavaScript](javascript/bot-builder-javascript-quickstart.md) |
[Python](python/bot-builder-python-quickstart.md)) para mostrar como depurar e inspecionar o estado da mensagem do bot. Você também pode [Depurar um bot usando o IDE](./bot-service-debug-bot.md) ou [Depurar com o Bot Framework Emulator](./bot-service-debug-emulator.md), mas, para depurar o estado, você precisa adicionar o middleware de inspeção ao bot. Os exemplos de bot de inspeção estão disponíveis aqui: [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/47.inspection), [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/47.inspection) e [Python](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/python/47.inspection).

## <a name="prerequisites"></a>Pré-requisitos

- Baixar e instalar o [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started)
- Conhecimento do [middleware](v4sdk/bot-builder-concept-middleware.md) de bot
- Conhecimento do [estado de gerenciamento](v4sdk/bot-builder-concept-state.md) de bot
- Baixar e instalar o [ngrok](https://ngrok.com/) (se você quiser depurar um bot configurado no Azure para usar canais adicionais)

## <a name="update-your-emulator-to-the-latest-version"></a>Atualizar o emulador para a versão mais recente

Antes de usar o middleware de inspeção de bot para depurar o bot, você precisa atualizar seu emulador para que seja a versão 4,5 ou mais recente. Verifique a [versão mais recente](https://github.com/Microsoft/BotFramework-Emulator/releases) para obter atualizações.

Para verificar a versão do seu emulador, selecione **ajuda**  >   no no menu. Você verá a versão atual do seu emulador.

![versão atual](./media/bot-debug-inspection-middleware/bot-debug-check-emulator-version.png)

## <a name="update-your-bots-code"></a>Atualizar o código do bot

### <a name="c"></a>[C#](#tab/csharp)

Configure o estado de inspeção e adicione o middleware de inspeção ao adaptador no arquivo **Startup.cs**. O estado de inspeção é fornecido por injeção de dependência. Veja a atualização do código abaixo ou consulte o exemplo de inspeção aqui: [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/47.inspection).

**Startup.cs**  
[!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/Startup.cs?range=17-37)]

**AdapterWithInspection.cs**  
[!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/AdapterwithInspection.cs?range=11-37)]

Atualize a classe do bot no arquivo **EchoBot.cs**.

**EchoBot.cs**  
[!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/Bots/EchoBot.cs?range=14-43)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Antes de atualizar o código do bot, você deve atualizar os pacotes dele para as versões mais recentes executando o seguinte comando no terminal:

```cmd
npm install --save botbuilder@latest
```

Em seguida, você precisa atualizar o código do bot JavaScript conforme descrito a seguir. Você pode ler mais a respeito aqui: [Atualize o código do bot](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md#1-update-your-bots-code) ou consulte o exemplo de inspeção aqui: [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/47.inspection).

**index.js**

Configure o estado de inspeção e adicione o middleware de inspeção ao adaptador no arquivo **index.js**.

[!code-javascript [inspection bot sample](~/../botbuilder-samples/samples/javascript_nodejs/47.inspection/index.js?range=10-43)]

**bot.js**

Atualize a classe bot no arquivo **bot.js**.

[!code-javascript [inspection bot sample](~/../botbuilder-samples/samples/javascript_nodejs/47.inspection/bot.js?range=6-52)]

### <a name="python"></a>[Python](#tab/python)

Antes de atualizar a execução de código do bot, instale os pacotes PyPI necessários executando os seguintes comandos em um terminal:

```cmd
pip install aiohttp
pip install botbuilder-core>=4.7.0
```

Configure o estado de inspeção no arquivo **app.py** adicionando um middleware ao adaptador.

**app.py**

[!code-python [inspection bot sample](~/../botbuilder-samples/samples/python/47.inspection/app.py?range=76-84)]

Atualize a classe do bot no arquivo **echo_bot.py**.

**bots/echo_bot.py**

[!code-python [inspection bot sample](~/../botbuilder-samples/samples/python/47.inspection/bots/echo_bot.py?range=16-64)]

---

## <a name="test-your-bot-locally"></a>Testar o bot localmente

Depois de atualizar o código, você pode executar o bot localmente e testar o recurso de depuração usando dois emuladores: um para enviar e receber mensagens e o outro para inspecionar o estado das mensagens no modo de depuração. Para testar o bot localmente, execute as seguintes etapas:

1. Navegue até o diretório do bot em um terminal e execute o seguinte comando para executar o bot localmente:

    ### <a name="c"></a>[C#](#tab/csharp)

    ```cmd
    dotnet run
    ```

    ### <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```cmd
    npm start
    ```

    ### <a name="python"></a>[Python](#tab/python)

    ```cmd
    python app.py
    ```

    ---

1. Abra seu emulador. Clique **Abrir Bot**. Preencha a URL do bot com http://localhost:3978/api/messages e os valores **MicrosoftAppId** e **MicrosoftAppPassword**. Se você tiver um bot JavaScript, poderá encontrar esses valores no arquivo **.env** desse bot. Se você tiver um bot C#, poderá encontrar esses valores no arquivo **appsettings.json**. Clique em **Conectar**.

1. Agora, abra outra janela do emulador. Essa segunda janela do emulador funcionará como um depurador. Siga as instruções descritas na etapa anterior. Marque **Abrir no modo de depuração** e clique em **Conectar**.

1. Neste ponto, você verá um comando com um identificador exclusivo ( `/INSPECT attach <identifier>` ) em seu emulador de depuração. Copie o comando inteiro com o identificador do Emulador de depuração e cole-o na caixa de chat do primeiro Emulador.

    > [!NOTE]
    > Um identificador exclusivo é gerado toda vez que o emulador é iniciado no modo de depuração depois que você adiciona o middleware de inspeção no código do bot.

1. Agora você pode enviar mensagens na caixa de chat do seu primeiro emulador e inspecionar as mensagens no emulador de depuração. Para inspecionar o estado das mensagens, clique em **estado do bot** no emulador de depuração e desdobrar os **valores** na janela **JSON** à direita. Você verá o estado do bot no emulador de depuração:

    ![estado do bot](./media/bot-debug-inspection-middleware/bot-debug-bot-state.png)

## <a name="inspect-the-state-of-a-bot-configured-in-azure"></a>Inspecionar o estado de um bot configurado no Azure

Se você quiser inspecionar o estado do bot configurado no Azure e conectado aos canais (como o Teams), precisará instalar e executar o [ngrok](https://ngrok.com/).

### <a name="run-ngrok"></a>Executar o ngrok

Neste ponto, você atualizou o emulador para a versão mais recente e adicionou o middleware de inspeção no código do bot. A próxima etapa é executar o ngrok e configurar o bot local para o registro de canais de bot do Azure. Antes de executar o ngrok, você precisa executar o bot localmente.

Para executar o bot localmente, faça o seguinte:

1. Navegue até a pasta do bot em um terminal e defina o registro do npm para usar os [builds mais recentes](https://botbuilder.myget.org/feed/botbuilder-v4-js-daily/package/npm/botbuilder-azure)

1. Execute o bot localmente. Você verá que o bot expõe um número de porta como `3978` .

1. Abra outro prompt de comando e navegue até a pasta do projeto do bot. Execute o comando a seguir:

    ```cmd
    ngrok http 3978
    ```

1. O ngrok agora está conectado ao bot executado localmente. Copie o endereço IP público.

    ![ngrok-success](./media/bot-debug-inspection-middleware/bot-debug-ngrok.png)

### <a name="update-channel-registrations-for-your-bot"></a>Atualizar registros de canal para o bot

Agora que o bot local está conectado ao ngrok, você pode configurar o bot local para o registro de canais de bot no Azure.

1. Vá até o registro de canais de bot no Azure. Clique em **Configurações** no menu à esquerda e defina o **ponto de extremidade de mensagens** com seu IP do ngrok. Se necessário, adicione **/api/messages** após o endereço IP. (Por exemplo: https://e58549b6.ngrok.io/api/messages). Marque **Habilitar Ponto de Extremidade de Streaming** e **Salvar**.

    ![endpoint](./media/bot-debug-inspection-middleware/bot-debug-channels-setting-ngrok.png)

    > [!TIP]
    > Se **Salvar** não estiver habilitado, você poderá desmarcar **Habilitar Ponto de Extremidade de Streaming** e clicar em **Salvar**, depois marcar **Habilitar Ponto de Extremidade de Streaming** e clicar em **Salvar** novamente. Você precisa verificar se **Habilitar Ponto de Extremidade de Streaming** está marcado e a configuração do ponto de extremidade foi salva.

1. Vá para o grupo de recursos do bot, clique em **Implantação** e selecione o registro de canais de bot que foi implantado anteriormente com êxito. Clique em **Entradas** no lado esquerdo para obter **appId** e **appSecret**. Atualize o arquivo **.env** do bot (ou arquivo **appsettings.json** se você tiver um bot C#) com o **appId** e o **appSecret**.

    ![get-inputs](./media/bot-debug-inspection-middleware/bot-debug-get-inputs-id-secret.png)

1. Inicie o emulador, clique em **abrir bot** e coloque http://localhost:3978/api/messages a **URL do bot**. Preencha a **ID do Aplicativo Microsoft** e a **Senha do Aplicativo Microsoft** com os mesmos **appId** e **appSecret** que você adicionou ao arquivo **.env** do nosso bot (**appsettings.json**). E clique em **Conectar**.

1. O bot em execução agora está conectado ao registro de canais de bot no Azure. Você pode testar o Webchat clicando em **Testar no Webchat** e enviando mensagens na caixa de chat.

    ![test-web-chat](./media/bot-debug-inspection-middleware/bot-debug-test-webchat.png)

1. Agora, vamos habilitar o modo de depuração no emulador. Em seu emulador, selecione **depurar**  >  **Iniciar Depuração**. Insira o endereço IP ngrok (não se esqueça de adicionar **/API/messages**) para a **URL do bot** (por exemplo, `https://e58549b6.ngrok.io/api/messages` ). Para **ID do aplicativo da Microsoft**, insira a ID do aplicativo do bot. Para **senha de aplicativo da Microsoft**, insira o segredo do aplicativo do bot. Verifique se a opção **Abrir no modo de depuração** também está marcada. Clique em **Conectar**.

1. Quando o modo de depuração estiver habilitado, um UUID será gerado em seu emulador. Um UUID é uma ID exclusiva gerada toda vez que você inicia o modo de depuração em seu emulador. Copie e cole o UUID na caixa de chat **Testar no Webchat** ou na caixa de chat do canal. Você verá a mensagem "Anexado à sessão, todo o tráfego está sendo replicado para inspeção" na caixa de chat.

Você pode iniciar a depuração do bot enviando mensagens na caixa de chat do canal configurado. Seu emulador local atualizará automaticamente as mensagens com todos os detalhes de depuração. Para inspecionar o estado de mensagens do bot, clique em **Estado do Bot** e desdobre os **valores** na janela JSON à direita.

![debug-inspection-middleware](./media/bot-debug-inspection-middleware/debug-state-inspection-channel-chat.gif)

## <a name="additional-resources"></a>Recursos adicionais

- Experimente o exemplo de bot de middleware de inspeção em [C#](https://github.com/microsoft/BotBuilder-Samples/tree/main/samples/csharp_dotnetcore/47.inspection), [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/main/samples/javascript_nodejs/47.inspection)ou [python](https://github.com/microsoft/BotBuilder-Samples/blob/main/samples/python/47.inspection) .
- Leia [solucionar problemas gerais](bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção.
- Leia o artigo de instruções [Depurar com o Emulador](bot-service-debug-emulator.md).
