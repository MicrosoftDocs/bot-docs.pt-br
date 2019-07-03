---
title: Conectar um bot ao Twilio | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Twilio.
keywords: Twilio, canais de bot, SMS, Aplicativo, telefone, configurar o Twilio, comunicação em nuvem, texto
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/9/2018
ms.openlocfilehash: 4de93d821c6b652021a9f695536350610776f5b4
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67405898"
---
# <a name="connect-a-bot-to-twilio"></a>Conectar um bot ao Twilio

Configure seu bot para se comunicar com as pessoas que usam a plataforma de comunicação em nuvem Twilio.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Faça logon ou crie uma conta do Twilio para enviar e receber mensagens SMS

Caso não tenha uma conta do Twilio, <a href="https://www.twilio.com/try-twilio" target="_blank">crie uma conta</a>.

## <a name="create-a-twiml-application"></a>Criar um aplicativo TwiML

<a href="https://support.twilio.com/hc/articles/223180928-How-Do-I-Create-a-TwiML-App-" target="_blank">Crie um aplicativo TwiML</a> seguindo as instruções.

![Criar aplicativo](~/media/channels/twi-StepTwiml.png)

Em **Propriedades**, insira um **NOME AMIGÁVEL**. Neste tutorial, usamos "Meu aplicativo TwiML" como exemplo. A **URL DE SOLICITAÇÃO** em Voz pode ser deixada em branco. Em **Mensagens**, a **URL de Solicitação** deve ser `https://sms.botframework.com/api/sms`.

## <a name="select-or-add-a-phone-number"></a>Selecionar ou adicionar um número de telefone

Siga <a href = "https://support.twilio.com/hc/articles/223180048-Adding-a-Verified-Phone-Number-or-Caller-ID-with-Twilio" target="_blank">estas</a> instruções para adicionar uma ID de chamador verificada por meio do site do console. Depois de terminar, você verá seu número verificado em **Números Ativos** sob **Gerenciar Números**.

![Definir número de telefone](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-voice-and-messaging"></a>Especificar o aplicativo a ser usado para Voz e Mensagens

Clique no número e vá até **Configurar**. Em Voz e Mensagens, defina **CONFIGURAR COM** como Aplicativo TwiML e defina **Aplicativo TWIML** como Meu aplicativo TwiML. Depois de concluir, clique em **Salvar**.

![Especificar o aplicativo](~/media/channels/twi-StepPhone2.png)

Volte para **Gerenciar Números**, você verá que a configuração de Voz e Mensagens foram alteradas para Aplicativo TwiML.

![Número especificado](~/media/channels/twi-StepPhone3.png)


## <a name="gather-credentials"></a>Obter credenciais

Volte para o [página inicial do console](https://www.twilio.com/console/), você verá o SID da sua Conta e o Token de Autenticação no painel do projeto, conforme mostrado abaixo.

![Coletar credenciais do aplicativo](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Enviar credenciais

Em uma janela separada, volte para o site do Bot Framework em https://dev.botframework.com/. 

- Selecione **Meus bots** e escolha o Bot que você deseja conectar ao Twilio. Você será direcionado para o portal do Azure.
- Selecione **Canais** sob **Gerenciamento de Bot**. Clique no ícone de Twilio (SMS).
- Insira o Número de Telefone, o SID da Conta e o Token de Autenticação que você registrou anteriormente. Depois de concluir, clique em **Salvar**.

![Enviar credenciais](~/media/channels/twi-StepSubmit.png)

Depois que você concluir essas etapas, o bot será configurado com êxito para se comunicar com os usuários usando o Twilio.

## <a name="also-available-as-an-adapter"></a>Também disponível como um adaptador

Esse canal também [está disponível como um adaptador](https://botkit.ai/docs/v4/platforms/twilio-sms.html). Para ajudá-lo a escolher entre um adaptador e um canal, confira [Adaptadores disponíveis no momento](bot-service-channel-additional-channels.md#currently-available-adapters).