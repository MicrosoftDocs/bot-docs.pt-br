---
title: Diretrizes de depuração| Microsoft Docs
description: Entenda como depurar o bot.
keywords: depuração de bots, depuração de botframework
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e33d7c0d4ec16b8905392a9fda7804914e47d69d
ms.sourcegitcommit: 23a1808e18176f1704f2f6f2763ace872b1388ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483962"
---
# <a name="debugging-guidelines"></a>Diretrizes de depuração

[!INCLUDE[applies-to](../includes/applies-to.md)]

Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado.

A depuração do bot, às vezes, pode ser uma tarefa difícil. Cada desenvolvedor tem sua própria maneira preferencial para realizar essa tarefa; as diretrizes que apresentamos abaixo são sugestões de uso que se aplicam a uma grande maioria dos bots.

Depois de verificar se o bot parece funcionar como desejado, a próxima etapa é conectá-lo a um canal. Para fazer isso, você pode implantar o bot em um servidor de preparo e criar seu próprio cliente de linha direta ao qual o bot se conectará.
<!--IBTODO [Direct Line client](bot-builder-howto-direct-line.md)-->

A criação de seu próprio cliente permite que você defina o funcionamento interno do canal, além de testar especificamente como o bot responde a determinadas trocas de atividade. Depois de conectado ao cliente, execute os testes para definir o estado do bot e verificar os recursos. Se o bot utiliza um recurso, como a fala, o uso desses canais pode oferecer uma maneira de verificar essa funcionalidade.

O uso do Emulador e do Webchat por meio do portal do Azure aqui pode fornecer mais insights sobre o desempenho do bot durante a interação com diferentes canais.

A depuração do bot funciona da mesma forma que outros aplicativos multi-thread, com a capacidade de definir pontos de interrupção ou usar recursos como a janela imediata. 

Os bots seguem um paradigma de programação controlada por evento, que poderá ser difícil de ser racionalizado se você não estiver familiarizado com ele. A ideia de o bot ser sem estado, multi-thread e lidar com chamadas assíncronas/await pode resultar em bugs inesperados. Embora a depuração do bot funcione de forma semelhante a outros aplicativos multi-thread, abordaremos algumas sugestões, ferramentas e recursos para ajudar.

## <a name="understanding-bot-activities-with-the-emulator"></a>Noções básicas de atividades do bot com o emulador

