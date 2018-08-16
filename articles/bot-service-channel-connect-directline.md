---
title: Conecte um bot à linha direta | Microsoft Docs
description: Aprenda como configurar a conexão de um bot para a linha direta.
keywords: linha direta, canais bot, cliente customizado, conectar aos canais, configurar
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: 2ec11c4cc0e12b6a130b2f703f30660c4bc9f072
ms.sourcegitcommit: f95702d27abbd242c902eeb218d55a72df56ce56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39297540"
---
# <a name="connect-a-bot-to-direct-line"></a>Conecte um bot à linha direta

Você pode ativar seu próprio aplicativo cliente para se comunicar com seu bot usando o canal Direct Line. 

## <a name="add-the-direct-line-channel"></a>Adicionar o canal de linha direta

Para adicionar o canal Linha Direta, abra o bot no [Portal do Azure](https://portal.azure.com/), clique na folha **Canais** e, em seguida, clique em **Linha Direta**.

![Adicionar canal de linha direta](~/media/bot-service-channel-connect-directline/directline-addchannel.png)

## <a name="add-new-site"></a>Adicionar novo site

Em seguida, adicione um novo site que represente o aplicativo cliente que você deseja conectar ao seu bot. Clique em **Adicionar novo site**, insira um nome para o seu site e clique em **Concluído**.

![Adicionar linha direta de site](~/media/bot-service-channel-connect-directline/directline-addsite.png)

## <a name="manage-secret-keys"></a>Gerenciar chaves secretas

Quando seu site é criado, o Bot Framework gera chaves secretas que seu aplicativo cliente pode usar para [autenticar](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md) as solicitações da API da Linha Direta que emite para se comunicar com seu bot. Para exibir uma chave em texto sem formatação, clique em **Mostrar** para a chave correspondente.

![Mostrar chave de linha direta](~/media/bot-service-channel-connect-directline/directline-showkey.png)

Copie e armazene com segurança a chave que é mostrada. Em seguida, use a chave para [autenticar](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md) as solicitações da API da Linha Direta que seu cliente emite para se comunicar com seu bot.
Como alternativa, use a API de linha direta para [trocar a chave para um token](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#generate-token) que seu cliente pode usar para autenticar suas solicitações subsequentes dentro do escopo de uma única conversa.

![Copie a chave de linha direta](~/media/bot-service-channel-connect-directline/directline-copykey.png)

## <a name="configure-settings"></a>Configurar definições

Por fim, defina as configurações do site.

- Selecione a versão do protocolo Direct Line que seu aplicativo cliente usará para se comunicar com seu bot.

> [!TIP]
> Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use o Direct Line API 3.0.

Quando terminar, clique em **Concluído** para salvar a configuração do site. Você pode repetir esse processo, começando com [Adicionar novo site](#add-new-site), para cada aplicativo cliente que você deseja conectar ao seu bot.
