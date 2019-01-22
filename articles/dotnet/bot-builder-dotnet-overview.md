---
title: SDK do Bot Framework para .NET | Microsoft Docs
description: Introdução ao SDK do Bot Framework para .NET, um framework poderoso e simples para criar bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f54ea91bbe04f5b9b8a0701a3473ef7e76cacaeb
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224721"
---
# <a name="bot-framework-sdk-for-net"></a>SDK do Bot Framework para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

O SDK do Bot Framework para .NET é um framework avançado para a criação de bots que podem manipular interações livres e conversas mais guiadas, nas quais o usuário seleciona a partir de valores possíveis. Ele é fácil de usar e aproveita a linguagem C# para fornecer um jeito conhecido para os desenvolvedores em .NET escreverem bots.

Com o SDK, você pode criar bots que aproveitam os recursos do SDK a seguir: 

- Sistema de diálogo avançado com diálogos isolados e combináveis
- Solicitações internas para coisas simples como Sim/Não, cadeias de caracteres, números e enumerações
- Diálogos internos que utilizam uma estrutura de IA avançada, como o <a href="http://luis.ai" target="_blank">LUIS</a>
- FormFlow para gerar automaticamente um bot (de uma classe de C#) que orienta o usuário pela conversa, fornecendo ajuda, navegação, esclarecimento e confirmação conforme o necessário

> [!IMPORTANT]
> Em 31 de julho de 2017, foram implementadas alterações consideráveis no protocolo de segurança do Bot Framework. Para evitar que essas alterações afetem negativamente seu bot, verifique se seu aplicativo está usando o SDK do Bot Framework v3.5 ou superior. Se você criou um bot usando um SDK obtido antes de 5 de janeiro de 2017 (a data de lançamento do SDK do Bot Framework v3.5), atualize para o SDK do Bot Framework v3.5 ou posterior.

## <a name="get-the-sdk"></a>Obter o SDK

O SDK está disponível como um pacote do NuGet e como software livre no <a href="https://github.com/Microsoft/BotBuilder" target="_blank">GitHub</a>.

> [!IMPORTANT]
> O SDK do Bot Framework para .NET exige o .NET Framework 4.6 ou mais recente. Caso esteja adicionando o SDK a um projeto existente direcionado a uma versão inferior do .NET Framework, você precisará atualizar o projeto para direcioná-lo ao .NET Framework 4.6 primeiro.

Para instalar o SDK dentro de um projeto do Visual Studio, conclua estas etapas:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...**.
2. Na guia **Procurar**, digite "Microsoft.Bot.Builder" na caixa de pesquisa.
3. Selecione **Microsoft.Bot.Builder** na lista de resultados, clique em **Instalar** e aceite as alterações.

## <a name="get-code-samples"></a>Obter exemplos de código

Esse SDK inclui um [exemplo de código-fonte](bot-builder-dotnet-samples.md) que usa os recursos do SDK do Bot Framework para .NET.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar bots usando o SDK do Bot Framework para .NET conferindo os artigos desta seção, começando pelo:

- [Início Rápido](bot-builder-dotnet-quickstart.md): crie e teste rapidamente um bot simples seguindo as instruções neste tutorial passo a passo.
- [Principais conceitos](bot-builder-dotnet-concepts.md): saiba mais sobre os principais conceitos do SDK do Bot Framework para .NET.

Se você encontrar problemas ou tiver sugestões sobre o SDK do Bot Framework para .NET, consulte a lista de recursos disponíveis em [Suporte](../bot-service-resources-links-help.md). 