O bot lida com diferentes tipos de [atividades](bot-builder-basics.md#the-activity-processing-stack), além da atividade normal de _mensagem_. O uso do [emulador](../bot-service-debug-emulator.md) mostrará a você quais são essas atividades, quando elas ocorrem e as informações que elas contêm. A compreensão dessas atividades ajudará você a codificar o bot com eficiência e permite que você verifique se as atividades que o bot está enviando e recebendo são as esperadas.

## <a name="saving-and-retrieving-user-interactions-with-transcripts"></a>Salvar e recuperar as interações do usuário com transcrições

O armazenamento de transcrição de Blobs do Azure fornece um recurso especializado em que você pode [armazenar e recuperar transcrições](bot-builder-howto-v4-storage.md) contendo as interações entre os usuários e seu bot.  

Além disso, depois que as interações de entrada do usuário foram armazenadas, você pode usar o “_gerenciador de armazenamento_” do Azure para exibir manualmente os dados contidos em transcrições armazenadas em seu repositório de transcrição de blob. O exemplo a seguir abre o “_gerenciador de armazenamento_” a partir das configurações para “_mynewtestblobstorage_”. Para abrir uma entrada de usuário salva selecione:    Contêiner de Blob > ChannelId > TranscriptId > ConversationId

![Examine_stored_transcript_text](./media/examine_transcript_text_in_azure.png)

Isso abre a entrada de conversação do usuário armazenada no formato JSON. A entrada do usuário é preservada junto com a chave “_texto:_ ”.

## <a name="how-middleware-works"></a>Como funciona o middleware

O [middleware](bot-builder-concept-middleware.md) pode não ser intuitivo na primeira tentativa de usá-lo, particularmente, com relação à continuação, ou curto-circuito, de execução. O middleware pode ser executado na borda à esquerda ou à direita de um turno, com uma chamada ao delegado `next()` que instrui quando a execução é passada para a lógica do bot. 

Se você estiver usando várias partes do middleware, o delegado poderá passar a execução para uma parte diferente do middleware se o pipeline for orientado desse modo. Detalhes sobre o [pipeline de middleware do bot](bot-builder-concept-middleware.md#the-bot-middleware-pipeline) podem ajudar a esclarecer essa ideia.

Se o delegado `next()` não for chamado, isso será conhecido como [roteamento de curto-circuito](bot-builder-concept-middleware.md#short-circuiting). Isso acontece quando o middleware atende à atividade atual e determina que não é necessário passar a execução. 

Compreender quando e por quê o middleware causa curto-circuito ajuda a indicar qual parte do middleware deve vir primeiro no pipeline. Além disso, é particularmente importante compreender o que esperar para o middleware interno fornecido pelo SDK ou por outros desenvolvedores. Algumas pessoas acreditam ser útil tentar criar seu próprio middleware primeiro para experimentar um pouco antes de se aprofundarem no middleware interno.

<!-- Snip: QnA was once implemented as middleware.
For example [QnA maker](bot-builder-howto-qna.md) is designed to handle certain interactions and short-circuit the pipeline when it does, which can be confusing when first learning how to use it.
-->

## <a name="understanding-state"></a>Reconhecendo o estado

O acompanhamento do estado é uma parte importante do bot, particularmente, para tarefas complexas. Em geral, a melhor prática é processar as atividades o mais rapidamente possível e permitir a conclusão do processamento para que o estado seja persistido. As atividades podem ser enviadas ao bot praticamente ao mesmo tempo, e isso pode introduzir bugs muito confusos devido à arquitetura assíncrona.

O mais importante é garantir a persistência do estado de uma maneira que corresponda às suas expectativas. Dependendo do local em que reside o estado persistente, os emuladores de armazenamento do [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) e do [Armazenamento de Tabelas do Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) podem ajudá-lo a verificar o estado antes de usar o armazenamento de produção.

## <a name="how-to-use-activity-handlers"></a>Como usar manipuladores de atividades

Os manipuladores de atividade podem introduzir outra camada de complexidade, principalmente porque cada atividade é executada em um thread independente (ou em web workers, dependendo da linguagem). Dependendo do que os manipuladores estão fazendo, isso pode causar problemas em que o estado atual não é o esperado.

O estado interno é gravado no final de um turno; no entanto, todas as atividades geradas por esse turno são executadas independentemente do pipeline de turno. Geralmente, isso não nos afeta, mas se um manipulador de atividade muda de estado, precisamos gravar o estado para que ele contenha essa alteração. Nesse caso, o pipeline de turno pode aguardar a conclusão do processamento da atividade antes de ser concluído, para garantir que ele registre o estado correto desse turno.

O método _enviar atividade_ e seus manipuladores apresentam um único problema. Simplesmente chamar _enviar atividade_ de dentro do manipulador _ao enviar atividades_ causa uma bifurcação infinita de threads. Há várias maneiras de resolver esse problema; por exemplo, acrescentando mensagens adicionais às informações de saída ou gravando em outro local, como o console ou um arquivo, para evitar a falha do bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como realizar testes de unidade em bots](unit-test-bots.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Depurando no Visual Studio](https://docs.microsoft.com/visualstudio/debugger/index)
* [Depurando, rastreando e criando um perfil](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/) para o Bot Framework
* Usar o [ConditionalAttribute](https://docs.microsoft.com/dotnet/api/system.diagnostics.conditionalattribute?view=netcore-2.0) para métodos que você não deseja incluir no código de produção
* Usar ferramentas como o [Fiddler](https://www.telerik.com/fiddler) para ver o tráfego de rede
* [Solucionar problemas gerais](../bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção
