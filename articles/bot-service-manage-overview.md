---
title: Gerenciar um bot | Microsoft Docs
description: Saiba como gerenciar um bot por meio do portal de serviço de bot.
keywords: portal do Azure, gerenciamento de bot, teste em webchat, MicrosoftAppID, MicrosoftAppPassword, configurações de aplicativo
author: v-ducvo
ms.author: rstand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/13/2017
ms.openlocfilehash: fef82d27c6dd4fb61c9a0cf864e76a88128847d7
ms.sourcegitcommit: 05ddade244874b7d6e2fc91745131b99cc58b0d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591157"
---
# <a name="manage-a-bot"></a>Gerenciar um bot

Este tópico explica como gerenciar um bot usando o portal do Azure.

## <a name="bot-settings-overview"></a>Visão geral das configurações de bot

![Visão geral das configurações de bot](~/media/azure-manage-a-bot/overview.png)

Na folha **Visão geral**, você pode encontrar informações de alto nível sobre o bot. Por exemplo, é possível ver a **ID da assinatura**, o **tipo de preço** e o **ponto de extremidade de mensagens** do bot.

## <a name="bot-management"></a>Gerenciamento de bot

 Você pode encontrar a maioria das opções de gerenciamento do bot na seção **GERENCIAMENTO DE BOT**. Segue abaixo uma lista de opções para ajudá-lo a gerenciar o bot.

![Gerenciamento de bot](~/media/azure-manage-a-bot/bot-management.png)

| Opção |  DESCRIÇÃO |
| ---- | ---- |
| **Compilar** | A guia Compilar fornece opções para fazer alterações no bot. Essa opção não está disponível para **Bot Somente Registro**. |
| **Testar no Webchat** | Use o controle de Webchat integrado para ajudá-lo a testar rapidamente o seu bot. |
| **Analytics** | Se a análise for ativada para o bot, será possível exibir os dados de análise coletados pelo Application Insights para o bot. |
| **Channels** | Configure os canais que o bot usa para comunicar-se com os usuários. |
| **Configurações** | Gerencie várias configurações de perfil de bot, como nome de exibição, análise e ponto de extremidade de mensagens. |
| **Preparação da fala** | Gerencie as conexões entre o aplicativo LUIS e o serviço de Fala do Bing. |
| **Preços do Serviço de Bot** | Gerencie o tipo de preço para o serviço de bot. |

## <a name="app-service-settings"></a>Configurações do serviço de aplicativo

![Configurações do Serviço de Aplicativo](~/media/azure-manage-a-bot/app-service-settings.png)

A folha **Configurações de Aplicativo** contém informações detalhadas sobre o bot, como o ambiente do bot, configurações de depuração e as chaves de configuração do aplicativo, como botFilePath e botFileSecret.

### <a name="microsoftappid-and-microsoftapppassword"></a>MicrosoftAppID e MicrosoftAppPassword

O **MicrosoftAppID** e **MicrosoftAppPassword** são mantidos dentro do arquivo `.bot` do bot. Para recuperá-los, baixe o arquivo de bot e descriptografe-o, que pode ser necessário para que você possa testar localmente com a ID e senha.

### <a name="bot-file-path-and-secret"></a>Segredo e caminho do arquivo de bot

Você pode encontrar o **botFilePath** e o **botFileSecret** do bot na folha **Configurações do aplicativo**.

![Segredo e caminho do arquivo de bot da Microsoft](~/media/azure-manage-a-bot/app-settings.png)

> [!NOTE]
> O serviço de bot **Registro de Canais de Bot** vem com uma *MicrosoftAppID*, mas por não existir nenhum serviço de aplicativo associado a esse tipo de serviço, não há nenhuma folha **Configurações do Aplicativo** para pesquisar a *MicrosoftAppPassword*. Para obter a senha, é necessário gerar uma. Para gerar a senha para um **Registro de Canais de Bot**, consulte [Senha do Registro de Canais de Bot](bot-service-quickstart-registration.md#bot-channels-registration-password)

## <a name="next-steps"></a>Próximas etapas
Agora que você já explorou a folha do Serviço de Bot no portal do Azure, saiba como usar o Editor de Código Online para personalizar o bot.
> [!div class="nextstepaction"]
> [Usar o Editor de Código online](bot-service-build-online-code-editor.md)
