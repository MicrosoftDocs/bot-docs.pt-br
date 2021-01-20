---
title: Novidades – Serviço de Bot
description: Saiba mais sobre melhorias e novos recursos na versão de agosto de 2020 do SDK do bot Framework, incluindo novas funcionalidades em habilidades, equipes e outras áreas.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 11/13/2020
ms.openlocfilehash: 9ccaf741e10e093a33be4dc446ae519255c7314a
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577508"
---
# <a name="whats-new-november-2020"></a>O que há de novo em novembro de 2020

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O SDK do bot Framework v4 é um [SDK](https://github.com/microsoft/botframework-sdk/#readme) de software livre que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|Item | C#  | JS  | Python | Java
|:----|:---:|:---:|:------:|:-----:
|Versão |[4,11 (GA)][1] | [4,11 (GA)][2] | [4,11 (GA)][3] | [visualização de 4,7][3a]
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11a] |

Bem-vindo à versão de novembro de 2020 do SDK do bot Framework. Há uma série de atualizações nessa versão que esperamos que você goste; alguns dos principais destaques incluem:

- [Documentação](#documentation-updates): aprimoramentos na documentação existente, incluindo Leiame para os repositórios de código e amostras.
- Equipes: suporte adicionado para a API de reunião do participante e outras melhorias gerais.
- Habilidades: agora podem ser executadas e testadas localmente no emulador sem uma ID do aplicativo e uma senha, suporte aprimorado para habilidades em caixas de diálogo adaptáveis.
- Orchestrator (visualização): uma tecnologia de Reconhecimento vocal para roteamento de declarações de usuário de entrada para uma habilidade apropriada ou para o serviço de processamento de idioma subsequente, como LUIS ou QnA Maker.
- Adaptador de nuvem (visualização, somente .NET): um adaptador de bot que dá suporte à Hospedagem de um bot em qualquer ambiente de nuvem.

**Participantes do Programa Windows Insider**: deseja experimentar novos recursos assim que possível? Você pode baixar o build noturno para Participantes do Programa Windows Insider [[C#](https://github.com/microsoft/botbuilder-dotnet/blob/main/UsingMyGet.md)] [[JS](https://github.com/microsoft/botbuilder-js/blob/main/UsingMyGet.md)] [[Python](https://github.com/microsoft/botbuilder-python/blob/main/UsingTestPyPI.md)] [[CLI](https://github.com/Microsoft/botframework-cli#nightly-builds)] e experimentar as atualizações mais recentes assim que estiverem disponíveis. E, para obter as notícias, atualizações e conteúdo mais recentes sobre o Bot Framework, siga-nos no Twitter @msbotframework!

## <a name="documentation-updates"></a>Atualizações na documentação

Após os comentários dos clientes e da equipe de suporte do bot Framework, vários documentos foram criados ou atualizados.Eles são úteis para fornecer respostas e informações relacionadas a problemas recorrentes de desenvolvedores de bot.

- Documentação de comentário de código expandida nos repositórios do SDK.
- LEIAme aprimorados nos exemplos e repositórios do SDK.
- Documentos novos e atualizados que abordam problemas de desenvolvedores de bot recorrentes:
  - Atualizado e expandido os artigos [conceitual](v4sdk/bot-builder-adaptive-dialog-Introduction.md) e de [instruções](v4sdk/bot-builder-adaptive-dialog-setup.md) para caixas de diálogo adaptáveis.
  - Atualizados e reorganizados os artigos de [autenticação e segurança](v4sdk/bot-builder-authentication-basics.md) .
  - Atualizações para a documentação do [bot Framework Composer](/composer/) .
  - Correções de problemas e aprimoramentos gerais de documentação para geração de resposta, serviços cognitivas, expressões adaptáveis, habilidades, canais e outros tópicos.
  - A documentação do SDK v3 foi removida do conjunto de documentos principal e está disponível no site de [versões anteriores](/previous-versions) .

## <a name="additional-information"></a>Informações adicionais

- Você pode ver anúncios anteriores nas [informações arquivadas](what-is-new-archive.md).
- Consulte as [notas de versão](https://github.com/microsoft/botframework-sdk/releases/) do SDK do bot Framework para obter mais informações sobre as alterações feitas no SDK na versão 4,11.

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[5]:index.yml
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[11a]:https://aka.ms/python-sample-repo
