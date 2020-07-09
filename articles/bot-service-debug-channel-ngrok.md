---
title: Depurar um canal usando o serviço ngrok-bot
description: Entenda como depurar um canal usando o ngrok
keywords: depuração, canal, ngrok
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservices: sdk
ms.date: 06/16/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 25d154f765b2e3fbd4c3d6871b3a5683651fe754
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124609"
---
# <a name="debug-a-bot-from-any-channel-using-ngrok"></a>Depurar um bot de qualquer canal usando ngrok

Quando o bot estiver em desenvolvimento, você poderá [depurar o bot localmente usando um IDE](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0) , como o Visual Studio ou o Visual Studio Code. Você também pode [depurar um bot usando o emulador do bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0) ao bater papo com o bot localmente e inspecionar as mensagens que o bot envia e recebe. Você pode até mesmo fazer pequenas atualizações do código do bot e habilitar o recurso para [depurar um bot com middleware de inspeção](https://docs.microsoft.com/azure/bot-service/bot-service-debug-inspection-middleware?view=azure-bot-service-4.0).

Quando o bot já estiver em produção, você poderá depurar o bot de qualquer [canal](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0) usando o **ngrok**. A conexão direta do bot a vários canais é um recurso importante disponível no bot Framework. Neste artigo, mostraremos como depurar localmente o bot de qualquer canal no qual o bot de produção está configurado, usando **ngrok**. Usamos um exemplo de [EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot) conectado ao [canal do Microsoft Teams](https://docs.microsoft.com/azure/bot-service/channel-connect-teams?view=azure-bot-service-4.0) em todo o artigo para obter instruções.

<!-- the Bot Framework Emulator uses an instance of the [Web Chat control](https://github.com/Microsoft/BotFramework-WebChat), which is only used in DirectLine, or embedded into web sites using a standard or custom configuration. Popular third party channels such as Slack, Facebook Messenger, Kik, etc. all implement their own chat channel user interfaces. In this article, we’ll discuss how you can locally debug your bot from any channel your production bot is configured for, using [ngrok](https://ngrok.com/docs). -->

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/).
* Instale o [ngrok](https://ngrok.com/).
* Um [bot básico](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot) conectado a qualquer [canal](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

## <a name="run-ngrok"></a>Executar o ngrok

O [**Ngrok**](https://ngrok.com/docs) é um aplicativo de plataforma cruzada que "permite expor um servidor Web em execução no computador local para a Internet". Essencialmente, o que faremos é usar **ngrok** para encaminhar mensagens de canais externos na Web diretamente para nosso computador local para permitir a depuração, ao contrário do ponto de extremidade de mensagens padrão configurado no portal do Azure.

1. Abra um terminal e navegue até a pasta onde seu executável **ngrok** é.

2. Execute **ngrok** com o comando a seguir para criar um novo túnel. 

    ```cmd
    ngrok http 3978 -host-header="localhost:3978"

    ```

    > [!NOTE]
    > Observe que a porta especificada é a porta em que o bot está sendo executado. Você pode usar qualquer porta localhost que desejar.

3. Quando **ngrok** for iniciado, copie e salve a URL de encaminhamento público para mais tarde. 
   
    > [!div class="mx-imgBorder"]
    > ![URL de encaminhamento do ngrok](./media/debug-ngrok/ngrok-forwarding-url.png)

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Enquanto o **ngrok** estiver em execução, faça logon em seu portal do Azure e exiba as configurações de bot para fazer alguma configuração.

1. Selecione o **registro dos canais de bot** conectado ao bot local.

2. Role para baixo até **configuração**. Copie e cole a URL de encaminhamento **ngrok** no campo **ponto de extremidade de mensagens** . Certifique-se de manter "/API/messages" no final da URL.
   
    > [!div class="mx-imgBorder"]
    > ![ponto de extremidade de mensagens](./media/debug-ngrok/messaging-endpoint.png)

3. Role para cima e selecione **salvar**.

## <a name="test"></a>Teste

Neste ponto, as mensagens de entrada do seu bot de canais externos agora serão enviadas para o bot local. O bot de exemplo que usaremos para demonstrar que isso já está configurado em tempo real para **o Microsoft Teams**. Leia [conectar um bot à Microsoft Teams](https://docs.microsoft.com/azure/bot-service/channel-connect-teams?view=azure-bot-service-4.0) sobre como conectar um bot local ao canal **do Microsoft Teams** .

> [!div class="mx-imgBorder"]
> ![canal de equipes](./media/debug-ngrok/teams-channel.png)

Localmente, você pode definir pontos de interrupção no Visual Studio. Expandindo a propriedade Text do objeto de atividade de entrada, você verá que a mensagem que enviou o bot das equipes está sendo interceptada localmente para depuração.

> [!div class="mx-imgBorder"]
> ![definir pontos de interrupção](./media/debug-ngrok/breakpoint.png)

A partir daqui, você pode depurar normalmente e executar o código passo a passo. Você pode usar isso para depurar o bot de qualquer canal.

> [!div class="mx-imgBorder"]
> ![continuar depuração](./media/debug-ngrok/debug-continue.png)

## <a name="additional-information"></a>Informações adicionais
* [Conectar um bot aos canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)
