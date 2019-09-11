---
title: Criar um bot usando o SDK do Bot Framework para JavaScript | Microsoft Docs
description: Crie rapidamente um bot usando o SDK do Bot Framework para JavaScript.
keywords: início rápido, sdk do bot framework, introdução
author: jonathanfingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e870e34e4f7143378813e5cbcc2d0c0b3d892609
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299168"
---
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
