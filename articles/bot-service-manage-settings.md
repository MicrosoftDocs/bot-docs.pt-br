---
title: Definir configurações do bot – Serviço de Bot
description: Saiba como configurar as várias opções para seu bot usando o Portal do Azure.
keywords: definir configurações do bot, nome de exibição, ícone, Application Insights, folha de configurações
author: v-royhar
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: d80d2eba59db1f3bba530abc2022ab6d84aff51d
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414804"
---
# <a name="configure-bot-registration-settings"></a>Definir configurações de registro de bot

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

As configurações de registro de bot, como nome de exibição, ícone e Application insights, podem ser exibidas e modificadas no painel **configurações** .

![configurações de registro de bot](media/bot-service-portal-configure-settings/bot-service-settings.png)

Abaixo está a lista dos campos de **configurações** :

> [!div class="mx-tdBreakAll"]
> | Campo <img width="400px"/>| Descrição |
> | :---  | :---        |
> | ícone | Um ícone personalizado para identificar visualmente seu bot nos canais e como o ícone para Cortana e outros serviços. Esse ícone deve estar no formato PNG e não pode exceder 30K em tamanho. Esse valor pode ser alterado a qualquer momento. |
> | Nome de exibição | O nome do seu bot em canais e diretórios. Esse valor pode ser alterado a qualquer momento. Limite de 35 caracteres. |
> | Identificador de Bot | Identificador exclusivo para seu bot. Esse valor não pode ser alterado depois de criar seu bot com o Serviço de Bot. |
> | Ponto de extremidade de mensagens | O ponto de extremidade para se comunicar com seu bot. |
> | ID do Aplicativo da Microsoft | Identificador exclusivo para seu bot. Esse valor não pode ser alterado. Você pode gerar uma nova senha clicando no link **Gerenciar**. |
> | Chave de instrumentação do Application Insights | Chave exclusiva para a telemetria de bot. Copie sua chave do Application Insights do Azure para esse campo se você quiser receber telemetria de bot para este bot. Esse valor é opcional. Os bots criados no Portal do Azure terão essa chave gerada para eles. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |
> | Chave de API do Application Insights | Chave exclusiva para a análise de bot. Copie sua chave de API do Application Insights do Azure para esse campo se você quiser exibir análises sobre seu bot no painel. Esse valor é opcional. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |
> | ID do Aplicativo do Application Insights | ID exclusiva para análise de bot. Copie sua chave da ID do Application Insights do Azure para esse campo se você quiser exibir análises sobre seu bot no painel. Esse valor é opcional. Os bots criados no Portal do Azure terão essa chave gerada para eles. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |

Depois de alterar as configurações para o bot, clique no botão **Salvar** na parte superior da folha para salvar as configurações de seu novo bot.

## <a name="application-id-and-password"></a>ID do aplicativo e senha

A ID e a senha do aplicativo de registro são atribuídas às variáveis do bot `MicrosoftAppID` e `MicrosoftAppPassword` nos arquivos `appsettings.json` (.net) e `.env` (JavaScript).

> [!NOTE]
> O registro de canais de bot tem uma ID de aplicativo, mas como não há nenhum serviço de aplicativo associado a ele, não há senha. Para gerar a senha, siga as etapas na próxima seção. 

### <a name="get-registration-password"></a>Obter a senha de registro

Um aplicativo de registro tem uma **ID de aplicativo** (ID de aplicativo) e uma **senha** associada a ele.
O serviço de bot do Azure atribui uma ID de aplicativo exclusiva ao aplicativo. Você pode obter a senha seguindo as etapas descritas abaixo.

1. Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com).
1. Na lista de recursos, clique no nome do aplicativo de registro.
1. No painel direito, vá para a seção *Gerenciamento de bot* e clique em **configurações**. A página *configurações* do aplicativo de registro será exibida.
1. Clique no link **gerenciar** ao lado de *ID do aplicativo da Microsoft*.

    ![senha de configurações de registro de bot](media/azure-bot-quickstarts/bot-channels-registration-password.png)

1. Na página exibida *Certificados e segredos*, clique no botão **Novo segredo do cliente**.

    ![senha de configurações de registro de bot](media/azure-bot-quickstarts/bot-channels-registration-app-secrets.png)

1. Adicione a descrição, selecione a hora de expiração e clique no botão **Adicionar**.

    ![senha de configurações de registro de bot](media/azure-bot-quickstarts/bot-channels-registration-app-secrets-create.png)

    Isso irá gerar uma nova senha para o seu bot. Copie esta senha e salve-a em um arquivo. Esta é a única vez que você verá a senha. Se você não tiver a senha completa salva, será preciso repetir o processo para criar uma nova senha caso precise dela posteriormente.

## <a name="additional-information"></a>Informações adicionais

Você pode usar [az bot update](https://docs.microsoft.com/cli/azure/bot?view=azure-cli-latest#az-bot-update) para atualizar as configurações de bot por meio da linha de comando.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como definir as configurações para seu serviço de bot, saiba mais sobre como configurar a preparação da fala.
> [!div class="nextstepaction"]
> [Preparação da fala](bot-service-manage-speech-priming.md)
