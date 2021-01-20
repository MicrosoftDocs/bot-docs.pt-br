---
title: Principais conceitos no Conector de Bot e nos Serviços de Estado do Bot – Serviço de Bot
description: Entenda os principais conceitos do serviço do conector do bot Framework e do serviço de Estado do Bot.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: 63b18b070777edb270add6586b8cfdc7e639f862
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576768"
---
# <a name="key-concepts---bot-connector-api"></a>Principais conceitos-API do conector de bot

O Bot Framework e o serviço de Bot do Azure permitem que o bot comunique-se com usuários no Teams, no Cortana, no Facebook e muito mais. Os canais estão disponíveis em duas formas: como serviço incluído como parte do Serviço de Bot do Azure e como bibliotecas de adaptador para uso com o SDK do Bot Framework. Este artigo se concentra nos canais padronizados incluídos no Serviço de Bot do Azure.

## <a name="bot-framework-channels"></a>Canais do Bot Framework

Os canais do bot Framework permitem que o bot troque mensagens com canais configurados no [portal do Azure](https://portal.azure.com). Ele usa o padrão do setor REST e JSON via HTTPS e permite a autenticação com tokens de Portador JWT. Para obter informações detalhadas sobre como usar o serviço do Conector de Bot, consulte [Autenticação](bot-framework-rest-connector-authentication.md) e os outros artigos desta seção.

### <a name="activity"></a>Atividade

O serviço do conector troca informações entre o bot e o canal (usuário), passando um objeto de [atividade][Activity] . O tipo de atividade mais comum é **mensagem**, mas há outros tipos de atividade que podem ser usados para comunicar diversos tipos de informações a um bot ou canal. Para obter detalhes sobre as Atividades no serviço do Conector do Bot, consulte [Visão geral das atividades](https://aka.ms/botSpecs-activitySchema).

## <a name="authentication"></a>Autenticação

Os Bot Framework Services usam tokens de portador JWT para autenticação. Para obter informações detalhadas sobre como autenticar solicitações de saída enviadas por seu bot para o Bot Framework, como autenticar solicitações de entrada que seu bot recebe do Bot Framework e muito mais, consulte [Autenticação](bot-framework-rest-connector-authentication.md).

## <a name="client-libraries"></a>Bibliotecas de cliente

O Bot Framework fornece bibliotecas de clientes que podem ser usadas para criar bots em C#, JavaScript e Python.

- Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md).
- Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md).

Além de simplificar chamadas às APIs REST do Bot Framework, cada SDK do Bot Framework também fornece um sistema avançado para a criação de diálogos que encapsulam a lógica de conversa, solicitações internas de coisas simples como Sim/Não, cadeias de caracteres, números e enumerações, suporte interno para estruturas de IA avançadas como [LUIS](https://www.luis.ai/) e muito mais.

> [!NOTE]
> Como alternativa ao uso do SDK, você pode gerar sua própria biblioteca de clientes na linguagem de sua escolha usando o [arquivo Swagger do Conector do Bot](https://aka.ms/connector-swagger-file) ou codificar diretamente para a API REST.

## <a name="bot-state-service"></a>Serviço de Estado do Bot

O serviço de Estado do Microsoft Bot Framework foi desativado em 30 de março de 2018. Anteriormente, bots criados no Serviço de Bot do Azure ou no SDK do Bot Builder tinham uma conexão padrão com esse serviço hospedado pela Microsoft para armazenar dados de estado do bot. Os bots deverão ser atualizados para usar seu próprio armazenamento de estado.

## <a name="additional-resources"></a>Recursos adicionais

Saiba mais sobre a criação de bots usando o serviço de conector revisando artigos em toda esta seção, começando com a [autenticação](bot-framework-rest-connector-authentication.md). Se você tiver problemas ou tiver sugestões sobre o serviço do conector, consulte [suporte](../bot-service-resources-links-help.md) para obter uma lista de recursos disponíveis.

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
