---
title: Principais conceitos no SDK do Bot Framework para .NET | Microsoft Docs
description: Entenda os principais conceitos e ferramentas para criar e implementar bots de conversação disponíveis no SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e01473a06a0cdbef635de33e5734b02351e36cea
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563451"
---
# <a name="key-concepts-in-the-bot-framework-sdk-for-net"></a>Principais conceitos no SDK do Bot Framework para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-concepts.md)
> - [Node.js](../nodejs/bot-builder-nodejs-concepts.md)

Este artigo apresenta os principais conceitos no SDK do Bot Framework para .NET.

## <a name="connector"></a>Conector

O [Conector do Bot Framework](bot-builder-dotnet-connector.md) oferece uma única API REST que permite que um bot se comunique por diversos canais, como Skype, Email, Slack e muito mais. Ele facilita a comunicação entre o bot e o usuário por mensagens de retransmissão do bot para o canal e do canal para o bot. 

No SDK do Bot Framework para .NET, a biblioteca do [Conector][connectorLibrary] permite o acesso ao Conector. 

## <a name="activity"></a>Atividade

[!INCLUDE [Activity concept overview](../includes/snippet-dotnet-concept-activity.md)]

Veja mais detalhes sobre as Atividades no SDK do Bot Framework para .NET em [Visão geral das Atividades](bot-builder-dotnet-activities.md).

## <a name="dialog"></a>caixa de diálogo

Quando você cria um bot usando o SDK do Bot Framework para .NET, é possível usar os [diálogos](bot-builder-dotnet-dialogs.md) para modelar uma conversa e gerenciar um [fluxo de conversa](../bot-service-design-conversation-flow.md#dialog-stack). Um diálogo pode ser composto por outros diálogos para maximizar a reutilização e o contexto de um diálogo mantém a [pilha de diálogos](../bot-service-design-conversation-flow.md) que estão ativos na conversa em qualquer momento específico. Uma conversa composta por diálogos pode ser transportada entre vários computadores, permitindo dimensionar a implementação do bot. 

No SDK do Bot Framework para .NET, a biblioteca do [Construtor][builderLibrary] permite gerenciar diálogos.

## <a name="formflow"></a>FormFlow

Você pode usar o [FormFlow](bot-builder-dotnet-formflow.md) no SDK do Bot Framework para .NET para simplificar a criação de um bot que colete informações do usuário. Por exemplo, um bot que recebe pedidos de sanduíches deve coletar várias informações do usuário, como o tipo de pão, os ingredientes escolhidos, o tamanho e assim por diante. Considerando as diretrizes básicas, o FormFlow pode gerar automaticamente os diálogos necessários para gerenciar uma conversa guiada como esta.

## <a name="state"></a>Estado

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

Veja mais detalhes sobre como gerenciar o estado usando o SDK do Bot Framework para .NET em [Gerenciar dados do estado](bot-builder-dotnet-state.md).

## <a name="naming-conventions"></a>Convenções de nomenclatura

O SDK do Bot Framework para a biblioteca .NET usa as convenções de nomenclatura Pascal-Case fortemente tipadas. No entanto, as mensagens JSON que são transportadas de um lugar para outro por cabo usam convenção de nomenclatura em minúsculas. Por exemplo, a propriedade C# **ReplyToId** é serializada como **replyToId** na mensagem JSON que é transportada por cabo.

## <a name="security"></a>Segurança

Verifique se o ponto de extremidade do seu bot só pode ser chamado pelo serviço do Bot Framework Connector. Para saber mais sobre este tópico em [Proteger o seu bot](bot-builder-dotnet-security.md).

## <a name="next-steps"></a>Próximas etapas

Agora você conhece os conceitos por trás de cada bot. Você pode [criar rapidamente um bot com o Visual Studio](bot-builder-dotnet-quickstart.md) usando um modelo. Em seguida, estude cada conceito-chave mais detalhadamente, começando pelos diálogos.

> [!div class="nextstepaction"]
> [Diálogos no SDK do Bot Framework para .NET](bot-builder-dotnet-dialogs.md)

[connectorLibrary]: /dotnet/api/microsoft.bot.connector

[builderLibrary]: /dotnet/api/microsoft.bot.builder.dialogs
