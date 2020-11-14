---
title: Novidades – Serviço de Bot
description: Saiba mais sobre melhorias e novos recursos na versão de agosto de 2020 do SDK do bot Framework, incluindo novas funcionalidades em habilidades, equipes e outras áreas.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 08/21/2020
ms.openlocfilehash: 2b87e836bbeb356790dd870b5255de7e40f04036
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94597298"
---
# <a name="whats-new-august-2020"></a>Novidades de agosto de 2020

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O SDK do bot Framework v4 é um [SDK](https://github.com/microsoft/botframework-sdk/#readme) de software livre que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|Item | C#  | JS  | Python | Java
|:----|:---:|:---:|:------:|:-----:
|Versão |[4,10 (GA)][1] | [4,10 (GA)][2] | [4,10 (GA)][3] | [4.6 Versão preliminar][3a]
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11a] |

Bem-vindo à versão de agosto de 2020 do SDK do bot Framework. Há uma série de atualizações nessa versão que esperamos que você goste; alguns dos principais destaques incluem:

- [Documentação](#documentation): aprimoramentos na documentação existente, incluindo Leiame para os repositórios de código e amostras.
- [Suporte ao cliente](#customer-supportability): aprimoramentos focados em desenvolvedores que buscam assistência usando o bot Framework, ferramentas e SDKs.
- [Solicitações de clientes](#customer-requests): aprimoramentos focados em solicitações de recursos da comunidade de desenvolvedores e de terceiros usando o SDK e as ferramentas do bot Framework.
- [Qualidade de código](#code-quality): aprimoramentos concentrados na cobertura de teste de unidade e funcional e na documentação de referência.
- [Novos recursos do SDK](#new-sdk-features): novos recursos de visualização adicionados nesta versão.
- [Outros aprimoramentos](#other-improvements): outros aprimoramentos no SDK.

**Participantes do Programa Windows Insider** : deseja experimentar novos recursos assim que possível? Você pode baixar o build noturno para Participantes do Programa Windows Insider [[C#](https://github.com/microsoft/botbuilder-dotnet/blob/main/UsingMyGet.md)] [[JS](https://github.com/microsoft/botbuilder-js/blob/main/UsingMyGet.md)] [[Python](https://github.com/microsoft/botbuilder-python/blob/main/UsingTestPyPI.md)] [[CLI](https://github.com/Microsoft/botframework-cli#nightly-builds)] e experimentar as atualizações mais recentes assim que estiverem disponíveis. E, para obter as notícias, atualizações e conteúdo mais recentes sobre o Bot Framework, siga-nos no Twitter @msbotframework!

## <a name="documentation"></a>Documentação

Após os comentários dos clientes e da equipe de suporte do bot Framework, vários documentos foram criados ou atualizados.Eles são úteis para fornecer respostas e informações relacionadas a problemas recorrentes de desenvolvedores de bot.

- Documentação de comentário de código expandida nos repositórios do SDK.
- LEIAme aprimorados nos exemplos e repositórios do SDK.
- Documentos novos e atualizados que abordam problemas de desenvolvedores de bot recorrentes:
  - Adição de uma página de Hub do serviço de bot do Azure] (/Azure/bot-Service/) que vincula ao bot Framework Composer e à documentação do SDK do bot Framework.
  - Atualizações para [geração de idioma](v4sdk/bot-builder-concept-language-generation.md), [expressões adaptáveis](v4sdk/bot-builder-concept-adaptive-expressions.md)e artigos de [caixa de diálogo adaptáveis](v4sdk/bot-builder-adaptive-dialog-introduction.md) .
  - Atualizações para a documentação do [bot Framework Composer](/composer/) .
  - Correções de problemas e aprimoramentos gerais de documentação para autenticação, habilidades, canais e outros tópicos.

## <a name="customer-supportability"></a>Suporte ao cliente

Os desenvolvedores que usam o Microsoft bot Framework têm muitos [recursos](bot-service-resources-links-help.md) para obter ajuda. Ferramentas internas foram aprimoradas para aumentar a capacidade de resposta da equipe de engenharia em áreas de interesse dos desenvolvedores.

- Criação de bots internos e ferramentas aprimoradas para atendimento ao cliente.
- Análise aprimorada de tendências em solicitações e problemas de recursos relatados pelo cliente.
- Coordenação de rótulos entre repositórios do GitHub.

## <a name="customer-requests"></a>Solicitações de clientes

- Eventos adicionais do ciclo de vida do canal de equipes.
- Integração de Application Insights aprimorada.
- Coordenação de rótulos entre repositórios do GitHub.
- Adição de uma localidade à atividade de atualização de conversa.
- Adicionado suporte de texto ALT para as ações de cartão para imagens em botões.
- O manipulador de habilidades foi atualizado para retornar um objeto de resposta de recurso.
- Suporte incluído para a versão mais recente do armazenamento de BLOBs do Azure.
- Melhorias na caixa de diálogo de prompt do OAuth.
- Várias correções de bugs e aprimoramentos de telemetria.

## <a name="code-quality"></a>Qualidade do código

- Aplicação de regras de formato e estilo de código.
- Cobertura e qualidade de código de teste de unidade aprimoradas.
- Maior criação de perfil da base de código.
- Arquivos Swagger da API REST unificados em repositórios do SDK. Introduziu uma versão para os arquivos.
- Adicionou um padrão de objeto de configurações para adaptadores C#.
- Adicionada a vigilância de dependência no SDK do JavaScript.
- Foram adicionados testes de integração para cartões adaptáveis e JavaScript de linha direta.

## <a name="new-sdk-features"></a>Novos recursos do SDK

- [Orchestrator (versão prévia)](https://github.com/microsoft/BotBuilder-Samples/blob/main/experimental/orchestrator/README.md): uma solução baseada em transformador que é executada localmente com seu bot para expedição em uma ou mais habilidades de bot Builder, aplicativos Luis ou bases de dados de conhecimento QnA Maker.
- [Bot Builder filas do Azure (visualização em C#)](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure.Queues): melhor integração com as filas do Azure e a caixa de diálogo _continuar conversa adiante_ .
- [Bot Builder BLOBs do Azure (visualização em C#)](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure.Blobs): novas implementações do armazenamento de BLOBs do Azure. Essa biblioteca é uma substituição para a implementação mais antiga do armazenamento de BLOBs do Azure.

## <a name="other-improvements"></a>Outros aprimoramentos

- Melhorias contínuas no suporte à API do Microsoft Team.
- Ferramentas da CLI do bot Framework adicionadas `lg` como um plug-in principal e incluíam outros aprimoramentos gerais de ferramentas.
- Foram atualizados os arquivos LEIAme nos exemplos e foram adicionados novos exemplos de typescript de equipes.
- O Composer melhorou o suporte para habilidades e integração aprimorada para serviços cognitivas.
- O Web Chat adicionou muitas melhorias de acessibilidade.
- O emulador adicionou correções de bugs e atualizações.

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/main/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/main/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/main/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/main/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/main/samples/csharp_webapi
[11a]:https://aka.ms/python-sample-repo

## <a name="additional-information"></a>Informações adicionais

Você pode ver os comunicados anteriores [aqui](what-is-new-archive.md).
