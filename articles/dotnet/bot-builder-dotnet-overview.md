---
title: SDK do Construtor de bot para .NET | Microsoft Docs
description: Introdução ao SDK do Construtor de Bot para .NET, uma estrutura poderosa e fácil de usar para criar bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 801925e2c179392804d9707e62bfaef082c8e81b
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49996694"
---
# <a name="bot-builder-sdk-for-net"></a>SDK do Construtor de Bot para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

O SDK do Construtor de Bot para .NET é uma estrutura avançada para a criação de bots que podem manipular interações livres e conversas mais guiadas, nas quais o usuário seleciona entre valores possíveis. Ele é fácil de usar e aproveita a linguagem C# para fornecer um jeito conhecido para os desenvolvedores em .NET escreverem bots.

Com o SDK, você pode criar bots que aproveitam os recursos do SDK a seguir: 

- Sistema de diálogo avançado com diálogos isolados e combináveis
- Solicitações internas para coisas simples como Sim/Não, cadeias de caracteres, números e enumerações
- Diálogos internos que utilizam uma estrutura de IA avançada, como o <a href="http://luis.ai" target="_blank">LUIS</a>
- FormFlow para gerar automaticamente um bot (de uma classe de C#) que orienta o usuário pela conversa, fornecendo ajuda, navegação, esclarecimento e confirmação conforme o necessário

> [!IMPORTANT]
> Em 31 de julho de 2017, foram implementadas alterações consideráveis no protocolo de segurança do Bot Framework. Para evitar que essas alterações afetem negativamente seu bot, verifique se o seu aplicativo está usando o SDK do Construtor de Bot v3.5 ou superior. Se você criou um bot usando um SDK que você obteve antes de 5 de janeiro de 2017 (a data de lançamento do SDK do Construtor de Bot v3.5), atualize para o SDK do Construtor de Bot v3.5 ou posterior.

## <a name="get-the-sdk"></a>Obter o SDK

O SDK está disponível como um pacote do NuGet e como software livre no <a href="https://github.com/Microsoft/BotBuilder" target="_blank">GitHub</a>.

> [!IMPORTANT]
> O SDK do Construtor de Bot para .NET exige o .NET Framework 4.6 ou mais recente. Se você estiver adicionando o SDK a um projeto existente direcionado para uma versão inferior do .NET Framework, será necessário atualizar seu projeto para direcioná-lo ao .NET Framework 4.6 primeiro.

Para instalar o SDK dentro de um projeto do Visual Studio, conclua estas etapas:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...**.
2. Na guia **Procurar**, digite "Microsoft.Bot.Builder" na caixa de pesquisa.
3. Selecione **Microsoft.Bot.Builder** na lista de resultados, clique em **Instalar** e aceite as alterações.

## <a name="get-code-samples"></a>Obter exemplos de código

Esse SDK inclui um [exemplo de código-fonte](bot-builder-dotnet-samples.md) que usa os recursos do SDK do Construtor de Bot para .NET.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar bots usando o SDK do Construtor de Bot para .NET conferindo os artigos desta seção, começando com:

- [Início Rápido](bot-builder-dotnet-quickstart.md): crie e teste rapidamente um bot simples seguindo as instruções neste tutorial passo a passo.
- [Principais conceitos](bot-builder-dotnet-concepts.md): saiba mais sobre os principais conceitos do SDK do Construtor de Bot para .NET.

Se você encontrar problemas ou tiver sugestões sobre o SDK do Construtor de Bot para .NET, confira uma lista dos recursos disponíveis em [Suporte](../bot-service-resources-links-help.md). 
