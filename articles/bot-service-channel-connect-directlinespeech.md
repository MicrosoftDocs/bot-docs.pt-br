---
title: Conectar um bot ao Direct Line Speech
titleSuffix: Bot Service
description: Saiba como conectar um bot ao canal de fala de linha direta para interação de voz do usuário com alta confiabilidade e baixa latência.
services: bot-service
author: mmiele
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: v-mimiel
ms.openlocfilehash: b0aec96cf45e2c2cb7786532026408b56c266f7b
ms.sourcegitcommit: e37cf15f4907910560f34445a0fbdd7ae75b4787
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440079"
---
# <a name="connect-a-bot-to-direct-line-speech"></a>Conectar um bot ao Direct Line Speech

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como conectar um bot ao canal de **fala de linha direta**. Use este canal para permitir que os usuários interajam com um bot por meio de voz.

Depois de criar seu bot, integrá-lo ao Direct Line Speech habilitará uma conexão de baixa latência e alta confiabilidade com aplicativos cliente, usando a [SDK de fala](https://aka.ms/speech-service-docs). Essas conexões são otimizadas para experiências de conversação com entrada e saída de voz. Para obter mais informações sobre o Direct Line Speech e como criar aplicativos cliente, visite a página [personalizar assistente virtual que tem como prioridade o uso da voz](https://aka.ms/cognitive-services-voice-assistants).

## <a name="prerequisites"></a>Pré-requisitos

 O canal de fala de linha direta requer um recurso de **Serviços cognitivas** , especificamente um recurso de serviço de percepção de **fala** . Você pode usar um grupo de recursos existente ou criar um. Para criar um recurso de fala, siga estas etapas:

1. No navegador, navegue até o [portal do Azure criar recursos](https://ms.portal.azure.com/#create/hub).
1. No painel esquerdo. clique em **criar um recurso**.
1. No painel direito, na caixa de pesquisa, digite **fala**.
1. Na lista suspensa, selecione **fala**. O seguinte resultado é exibido:

    ![criar recurso cognitivo de fala](media/voice-first-virtual-assistants/create-speech-cognitive-resource.PNG "Criar serviço de cognitiva de fala")

1. Clique em **criar** e siga as etapas do assistente.

    Para obter mais informações, confira [Criar um recurso dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="add-the-direct-line-speech-channel"></a>Adicionar o canal do Direct Line Speech

1. No navegador, navegue até o [portal do Azure](https://portal.azure.com).
1. Em seus recursos, selecione o **registro do canal bot** ou recurso de **bot do aplicativo Web** , dependendo de como você implantou o bot.
1. No painel esquerdo, selecione  **canais**.

    ![selecionar canais](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-selectchannel.png "selecionar canais")

1. No painel direito, clique no `Direct Line Speech` ícone.

    ![selecionar canal do Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-connectspeechchannel.png "conectar o Direct Line Speech")

1. Configure o Direct Line Speech, conforme mostrado na imagem abaixo. Especificamente, adicione a conta de serviço cognitiva (**serviço de fala**) mencionada na seção [pré-requisitos](#prerequisites) .

    ![configurar canal de fala de linha direta](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-cognitivesericesaccount-selection.png "selecionar recurso dos Serviços Cognitivos")

1. Depois de ler os termos de uso, clique em `Save` para confirmar a seleção do canal. Isso adicionará o canal ao bot.

    ![salvando o canal de fala de linha direta habilitado](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-added.png "canal de fala de linha direta adicionado")

## <a name="enable-the-bot-framework-protocol-streaming-extensions"></a>Habilitar as extensões de streaming de protocolo do Bot Framework

Com o canal do Direct Line Speech conectado ao seu bot, agora é preciso habilitar o suporte a extensões de streaming de protocolo do Bot Framework para obter a interação ideal e de baixa latência.

1. No painel esquerdo, selecione **configurações**.
1. No painel direito, marque a caixa pelo ponto de **extremidade habilitar streaming**.

    ![habilitar o protocolo de streaming](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablestreamingsupport.png "habilitar o suporte à extensão de streaming")

1. Na parte superior da página, clique em **Salvar**.

1. Navegue até o serviço de aplicativo bot.
1. No painel esquerdo, na categoria **configurações do serviço de aplicativo** , selecione **configuração**.

    ![navegar até as configurações do serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-configureappservice.png "configurar o serviço de aplicativo")

1. No painel direito, selecione a `General settings` guia.
1. Definido `Web sockets` como **ativado**.

    ![habilitar websockets no serviço de aplicativo](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablewebsockets.png "habilitar websockets")

1. Clique em `Save` na parte superior da página de configuração.

1. As extensões de streaming de protocolo do Bot Framework estão habilitadas no seu bot. Agora, você está pronto para atualizar o código do bot e [integrar o suporte para extensões de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) a um projeto de bot existente.

## <a name="example"></a>Exemplo

Se você seguiu todas as etapas descritas, você pode conversar com o bot usando o aplicativo cliente baixável neste local: [cliente do assistente de voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md#windows-voice-assistant-client).

A imagem a seguir mostra a interface do aplicativo cliente ao se comunicar com um bot de eco simples. Consulte também [Habilitar voz para o bot usando o SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk).

![cliente de assistente de voz](media/voice-first-virtual-assistants/voice-assistant-client.png "cliente de assistente de voz")

## <a name="adding-protocol-support-to-your-bot"></a>Adicionar suporte para protocolo ao seu bot

> [!NOTE]
> A etapa a seguir só é necessária para os bots criados antes do lançamento dos SDKs 4,8.

Com o canal do Direct Line Speech conectado e suporte habilitado para extensões de streaming de protocolo do Bot Framework, só resta adicionar código ao seu bot para dar suporte para comunicação otimizada. Siga as instruções contidas em [Adicionar suporte para extensões de streaming ao seu bot](https://aka.ms/botframework/addstreamingprotocolsupport) para garantir compatibilidade total com o Direct Line Speech.