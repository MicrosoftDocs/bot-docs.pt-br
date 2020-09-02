---
title: Gerenciar um bot – Serviço de Bot
description: Saiba como gerenciar bots. Consulte como usar o portal do Azure para encontrar informações sobre logs de atividades, opções de compilação, configurações de depuração e outras propriedades.
keywords: portal do Azure, gerenciamento de bot, teste em webchat, MicrosoftAppID, MicrosoftAppPassword, configurações de aplicativo
author: v-ducvo
ms.author: rstand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/2/2020
ms.openlocfilehash: 783a27238729c9a321f8b386a0d43120ad80ef96
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360979"
---
# <a name="manage-a-bot"></a>Gerenciar um bot

[!INCLUDE [applies-to-both](includes/applies-to-both.md)]

Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com/). Selecione o aplicativo de recurso, como um **registro de canais de bot**. No painel esquerdo, você verá as seções descritas abaixo.

### <a name="bot-settings-overview"></a>Visão geral das configurações de bot

Esses links exibem painéis à direita que fornecem informações gerais aplicáveis a um bot.

![Visão geral das configurações de bot](~/media/azure-manage-a-bot/overview.png)

> [!div class="mx-tdBreakAll"]
> | Opção |  Descrição |
> | ---- | ---- |
> |**Visão geral** <img width="200px"/>| O painel relacionado contém informações de alto nível sobre o bot. Por exemplo, você pode ver a ID da **assinatura** e o **ponto de extremidade do sistema de mensagens**de um bot. |
> |**Log de atividades**| Os logs de plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e a plataforma do Azure da qual dependem. Para obter mais informações, consulte [visão geral dos logs da plataforma Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview).|
> |**Controle de acesso (IAM)**| Exibir o acesso que um usuário ou outra entidade de segurança tem aos recursos do Azure. Para obter mais informações, consulte [Exibir o acesso que um usuário tem aos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/check-access).|
> |**Marcas**|Aplique marcas aos recursos do Azure, grupos de recursos e assinaturas para organizá-los logicamente. Para obter mais informações, veja [Usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources). |

### <a name="bot-management"></a>Gerenciamento de bot

 Você pode encontrar a maioria das opções de gerenciamento de bot na seção de **Gerenciamento de bot** . Segue abaixo uma lista de opções para ajudá-lo a gerenciar o bot.

![Gerenciamento de bot](~/media/azure-manage-a-bot/bot-management.png)

> [!div class="mx-tdBreakAll"]
> | Opção |  Descrição |
> | ---- | ---- |
> | **Compilar** <img width="200px"/>| Forneça opções para fazer alterações no bot. Não disponível somente para o **bot de registro**. |
> | **Testar no Webchat** | Use o controle de chat Web integrado para testar rapidamente o bot. |
> | **Análise** | Habilite a análise para exibir os dados coletados com Application Insights. |
> | **Channels** | Configure os canais que o bot usa para comunicar-se com os usuários. |
> | **Configurações** | Gerencie várias configurações de perfil de bot, como nome de exibição, análise e ponto de extremidade de mensagens. |
> | **Preparação da fala** | Gerencie as conexões entre seu aplicativo LUIS e o serviço Fala do Bing. |
> | **Preços do Serviço de Bot** | Gerencie o tipo de preço para o serviço de bot. |

## <a name="application-service-settings"></a>Configurações do serviço de aplicativo

Um aplicativo bot, também conhecido como **serviço de aplicativo**, tem um conjunto de **configurações de aplicativo** que você pode acessar por meio do portal do Azure conforme descrito abaixo. Essas configurações de aplicativo são variáveis passadas como variáveis de ambiente para o código do aplicativo. Para obter mais informações, consulte [configurar um aplicativo do serviço de aplicativo no portal do Azure](https://docs.microsoft.com/azure/app-service/configure-common).

1. Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com/).
1. Procure seu serviço de aplicativo bot. Clique em seu nome.
1. O serviço de aplicativo bot é exibido.

    ![Configurações do Serviço de Aplicativo](~/media/azure-manage-a-bot/app-service-settings.png)

O painel esquerdo **configurações do aplicativo** contém informações detalhadas sobre o bot, como o ambiente do bot, as configurações de depuração e as chaves de configurações do aplicativo.

### <a name="microsoftappid-and-microsoftapppassword"></a>MicrosoftAppID e MicrosoftAppPassword

A **MicrosoftAppID** e a **MicrosoftAppPassword** são mantidas no arquivo de configurações do bot (`appsettings.json` ou `.env`) ou no Azure Key Vault. Para recuperá-las, baixe a configuração ou o arquivo de configuração do bot (para bots antigos, se houver) ou acesse o Azure Key Vault. Pode ser necessário fazer um teste local com a ID e a senha.

> [!NOTE]
> O serviço de bot de **registro de canais de bot** vem com um *MicrosoftAppID*, mas como não há nenhum serviço de aplicativo associado a esse tipo de serviço, também não há nenhuma folha de **configurações de aplicativo** para você procurar o *MicrosoftAppPassword*. Consulte [senha de registro de canais de bot](bot-service-manage-settings.md#get-registration-password) para saber como gerar a senha.

## <a name="next-steps"></a>Próximas etapas
Agora que você já explorou a folha do Serviço de Bot no portal do Azure, saiba como usar o Editor de Código Online para personalizar o bot.
> [!div class="nextstepaction"]
> [Como funcionam os bots](~/v4sdk/bot-builder-basics.md)
