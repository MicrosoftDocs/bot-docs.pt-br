---
title: Definições de política internas para o serviço de bot do Azure
description: Lista Azure Policy definições de política internas para o serviço de bot do Azure. Essas definições de políticas internas fornecem abordagens comuns para gerenciar seus recursos do Azure.
ms.date: 02/05/2021
ms.topic: reference
ms.custom: subject-policy-reference
ms.service: bot-service
author: JonathanFingold
ms.author: kamrani
ms.openlocfilehash: bbcf85630bb864a9d9fcb4eac488429b0a44f3a0
ms.sourcegitcommit: 22a92bc07c85f899b3b1dca4f19421bc302db23f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100272227"
---
# <a name="azure-policy-built-in-definitions-for-azure-bot-service"></a>Azure Policy definições internas para o serviço de bot do Azure

Esta página é um índice de [Azure Policy](/azure/governance/policy/overview) definições de política internas para o serviço de bot do Azure. Para obter políticas internas adicionais do Azure Policy para outros serviços, confira [Definições internas do Azure Policy](/azure/governance/policy/samples/built-in-policies).

O nome de cada definição de política interna leva à definição da política no portal do Azure. Use o link na coluna **Versão** para ver a origem no [repositório GitHub do Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-bot-service"></a>Serviço de Bot do Azure

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O ponto de extremidade do serviço de bot deve ser um URI HTTPS válido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Os dados podem ser adulterados durante a transmissão. Existem protocolos que fornecem criptografia para resolver problemas de uso indevido e adulteração. Para garantir que seus bots estejam se comunicando apenas por meio de canais criptografados, defina o ponto de extremidade para um URI HTTPS válido. Isso garante que o protocolo HTTPS seja usado para criptografar seus dados em trânsito e, muitas vezes, é um requisito de conformidade com padrões regulatórios ou do setor. Acesse: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](/azure/bot-service/bot-builder-security-guidelines). |auditar, negar, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[O Serviço de Bot deve ser criptografado com uma chave gerenciada pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |O Serviço de Bot do Azure criptografa automaticamente o seu recurso para proteger os seus dados e atender aos compromissos de conformidade e segurança da organização. Por padrão, são usadas as chaves de criptografia gerenciadas pela Microsoft. Para obter mais flexibilidade no gerenciamento de chaves ou no controle de acesso à sua assinatura, selecione as chaves gerenciadas pelo cliente, também conhecidas como BYOK (Bring Your Own Key). Saiba mais sobre a criptografia do Serviço de Bot do Azure: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption). |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |

## <a name="next-steps"></a>Próximas etapas

- Confira os internos no [repositório Azure Policy GitHub](https://github.com/Azure/azure-policy).
- Revise a [estrutura de definição do Azure Policy](/azure/governance/policy/concepts/definition-structure).
- Revisar [Compreendendo os efeitos da política](/azure/governance/policy/concepts/effects).
