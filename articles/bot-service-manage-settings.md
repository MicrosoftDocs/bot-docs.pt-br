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
ms.openlocfilehash: a34c99e6995835341cb610525d8033567d914e5c
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81395699"
---
# <a name="configure-bot-settings"></a>Definir as configurações do bot

As configurações do bot, como Nome de Exibição, Ícone e Application Insights, podem ser exibidas e modificadas em sua folha **Configurações**.

![A folha de configurações do bot](~/media/bot-service-portal-configure-settings/bot-settings-blade.png)

Veja abaixo a lista de campos na folha **Configurações**:

| Campo | Descrição |
| :---  | :---        |
| ícone | Um ícone personalizado para identificar visualmente seu bot nos canais e como o ícone para Cortana e outros serviços. Esse ícone deve estar no formato PNG e não pode exceder 30K em tamanho. Esse valor pode ser alterado a qualquer momento. |
| Nome de exibição | O nome do seu bot em canais e diretórios. Esse valor pode ser alterado a qualquer momento. Limite de 35 caracteres. |
| Identificador de Bot | Identificador exclusivo para seu bot. Esse valor não pode ser alterado depois de criar seu bot com o Serviço de Bot. |
| Ponto de extremidade de mensagens | O ponto de extremidade para se comunicar com seu bot. |
| ID do Aplicativo da Microsoft | Identificador exclusivo para seu bot. Esse valor não pode ser alterado. Você pode gerar uma nova senha clicando no link **Gerenciar**. |
| Chave de instrumentação do Application Insights | Chave exclusiva para a telemetria de bot. Copie sua chave do Application Insights do Azure para esse campo se você quiser receber telemetria de bot para este bot. Esse valor é opcional. Os bots criados no Portal do Azure terão essa chave gerada para eles. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |
| Chave de API do Application Insights | Chave exclusiva para a análise de bot. Copie sua chave de API do Application Insights do Azure para esse campo se você quiser exibir análises sobre seu bot no painel. Esse valor é opcional. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |
| ID do Aplicativo do Application Insights | Chave exclusiva para a análise de bot. Copie sua chave da ID do Application Insights do Azure para esse campo se você quiser exibir análises sobre seu bot no painel. Esse valor é opcional. Os bots criados no Portal do Azure terão essa chave gerada para eles. Para obter mais detalhes sobre esse campo, veja [Chaves do Application Insights](~/bot-service-resources-app-insights-keys.md). |

Depois de alterar as configurações para o bot, clique no botão **Salvar** na parte superior da folha para salvar as configurações de seu novo bot.

## <a name="additional-information"></a>Informações adicionais

Você pode usar [az bot update](https://docs.microsoft.com/cli/azure/bot?view=azure-cli-latest#az-bot-update) para atualizar as configurações de bot por meio da linha de comando.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como definir as configurações para seu serviço de bot, saiba mais sobre como configurar a preparação da fala.
> [!div class="nextstepaction"]
> [Preparação da fala](bot-service-manage-speech-priming.md)
