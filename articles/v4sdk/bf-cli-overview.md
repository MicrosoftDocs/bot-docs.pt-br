---
title: Visão geral da CLI (Interface de Linha de Comando) do Azure Bot Framework | Microsoft Docs
description: Saiba mais sobre a CLI (Interface de Linha de Comando) do Bot Framework.
keywords: Interface de linha de comando do Bot Framework, CLI do Bot Framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4780d5258af7d2c93fafece361326fd2b0f8df77
ms.sourcegitcommit: 4751c7b8ff1d3603d4596e4fa99e0071036c207c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2019
ms.locfileid: "73443184"
---
<!--TODO:
- [?] Add to TOC: Reference/Bot Framework CLI/Reference
- [?] Add other topics to the same node for each of the command groups
-->
# <a name="bot-framework-cli-overview"></a>Visão geral da CLI do Bot Framework

[!INCLUDE[applies-to](../includes/applies-to.md)]

A CLI (Interface de Linha de Comando) do Bot Framework é uma ferramenta multiplataforma que permite gerenciar bots e serviços relacionados. Ela substitui uma coleção de ferramentas de CLI autônomas mais antigas, agregando-as em uma única ferramenta. 

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/), versão 10.14.1 ou posterior.

## <a name="installation"></a>Instalação

Instale a CLI do BF por meio da linha de comando.

~~~cmd
npm i -g @microsoft/botframework-cli
~~~

## <a name="available-commands"></a>Comandos disponíveis

Os seguintes comandos estão disponíveis no momento.

| Ferramenta antiga | Conjunto de comandos do BF | DESCRIÇÃO |
| :--- | :--- | :--- |
| ChatDown | [`bf chatdown`](bf-cli-reference.md#bf-chatdown) | Comandos para trabalhar com arquivos de diálogo de chat ( **.chat**). |
| na | [`bf config`](bf-cli-reference.md#bf-config) | Define várias configurações na CLI. |
| LuDown, LuisGen | [`bf luis`](bf-cli-reference.md#bf-luis) | Comandos para trabalhar com arquivos de recursos do LUIS e gerenciar modelos do LUIS. |
| QnAMaker | [`bf qnamaker`](bf-cli-reference.md#bf-qnamaker) | Comandos para trabalhar com os arquivos de recurso do QnA Maker e gerenciar bases de dados de conhecimento. |

As seguintes ferramentas serão portadas nas versões futuras:
- LUIS (API)
- Dispatch

Consulte [Mapa de portabilidade](https://github.com/microsoft/botframework-cli/blob/master/PortingMap.md) para obter uma referência de mapeamento entre as ferramentas novas e antigas.

_Observação: As ferramentas mais antigas da CLI serão preteridas em versões futuras e o suporte para elas será encerrado futuramente. Todos os novos investimentos, correções de bug e novos recursos nessa área terão como destino apenas a CLI do BF._

## <a name="overview"></a>Visão geral

A CLI do BF gerencia bots e serviços relacionados. Ela faz parte do Microsoft Bot Framework, uma estrutura abrangente para criar experiências de IA conversacional de nível empresarial. Além de gerenciar recursos relacionados do bot, a CLI do BF pode ser usada como parte dos pipelines de CI/CD (integração contínua e implantação contínua). Conforme você cria seu bot, também pode ser necessário integrar os serviços de IA como o LUIS para reconhecimento vocal, o QnA Maker para seu bot responder a perguntas simples em um formato de Perguntas e Respostas e mais. Para integrar o serviço de IA em seu bot, use:

* O comando [`bf luis`](bf-cli-reference.md#bf-luis) para trabalhar com arquivos de recursos **.lu** do LUIS e gerenciar modelos do LUIS. Ele também pode gerar um código-fonte (C# ou JavaScript) correspondente.
* A [ferramenta de APIs do LUIS](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS/readme.md) para implantar os arquivos locais, treinar, testar e publicá-las como modelos de Reconhecimento Vocal no serviço LUIS.
* O comando [`bf qnamaker`](bf-cli-reference.md#bf-qnamaker) para trabalhar com as bases de dados de conhecimento do QnA Maker. Ele pode criar e gerenciar ativos de QnA Maker localmente e no serviço do QnA Maker.

* Consulte a [documentação](https://github.com/microsoft/botframework-cli/tree/master/packages/lu/README.md) da biblioteca lu para saber como trabalhar com formatos de arquivo **.lu** e **.qna**.

À medida que seu bot se torna mais sofisticado, use a ferramenta de CLI [Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) para criar, avaliar e expedir intenção entre vários módulos LUIS e bases de dados de conhecimento do QnA Maker.

Para testar e refinar seu bot, você pode usar o novo [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases). O Emulator permite que você teste e depure seus bots no computador local ou na nuvem.

Durante os estágios iniciais de design, talvez convenha criar conversas fictícias entre o usuário e o bot para os cenários específicos aos quais seu bot dará suporte. Use o comando [`bf chatdown`](bf-cli-reference.md#bf-chatdown) para criar arquivos **.chat** fictícios de conversa, convertê-los em transcrições avançadas e exibir as conversas no Emulator.

Por fim, com a [CLI do Azure](https://github.com/microsoft/botframework-cli/blob/master/AzureCli.md) (comando `az bot`), você pode criar, baixar, publicar e configurar canais com o [Serviço de Bot do Azure](https://azure.microsoft.com/services/bot-service/). É um plug-in que estende a funcionalidade da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para gerenciar os ativos do Serviço de Bot do Azure.

## <a name="privacy-and-instrumentation"></a>Privacidade e instrumentação
A CLI do BF contém opções de instrumentação criadas para nos ajudar a melhorar a ferramenta com base em padrões de uso **anônimos**. __Ela está desabilitada, recusada por padrão__. Se você a aceitar, a Microsoft coletará alguns dados de uso:

* Chamadas do grupo de comandos
* Sinalizadores usados, **excluindo** valores específicos. Por exemplo, par ao parâmetro `--folder:name`, apenas o uso de `--folder` é coletado, o nome da pasta, não.

Para modificar o comportamento da coleta de dados, use o comando [`bf config`](bf-cli-reference.md#bf-config).

Consulte a [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement) para obter mais detalhes.  

## <a name="issues-and-feature-requests"></a>Problemas e solicitações de recursos
- Você pode enviar problemas e solicitações de recursos [aqui](https://github.com/microsoft/botframework-cli/issues).
- Você pode encontrar problemas conhecidos [aqui](https://github.com/microsoft/botframework-cli/labels/known-issues).

## <a name="next-steps"></a>Próximas etapas
- [Referência da CLI do BF](bf-cli-reference.md)
