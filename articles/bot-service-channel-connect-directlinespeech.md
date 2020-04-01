---
title: Conectar um bot ao Direct Line Speech
titleSuffix: Bot Service
description: Visão geral e as etapas necessárias para conectar um bot do Bot Framework ao canal do Direct Line Speech para interação com entrada e saída de voz, com confiabilidade alta e latência baixa.
services: bot-service
author: trrwilson
manager: nitinme
ms.service: bot-service
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: travisw
ms.openlocfilehash: 3202c8271ffbdb09be10087861449c36f61cfac5
ms.sourcegitcommit: 64b25f796f89e8bb6fa53d3c824b73b8ce4d6ed8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80250125"
---
# <a name="connect-a-bot-to-direct-line-speech"></a>Conectar um bot ao Direct Line Speech

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Você pode configurar seu bot para permitir que aplicativos cliente se comuniquem com ele por meio do canal do Direct Line Speech.

Depois de criar seu bot, integrá-lo ao Direct Line Speech habilitará uma conexão de baixa latência e alta confiabilidade com aplicativos cliente, usando a [SDK de fala](https://aka.ms/speech-service-docs). Essas conexões são otimizadas para experiências de conversação com entrada e saída de voz. Para obter mais informações sobre o Direct Line Speech e como criar aplicativos cliente, visite a página [personalizar assistente virtual que tem como prioridade o uso da voz](https://aka.ms/cognitive-services-voice-assistants).

## <a name="add-the-direct-line-speech-channel"></a>Adicionar o canal do Direct Line Speech

1. Para adicionar o Canal do Direct Line Speech, primeiro abra o bot no [portal do Azure](https://portal.azure.com). Em seus recursos, selecione seu recurso de **Registro do Canal do Bot**. Clique em **Canais** na folha de configuração.

    ![realce do local para selecionar canais aos quais se conectar](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-selectchannel.png "selecionar canais")

1. Na página de seleção de canal, localize e clique em `Direct Line Speech` para escolher o canal.

    ![selecionar canal do Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-connectspeechchannel.png "conectar o Direct Line Speech")

1. Configure o Direct Line Speech, conforme mostrado na imagem abaixo.

    ![selecionar canal do Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-cognitivesericesaccount-selection.png "selecionar recurso dos Serviços Cognitivos")

    O Canal do Direct Line Speech requer um recurso de Serviços Cognitivos, especificamente um recurso de serviço cognitivo de **fala**. Você pode usar um grupo de recursos existente ou criar um. Para criar um recurso de fala, siga estas etapas:

    - Acesse [criar recursos no portal do Azure](https://ms.portal.azure.com/#create/hub).
    - Pesquise *Fala* e selecione-a na lista suspensa. O seguinte resultado é exibido:

        ![criar recurso cognitivo de fala](media/voice-first-virtual-assistants/create-speech-cognitive-resource.PNG "Criar recurso cognitivo de fala")

    - Siga as etapas do assistente.

    Para obter mais informações, confira [Criar um recurso dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

1. Depois de ler os termos de uso, clique em `Save` para confirmar a seleção do canal.

    ![salvar a habilitação do canal do Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-savechannel.png "Salvar a configuração do canal")

## <a name="enable-the-bot-framework-protocol-streaming-extensions"></a>Habilitar as extensões de streaming de protocolo do Bot Framework

Com o canal do Direct Line Speech conectado ao seu bot, agora é preciso habilitar o suporte a extensões de streaming de protocolo do Bot Framework para obter a interação ideal e de baixa latência.

1. Caso ainda não tenha feito, abra a folha do seu bot no [Portal do Azure](https://portal.azure.com).

1. Clique em **Configurações** na categoria **Gerenciamento de Bot** (à direita, abaixo de **Canais**). Marque a caixa de seleção **Habilitar ponto de extremidade de streaming**.

    ![habilitar o protocolo de streaming](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablestreamingsupport.png "habilitar o suporte à extensão de streaming")

1. Na parte superior da página, clique em **Salvar**.

1. Na mesma folha, na categoria **Configurações do Serviço de Aplicativo**, clique em **Configuração**.

    ![navegar até as configurações do serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-configureappservice.png "configurar o serviço de aplicativo")

1. Clique em `General settings` e selecione a opção para habilitar o suporte para `Web socket`.

    ![habilitar websockets no serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablewebsockets.png "habilitar websockets")

1. Clique em `Save` na parte superior da página de configuração.

1. As extensões de streaming de protocolo do Bot Framework estão habilitadas no seu bot. Agora, você está pronto para atualizar o código do bot e [integrar o suporte para extensões de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) a um projeto de bot existente.

## <a name="adding-protocol-support-to-your-bot"></a>Adicionar suporte para protocolo ao seu bot

Com o canal do Direct Line Speech conectado e suporte habilitado para extensões de streaming de protocolo do Bot Framework, só resta adicionar código ao seu bot para dar suporte para comunicação otimizada. Siga as instruções contidas em [Adicionar suporte para extensões de streaming ao seu bot](https://aka.ms/botframework/addstreamingprotocolsupport) para garantir compatibilidade total com o Direct Line Speech.


