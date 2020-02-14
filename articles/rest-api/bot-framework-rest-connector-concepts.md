---
title: Principais conceitos no Conector de Bot e nos Serviços de Estado do Bot – Serviço de Bot
description: Entenda os principais conceitos do serviço Conector do Bot e do serviço Estado do Bot do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/16/2019
ms.openlocfilehash: 15f30ff5d4db24d222a1a9df3ac98e24e576b238
ms.sourcegitcommit: d24fe2178832261ac83477219e42606f839dc64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071884"
---
# <a name="key-concepts"></a>Principais conceitos

Você pode usar o serviço de Conector do Bot para se comunicar com usuários em vários canais como Skype, Email, Slack e muitos outros. Este artigo apresenta conceitos importantes do serviço de Conector do Bot.

## <a name="bot-connector-service"></a>Serviço do Conector de Bot

O serviço Bot Connector permite que seu bot troque mensagens com canais configurados no [Portal do Azure](https://portal.azure.com). Ele usa o padrão do setor REST e JSON via HTTPS e permite a autenticação com tokens de Portador JWT. Para obter informações detalhadas sobre como usar o serviço do Conector de Bot, consulte [Autenticação](bot-framework-rest-connector-authentication.md) e os outros artigos desta seção.

### <a name="activity"></a>Atividade

O serviço Bot Connector troca informações entre o bot e o canal (usuário) passando um objeto [Activity][Activity]. O tipo de atividade mais comum é **mensagem**, mas há outros tipos de atividade que podem ser usados para comunicar diversos tipos de informações a um bot ou canal. Para obter detalhes sobre as Atividades no serviço do Conector do Bot, consulte [Visão geral das atividades](https://aka.ms/botSpecs-activitySchema).

## <a name="bot-state-service"></a>Serviço de Estado do Bot

O serviço de Estado do Microsoft Bot Framework foi desativado em 30 de março de 2018. Anteriormente, bots criados no Serviço de Bot do Azure ou no SDK do Bot Builder tinham uma conexão padrão com esse serviço hospedado pela Microsoft para armazenar dados de estado do bot. Os bots deverão ser atualizados para usar seu próprio armazenamento de estado.

## <a name="authentication"></a>Autenticação

O serviço de Conector do Bot permite a autenticação com Tokens de portador JWT. Para obter informações detalhadas sobre como autenticar solicitações de saída enviadas por seu bot para o Bot Framework, como autenticar solicitações de entrada que seu bot recebe do Bot Framework e muito mais, consulte [Autenticação](bot-framework-rest-connector-authentication.md). 

## <a name="client-libraries"></a>Bibliotecas de cliente

O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js. 

- Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md). 
- Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md). 

Além da modelagem do serviço de Conector do Bot, cada SDK do Bot Framework também fornece um sistema avançado para a criação de diálogos que encapsulam a lógica de conversação, solicitações internas de coisas simples como Sim/Não, cadeias de caracteres, números e enumerações, suporte interno para estruturas de IA avançadas como <a href="https://www.luis.ai/" target="_blank">LUIS</a> e muito mais. 

> [!NOTE]
> Como alternativa ao uso do SDK do C# ou do SDK do Node.js, você pode gerar sua própria biblioteca de cliente na linguagem de sua escolha usando o <a href="https://aka.ms/connector-swagger-file" target="_blank">arquivo Swagger do Conector do Bot</a>.

## <a name="additional-resources"></a>Recursos adicionais

Saiba mais sobre como criar bots usando o serviço de Conector do Bot conferindo os artigos apresentados nesta seção, começando com [Autenticação](bot-framework-rest-connector-authentication.md). Se você encontrar problemas ou sugestões sobre o serviço de Conector do Bot, consulte [Suporte](../bot-service-resources-links-help.md) para obter uma lista de recursos disponíveis. 

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
