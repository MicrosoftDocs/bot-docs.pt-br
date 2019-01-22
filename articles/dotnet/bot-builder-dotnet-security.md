---
title: Proteger o bot | Microsoft Docs
description: Saiba como proteger seu bot usando a Autenticação HTTPS e do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 455ee87f31e70470d8d118f010c77327347c032d
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224881"
---
# <a name="secure-your-bot"></a>Proteger o bot

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O bot pode ser conectado a muitos canais de comunicação diferentes (Skype, SMS, email e outros) por meio do serviço Bot Framework Connector. Este artigo descreve como proteger seu bot usando a autenticação HTTPS e do Bot Framework.

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
