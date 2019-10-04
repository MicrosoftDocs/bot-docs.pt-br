---
title: Conectar um bot ao Direct Line Speech (versão prévia)
titleSuffix: Bot Service
description: Visão geral e as etapas necessárias para conectar um bot do Bot Framework ao canal do Direct Line Speech para interação com entrada e saída de voz, com confiabilidade alta e latência baixa.
services: bot-service
author: trrwilson
manager: nitinme
ms.service: bot-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: ec0a4afb33c560a6b53ff6a02da9b1cfed07f16b
ms.sourcegitcommit: d493caf74b87b790c99bcdaddb30682251e3fdd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71278996"
---
# <a name="connect-a-bot-to-direct-line-speech-channel"></a>Conectar um bot ao canal do Direct Line Speech

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

> [!WARNING]
> O **canal do Direct Line Speech** está em **versão prévia** pública.  

Você pode configurar seu bot para permitir que aplicativos cliente se comuniquem com ele por meio do canal do Direct Line Speech.

Depois de criar seu bot, integrá-lo ao Direct Line Speech habilitará uma conexão de baixa latência e alta confiabilidade com aplicativos cliente, usando a [SDK de fala](https://aka.ms/speech-services-docs). Essas conexões são otimizadas para experiências de conversação com entrada e saída de voz. Para obter mais informações sobre o Direct Line Speech e como criar aplicativos cliente, visite a página [personalizar assistente virtual que tem como prioridade o uso da voz](https://aka.ms/voice-first-va).  

## <a name="sign-up-for-direct-line-speech-preview"></a>Inscrever-se na versão prévia do Direct Line Speech

O Direct Line Speech está na versão prévia e requer uma inscrição rápida no [Portal do Azure](https://portal.azure.com). Veja detalhes abaixo. Após a aprovação, você receberá acesso ao canal.

## <a name="add-the-direct-line-speech-channel"></a>Adicionar o canal do Direct Line Speech

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com). Em seus recursos, selecione o recurso de **Registro de Canal de Bot**. Clique em **Canais** na seção *Gerenciamento de bot* da folha de configuração.

    ![realce da localização ao selecionar canais para se conectar](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-selectchannel.png "seleção de canais")

1. Na página de seleção de canal, localize e clique em `Direct Line Speech` para escolher o canal.

    ![seleção do canal do direct line speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-connectspeechchannel.png "conexão com o Direct Line Speech")

1. Se ainda não tiver acesso, você verá uma página para solicitá-lo. Preencha as informações solicitadas e clique em “Solicitar”. Uma página de confirmação será exibida. Enquanto a aprovação da solicitação estiver pendente, você não passará dessa página.   

1. Após a aprovação do acesso, uma página de configuração do Direct Line Speech será mostrada. Depois de ler os termos de uso, clique em `Save` para confirmar a seleção do canal.

    ![salvando a habilitação do canal do Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-savechannel.png "Salvar a configuração do canal")

## <a name="enable-the-bot-framework-protocol-streaming-extensions"></a>Habilitar as extensões de streaming de protocolo do Bot Framework

Com o canal do Direct Line Speech conectado ao seu bot, agora é preciso habilitar o suporte a extensões de streaming de protocolo do Bot Framework para obter a interação ideal e de baixa latência.

1. Na folha de configuração do recurso **Registro do Canal de Bot**, clique em **Configurações** na categoria **Gerenciamento de Bot** (logo abaixo de **Canais**). Marque a caixa de seleção **Habilitar ponto de extremidade de streaming**.

    ![habilitar o protocolo de streaming](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablestreamingsupport.png "habilitar suporte para extensão de streaming")

1. Na parte superior da página, clique em **Salvar**.

1. Em seus recursos, selecione o recurso **Serviço de Aplicativo**. Na folha exibida, na categoria **Configurações**, clique em **Configuração**.

    ![navegar até configurações do serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-configureappservice.png "configurar o serviço de aplicativo")

1. Clique na guia `General settings` e selecione a opção para habilitar o suporte para `Web socket`.

    ![habilitar websockets no serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablewebsockets.png "habilitar websockets")

1. Clique em `Save` na parte superior da página de configuração.

1. As extensões de streaming de protocolo do Bot Framework estão habilitadas no seu bot. Agora, você está pronto para atualizar o código do bot e [integrar o suporte para extensões de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) a um projeto de bot existente.

## <a name="manage-secret-keys"></a>Gerenciar chaves secretas

Aplicativos cliente precisarão de um segredo de canal para se conectar ao seu bot por meio do canal do Direct Line Speech. Depois de salvar a seleção de canal, você poderá recuperar essas chaves secretas seguindo as próximas etapas.

1. Em seus recursos, selecione o recurso de **Registro de Canal de Bot**. Clique em **Canais** na seção *Gerenciamento de bot* da folha de configuração.
1. Clique no link **Editar** para Direct Line Speech.

    ![obtendo chaves secretas para o Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-getspeechsecretkeys1.png "obtendo chaves secretas para o Direct Line Speech")

    A seguinte janela é exibida.

    ![obtendo chaves secretas para o Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-getspeechsecretkeys.png "obtendo chaves secretas para o Direct Line Speech")
1. Mostre e copie as chaves a serem usadas em seu aplicativo.

## <a name="adding-protocol-support-to-your-bot"></a>Adicionar suporte para protocolo ao seu bot

Com o canal do Direct Line Speech conectado e suporte habilitado para extensões de streaming de protocolo do Bot Framework, só resta adicionar código ao seu bot para dar suporte para comunicação otimizada. Siga as instruções contidas em [Adicionar suporte para extensões de streaming ao seu bot](https://aka.ms/botframework/addstreamingprotocolsupport) para garantir compatibilidade total com o Direct Line Speech.

## <a name="known-issues"></a>Problemas conhecidos

Lembre-se de que o serviço está na versão prévia e sujeito a alterações, o que pode afetar o desenvolvimento e o desempenho geral do seu bot. Veja aqui uma lista dos problemas conhecidos: 

1. Atualmente, o serviço está implantado na [região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) oeste dos EUA 2. Logo implantaremos em outras regiões; assim, todos os clientes terão o benefício das interações de fala de baixa latência em seus bots.

1. Pequenas alterações nos campos de controle, como [serviceUrl](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#service-url), estarão disponíveis

1. As atividades [conversationUpdate](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation-update-activity) e [endOfCoversation](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#end-of-conversation-activity), utilizadas para sinalizar o início e o fim das conversas, geralmente usadas para gerar mensagens de boas-vindas, serão atualizadas para que haja consistência com outros canais

1. O canal ainda não dá suporte para [SigninCard](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-add-rich-cards?view=azure-bot-service-4.0) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Use o Direct Line por Fala no seu bot](./directline-speech-bot.md)