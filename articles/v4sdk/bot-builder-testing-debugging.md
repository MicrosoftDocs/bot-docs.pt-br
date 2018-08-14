---
title: Testando e depurando o bot | Microsoft Docs
description: Entenda como testar e depurar o bot.
keywords: princípios de teste, elementos fictícios, perguntas frequentes, níveis de teste
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/09/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ea1c458e98f67f76c56fdd70595307a0e0252c1d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296607"
---
# <a name="testing-and-debugging-your-bot"></a>Testando e depurando o bot

Os bots são aplicativos complexos, com muitas partes diferentes que trabalham em conjunto. Como qualquer outro aplicativo complexo, isso pode resultar em alguns bugs interessantes ou fazer com que o bot se comporte de maneira diferente do esperado.

O teste e a depuração posterior do bot, às vezes, pode ser uma tarefa difícil. Cada desenvolvedor tem sua própria maneira preferencial para realizar essa tarefa; as diretrizes que apresentamos abaixo são sugestões de uso que se aplicam a uma grande maioria dos bots.

## <a name="testing-your-bot"></a>Testando o bot

As diretrizes abaixo são apresentadas em três **níveis** diferentes.  Cada nível adiciona complexidade e recursos ao teste e, portanto, sugerimos que você esteja satisfeito com um nível antes de passar para o próximo. Fazer isso permitirá que você isole e corrija primeiro os problemas de nível mais baixo antes de adicionar mais complexidade.

A melhor prática para testes abordará ângulos diferentes sempre que aplicável. Isso pode incluir segurança, integração, URLs mal formadas, explorações de validação, códigos de status HTTP, conteúdo JSON, valores nulos e assim por diante. Se o bot manipula informações que não têm impacto sobre a privacidade do usuário, isso é particularmente importante.

### <a name="level-1-use-mock-elements"></a>Nível 1: usar elementos fictícios

A garantia de que cada parte pequena do aplicativo ou, neste caso, nosso bot, funcione exatamente como deveria, é o primeiro nível de teste. Para fazer isso, você pode usar elementos fictícios para as coisas que você não está testando no momento. Para referência, esse nível pode geralmente ser considerado como testes de unidade e de integração.

**Usar elementos fictícios para testar seções individuais**

A simulação do máximo de elementos possível permite um melhor isolamento da parte que você está testando. Os candidatos a elementos fictícios incluem armazenamento, adaptador, middleware, pipeline de atividade, canais e qualquer outra coisa que não faça parte diretamente do bot. Isso pode também remover alguns aspectos temporariamente, como o middleware não envolvido na parte do bot que está sendo testada, a fim de isolar cada parte. No entanto, se estiver testando o middleware, talvez você deseje simular o bot.

Os elementos fictícios podem assumir várias formas, da substituição de um elemento com um objeto conhecido diferente à implementação de uma funcionalidade de funções básicas do Olá, Mundo. Isso poderá também assumir a forma de apenas remover o elemento, se ele não for necessário, ou simplesmente forçá-lo a não executar nenhuma ação. 

Esse nível deve exercer funções e métodos individuais dentro do bot. O teste de métodos individuais pode ser feito por meio de testes de unidade internos, que são recomendados, com seu próprio conjunto de testes ou aplicativo de teste, ou fazendo isso manualmente no IDE. 

**Usar elementos fictícios para testar recursos maiores**

Quando estiver satisfeito com o comportamento de cada método, use esses elementos fictícios para testar recursos mais completos no bot. Isso demonstra como algumas camadas trabalham juntas para conversar com o usuário. 

Algumas ferramentas são fornecidas para ajudar com isso. Por exemplo, o [Azure Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator) fornece um canal emulado para se comunicar com o bot. O uso do emulador se torna uma situação mais complexa do que apenas testes de unidade e de integração e, portanto, também passa para o próximo nível de teste.

### <a name="level-2-use-a-direct-line-client"></a>Nível 2: usar um cliente da Linha Direta

Depois de verificar se o bot parece funcionar como desejado, a próxima etapa é conectá-lo a um canal. Para fazer isso, você pode implantar o bot em um servidor de preparo e criar seu próprio [cliente de Linha Direta](bot-builder-howto-direct-line.md) ao qual o bot se conectará.

A criação de seu próprio cliente permite que você defina o funcionamento interno do canal, além de testar especificamente como o bot responde a determinadas trocas de atividade. Depois de conectado ao cliente, execute os testes para definir o estado do bot e verificar os recursos. Se o bot utiliza um recurso, como a fala, o uso desses canais pode oferecer uma maneira de verificar essa funcionalidade.

O uso do Emulador e do Webchat por meio do portal do Azure aqui pode fornecer mais insights sobre o desempenho do bot durante a interação com diferentes canais.

### <a name="level-3-channel-tests"></a>Nível 3: testes de canal

Quando você estiver confiante do desempenho independente do o bot, será importante ver como ele funciona com vários canais nos quais ele estará disponível. 

