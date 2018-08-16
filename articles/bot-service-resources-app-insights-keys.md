---
title: Chaves do Application Insights | Microsoft Docs
description: Saiba como obter as chaves do Application Insights para adicionar telemetria a um bot.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3d098afd70bef9cf1a3b3fab87455e96ffd9c292
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296597"
---
# <a name="application-insights-keys"></a>Chaves do Application Insights

O Azure **Application Insights** exibe dados sobre o seu aplicativo em um recurso do Microsoft Azure. Para adicionar telemetria ao seu bot é preciso ter uma assinatura do Azure e criar um recurso do Application Insights para o seu bot. Nesse recurso, você pode obter as três chaves para configurar o seu bot:

1. Chave de instrumentação
2. ID do aplicativo
3. Chave de API

Este tópico mostrará como criar essas chaves do Application Insights.

> [!NOTE]
> Durante a criação ou o processo de registro do bot, você tem a opção de *Ativar* ou *Desativar* o **Application Insights**. Se ele já estiver *Ativado*, seu bot já terá todas as chaves necessárias do Application Insights. No entanto, se ele estiver *Desativado*, siga as instruções neste tópico para ajudar a criar manualmente estas chaves.

## <a name="instrumentation-key"></a>Chave de instrumentação

Para obter a chave de instrumentação, faça o seguinte:
1. No [portal.azure.com](http://portal.azure.com), na seção Monitor, crie um novo recurso do **Application Insights** (ou use um já existente).
![Captura de tela do portal de listagem do Application Insights](~/media/portal-app-insights-add-new.png)

2. Na lista de recursos do Application Insights, clique no recurso do Application Insight que você acabou de criar.

3. Clique em **Visão Geral**.

4. Expanda o bloco **Conceitos básicos** e encontre a **Chave de Instrumentação**. 
![Captura de tela do portal da chave de instrumentação](~/media/portal-app-insights-instrumentation-key.png)

5. Copie a **Chave de Instrumentação** e cole-a no campo **Chave de Instrumentação do Application Insights** das configurações do bot.

## <a name="application-id"></a>ID do aplicativo

Para obter o ID do Aplicativo, faça o seguinte:
1. No recurso do Application Insights, clique em **Acesso à API**.

2. Copie o **ID do Aplicativo** e cole-o no campo **ID do Aplicativo do Application Insights** das configurações do bot. 
![Captura de tela do portal do ID do Aplicativo](~/media/portal-app-insights-appid.png)

## <a name="api-key"></a>Chave de API

Para obter a chave da API, faça o seguinte:
1. No recurso do Application Insights, clique em **Acesso à API**.

2. Clique em **Criar Chave de API**.

3. Insira uma descrição curta, marque a opção de **Telemetria de leitura** e clique no botão **Gerar chave**.
![Captura de tela do portal do ID do Aplicativo e da Chave de API](~/media/portal-app-insights-appid-apikey.png)

   > [!WARNING]
   > Copie esta **chave de API** e salve-a porque ela não será exibida para você novamente. Se você perder esta chave, será preciso criar uma nova.

4. Copie a chave de API para o campo **Chave de API do Application Insights** das configurações do seu bot.

## <a name="additional-resources"></a>Recursos adicionais
Para saber mais sobre como conectar esses campos às configurações do bot em [Habilitar a análise](~/bot-service-manage-analytics.md#enable-analytics).