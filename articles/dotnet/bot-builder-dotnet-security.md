---
title: Proteger o bot – Serviço de Bot
description: Saiba como proteger seu bot usando a Autenticação HTTPS e do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9e4a1417dfe60805b09e4f30880aeec6d50a3d3f
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80648406"
---
# <a name="secure-your-bot"></a>Proteger o bot

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O bot pode ser conectado a muitos canais de comunicação diferentes (SMS, email e outros) por meio do serviço Bot Framework Connector. Este artigo descreve como proteger seu bot usando a autenticação HTTPS e do Bot Framework.

## <a name="use-https-and-bot-framework-authentication"></a>Usar a autenticação HTTPS e do Bot Framework

Para garantir que o ponto de extremidade do bot só possa ser acessado pelo Bot Framework [Connector](bot-builder-dotnet-concepts.md#connector), configure o ponto de extremidade do bot para que ele use somente HTTPS e habilite a autenticação do Bot Framework [registrando](~/bot-service-quickstart-registration.md) o bot para adquirir sua appID e a senha. 

## <a name="configure-authentication-for-your-bot"></a>Configurar a autenticação para o bot

Especifique a appID e a senha do bot no arquivo web.config do bot. 

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** do seu bot, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

```xml
<appSettings>
    <add key="MicrosoftAppId" value="_appIdValue_" />
    <add key="MicrosoftAppPassword" value="_passwordValue_" />
</appSettings>
```

Em seguida, use o atributo `[BotAuthentication]` para especificar as credenciais de autenticação ao usar o SDK do Bot Framework para .NET para criar o bot. 

Para usar as credenciais de autenticação armazenadas no arquivo web.config, especifique a `[BotAuthentication]` sem parâmetros.

[!code-csharp[Use botAuthentication attribute](../includes/code/dotnet-security.cs#attribute1)]

Para usar outros valores para as credenciais de autenticação, especifique o atributo `[BotAuthentication]` e passe esses valores.

[!code-csharp[Use botAuthentication attribute with parameters](../includes/code/dotnet-security.cs#attribute2)]

## <a name="additional-resources"></a>Recursos adicionais

- [SDK do Bot Framework para .NET](bot-builder-dotnet-overview.md)
- [Principais conceitos do SDK do Construtor de Bot para .NET](bot-builder-dotnet-concepts.md)
- [Registrar um bot no Bot Framework](~/bot-service-quickstart-registration.md)
