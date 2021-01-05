---
title: Chaves do Application Insights – Serviço de Bot
description: Saiba como adicionar telemetria a um bot. Veja como criar as chaves de que você precisa para exibir dados que Aplicativo Azure insights coleta sobre seu aplicativo.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/19/2019
ms.openlocfilehash: a13f5649d7056ce7aab8dc4c2062fda67caef82e
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97758825"
---
# <a name="application-insights-keys"></a>Chaves do Application Insights

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O Azure **Application Insights** exibe dados sobre o seu aplicativo em um recurso do Microsoft Azure. Para adicionar telemetria ao seu bot é preciso ter uma assinatura do Azure e criar um recurso do Application Insights para o seu bot. Nesse recurso, você pode obter as três chaves para configurar o seu bot:

1. Chave de instrumentação
2. ID do aplicativo
3. Chave de API

Este tópico mostrará como criar essas chaves do Application Insights.

> [!NOTE]
> Durante a criação ou o processo de registro do bot, você tem a opção de *Ativar* ou *Desativar* o **Application Insights**. Se ele já estiver *Ativado*, seu bot já terá todas as chaves necessárias do Application Insights. No entanto, se ele estiver *Desativado*, siga as instruções neste tópico para ajudar a criar manualmente estas chaves.

## <a name="instrumentation-key"></a>Chave de instrumentação

Para obter a chave de instrumentação, faça o seguinte:
1. No [portal do Azure](https://portal.azure.com), na seção Monitor, crie um novo recurso do **Application Insights** (ou use um já existente).
![Captura de tela do portal de listagem do Application Insights](~/media/portal-app-insights-add-new.png)

2. Na lista de recursos do Application Insights, clique no recurso do Application Insight que você acabou de criar.

3. Clique em **Visão Geral**.

4. Expanda o bloco **Conceitos básicos** e encontre a **Chave de Instrumentação**.
![Captura de tela do portal da Visão geral](~/media/portal-app-insights-instrumentation-key-dropdown.png)
![Captura de tela do Portal da Chave de instrumentação](~/media/portal-app-insights-instrumentation-key.png)

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

3. Insira uma breve descrição, marque a opção **ler telemetria** e clique no botão **gerar chave** .
![Captura de tela do portal do ID do Aplicativo e da Chave de API](~/media/portal-app-insights-appid-apikey.png)

   > [!WARNING]
   > Copie esta **chave de API** e salve-a porque ela não será exibida para você novamente. Se você perder esta chave, será preciso criar uma nova.

4. Copie a chave de API para o campo **Chave de API do Application Insights** das configurações do seu bot.

## <a name="additional-resources"></a>Recursos adicionais
Para saber mais sobre como conectar esses campos às configurações do bot em [Habilitar a análise](~/bot-service-manage-analytics.md#enable-analytics).
