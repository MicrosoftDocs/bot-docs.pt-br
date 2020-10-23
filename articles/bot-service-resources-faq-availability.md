---
title: Perguntas frequentes sobre o bot Framework-serviço de bot
description: Perguntas frequentes sobre a experiência e a disponibilidade do bot Framework.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/08/2020
ms.openlocfilehash: 4e46c360812015e08623eba65d3b58c73d7be21c
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414617"
---
# <a name="background-and-availability"></a>Informações e disponibilidade

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

<!-- Attention writers!!
     1 - This article contains FAQs regarding Bot Framework background and availability.
     1 - When you create a new FAQ, please add the related link to the proper section in bot-service-resources-bot-framework-faq.md.-->

## <a name="why-did-microsoft-develop-the-bot-framework"></a>Por que a Microsoft desenvolveu o Bot Framework?

Criamos o Bot Framework para facilitar para os desenvolvedores criar e conectar bots excelentes aos usuários, independentemente do local em que eles conversarem, incluindo os canais premier da Microsoft.

## <a name="what-is-the-v4-sdk"></a>O que é o SDK v4?
O SDK do Bot Framework v4 baseia-se nos comentários e nas lições aprendidas dos SDKs anteriores do Bot Framework. Ele apresenta os níveis certos de abstração, ao mesmo tempo que permite a "componentização" avançada dos blocos de construção de bot. Comece com um bot simples e aumente a sofisticação dele usando uma estrutura modular e extensível. Encontre as [perguntas frequentes](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) sobre o SDK no GitHub.

## <a name="how-to-run-a-bot-offline"></a>Como executar um bot offline?

<!-- WIP -->
Antes de falar sobre o uso de um bot offline, o que significa que um bot não foi implantado no Azure ou em outros serviços de host, mas no local, vamos esclarecer alguns pontos.

- Um bot é um serviço Web que não tem uma interface do usuário, portanto, ele deve interagir com o bot por outros meios, na forma de canais, que usam o [Bot Framework Service](rest-api/bot-framework-rest-connector-concepts.md). O conector funciona como um *proxy* para retransmitir mensagens entre um cliente e o bot.
- O **conector** é um aplicativo global hospedado em nós do Azure e distribuído geograficamente para obter disponibilidade e escalabilidade.
- Use o [Registro do Canal de Bot](bot-service-quickstart-registration.md) para registrar o bot com o conector.
    >[!NOTE]
    > O bot deve ter o ponto de extremidade acessível publicamente pelo conector.

É possível executar um bot offline com funcionalidades limitadas. Por exemplo, caso queira usar um bot offline que tenha a funcionalidade LUIS, você deverá criar um contêiner para o bot, as ferramentas necessárias e um contêiner para LUIS. Ambos conectados por meio da rede de transição Docker Compose.

Esta é uma solução offline "parcial" porque um contêiner de Serviços Cognitivos precisa de uma conexão online periódica.

> [!NOTE]
> O serviço QnA não tem suporte em um bot de execução offline.

Para obter mais informações, consulte:

- [Como implantar o contêiner do LUIS (Reconhecimento Vocal) nas Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/cognitive-services/luis/deploy-luis-on-container-instances)
- [Suporte de contêiner nos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)

## <a name="bot-framework-sdk-version-3-lifetime-support-and-deprecation-notice"></a>Suporte de tempo de vida do SDK Versão 3 do Bot Framework e aviso de substituição

O SDK V4 do Microsoft Bot Framework foi lançado em setembro de 2018 e, desde então, enviamos algumas melhorias em atualizações secundárias. Conforme anunciado anteriormente, o SDK V3 está sendo desativado. Assim sendo, não haverá mais desenvolvimento em repositórios V3. **As cargas de trabalho de bot V3 existentes continuarão a ser executadas sem interrupção. Não temos planos de interromper nenhuma carga de trabalho em execução**.

Conforme mencionado anteriormente, os bots do SDK V3 do Bot Builder continuam sendo executados e tendo suporte do Serviço de Bot do Azure. O SDK V3 do Bot Builder será compatível apenas para correções críticas de bugs de segurança, atualizações de conector e de compatibilidade de camada de protocolo.

Todos os novos recursos e funcionalidades são desenvolvidos exclusivamente no [SDK V4 do Bot Framework](https://github.com/microsoft/botframework-sdk).  Os clientes são incentivados a migrar seus bots para V4 assim que possível.

É altamente recomendável começar a migrar seus bots da V3 para a V4. Para dar suporte a essa migração, criamos a documentação relacionada e forneceremos suporte estendido para iniciativas de migração (por meio dos canais padrão como o Stack Overflow e Suporte ao Cliente Microsoft).


Para obter mais informações, confira as referências a seguir:
* [Diretrizes essenciais para migração](https://aka.ms/bf-migration-overview)
* Repositórios v4 primários para desenvolver bots do Bot Framework
  * [Bot Builder para dotnet](https://github.com/microsoft/botbuilder-dotnet)
  * [Bot Builder para JS](https://github.com/microsoft/botbuilder-js)
* As bibliotecas do QnA Maker foram substituídas pelas seguintes bibliotecas V4:
  * [Bibliotecas para dotnet](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.AI.QnA)
  * [Bibliotecas para JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-ai/src/qnaMaker.ts)
* As bibliotecas do Azure foram substituídas pelas seguintes bibliotecas V4:
  * [Bot Builder para JS Azure](https://github.com/Microsoft/botbuilder-js/tree/master/libraries/botbuilder-azure)
  * [Bot Builder para dotnet Azure](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.Azure)

### <a name="v3-status-summary"></a>Resumo do Status V3

#### <a name="abs-service"></a>Serviço de ABS
1. O lado do serviço do ABS continuará a dar suporte a bots do V3 em execução sem nenhum fim de vida planejado. Eventuais bots em execução não serão interrompidos.
1. Os canais permanecerão compatíveis com V3 sem interrupção nem plano de fim de vida útil.
1. A criação de novos bots V3 está desabilitada no portal. No entanto, os usuários especialistas que desejarem implantar seus bots V3 de modo independente e sem o ABS (por exemplo, como serviço de aplicativo Web) poderão fazê-lo.

#### <a name="sdk-and-tools"></a>SDK e Ferramentas
1.    Não estamos investindo na V3 do lado do SDK e aplicaremos apenas correções de segurança críticas às ramificações do SDK no futuro próximo (exceção: planejamos adicionar um conector de habilidades para permitir que os bots V4 chamem bots V3 herdados).
2.    O desenvolvimento de SDKs e ferramentas é exclusivamente no V4, sem nenhum trabalho V3 feito ou planejado (portanto, já é o cenário em que estamos).
3.    Não impedimos que ninguém execute ferramentas antigas para gerenciar seus próprios bots V3.

## <a name="how-can-i-migrate-azure-bot-service-from-one-region-to-another"></a>Como migrar o Serviço de Bot do Azure de uma região para outra?

O Serviço de Bot do Azure não dá suporte à mudança de região. Ele é um serviço global que não está vinculado a nenhuma região específica.
