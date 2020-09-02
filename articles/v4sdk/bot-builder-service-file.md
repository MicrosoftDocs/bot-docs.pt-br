---
title: Criar um bot usando o SDK do Bot Framework para JavaScript – Arquivo – Serviço de Bot
description: Exiba um arquivo. bot de exemplo que um bot pode usar para gerenciar um recurso que ele usa, como Reconhecimento vocal (LUIS).
keywords: início rápido, sdk do bot framework, introdução
author: jonathanfingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2076e87136b77a44a4d6db1f2ac4de0cab4d77eb
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360019"
---
# <a name="sample-bot-file"></a>Arquivo .bot de exemplo

```
{
    "name": "BasicBot",
    "description": "Demo",
    "services": [
        {
            "type": "endpoint",
            "name": "development",
            "id": "http://localhost:3978/api/messages",
            "appId": "",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages"
        },
        {
            "type": "luis",
            "name": "basic-bot-LUIS",
            "appId": "<your app id>",
            "version": "0.1",
            "authoringKey": "<your authoring key>",
            "subscriptionKey": "<your subscription key>",
            "region": "westus",
            "id": "206"
        }
    ],
    "secretKey": "",
    "version": "2.0"
}
```
