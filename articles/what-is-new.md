---
title: Novidades – Serviço de Bot
description: Conheça as novidades do Bot Framework.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/18/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5c35e70fa09cbfde025266470a9fce6c59255b7b
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143945"
---
# <a name="whats-new-may-2020"></a>Novidades de maio de 2020

[!INCLUDE[applies-to](includes/applies-to.md)]

O SDK do Bot Framework v4 é um [SDK open-source](https://github.com/microsoft/botframework-sdk/#readme) que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Versão |[4.9.1 (GA)][1] | [4.9.0 (GA)][2] | [4.9.0 (GA)][3] | [4.6 Versão preliminar][3a]|
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11a] | | 

Bem-vindo à versão de maio de 2020 do SDK do Bot Framework. Há uma série de atualizações nessa versão que esperamos que você goste; alguns dos principais destaques incluem:

* [skills](#skills) – os skills agora dão suporte a caixas de diálogo adaptáveis e a todos os tipos de atividade e têm suporte aprimorado para SSO e OAuth. Agora, o manifesto de skills v2.1 está em disponibilidade geral.  Também adicionamos suporte do Bot Framework Composer para criação e consumo de skills.   
* [Microsoft Teams](#microsoft-teams) – melhorias no suporte da API do Microsoft Teams, incluindo suporte em Java! 
* [Telemetria de bot](#bot-telemetry) – mapeamento de caixa de diálogo para eventos de exibição da página do Azure AppInsights.
* [Caixas de diálogo adaptáveis](#adaptive-dialogs) – um sistema de caixa de diálogo mais flexível e orientado por eventos para implementar padrões de conversação de vários turnos. 
* [Ferramentas da CLI para caixas de diálogo adaptáveis](#cli-tools-for-adaptive-dialogs) – nova capacidade de mesclar e validar ativos de esquema adaptáveis.
* [Geração de Linguagem](#language-generation) – adicione respostas com personalidade e elementos de linguagem às suas conversas de bot. 
* [Expressões Adaptáveis](#adaptive-expressions) – use expressões com reconhecimento de bot para reagir à entrada do usuário e à funcionalidade de bot da unidade.
* [Melhorias de Autenticação](#authentication-improvements) – SSO entre bots e skills e melhorias na autenticação X.509.
* [Caixas de diálogo geradas](#generated-dialogs---early-preview) (versão prévia antecipada) – crie automaticamente ativos robustos do Bot Framework Composer a partir de JSON ou Esquema JSON que aproveitem as caixas de diálogo adaptáveis.
* [Depurador do VS Code para caixas de diálogo adaptáveis](#vs-code-debugger---early-preview) (versão preliminar antecipada) – crie e valide documentos .lu e .lg e depure caixas de diálogo adaptáveis definidas declarativamente.
* [Bot Framework Composer](#bot-framework-composer) – uma tela de criação visual para desenvolvedores e equipes multidisciplinares criarem bots.

**Participantes do Programa Windows Insider**: deseja experimentar novos recursos assim que possível? Você pode baixar o build noturno para Participantes do Programa Windows Insider [[C#](https://github.com/microsoft/botbuilder-dotnet/blob/master/UsingMyGet.md)] [[JS](https://github.com/microsoft/botbuilder-js/blob/master/UsingMyGet.md)] [[Python](https://github.com/microsoft/botbuilder-python/blob/master/UsingTestPyPI.md)] [[CLI](https://github.com/Microsoft/botframework-cli#nightly-builds)] e experimentar as atualizações mais recentes assim que estiverem disponíveis. E, para obter as notícias, atualizações e conteúdo mais recentes sobre o Bot Framework, siga-nos no Twitter @msbotframework!

## <a name="skills"></a>Habilidades 
Os [skills](v4sdk/skills-conceptual.md) foram atualizados para funcionar com caixas de diálogo adaptáveis, e as caixas de diálogo adaptáveis e tradicionais agora aceitarão todos os tipos de atividades.
 
O esquema de manifesto de skill foi atualizado para a [versão 2.1](https://github.com/microsoft/botframework-sdk/tree/master/schemas/skills). Os aprimoramentos nessa versão incluem a capacidade de declarar e compartilhar seus modelos de linguagem e definir qualquer tipo de atividade que seu skill possa receber.
 
Essa versão também inclui melhorias de autenticação com skills, incluindo o uso de SSO com caixa de diálogo e OAuth sem a necessidade de um código mágico no WebChat e DirectLine.

## <a name="microsoft-teams"></a>Equipes da Microsoft
Continuamos a nos concentrar em garantir que todas as APIs específicas do Teams tenham suporte total em nossos SDKs. Essa versão dá suporte completo para as APIs do Microsoft Teams na versão prévia do [SDK do Java](https://github.com/microsoft/botbuilder-java), incluindo [exemplos](https://github.com/microsoft/botbuilder-java/tree/master/samples).
 
O evento `OnTeamsMemberAdded` no manipulador de atividades foi atualizado para usar o ponto de extremidade obter membro único nos bastidores, o que deve reduzir significativamente a latência e a confiabilidade desse evento em equipes grandes.
 
O objeto `TeamsChannelAccount` foi atualizado para incluir `userRole` (de proprietário, membro ou convidado) e `tenantId` (para a ID de locatário do usuário).

## <a name="bot-telemetry"></a>Telemetria de bot 
Os bots agora capturam eventos de Exibição de Página, nativos do Application Insights, sempre que uma caixa de diálogo é iniciada. Isso permite que você use o painel de Fluxos dos Usuários no Application Insights para ver como os usuários passam pelo bot, entre caixas de diálogo e por onde eles saem.

![Telemetria no AppInsights](https://raw.githubusercontent.com/microsoft/botframework-sdk/master/docs/media/UserFlowsAppInsights.jpg?raw=true)

## <a name="adaptive-dialogs"></a>Caixas de Diálogo Adaptáveis 
Também estamos empolgados em tornar as [caixas de diálogo adaptáveis](v4sdk/bot-builder-adaptive-dialog-introduction.md) disponíveis ao público geral no C# e como uma versão prévia no JavaScript! 

As caixas de diálogo adaptáveis, que sustentam o design da caixa de diálogo e os recursos de criação de gerenciamento encontrados no Bot Framework Composer, permitem que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base no contexto e nos eventos. Isso é especialmente útil ao lidar com requisitos de conversa mais sofisticados, como alternâncias de contexto e interrupções.  Os skills do Bot Framework agora podem também aproveitar as caixas de diálogo adaptáveis.

As caixas de diálogo adaptáveis agora também dão suporte à Telemetria. Os dados das caixas de diálogo adaptáveis, incluindo gatilhos, ações e reconhecedores, agora fluem para sua instância do Azure Application Insights.

## <a name="cli-tools-for-adaptive-dialogs"></a>Ferramentas da CLI para caixa de diálogo adaptáveis
[Ferramentas da CLI](https://github.com/Microsoft/botframework-cli) para treinamentos de caixas de diálogo adaptáveis, Geração de Linguagem, QnaMaker e Luis Cross – nova capacidade de mesclar e validar ativos de esquema adaptáveis, aumentar arquivos qna e lu, criar/atualizar/substituir/treinar/publicar aplicativo LUIS e/ou QnA Maker e manipulação de modelos de Geração de Linguagem.

Novas ferramentas da CLI foram adicionadas para gerenciamento de caixas de diálogo adaptáveis.
- [bf-dialog](https://github.com/microsoft/botframework-cli/tree/master/packages/dialog#relevant-docs) dá suporte à mesclagem de arquivos de esquema de caixa de diálogo e verificação da exatidão do formato de arquivo.
- [bf-luis](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#relevant-docs) adiciona comandos para aumentar os arquivos lu e criar/atualizar/substituir/treinar/publicar LUIS
- [bf-qnamaker](https://github.com/microsoft/botframework-cli/tree/master/packages/qnamaker#relevant-docs) adiciona comandos para aumentar os arquivos qna e criar/atualizar/substituir/treinar/publicar QnAMaker
- [bf-lg](https://github.com/microsoft/botframework-cli/tree/master/packages/lg#relevant-docs) Analisar, agrupar, expandir e traduzir arquivos LG.

## <a name="language-generation"></a>Geração de Linguagem

A LG tem GA (em disponibilidade geral) nas plataformas C# e JS. 

A [LG (Geração de Linguagem)](v4sdk/bot-builder-concept-language-generation.md) permite que você defina diversas variações de uma frase, execute expressões simples com base no contexto e confira a memória de conversação. No núcleo da geração de linguagem fica a expansão de modelo e a substituição de entidades. Você pode fornecer uma das variações de expansão, bem como a expansão condicional de um modelo. A saída da geração de linguagem pode ser uma cadeia de caracteres de texto simples ou uma resposta de várias linhas ou uma carga de objeto complexo que uma camada acima da geração de linguagem usará para construir uma atividade completa. O Bot Framework Composer dá suporte nativo à geração de linguagem para produzir atividades de saída usando o sistema de modelagem da LG.

Você pode usar a Geração de Linguagem para:
* Obter um tom de voz com personalidade coerente para o bot.
* Separar a lógica de negócios da apresentação.
* Incluir variações e composição sofisticada para qualquer uma das respostas do bot.
* Criar cartões, ações sugeridas e anexos usando um modelo de resposta estruturado.

A Geração de Linguagem é obtida por meio de:

* Um arquivo .lg com base em Markdown que contém os modelos e sua composição.
Acesso completo à memória do bot atual para que você possa associar dados da linguagem ao estado da memória.
* Bibliotecas de runtime e analisador que ajudam a obter a resolução de runtime.

## <a name="adaptive-expressions"></a>Expressões Adaptáveis
As [Expressões Adaptáveis](v4sdk/bot-builder-concept-adaptive-expressions.md) têm GA (disponibilidade geral) nas plataformas C# e JS. 

Os bots usam expressões para avaliar o resultado de uma condição com base nas informações de runtime disponíveis na memória para a caixa de diálogo ou o sistema de Geração de Linguagem. Essas avaliações determinam como o bot reage à entrada do usuário e a outros fatores que afetam a funcionalidade de bot.

As expressões adaptáveis foram criadas para atender a essa necessidade básica, bem como fornecer uma linguagem de expressão adaptável que possa ser usada com o SDK do Bot Framework e outros componentes de IA de conversação, como o [Bot Framework Composer](https:/docs.microsoft.com/composer/), Geração de idioma, caixas de diálogo adaptáveis e [Cartões Adaptáveis](https://docs.microsoft.com/adaptive-cards/).

Uma expressão adaptável pode conter um ou mais valores explícitos, funções predefinidas ou funções personalizadas. Os consumidores de expressões adaptáveis também têm a capacidade de injetar funções adicionais com suporte. Por exemplo, todos os modelos de geração de linguagem estão disponíveis como funções, bem como funções adicionais que só estão disponíveis no uso do componente de expressões adaptáveis.

## <a name="authentication-improvements"></a>Melhorias de autenticação
Adicionamos suporte para logon único usando Respostas Esperadas. Isso se aplica ao SSO executado entre um par de bots: host e um skill.

Para a identificação do bot, adicionamos a capacidade de especificar o parâmetro `sendx5c` para autenticação de certificado. Esse recurso foi solicitado pelos clientes e permite mais flexibilidade ao usar a autenticação de certificado. 

Há suporte para Nuvens Soberanas adicionais.

## <a name="generated-dialogs---early-preview"></a>Caixas de diálogo geradas – Versão prévia antecipada

O Bot Framework tem uma vasta coleção de blocos de construção de conversação, mas a criação de um bot com o qual uma conversa pareça natural requer a compreensão e a coordenação da compreensão da linguagem, da geração de linguagem e do gerenciamento de caixa de diálogo. Para simplificar esse processo e capturar as melhores práticas, criamos o plug-in [bf-generate](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/generation/generator/README.md) para a [ferramenta da CLI do BotFramework](https://github.com/microsoft/botframework-cli). As caixas de diálogo geradas utilizam caixas de diálogo adaptáveis controladas por eventos com um conjunto de recursos avançado e em evolução, incluindo:

- Manipular múltiplas respostas fora de ordem para propriedades simples e de matriz.
- Adicionar, remover, limpar e mostrar propriedades.
- Suporte para escolher entre valores de entidade ambíguos e mapeamentos de propriedade de entidade.
- Reconhecimento e mapeamento para todas as entidades predefinidas do LUIS.
- Função Ajuda, incluindo a ajuda automática em várias repetições.
- Cancelar
- Confirmação

## <a name="vs-code-debugger---early-preview"></a>Depurador do VS Code – Versão prévia antecipada
As [ferramentas adaptáveis](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-tool) são uma nova extensão de código do Visual Studio que você pode usar para criar/validar documentos .lu e .lg, bem como depurar caixas de diálogo adaptáveis definidas declarativamente. Essa extensão fornece recursos avançados de criação e edição para os formatos de arquivo .lu e .lg, incluindo realce de sintaxe, sugestão automática e preenchimento automático.

Prevemos a adição de uma versão prévia antecipada ao VS Marketplace logo após essa versão. 

## <a name="bot-builder-community"></a>Comunidade do Bot Builder
Durante essa versão, a Comunidade do Bot Builder aumentou ainda mais a exigência adicionando mais recursos, mais adaptadores e corrigindo mais bugs.

1. Um [Adaptador da Alexa](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.Alexa) em C# revisado e um [Adaptador do Google Home]() reconstruído a partir do zero, começando com a Alexa, para permitir que os adaptadores sejam consumidos pelo Serviço de Bot do Azure e disponibilizados como canais. Os aprimoramentos incluem melhor mapeamento de tipos de atividade nativa, renderização de markdown e suporte aprimorados para cenários mais complexos (como a mesclagem de várias atividades de saída).

2. Um novo [adaptador de zoom](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.Zoom) em C# que dá suporte aos recursos de zoom 1:1 e canal de bate-papo e os converte em tipos de atividade BF nativos. Com ele, você pode assinar qualquer evento com suporte com zoom, com suporte total para mensagens interativas de zoom e modelos de mensagens avançadas. (O adaptador traduz eventos de zoom em atividades de evento BF.)

3. Um [Adaptador do RingCentral](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.RingCentral). O adaptador do [RingCentral](https://www.ringcentral.com/) Engage permite que você adicione um ponto de extremidade extra ao bot para integração da [RingCentral Engage Digital Platform](https://www.ringcentral.com/digital-customer-engagement.html). O ponto de extremidade RingCentral pode ser usado em conjunto com outros canais, o que significa que, por exemplo, você pode ter um bot exposto em canais prontos para uso, como o Facebook e o Teams, mas também integrado como um [SDK do RingCentral Digital Source](https://support.ringcentral.com/s/article/RingCentral-Engage-Digital-Introduction?language=en_US) ao RingCentral.

## <a name="bot-framework-composer"></a>Bot Framework Composer

O [Bot Framework Composer](https://docs.microsoft.com/composer/) está em GA (disponibilidade geral) nas plataformas [Windows](https://aka.ms/bf-composer-download-win) | [macOS](https://aka.ms/bf-composer-download-mac) | [Linux](https://aka.ms/bf-composer-download-linux).

O Bot Framework Composer é uma tela de criação visual para desenvolvedores e equipes multidisciplinares criarem bots. É um aplicativo de conversa de software livre baseado no SDK do Microsoft Bot Framework. No Composer, você encontrará tudo de que precisa para criar uma experiência de conversa sofisticada:

* Uma tela de edição visual para o fluxo de conversa.
* Edição contextualizada para reconhecimento vocal.
* Ferramentas para treinar e gerenciar os componentes de reconhecimento vocal (como LUIS e QnA Maker).
* Sistemas avançados de criação de modelos e geração de linguagem.
* Um executável de tempo de execução de bot pronto para uso.

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[11a]:https://aka.ms/python-sample-repo

## <a name="additional-information"></a>Informações adicionais
- Você pode ver os comunicados anteriores [aqui](what-is-new-archive.md).
