---
title: Novidades (arquivo) – Serviço de Bot
description: Saiba mais sobre melhorias e novos recursos em versões sucessivas de 2019 do SDK do bot Framework, incluindo novas funcionalidades em habilidades, equipes e outras áreas.
keywords: bot framework, serviço de bot do azure
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 11/13/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9fccf343635e4e7f4b1a545652688ec37dca3618
ms.sourcegitcommit: 22a92bc07c85f899b3b1dca4f19421bc302db23f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100271945"
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

**Participantes do Programa Windows Insider**: deseja experimentar novos recursos assim que possível? Você pode baixar o build noturno para Participantes do Programa Windows Insider [[C#](https://github.com/microsoft/botbuilder-dotnet/blob/main/UsingMyGet.md)] [[JS](https://github.com/microsoft/botbuilder-js/blob/main/UsingMyGet.md)] [[Python](https://github.com/microsoft/botbuilder-python/blob/main/UsingTestPyPI.md)] [[CLI](https://github.com/Microsoft/botframework-cli#nightly-builds)] e experimentar as atualizações mais recentes assim que estiverem disponíveis. E, para obter as notícias, atualizações e conteúdo mais recentes sobre o Bot Framework, siga-nos no Twitter @msbotframework!

### <a name="documentation"></a>Documentação

Após os comentários dos clientes e da equipe de suporte do bot Framework, vários documentos foram criados ou atualizados.Eles são úteis para fornecer respostas e informações relacionadas a problemas recorrentes de desenvolvedores de bot.

- Documentação de comentário de código expandida nos repositórios do SDK.
- LEIAme aprimorados nos exemplos e repositórios do SDK.
- Documentos novos e atualizados que abordam problemas de desenvolvedores de bot recorrentes:
  - Adição de uma página de Hub do serviço de bot do Azure] (/Azure/bot-Service/) que vincula ao bot Framework Composer e à documentação do SDK do bot Framework.
  - Atualizações para [geração de idioma](v4sdk/bot-builder-concept-language-generation.md), [expressões adaptáveis](v4sdk/bot-builder-concept-adaptive-expressions.md)e artigos de [caixa de diálogo adaptáveis](v4sdk/bot-builder-adaptive-dialog-introduction.md) .
  - Atualizações para a documentação do [bot Framework Composer](/composer/) .
  - Correções de problemas e aprimoramentos gerais de documentação para autenticação, habilidades, canais e outros tópicos.

### <a name="customer-supportability"></a>Suporte ao cliente

Os desenvolvedores que usam o Microsoft bot Framework têm muitos [recursos](bot-service-resources-links-help.md) para obter ajuda. Ferramentas internas foram aprimoradas para aumentar a capacidade de resposta da equipe de engenharia em áreas de interesse dos desenvolvedores.

- Criação de bots internos e ferramentas aprimoradas para atendimento ao cliente.
- Análise aprimorada de tendências em solicitações e problemas de recursos relatados pelo cliente.
- Coordenação de rótulos entre repositórios do GitHub.

### <a name="customer-requests"></a>Solicitações de clientes

- Eventos adicionais do ciclo de vida do canal de equipes.
- Integração de Application Insights aprimorada.
- Coordenação de rótulos entre repositórios do GitHub.
- Adição de uma localidade à atividade de atualização de conversa.
- Adicionado suporte de texto ALT para as ações de cartão para imagens em botões.
- O manipulador de habilidades foi atualizado para retornar um objeto de resposta de recurso.
- Suporte incluído para a versão mais recente do armazenamento de BLOBs do Azure.
- Melhorias na caixa de diálogo de prompt do OAuth.
- Várias correções de bugs e aprimoramentos de telemetria.

### <a name="code-quality"></a>Qualidade do código

- Aplicação de regras de formato e estilo de código.
- Cobertura e qualidade de código de teste de unidade aprimoradas.
- Maior criação de perfil da base de código.
- Arquivos Swagger da API REST unificados em repositórios do SDK. Introduziu uma versão para os arquivos.
- Adicionou um padrão de objeto de configurações para adaptadores C#.
- Adicionada a vigilância de dependência no SDK do JavaScript.
- Foram adicionados testes de integração para cartões adaptáveis e JavaScript de linha direta.

### <a name="new-sdk-features"></a>Novos recursos do SDK

- [Orchestrator (versão prévia)](https://github.com/microsoft/BotBuilder-Samples/blob/main/experimental/orchestrator/README.md): uma solução baseada em transformador que é executada localmente com seu bot para expedição em uma ou mais habilidades de bot Builder, aplicativos Luis ou bases de dados de conhecimento QnA Maker.
- [Bot Builder filas do Azure (visualização em C#)](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure.Queues): melhor integração com as filas do Azure e a caixa de diálogo _continuar conversa adiante_ .
- [Bot Builder BLOBs do Azure (visualização em C#)](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure.Blobs): novas implementações do armazenamento de BLOBs do Azure. Essa biblioteca é uma substituição para a implementação mais antiga do armazenamento de BLOBs do Azure.

### <a name="other-improvements"></a>Outros aprimoramentos

- Melhorias contínuas no suporte à API do Microsoft Team.
- Ferramentas da CLI do bot Framework adicionadas `lg` como um plug-in principal e incluíam outros aprimoramentos gerais de ferramentas.
- Foram atualizados os arquivos LEIAme nos exemplos e foram adicionados novos exemplos de typescript de equipes.
- O Composer melhorou o suporte para habilidades e integração aprimorada para serviços cognitivas.
- O Web Chat adicionou muitas melhorias de acessibilidade.
- O emulador adicionou correções de bugs e atualizações.

## <a name="whats-new-may-2020"></a>Novidades de maio de 2020

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O SDK do Bot Framework v4 é um [SDK open-source](https://github.com/microsoft/botframework-sdk/#readme) que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|Item | C#  | JS  | Python | Java
|:----|:---:|:---:|:------:|:-----:
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

### <a name="skills"></a>Habilidades
Os [skills](v4sdk/skills-conceptual.md) foram atualizados para funcionar com caixas de diálogo adaptáveis, e as caixas de diálogo adaptáveis e tradicionais agora aceitarão todos os tipos de atividades.

O esquema de manifesto de skill foi atualizado para a [versão 2.1](https://github.com/microsoft/botframework-sdk/tree/master/schemas/skills). Os aprimoramentos nessa versão incluem a capacidade de declarar e compartilhar seus modelos de linguagem e definir qualquer tipo de atividade que seu skill possa receber.

Essa versão também inclui melhorias de autenticação com skills, incluindo o uso de SSO com caixa de diálogo e OAuth sem a necessidade de um código mágico no WebChat e DirectLine.

### <a name="microsoft-teams"></a>Equipes da Microsoft
Continuamos a nos concentrar em garantir que todas as APIs específicas do Teams tenham suporte total em nossos SDKs. Essa versão dá suporte completo para as APIs do Microsoft Teams na versão prévia do [SDK do Java](https://github.com/microsoft/botbuilder-java), incluindo [exemplos](https://github.com/microsoft/botbuilder-java/tree/master/samples).

O evento `OnTeamsMemberAdded` no manipulador de atividades foi atualizado para usar o ponto de extremidade obter membro único nos bastidores, o que deve reduzir significativamente a latência e a confiabilidade desse evento em equipes grandes.

O objeto `TeamsChannelAccount` foi atualizado para incluir `userRole` (de proprietário, membro ou convidado) e `tenantId` (para a ID de locatário do usuário).

### <a name="bot-telemetry"></a>Telemetria de bot
Os bots agora capturam eventos de Exibição de Página, nativos do Application Insights, sempre que uma caixa de diálogo é iniciada. Isso permite que você use o painel de Fluxos dos Usuários no Application Insights para ver como os usuários passam pelo bot, entre caixas de diálogo e por onde eles saem.

![Telemetria no AppInsights](https://raw.githubusercontent.com/microsoft/botframework-sdk/master/docs/media/UserFlowsAppInsights.jpg?raw=true)

### <a name="adaptive-dialogs"></a>Caixas de Diálogo Adaptáveis
Também estamos empolgados em tornar [caixas de diálogo adaptáveis](v4sdk/bot-builder-adaptive-dialog-introduction.md) geralmente disponíveis em C# e como uma versão de visualização em JavaScript!

As caixas de diálogo adaptáveis, que sustentam o design da caixa de diálogo e os recursos de criação de gerenciamento encontrados no Bot Framework Composer, permitem que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base no contexto e nos eventos. Isso é especialmente útil ao lidar com requisitos de conversa mais sofisticados, como alternâncias de contexto e interrupções.  Os skills do Bot Framework agora podem também aproveitar as caixas de diálogo adaptáveis.

As caixas de diálogo adaptáveis agora também dão suporte à Telemetria. Os dados das caixas de diálogo adaptáveis, incluindo gatilhos, ações e reconhecedores, agora fluem para sua instância do Azure Application Insights.

### <a name="cli-tools-for-adaptive-dialogs"></a>Ferramentas da CLI para caixa de diálogo adaptáveis
[Ferramentas da CLI](https://github.com/Microsoft/botframework-cli) para treinamentos de caixas de diálogo adaptáveis, Geração de Linguagem, QnaMaker e Luis Cross – nova capacidade de mesclar e validar ativos de esquema adaptáveis, aumentar arquivos qna e lu, criar/atualizar/substituir/treinar/publicar aplicativo LUIS e/ou QnA Maker e manipulação de modelos de Geração de Linguagem.

Novas ferramentas da CLI foram adicionadas para gerenciamento de caixas de diálogo adaptáveis.
- [bf-dialog](https://github.com/microsoft/botframework-cli/tree/master/packages/dialog#relevant-docs) dá suporte à mesclagem de arquivos de esquema de caixa de diálogo e verificação da exatidão do formato de arquivo.
- [bf-luis](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#relevant-docs) adiciona comandos para aumentar os arquivos lu e criar/atualizar/substituir/treinar/publicar LUIS
- [bf-qnamaker](https://github.com/microsoft/botframework-cli/tree/master/packages/qnamaker#relevant-docs) adiciona comandos para aumentar os arquivos qna e criar/atualizar/substituir/treinar/publicar QnAMaker
- [bf-lg](https://github.com/microsoft/botframework-cli/tree/master/packages/lg#relevant-docs) Analisar, agrupar, expandir e traduzir arquivos LG.

### <a name="language-generation"></a>Geração de Linguagem

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

### <a name="adaptive-expressions"></a>Expressões Adaptáveis
As [Expressões Adaptáveis](v4sdk/bot-builder-concept-adaptive-expressions.md) têm GA (disponibilidade geral) nas plataformas C# e JS.

Os bots usam expressões para avaliar o resultado de uma condição com base nas informações de runtime disponíveis na memória para a caixa de diálogo ou o sistema de Geração de Linguagem. Essas avaliações determinam como o bot reage à entrada do usuário e a outros fatores que afetam a funcionalidade de bot.

As expressões adaptáveis foram criadas para atender a essa necessidade básica, bem como fornecer uma linguagem de expressão adaptável que possa ser usada com o SDK do Bot Framework e outros componentes de IA de conversação, como o [Bot Framework Composer](https:/docs.microsoft.com/composer/), Geração de idioma, caixas de diálogo adaptáveis e [Cartões Adaptáveis](https://docs.microsoft.com/adaptive-cards/).

Uma expressão adaptável pode conter um ou mais valores explícitos, funções predefinidas ou funções personalizadas. Os consumidores de expressões adaptáveis também têm a capacidade de injetar funções adicionais com suporte. Por exemplo, todos os modelos de geração de linguagem estão disponíveis como funções, bem como funções adicionais que só estão disponíveis no uso do componente de expressões adaptáveis.

### <a name="authentication-improvements"></a>Melhorias de autenticação
Adicionamos suporte para logon único usando Respostas Esperadas. Isso se aplica ao SSO executado entre um par de bots: host e um skill.

Para a identificação do bot, adicionamos a capacidade de especificar o parâmetro `sendx5c` para autenticação de certificado. Esse recurso foi solicitado pelos clientes e permite mais flexibilidade ao usar a autenticação de certificado.

Há suporte para Nuvens Soberanas adicionais.

### <a name="generated-dialogs---early-preview"></a>Caixas de diálogo geradas – Versão prévia antecipada

O Bot Framework tem uma vasta coleção de blocos de construção de conversação, mas a criação de um bot com o qual uma conversa pareça natural requer a compreensão e a coordenação da compreensão da linguagem, da geração de linguagem e do gerenciamento de caixa de diálogo. Para simplificar esse processo e capturar as melhores práticas, criamos o plug-in [bf-generate](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/generation/generator/README.md) para a [ferramenta da CLI do BotFramework](https://github.com/microsoft/botframework-cli). As caixas de diálogo geradas utilizam caixas de diálogo adaptáveis controladas por eventos com um conjunto de recursos avançado e em evolução, incluindo:

- Manipular múltiplas respostas fora de ordem para propriedades simples e de matriz.
- Adicionar, remover, limpar e mostrar propriedades.
- Suporte para escolher entre valores de entidade ambíguos e mapeamentos de propriedade de entidade.
- Reconhecimento e mapeamento para todas as entidades predefinidas do LUIS.
- Função Ajuda, incluindo a ajuda automática em várias repetições.
- Cancelar
- Confirmação

### <a name="vs-code-debugger---early-preview"></a>Depurador do VS Code – Versão prévia antecipada
As [ferramentas adaptáveis](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-tool) são uma nova extensão de código do Visual Studio que você pode usar para criar/validar documentos .lu e .lg, bem como depurar caixas de diálogo adaptáveis definidas declarativamente. Essa extensão fornece recursos avançados de criação e edição para os formatos de arquivo .lu e .lg, incluindo realce de sintaxe, sugestão automática e preenchimento automático.

Prevemos a adição de uma versão prévia antecipada ao VS Marketplace logo após essa versão.

### <a name="bot-builder-community"></a>Comunidade do Bot Builder
Durante essa versão, a Comunidade do Bot Builder aumentou ainda mais a exigência adicionando mais recursos, mais adaptadores e corrigindo mais bugs.

1. Um [Adaptador da Alexa](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.Alexa) em C# revisado e um [Adaptador do Google Home]() reconstruído a partir do zero, começando com a Alexa, para permitir que os adaptadores sejam consumidos pelo Serviço de Bot do Azure e disponibilizados como canais. Os aprimoramentos incluem melhor mapeamento de tipos de atividade nativa, renderização de markdown e suporte aprimorados para cenários mais complexos (como a mesclagem de várias atividades de saída).

2. Um novo [adaptador de zoom](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.Zoom) em C# que dá suporte aos recursos de zoom 1:1 e canal de bate-papo e os converte em tipos de atividade BF nativos. Com ele, você pode assinar qualquer evento com suporte com zoom, com suporte total para mensagens interativas de zoom e modelos de mensagens avançadas. (O adaptador traduz eventos de zoom em atividades de evento BF.)

3. Um [Adaptador do RingCentral](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Adapters.RingCentral). O adaptador do [RingCentral](https://www.ringcentral.com/) Engage permite que você adicione um ponto de extremidade extra ao bot para integração da [RingCentral Engage Digital Platform](https://www.ringcentral.com/digital-customer-engagement.html). O ponto de extremidade RingCentral pode ser usado em conjunto com outros canais, o que significa que, por exemplo, você pode ter um bot exposto em canais prontos para uso, como o Facebook e o Teams, mas também integrado como um [SDK do RingCentral Digital Source](https://support.ringcentral.com/s/article/RingCentral-Engage-Digital-Introduction?language=en_US) ao RingCentral.

### <a name="bot-framework-composer"></a>Bot Framework Composer

O [Bot Framework Composer](https://docs.microsoft.com/composer/) está em GA (disponibilidade geral) nas plataformas [Windows](https://aka.ms/composer-windows-download) | [macOS](https://aka.ms/composer-mac-download) | [Linux](https://aka.ms/composer-linux-download).

O Bot Framework Composer é uma tela de criação visual para desenvolvedores e equipes multidisciplinares criarem bots. É um aplicativo de conversa de software livre baseado no SDK do Microsoft Bot Framework. No Composer, você encontrará tudo de que precisa para criar uma experiência de conversa sofisticada:

* Uma tela de edição visual para o fluxo de conversa.
* Edição contextualizada para reconhecimento vocal.
* Ferramentas para treinar e gerenciar os componentes de reconhecimento vocal (como LUIS e QnA Maker).
* Sistemas avançados de criação de modelos e geração de linguagem.
* Um executável de tempo de execução de bot pronto para uso.

## <a name="whats-new-november-2019"></a>Novidades em novembro de 2019

O SDK do Bot Framework v4 é um [SDK open-source](https://github.com/microsoft/botframework-sdk/#readme) que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|Item | C#  | JS  | Python | Java  |
|:----|:---:|:---:|:------:|:-----:|
|Versão |[4.6 GA][1] | [4.6 GA][2] | [Beta 4][3] | [Versão prévia 3][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | | |

#### <a name="bot-framework-sdk-for-microsoft-teams-ga"></a>SDK do Bot Framework para Microsoft Teams (GA)

A versão SDK do Bot Framework v4.6 integra totalmente o suporte para a criação de bots do Teams, permitindo que os usuários os usem em conversas de chat de canal ou grupo. Ao adicionar um bot a uma equipe ou chat, todos os usuários da conversa podem aproveitar a funcionalidade do bot diretamente na conversa.  [[Docs](https://docs.microsoft.com/azure/bot-service/bot-builder-basics-teams)]

#### <a name="bot-framework-for-power-virtual-agent-preview"></a>Bot Framework para Power Virtual Agent (Versão prévia)

O Power Virtual Agent foi projetado para permitir que os usuários corporativos criem bots dentro de uma experiência SaaS de criação de bots baseada em interface do usuário, sem precisar codificar nem gerenciar serviços de IA específicos.
Os Power Virtual Agents podem ser estendidos com o Microsoft Bot Framework, permitindo que os desenvolvedores e usuários de negócios colaborem na criação de bots para suas organizações. [[Docs](https://docs.microsoft.com/dynamics365/ai/customer-service-virtual-agent/overview)]


#### <a name="bot-framework-sdk-for-skills-preview"></a>SDK do Bot Framework para Habilidades (Versão prévia)

- **Habilidades para bots**: crie habilidades de conversa reutilizáveis para adicionar funcionalidade a um bot. Aproveite as habilidades predefinidas, como as habilidades Calendário, Email, Tarefa, Ponto de Interesse, Automotivo, Clima e Notícias. As habilidades incluem modelos de linguagem, caixas de diálogo, QnA e o código de integração entregue para personalizar e estender, conforme necessário. [[Docs](https://microsoft.github.io/botframework-solutions/overview/skills/)]

- **Habilidades para o Power Virtual Agent – Em breve!** : Para bots criados com Power Virtual Agents, você pode criar habilidades para esses bots usando o Bot Framework e os Serviços Cognitivos do Azure sem precisar criar um bot do zero.

#### <a name="adaptive-dialogs-preview"></a>Caixas de Diálogo Adaptáveis (Versão prévia)
As Caixas de Diálogo Adaptáveis permitem que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente prático ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa. [[Docs][48] | [Exemplos de C#][49]]

#### <a name="language-generation-preview"></a>Geração de Linguagem (Versão prévia)
Com a Geração de Linguagem, os desenvolvedores podem separar a lógica usada para gerar respostas do bot, incluindo a capacidade de definir diversas variações em uma frase, executar expressões simples baseadas em contexto e consultar a memória conversacional. [[Docs][44] | [Exemplos de C#][45]]

#### <a name="common-expression-language-preview"></a>Linguagem de Expressão Comum (Versão prévia)
A Linguagem de Expressão Comum permite que você avalie o resultado de uma lógica baseada em condição em runtime. A linguagem comum pode ser usada em todos os componentes de SDK do Bot Framework e de AI conversacional, como Caixas de Diálogo Adaptáveis e Geração de Linguagem. [[Docs][40] | [API][41]]

## <a name="whats-new-july-2019"></a>Quais são as novidades? (julho de 2019)

O SDK do Bot Framework v4 é um [SDK open-source][1a] que permite aos desenvolvedores modelar e criar conversas sofisticadas usando sua linguagem de programação favorita.

Este artigo resume os principais novos recursos e aprimoramentos no Bot Framework e no Serviço de Bot do Azure.

|Item | C#  | JS  | Python |
|:----|:---:|:---:|:------:|
|. |[4.5][1] | [4.5][2] | [4.4.0b2 (versão prévia)][3] |
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11] | |

### <a name="bot-framework-channels"></a>Canais do Bot Framework

- [Direct Line Speech (versão prévia pública)](https://aka.ms/streaming-extensions) | [documentos:](directline-speech-bot.md) O Bot Framework e os Serviços de Fala da Microsoft fornecem um canal que permite fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot, usando WebSockets.

- [Extensão do Serviço de Aplicativo Direct Line (versão prévia pública)](https://portal.azure.com) | [documentos](https://aka.ms/directline-ase): Uma versão da Direct Line que permite que os clientes se conectem diretamente a bots usando a Direct Line API. Isso oferece muitos benefícios, incluindo melhor desempenho e mais isolamento. A extensão do Serviço de Aplicativo Direct Line está disponível em todos os Serviços de Aplicativo do Azure, incluindo aqueles hospedados em um Ambiente do Serviço de Aplicativo do Azure. Um Ambiente do Serviço de Aplicativo do Azure oferece isolamento e é ideal para trabalhar em uma VNet. Uma VNet permite que você crie seu próprio espaço privado no Azure e é crucial para sua rede de nuvem, pois oferece isolamento, segmentação e outros benefícios importantes.

### <a name="bot-framework-sdk"></a>SDK do Bot Framework
- [Caixa de Diálogo Adaptável (SDK v4.6 versão prévia)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme) | [documentos](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs) | [exemplos de C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore): A Caixa de Diálogo Adaptável agora permite que os desenvolvedores atualizem dinamicamente o fluxo de conversa com base em contexto e eventos. Isso é especialmente útil ao lidar com alternâncias de contexto de conversa e interrupções no meio de uma conversa.

- [SDK do Python do Bot Framework (versão prévia 2)](https://github.com/microsoft/botbuilder-python) | [exemplos](https://github.com/Microsoft/botbuilder-python/tree/master/samples): O SDK do Python agora dá suporte a OAuth, Prompts, CosmosDB e inclui todas as principais funcionalidades no SDK 4.5. Além disso, há exemplos para ajudá-lo a saber mais sobre os novos recursos do SDK.

### <a name="bot-framework-testing"></a>Testes do Bot Framework
- [Documentos](./v4sdk/unit-test-bots.md) | Pacotes de teste de unidade ([C#](https://aka.ms/nuget-botbuilder-testing) /  [JavaScript](https://aka.ms/npm-botbuilder-testing)) | [Exemplo](https://aka.ms/cs-core-test-sample)  |  em C# [Exemplo de JS](https://aka.ms/js-core-test-sample): abordar os clientes e os desenvolvedores pedem melhores ferramentas de teste, a versão de julho do SDK apresenta uma nova funcionalidade de teste de unidade. O pacote Microsoft.Bot.Builder.testing simplifica o processo de caixas de diálogo de teste de unidade em seu bot.

- [Testes de canal](https://github.com/Microsoft/BotFramework-Emulator/releases) | [docs](bot-service-debug-inspection-middleware.md):

Introduzido no Microsoft Build 2019, o Inspetor de bot é um novo recurso no emulador do bot Framework que permite depurar e testar bots em canais como equipes da Microsoft, margem de atraso e muito mais. Conforme você usa o bot em canais específicos, as mensagens serão espelhadas para o Bot Framework Emulator, no qual você poderá inspecionar os dados da mensagem que o bot tiver recebido. Além disso, é renderizado um instantâneo do estado da memória do bot em todos os turnos entre o canal e o bot.

### <a name="web-chat"></a>Chat na Web
- Com base em solicitações de clientes corporativos, adicionamos um [exemplo de Webchat](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps#single-sign-on-demo-for-enterprise-apps-using-oauth) que mostra como autorizar um usuário a acessar recursos em um aplicativo empresarial com um bot. Dois tipos de recursos são usados para demonstrar a interoperabilidade do OAuth com o Microsoft Graph e a API do GitHub.

### <a name="solutions"></a>Soluções
- [Acelerador da solução Assistente Virtual](https://github.com/Microsoft/botframework-solutions#readme): Fornece um conjunto de modelos, aceleradores de solução e habilidades para ajudar a criar experiências de conversa sofisticadas. Novo cliente de aplicativo Android para o assistente virtual que se integra com o Direct-Line Speech e o Assistente Virtual, demonstrando como um cliente de dispositivo pode interagir com seu assistente virtual e renderizar Cartões Adaptáveis. As atualizações também incluem suporte para o Direct-Line Speech e o Microsoft Teams.

- [Dynamics 365 Virtual Agent for Customer Service (versão prévia pública)](https://dynamics.microsoft.com/en-us/ai/virtual-agent-for-customer-service/): Com a versão prévia pública, você pode fornecer um atendimento ao cliente excepcional com agentes virtuais inteligentes e adaptáveis. Os especialistas em atendimento ao cliente podem criar e aprimorar bots facilmente com informações baseadas em IA.

- [Chat para Dynamics 365](https://www.powerobjects.com/powerpacks/powerchat/): O Chat para Dynamics 365 oferece vários recursos para garantir que os agentes de suporte e os usuários finais possam interagir com eficiência e permaneçam altamente produtivos. Converse ao vivo e acompanhe conversas de visitantes em seu site no Microsoft Dynamics 365.

## <a name="whats-new-may-2019"></a>Quais são as novidades? (maio de 2019)

|Item | C#  | JS  | Python | Java  |
|:----|:---:|:---:|:------:|:-----:|
|. |[4.4.3][1] | [4.4.0][2] | [4.4.0b1 (versão prévia)][3] | [4.0.0a6 (versão prévia)][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemplos |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11] | |

<a name="V4-whats-new"></a>
## <a name="bot-framework-sdk-new-in-preview"></a>SDK do Bot Framework (Novo! Em versão prévia)

- [Diálogo Adaptável][47] | [docs][48] | [exemplos em C#][49]: Os Diálogos Adaptáveis permitem aos desenvolvedores criar conversas que podem ser alteradas dinamicamente conforme o andamento da conversa.  Tradicionalmente, os desenvolvedores mapeiam o fluxo inteiro de uma conversa com antecedência, o que limita sua flexibilidade.  Os Diálogos Adaptáveis permitem que elas sejam mais flexíveis, para responder a alterações no contexto e inserir novas etapas ou subdiálogos inteiros na conversa durante sua evolução.

- [Geração de linguagem][43] | [docs][44] | [exemplos em C#][45]: geração de linguagem, que permite ao desenvolvedor extrair as cadeias de caracteres inseridas dos seus arquivos de código e de recursos e gerenciá-las por meio de um formato arquivo e do runtime de Geração de Linguagem.  A Geração de Linguagem permite que os clientes definam diversas variações de uma frase, executem expressões simples com base no contexto e consultem a memória de conversação; ao longo do tempo, isso nos permitirá criar outras funcionalidades que levarão a uma experiência de conversa mais natural.

- [Linguagem de Expressão Comum][40] | [API][41]: tanto os Diálogos Adaptáveis quanto a Geração de Linguagem usam e dependem de uma linguagem de expressão comum para alimentar conversas de bot.

## <a name="botkit"></a>Botkit
O [Botkit][100] é uma ferramenta de desenvolvedor e um SDK de criação de chatbots, aplicativos e integrações personalizadas para as principais plataformas de mensagens. Bots do Botkit: gatilhos `hear()`, perguntas `ask()` e respostas `say()`. Os desenvolvedores podem usar essa sintaxe para criar diálogos; agora compatíveis com a versão mais recente do SDK do Bot Framework.

Além disso, o Botkit traz seis adaptadores de plataforma, permitindo que os aplicativos de bot JavaScript se comuniquem diretamente com as plataformas de mensagens: [Slack][102], [Webex Teams][103], [Google Hangouts][104], [Facebook Messenger][105], [Twilio][106] e [Webchat][107].

O Botkit faz parte do Microsoft Bot Framework e foi lançado com a [licença de software livre do MIT][101]

## <a name="bot-framework-solutions-new-in-preview"></a>Soluções do Bot Framework (Novo! Em versão prévia)

O [repositório de Soluções do Bot Framework](https://github.com/Microsoft/AI#readme) é a base para um conjunto de modelos, aceleradores de soluções e habilidades para ajudar a criar experiências de conversação avançadas que soam como um assistente.

| Nome | Descrição |
|:------------|:------------|
|[**Assistente Virtual**](https://github.com/Microsoft/AI/tree/master/docs#virtual-assistant) | Os clientes precisam realmente fornecer um assistente de conversação adaptado à sua marca, personalizado para seus usuários e disponibilizado em uma ampla variedade de telas e dispositivos. <br/><br/> O modelo empresarial simplifica bastante a criação de um projeto novo do bot, que inclui: intenções de conversação básicas, integração com o Dispatch, QnA Maker, Application Insights e uma implantação automatizada.|
|[**Habilidades**](https://github.com/Microsoft/AI/blob/master/docs/overview/skills.md)| Os desenvolvedores podem compor experiências de conversação combinando recursos de conversa reutilizáveis, conhecidos como Habilidades. As próprias Habilidades são Bots, chamados remotamente, e um modelo de desenvolvedor (.NET, TS) está disponível para facilitar a criação de novas Habilidades.
|[**Analytics**](https://github.com/Microsoft/AI/blob/master/docs/readme.md#analytics)| Receba informações importantes sobre a integridade e o comportamento do bot com as soluções de análise de ia de conversação. Reveja a telemetria disponível, as consultas de Application Insights de exemplo e Power BI Dashboards para entender toda a amplitude das conversas do bot com os usuários. |

## <a name="azure-bot-service"></a>Serviço de Bot do Azure
O Serviço de Bot do Azure permite que você hospede bots inteligentes e de nível empresarial com total controle e propriedade sobre seus dados.
Os desenvolvedores podem registrar e conectar seus bots aos usuários no Microsoft Teams, Web Chat e muito mais. [Azure][27]  |  [docs][28] | [conectar-se a canais][29]

* **Cliente JS Direct Line**: se você quiser usar o canal Direct Line no Serviço de Bot do Azure e não estiver usando o cliente WebChat, o cliente JS Direct Line poderá ser usado no seu aplicativo personalizado. Vá para o [GitHub][30] para obter mais informações.

<a name="ABS-whats-new"></a>

* **Novo! Canal de Fala do Direct Line**: estamos reunindo o Bot Framework e os Serviços de Fala da Microsoft para oferecer um canal que permita fluxo de fala e texto bidirecional entre o cliente e o aplicativo bot.  Para obter mais informações, confira como adicionar [canal de fala ao bot](directline-speech-bot.md).


## <a name="bot-framework-emulator"></a>Bot Framework Emulator
O [Bot Framework Emulator][60] é um aplicativo de área de trabalho multiplataforma que permite aos desenvolvedores testar e depurar bots criados usando o SDK do Bot Framework. Você pode usar o Bot Framework Emulator para testar bots em execução localmente no computador ou para se conectar a bots em execução remotamente.

- [Baixar mais recente][61]  |  [Documentos][62]

<a name="Emulator-whats-new"></a>
### <a name="bot-inspector-new-in-preview"></a>Inspetor de Bot (Novo! Em versão prévia)

O Bot Framework Emulator lançou uma versão beta do novo recurso Inspetor de Bot. Ele fornece uma maneira de depurar e testar seus bots do SDK do bot Framework V4 em canais como equipes da Microsoft, margem de atraso, Facebook Messenger, etc. Como você tem a conversa, as mensagens serão espelhadas no emulador do bot Framework, onde você poderá inspecionar os dados da mensagem recebidos pelo bot. Além disso, é renderizado um instantâneo do estado do bot em todos os turnos entre o canal e o bot. Leia mais sobre o [Inspetor de Bot](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md).


## <a name="related-services"></a>Serviços Relacionados

### <a name="language-understanding"></a>Reconhecimento Vocal
Um serviço baseado em aprendizado de máquina para criar experiências de linguagem natural. Crie rapidamente modelos personalizados e prontos para a empresa que melhoram continuamente. O [LUIS (Serviço Inteligente de Reconhecimento Vocal)][30] permite que o aplicativo entenda o que uma pessoa quer com suas próprias palavras.

<a name="LUIS-whats-new"></a>

- **Novo! Funções, entidades externas e entidades dinâmicas**: o LUIS adicionou vários recursos que permitem aos desenvolvedores extrair informações mais detalhadas do texto, para que os usuários agora possam criar soluções mais inteligentes com menos esforço. O LUIS também estendeu funções a todos os tipos de entidade, o que permite que essas entidades sejam classificadas com subtipos diferentes com base no contexto. Os desenvolvedores agora têm um controle mais granular sobre o que eles podem fazer com o LUIS, incluindo a capacidade de identificar e atualizar modelos no runtime por meio de listas dinâmicas e entidades externas. As listas dinâmicas são usadas para acrescentar às entidades de lista no tempo de previsão, permitindo que informações específicas do usuário tenham correspondência exata. Extratores de entidade suplementares separados são executados com entidades externas, e essas informações podem ser acrescentadas ao LUIS como sinais fortes para outros modelos.

- **Novo! Painel de Análise**: o LUIS está lançando um painel de análise abrangente mais detalhado e visualmente sofisticado. Seu design amigável realça os problemas comuns que a maioria dos usuários enfrenta ao projetar aplicativos, fornecendo explicações simples sobre como resolvê-los para ajudar os usuários a obter mais informações sobre a qualidade dos modelos, possíveis problemas de dados e orientação para adotar as práticas recomendadas.

[Docs][31] | [Adicionar reconhecimento vocal ao bot][32]

### <a name="qna-maker"></a>QnA Maker
O [QnA Maker][33] é um serviço de API baseado em nuvem que cria uma camada de perguntas e respostas em formato de conversação sobre seus dados. Com o QnA Maker, você pode compilar, treinar e publicar em minutos um bot simples de perguntas e respostas com base em URLs de perguntas frequentes, documentos estruturados, manuais de produto ou conteúdo editorial.

<a name="QnA-whats-new"></a>

- **Novo! Pipeline de extração**: agora você pode extrair informações hierárquicas de URLs, arquivos e SharePoint
- **Novo! Inteligência**: modelos de classificação contextuais, sugestões de aprendizado ativo
- **Novo! Conversa**: conversas em vários turnos no QnA Maker.

[Docs][34]  | [Adicionar o QnA Maker ao bot][35]

### <a name="speech-services"></a>Serviços de Fala
Os [Serviços de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/) convertem áudio em texto, realizam tradução de fala e conversão de texto em fala com os serviços de Fala unificados. Com os serviços de fala, você pode integrar fala ao seu bot, criar palavras de ativação personalizadas e criar em vários idiomas.

### <a name="adaptive-cards"></a>Cartões Adaptáveis
Os [Cartões Adaptáveis](https://adaptivecards.io) são um padrão aberto para desenvolvedores trocarem conteúdo de cartão de maneira comum e consistente e são usados por desenvolvedores do Bot Framework para criar ótimas experiências de conversa entre canais.

## <a name="additional-information"></a>Informações adicionais
- Visite a [página do GitHub](https://github.com/Microsoft/botframework/blob/master/whats-new.md#whats-new) para obter mais informações.

[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[1a]:https://github.com/microsoft/botframework-sdk/#readme
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[5]:index.yml
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[11]:https://github.com/Microsoft/botbuilder-python/tree/master/samples
[11a]:https://aka.ms/python-sample-repo

[18]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#readme
[19]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#readme

[27]:https://azure.microsoft.com/services/bot-service/
[28]:bot-service-overview-introduction.md
[29]:bot-service-manage-channels.md

[30]:https://www.luis.ai
[31]:https://docs.microsoft.com/azure/cognitive-services/LUIS/Home
[32]:v4sdk/bot-builder-howto-v4-luis.md
[33]:https://www.qnamaker.ai/
[34]:/azure/cognitive-services/qnamaker/overview/overview
[35]:v4sdk/bot-builder-howto-qna.md

[40]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/common-expression-language#readme
[41]:https://github.com/Microsoft/BotBuilder-Samples/blob/master/experimental/common-expression-language/api-reference.md
[43]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation#readme
[44]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/docs
[45]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/csharp_dotnetcore
[46]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/javascript_nodejs/13.core-bot
[47]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme
[48]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs
[49]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore
[50]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/declarative

[60]:https://github.com/Microsoft/BotFramework-Emulator#readme
[61]:https://github.com/Microsoft/BotFramework-Emulator/releases/latest
[62]:bot-service-debug-emulator.md

[100]:https://github.com/howdyai/botkit#readme
[101]:https://github.com/howdyai/botkit/blob/master/LICENSE.md
[102]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-slack#readme
[103]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-webex#readme
[104]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-hangouts#readme
[105]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-facebook#readme
[106]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-twilio-sms#readme
[107]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-web#readme
