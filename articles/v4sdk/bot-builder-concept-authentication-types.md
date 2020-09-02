---
title: Tipos de autenticação do bot Framework no serviço de bot do Azure-serviço bot
description: Saiba mais sobre os tipos de autenticação de bot no serviço de bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/14/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 499c24fe852a5c39e74407af74a3d644295b90fd
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364401"
---
# <a name="authentication-types"></a>Tipos de autenticação

A estrutura de bot fornece vários tipos de autenticação, conforme descrito abaixo.

## <a name="bot-authentication"></a>Autenticação do bot

Um bot é identificado pelo `MicrosoftAppID` e `MicrosoftAppPassword` que são mantidos dentro dos arquivos de configurações do bot: `appsettings.json` (.net), `.env` (JavaScript), `web.config` (Python) ou o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). Para obter mais informações, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

Quando você registra um bot no Azure, por exemplo, por meio do registro de canais de bot, um aplicativo de registro do Active Directory é criado. Esse aplicativo tem sua própria ID de aplicativo ( `MicrosoftAppID` ) e o segredo `MicrosoftAppPassword` do cliente () necessários para configurar o bot para implantação. A ID do aplicativo também é necessária para proteger o serviço para a comunicação de serviço entre o bot e os [canais do bot Framework](~/bot-service-manage-channels.md).

## <a name="client-authentication"></a>Autenticação de cliente

Um cliente pode autenticar solicitações para direcionar a API de linha 3,0 usando um **segredo** obtido na página de configuração do canal de linha direta no portal do Azure ou usando um **token** que você obtém no tempo de execução. O segredo ou o token devem ser especificados no cabeçalho de autorização de cada solicitação. Para obter mais informações, consulte [segredos e tokens](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#secrets-and-tokens).

## <a name="user-authentication"></a>Autenticação de usuário

Às vezes, um bot deve acessar recursos online protegidos em nome do usuário. Para fazer isso, o bot deve ser autorizado. Isso ocorre porque, para executar determinadas operações, como verificar o email, verificar o status do vôo ou fazer um pedido, o bot precisará chamar um serviço externo, como Microsoft Graph, GitHub ou o serviço REST de uma empresa. O OAuth é usado para autenticar o usuário e autorizar o bot.

> [!NOTE]
> Duas etapas de macro são envolvidas para um bot acessar os recursos de um usuário.
>
> 1. **Autenticação**. O processo de verificar a identidade do usuário.
> 1. **Autorização**. O processo de verificar se o bot pode acessar os recursos do usuário.
>
> Se a primeira etapa for bem-sucedida, um token baseado nas credenciais do usuário será emitido. Na segunda etapa, o bot usa o token para acessar os recursos do usuário.

Para obter mais informações, consulte [autenticação do usuário](bot-builder-concept-authentication.md).