---
title: Principais conceitos no serviço Conector do Bot e no serviço Estado do Bot | Microsoft Docs
description: Entenda os principais conceitos do serviço Conector do Bot e do serviço Estado do Bot do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: a3d6bd957b835a0b8d86e47595ce28506c32d636
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224551"
---
# <a name="key-concepts"></a>Principais conceitos

Você pode usar o serviço Conector do Bot e o serviço Estado do Bot para se comunicar com usuários em vários canais como Skype, Email, Slack e muitos outros. Este artigo apresenta conceitos importantes do serviço Conector do Bot e do serviço Estado do Bot.

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e pode ser reprovada em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para saber mais, confira o tópico **Gerenciar dados de estado** para implementação de [.NET](~/dotnet/bot-builder-dotnet-state.md) ou [Node](~/nodejs/bot-builder-nodejs-state.md).

## <a name="bot-connector-service"></a>Serviço do Conector de Bot

O serviço do Conector do Bot permite que seu bot troque mensagens com canais configurados no <a href="https://dev.botframework.com/" target="_blank">Portal do Bot Framework</a>. Ele usa o padrão do setor REST e JSON via HTTPS e permite a autenticação com tokens de Portador JWT. Para obter informações detalhadas sobre como usar o serviço do Conector de Bot, consulte [Autenticação](bot-framework-rest-connector-authentication.md) e os outros artigos desta seção.

### <a name="activity"></a>Atividade

O serviço Bot Connector troca informações entre o bot e o canal (usuário) passando um objeto [Atividade][Activity]. O tipo mais comum de atividade é **mensagem**, mas há outros tipos de atividade que podem ser usados para comunicar vários tipos de informações para um bot ou canal. Para obter detalhes sobre as Atividades no serviço do Conector do Bot, consulte [Visão geral das atividades](bot-framework-rest-connector-activities.md).

## <a name="bot-state-service"></a>Serviço de Estado do Bot

O serviço de Estado do Bot permite que o bot armazene e recupere dados de estado associados a um usuário, uma conversa ou um usuário específico no contexto de uma conversa específica. Ele usa o padrão do setor REST e JSON via HTTPS e permite a autenticação com tokens de Portador JWT. Todos os dados que você salva usando o serviço de Estado do Bot serão armazenados no Azure e criptografados em repouso.

O serviço Estado do Bot só é útil em conjunto com o serviço Conector do Bot. Ou seja, ele só pode ser usado para armazenar dados de estado que tenham relação com as conversas que seu bot conduz usando o serviço Conector do Bot. Para obter informações detalhadas sobre como usar o serviço Estado do Bot, consulte [Autenticação](bot-framework-rest-connector-authentication.md) e [Gerenciar dados de estado](bot-framework-rest-state.md).

## <a name="authentication"></a>Autenticação

O serviço Conector do Bot e o serviço Estado do Bot permitem a autenticação com Tokens de portador JWT. Para obter informações detalhadas sobre como autenticar solicitações de saída enviadas por seu bot para o Bot Framework, como autenticar solicitações de entrada que seu bot recebe do Bot Framework e muito mais, consulte [Autenticação](bot-framework-rest-connector-authentication.md). 

## <a name="client-libraries"></a>Bibliotecas de cliente

O Bot Framework fornece bibliotecas de cliente que podem ser usadas para criar bots em C# ou Node.js. 

- Para criar um bot usando C#, use o [SDK do Bot Framework para C#](../dotnet/bot-builder-dotnet-overview.md). 
- Para criar um bot usando Node.js, use o [SDK do Bot Framework para Node.js](../nodejs/index.md). 

Além da modelagem do serviço Conector do Bot e do serviço Estado do Bot, cada SDK do Bot Framework também fornece um sistema avançado para a criação de diálogos que encapsulam a lógica de conversação, solicitações internas de coisas simples como Sim/Não, cadeias de caracteres, números e enumerações, suporte interno para estruturas de IA avançadas como <a href="https://www.luis.ai/" target="_blank">LUIS</a> e muito mais. 

> [!NOTE]
> Como alternativa ao uso do SDK do C# ou do SDK do Node.js, você pode gerar sua própria biblioteca de cliente na linguagem de sua escolha usando o <a href="https://raw.githubusercontent.com/Microsoft/BotBuilder/master/CSharp/Library/Microsoft.Bot.Connector.Shared/Swagger/ConnectorAPI.json" target="_blank">arquivo Swagger do Conector do Bot</a> e o <a href="https://raw.githubusercontent.com/Microsoft/BotBuilder/master/CSharp/Library/Microsoft.Bot.Connector.Shared/Swagger/StateAPI.json" target="_blank">arquivo Swagger do Estado do Bot</a>.

## <a name="additional-resources"></a>Recursos adicionais

Saiba mais sobre como criar bots usando o serviço Conector do Bot e o serviço Estado do Bot conferindo os artigos apresentados nesta seção, começando com [Autenticação](bot-framework-rest-connector-authentication.md). Se você encontrar problemas ou sugestões sobre o serviço Conector do Bot ou Estado do Bot, consulte [Suporte](../bot-service-resources-links-help.md) para obter uma lista de recursos disponíveis. 

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object