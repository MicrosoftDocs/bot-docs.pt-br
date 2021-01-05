---
title: Conectar um bot à Direct Line – Serviço de Bot
description: Saiba como configurar os bots para se comunicar com aplicativos cliente. Consulte como usar o canal de linha direta para essa finalidade.
keywords: linha direta, canais bot, cliente customizado, conectar aos canais, configurar
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/04/2020
ms.openlocfilehash: 6259854cd482ff3f0c1bce9a60b13c4c6561c140
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759235"
---
# <a name="connect-a-bot-to-direct-line"></a>Conecte um bot à linha direta

Este artigo descreve como conectar um bot ao canal de **linha direta**. Use este canal para permitir que o aplicativo cliente se comunique com um bot.

> Observe que a linha direta é um canal padrão sobre o protocolo HTTPS para permitir a comunicação entre um aplicativo cliente e um bot. Se você precisar de isolamento de rede em vez disso, use a [extensão do serviço de aplicativo de linha direta](bot-service-channel-directline-extension.md) sobre o [protocolo WebSocket](https://tools.ietf.org/html/rfc6455).


## <a name="add-the-direct-line-channel"></a>Adicionar o canal de linha direta

A primeira coisa que você deve fazer é adicionar o canal de linha direta ao bot.

1. No navegador, navegue até o [portal do Azure](https://portal.azure.com/).
1. No painel esquerdo, clique no item **canais** .
1. No painel direito, em *Adicionar um canal em destaque*, clique no ícone de **linha direta** (marcado em vermelho na imagem abaixo).

    ![Adicionar canal de linha direta](media/bot-service-channel-connect-directline/add-directline-channel.png "Adicionar canal de linha direta")

1. A página **Configurar linha direta** é exibida. Clique no botão **concluído** na parte inferior da página. Isso adiciona o canal de linha direto ao bot, conforme mostrado na imagem abaixo.

    ![Canal de linha direta adicionado](media/bot-service-channel-connect-directline/added-directline-channel.png "Canal de linha direta adicionado")

## <a name="add-new-site"></a>Adicionar novo site

1. Em *conectar-se a canais*, clique no link **Editar** pela linha direta.
1. Em *Configurar linha direta*, clique em **Adicionar novo site** e insira um nome para o seu site. Isso representa o aplicativo cliente que você deseja conectar ao bot.

    ![Adicionar linha direta de site](media/bot-service-channel-connect-directline/directline-addsite.png)

1. Clique em **Concluído**.

## <a name="manage-secret-keys"></a>Gerenciar chaves secretas

Quando você adiciona o canal direto, a estrutura de bot gera chaves secretas. O aplicativo cliente usa essas chaves para autenticar as solicitações da API de linha direta que ele emite para se comunicar com um bot. Para obter mais informações, consulte [Autenticação](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md).

1. Em *Configurar linha direta*, para exibir uma chave em texto sem formatação, clique em **Mostrar** para a chave correspondente.

    ![Mostrar chave de linha direta](media/bot-service-channel-connect-directline/directline-showkey.png "Mostrar chave de linha direta")

1. Copie e armazene a chave com segurança. Use a chave para [autenticar](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md) as solicitações da API de linha direta que o aplicativo cliente emite para se comunicar com um bot.

    ![Copie a chave de linha direta](media/bot-service-channel-connect-directline/directline-copykey.png "Copie a chave de linha direta")

    > [!NOTE]
    > Os segredos não devem ser expostos ou inseridos em aplicativos cliente. Consulte a próxima etapa.

1. A prática recomendada é usar a API de linha direta para [trocar a chave por um token](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#generate-token). O aplicativo cliente usará o token para autenticar suas solicitações dentro do escopo de uma única conversa.

## <a name="configure-settings"></a>Definir configurações

1. Selecione a versão do protocolo de linha direta que seu aplicativo cliente usará para se comunicar com um bot.

    > [!TIP]
    > Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use o Direct Line API 3.0.

1. Quando terminar, clique em **Concluído** para salvar a configuração do site. Você pode repetir esse processo, começando com [Adicionar novo site](#add-new-site), para cada aplicativo cliente que você deseja conectar ao seu bot.

### <a name="configure-enhanced-authentication"></a>Configurar a autenticação avançada

Quando você habilita a **Autenticação avançada**, é solicitado que você selecione uma lista de **URLs de origem confiáveis**, também conhecidas como origens confiáveis ou domínios confiáveis, para a geração do token de autenticação. Se você habilitar a autenticação avançada, deverá especificar pelo menos uma origem confiável.

![Adicionar origens confiáveis](media/bot-service-channel-connect-directline/add-trusted-origin-urls.png "Copie a chave de linha direta")

Um domínio confiável é um domínio no qual o sistema confia para autenticar usuários. Em nosso caso, é um domínio em que a linha direta pode confiar para a geração de um token.

- Se você configurar origens confiáveis como parte da página da interface do usuário de configuração, elas serão **sempre** usadas como o único conjunto para a geração de um token. Enviar nenhuma ou origens confiáveis adicionais ao gerar um token ou iniciar uma conversa, elas serão ignoradas (ou seja, elas **não serão acrescentadas** à lista ou validadas em cruz).

- Se você não habilitou a autenticação avançada, qualquer URL de origem enviada como parte das chamadas à API será usada.

A autenticação avançada permite que você reduza os riscos de segurança ao se conectar a um bot (usando o controle de chat da Web, por exemplo). Para obter mais informações, consulte [Autenticação avançada de linha direta](v4sdk/bot-builder-security-enhanced.md).

## <a name="example"></a>Exemplo

Você pode baixar um exemplo do .NET deste local: [exemplo de bot de linha direta](https://github.com/microsoft/BotFramework-DirectLine-DotNet/tree/master/samples/core-DirectLine).

O exemplo contém dois projetos:

- [DirectLineBot](https://github.com/microsoft/BotFramework-DirectLine-DotNet/tree/master/samples/core-DirectLine/DirectLineBot). Ele cria um bot para se conectar por meio de um canal de linha direta.
- [DirectLineClient](https://github.com/microsoft/BotFramework-DirectLine-DotNet/tree/master/samples/core-DirectLine/DirectLineClient). Este é um aplicativo de console que se comunica com o bot anterior por meio do canal de linha direta.

### <a name="direct-line-api"></a>Direct Line API

- As credenciais para a API de linha direta devem ser obtidas no registro de canais de bot ou no bot do aplicativo Web no portal do Azure e só permitirão que o chamador se conecte ao bot para o qual eles foram gerados. No projeto bot, atualize o `appsettings.json` arquivo com esses valores.

    ```csharp
    {
    "MicrosoftAppId": "",
    "MicrosoftAppPassword": ""
    }
    ```

- No portal do Azure, habilite a linha direta na lista de canais e, em seguida, configure o segredo de linha direta. Verifique se a caixa de seleção da versão 3,0 está marcada. No projeto de cliente do console, atualize o `App.config` arquivo com a chave secreta de linha direta e o identificador de bot (ID do bot).

    ```xml
    <appSettings>
        <add key="DirectLineSecret" value="YourBotDirectLineSecret" />
        <add key="BotId" value="YourBotHandle" />
    </appSettings>
    ```

As mensagens do usuário são enviadas para o bot usando o método de cliente de linha direta `Conversations.PostActivityAsync` usando o `ConversationId` gerado anteriormente.

```csharp
while (true)
{
    string input = Console.ReadLine().Trim();

    if (input.ToLower() == "exit")
    {
        break;
    }
    else
    {
        if (input.Length > 0)
        {
            Activity userMessage = new Activity
            {
                From = new ChannelAccount(fromUser),
                Text = input,
                Type = ActivityTypes.Message
            };

            await client.Conversations.PostActivityAsync(conversation.ConversationId, userMessage);
        }
    }
}
```