A maneira de fazer isso pode variar bastante, do uso individual de diferentes canais e navegadores ao uso de uma ferramenta de terceiros, como o [Selenium](https://docs.seleniumhq.org/), para interagir por meio de um canal e extrair as respostas do bot.

### <a name="other-testing"></a>Outros testes

Diferentes tipos de teste podem ser feitos em conjunto com os níveis acima ou em ângulos diferentes, como o teste de estresse, o teste de desempenho ou a criação de perfil da atividade do bot. O Visual Studio fornece métodos para fazer isso localmente, bem como um [pacote de ferramentas](https://www.visualstudio.com/team-services/testing-tools/) para testar o aplicativo, e o [portal do Azure](https://portal.azure.com) fornece insights sobre o desempenho do bot.

## <a name="debugging"></a>Depurando

A depuração do bot funciona da mesma forma que outros aplicativos multi-thread, com a capacidade de definir pontos de interrupção ou usar recursos como a janela imediata. 

Os bots seguem um paradigma de programação controlada por evento, que poderá ser difícil de ser racionalizado se você não estiver familiarizado com ele. A ideia de o bot ser sem estado, multi-thread e lidar com chamadas assíncronas/await pode resultar em bugs inesperados. Embora a depuração do bot funcione de forma semelhante a outros aplicativos multi-thread, abordaremos algumas sugestões, ferramentas e recursos para ajudar.

**Noções básicas sobre as atividades do bot com o emulador**

O bot lida com diferentes tipos de [atividades](bot-builder-concept-activity-processing.md), além da atividade normal de _mensagem_. O uso do [emulador](../bot-service-debug-emulator.md) mostrará a você quais são essas atividades, quando elas ocorrem e as informações que elas contêm. A compreensão dessas atividades ajudará você a codificar o bot com eficiência e permite que você verifique se as atividades que o bot está enviando e recebendo são as esperadas.

**Como funciona o middleware**

O [middleware](bot-builder-concept-middleware.md) pode não ser intuitivo na primeira tentativa de usá-lo, particularmente, com relação à continuação, ou curto-circuito, de execução. O middleware pode ser executado na borda à esquerda ou à direita de um turno, com uma chamada ao delegado `next()` que instrui quando a execução é passada para a lógica do bot. 

Se você estiver usando várias partes do middleware, o delegado poderá passar a execução para uma parte diferente do middleware se o pipeline for orientado desse modo. Detalhes sobre o [pipeline de middleware do bot](bot-builder-concept-middleware.md#the-bot-middleware-pipeline) podem ajudar a esclarecer essa ideia.

Se o delegado `next()` não for chamado, isso será conhecido como [roteamento de curto-circuito](bot-builder-concept-middleware.md#short-circuiting). Isso acontece quando o middleware atende à atividade atual e determina que não é necessário passar a execução. 

Compreender quando e por quê o middleware causa curto-circuito ajuda a indicar qual parte do middleware deve vir primeiro no pipeline. Além disso, é particularmente importante compreender o que esperar para o middleware interno fornecido pelo SDK ou por outros desenvolvedores. Algumas pessoas acreditam ser útil tentar [criar seu próprio middleware](bot-builder-create-middleware.md) primeiro para experimentar um pouco antes de se aprofundarem no middleware interno.

Por exemplo, o [QnA Maker](bot-builder-howto-qna.md) foi projetado para manipular certas interações e causar curto-circuito do pipeline quando isso ocorre, o que pode ser confuso quando se está aprendendo a usá-lo pela primeira vez.

**Reconhecendo o estado**

O acompanhamento do estado é uma parte importante do bot, particularmente, para tarefas complexas. Em geral, a melhor prática é processar as atividades o mais rapidamente possível e permitir a conclusão do processamento para que o estado seja persistido. As atividades podem ser enviadas ao bot praticamente ao mesmo tempo, e isso pode introduzir bugs muito confusos devido à arquitetura assíncrona.

O mais importante é garantir a persistência do estado de uma maneira que corresponda às suas expectativas. Dependendo do local em que reside o estado persistente, os emuladores de armazenamento do [Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator) e do [Armazenamento de Tabelas do Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) podem ajudá-lo a verificar o estado antes de usar o armazenamento de produção.

**Como usar manipuladores de atividades**

Os manipuladores de atividade podem introduzir outra camada de complexidade, especialmente, pois cada atividade é executada em um thread independente (ou em web workers, dependendo da linguagem). Dependendo do que os manipuladores estão fazendo, isso pode causar problemas em que o estado atual não é o esperado.

O estado interno é gravado no final de um turno; no entanto, todas as atividades geradas por esse turno são executadas de maneira independente do pipeline de turno. Geralmente, isso não nos afeta, mas se um manipulador de atividade muda de estado, precisamos gravar o estado para que ele contenha essa alteração. Nesse caso, o pipeline de turno pode aguardar a conclusão do processamento da atividade antes de ser concluído, para garantir que ele registre o estado correto desse turno.

O método _send activity_ e seus manipuladores representarão um problema único se você desejar produzir algo para o usuário, pois a simples chamada a _send activity_ nesse método causa uma bifurcação infinita de threads. Há várias maneiras pelas quais você pode resolver esse problema, como pelo acréscimo da mensagem de depuração às informações de saída ou pela gravação em outro local, como o console ou um arquivo, para evitar o pane do bot.


## <a name="additional-resources"></a>Recursos adicionais
* [Depurando no Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/index)
* [Depurando, rastreando e criando um perfil](https://docs.microsoft.com/en-us/dotnet/framework/debug-trace-profile/) para o Bot Framework
* Usar o [ConditionalAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?view=netcore-2.0) para métodos que você não deseja incluir no código de produção 
* Usar ferramentas como o [Fiddler](https://www.telerik.com/fiddler) para ver o tráfego de rede 
* [Repositório de ferramentas de bot](https://github.com/Microsoft/botbuilder-tools)
* Estruturas podem ajudá-lo com o teste, como o [Moq](https://github.com/moq/moq4)
