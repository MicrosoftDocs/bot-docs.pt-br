---
title: Criar cliente .NET para a extensão de serviço de aplicativo do Direct Line
titleSuffix: Bot Service
description: Saiba como criar clientes .NET que se conectam a extensões do serviço de aplicativo Direct line. Veja como configurar clientes C# que se comunicam com bots por WebSockets.
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 45ced1fe668090de5d4ffd73f97438f96d3088a4
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456690"
---
# <a name="create-net-client-to-connect-to-direct-line-app-service-extension"></a>Criar um cliente .NET para se conectar à extensão do Serviço de Aplicativo do Direct Line

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como criar um cliente .NET em C# que se conecta à extensão do serviço de aplicativo do Direct Line.
Leia também este artigo complementar [Configurar bot do .NET da extensão](bot-service-channel-directline-extension-net-bot.md).

## <a name="get-direct-line-secret-key"></a>Obter chave secreta de linha direta

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. No portal do Azure, localize o recurso do **Serviço de Bot do Azure**
1. Clique em **canais** para configurar os canais do bot
1. Se ele ainda não estiver habilitado, clique no canal **Direct Line** para habilitá-lo.
1. Se ele já estiver habilitado, na tabela Conectar-se a canais, clique no link **Editar** na linha do Direct Line.
1. Role até a seção Sites. Normalmente, há um Site padrão, a menos que você o tenha excluído ou renomeado.
1. Clique no link **Mostrar** para revelar uma das chaves e, em seguida, copie e salve o valor dela. Você usará esse valor na seção [criar um cliente de linha direta C#](#create-a-c-direct-line-client).

    ![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys-net-client.png)

> [!NOTE]
> Esse valor é o segredo do cliente do Direct Line usado para se conectar à extensão do serviço de aplicativo do Direct Line. Você pode criar sites adicionais se desejar e usar esses valores secretos também.

## <a name="add-the-preview-nuget-package-source"></a>Adicionar a origem da versão prévia do pacote NuGet

Os pacotes NuGet em versão prévia necessários para criar um cliente do Direct Line em C# podem ser encontrados em um feed do NuGet.

1. No Visual Studio, navegue até o item de menu **Ferramentas -> Opções**.
1. Selecione o item **Gerenciador de Pacotes NuGet -> Origens do Pacote**.
1. Clique no botão + para adicionar uma nova origem de pacote com estes valores:
    - Nome: DL ASE Preview
    - Fonte: https://botbuilder.myget.org/F/experimental/api/v3/index.json
1. Clique no botão **Atualizar** para salvar os valores.
1. Clique em **OK** para sair da configuração de Origens do Pacote.

## <a name="create-a-c-direct-line-client"></a>Criar um cliente de linha direta C#

As interações com a extensão do serviço de aplicativo do Direct Line acontecem de modo diferente do Direct Line tradicional, pois a maior parte da comunicação ocorre em um *WebSocket*. O cliente do Direct Line atualizado inclui classes auxiliares para abrir e fechar um *WebSocket*, enviar comandos por meio do WebSocket e receber atividades de volta do bot. Esta seção descreve como criar um cliente em C# simples para interagir com um bot.

1. No Visual Studio, crie um novo projeto de aplicativo de console do .NET Core 2,2.
1. Adicione o **NuGet do cliente do Direct Line** ao seu projeto
    - Clique em Dependências na árvore de Solução
    - Selecione **Gerenciar Pacotes NuGet...**
    - Altere a origem do pacote para `DL ASE Preview` (consulte a seção [Adicionar a origem do pacote NuGet de visualização](#add-the-preview-nuget-package-source))
    - Localize o pacote [Microsoft.Bot.Connector.Directline](https://botbuilder.myget.org/feed/experimental/package/nuget/Microsoft.Bot.Connector.DirectLine) versão v3.0.3-Preview1 ou posterior.
    - Clique em **Instalar Pacote**.
1. Crie um cliente e gere um token usando um segredo. Esta etapa é o mesmo que criar qualquer outro cliente do Direct Line em C#, exceto pelo ponto de extremidade que você precisa usar em seu bot, ao qual é acrescentado o caminho **.bot/** , conforme mostrado a seguir. Não se esqueça do **/** no final.

    ```csharp
    string endpoint = "https://<your_bot_name>.azurewebsites.net/.bot/";
    string secret = "<your_bot_direct_line_secret_key>";

    var tokenClient = new DirectLineClient(
        new Uri(endpoint),
        new DirectLineClientCredentials(secret));
    var conversation = await tokenClient.Tokens.GenerateTokenForNewConversationAsync();
    ```

    Observe o seguinte:
    - O valor do ponto de extremidade é a URL do bot que você obteve quando implantou o bot no Azure.  Para obter mais informações, confira [Configurar o bot do .NET da extensão](bot-service-channel-directline-extension-net-bot.md).
    - O valor secreto mostrado como *YOUR_BOT_SECRET* é o valor que você salvou anteriormente na *seção de sites*.

1. Depois que tiver uma referência de conversa da geração de um token, você poderá usar a ID dessa conversa para abrir um WebSocket com a nova propriedade `StreamingConversations` no `DirectLineClient`. Para fazer isso, você precisa criar um retorno de chamada que será invocado quando o bot quiser enviar `ActivitySets` ao cliente:

    ```csharp
    public static void ReceiveActivities(ActivitySet activitySet)
    {
        if (activitySet != null)
        {
            foreach (var a in activitySet.Activities)
            {
                if (a.Type == ActivityTypes.Message && a.From.Id.Contains("bot"))
                {
                    Console.WriteLine($"<Bot>: {a.Text}");
                }
            }
        }
    }
    ```

1. Agora você está pronto para abrir o WebSocket na `StreamingConversations` propriedade usando o token da conversa, `conversationId` e seu retorno de `ReceiveActivities` chamada:

    ```csharp
    var client = new DirectLineClient(
        new Uri(endpoint),
        new DirectLineClientCredentials(conversation.Token));

    await client.StreamingConversations.ConnectAsync(
        conversation.ConversationId,
        ReceiveActivities);
    ```

1. Agora, o cliente agora pode ser usado para iniciar uma conversa e enviar `Activities` ao bot:

    ```csharp

    var startConversation = await client.StreamingConversations.StartConversationAsync();
    var from = new ChannelAccount() { Id = "123", Name = "Fred" };
    var message = Console.ReadLine();

    while (message != "end")
    {
        try
        {
            var response = await client.StreamingConversations.PostActivityAsync(
                startConversation.ConversationId,
                new Activity()
                {
                    Type = "message",
                    Text = message,
                    From = from
                });
        }
        catch (OperationException ex)
        {
            Console.WriteLine(
                $"OperationException when calling PostActivityAsync: ({ex.StatusCode})");
        }
        message = Console.ReadLine();
    }
    ```
