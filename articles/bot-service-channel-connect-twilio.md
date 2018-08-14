---
title: Conectar um bot ao Twilio | Microsoft Docs
description: Saiba como configurar a conexão de um bot com o Twilio.
keywords: Twilio, canais de bot, SMS, Aplicativo, telefone, configurar o Twilio, comunicação em nuvem, texto
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 7e09126d50cfbebfc0aad0ee7fcb71b4e7551a7d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296670"
---
# <a name="connect-a-bot-to-twilio"></a>Conectar um bot ao Twilio

Configure seu bot para se comunicar com as pessoas que usam a plataforma de comunicação em nuvem Twilio.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Faça logon ou crie uma conta do Twilio para enviar e receber mensagens SMS

Caso não tenha uma conta do Twilio, <a href="https://www.twilio.com/try-twilio" target="_blank">crie uma conta</a>

## <a name="create-a-twiml-application"></a>Criar um aplicativo TwiML

<a href="https://www.twilio.com/user/account/messaging/dev-tools/twiml-apps/add" target="_blank">Criar um aplicativo TwiML</a>

![Criar aplicativo](~/media/channels/twi-StepTwiml.png)

 Em Mensagens, a URL de Solicitação deve ser **https://sms.botframework.com/api/sms**.

## <a name="select-or-add-a-phone-number"></a>Selecionar ou adicionar um número de telefone

<a href="https://www.twilio.com/user/account/phone-numbers/incoming" target="_blank">Selecionar ou adicionar um número de telefone</a>. Clique no número para adicioná-lo ao aplicativo TwiML criado.

![Definir número de telefone](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-messaging"></a>Especificar o aplicativo a ser usado para Mensagens
Em **Mensagens**, defina o **Aplicativo TwiML** com o nome do Aplicativo TwiML recém-criado.
Copie o valor de **Número de Telefone** para uso posterior.

![Especificar o aplicativo](~/media/channels/twi-StepPhone2.png)

## <a name="gather-credentials"></a>Obter credenciais

<a href="https://www.twilio.com/user/account/settings" target="_blank">Colete as credenciais</a> e, em seguida, clique no ícone de "olho" para ver o Token de Autenticação.

![Coletar credenciais do aplicativo](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Enviar credenciais

Insira o número de telefone, o accountSID e o Token de Autenticação que você copiou anteriormente e clique em **Enviar Credenciais do Twilio**.

## <a name="enable-the-bot"></a>Habilitar o bot
Marque a opção **Habilitar este bot no SMS**. Em seguida, clique em **Já concluí a configuração do SMS**.

Depois que você concluir essas etapas, o bot será configurado com êxito para se comunicar com os usuários usando o Twilio.

