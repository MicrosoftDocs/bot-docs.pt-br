---
title: Sobre o serviço de bot | Microsoft Docs
description: Saiba mais sobre o serviço de bot, um serviço para criar, conectar, testar, implantar, monitorar e gerenciar bots.
keywords: Visão geral, a estrutura de tópicos de Introdução, SDK
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/03/2018
ms.openlocfilehash: 142d4cbe0c252e88bab800bb3823b70434a65bd6
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297400"
---
::: moniker range="azure-bot-service-3.0"

# <a name="azure-bot-service"></a>Serviço de Bot do Azure

O Serviço de Bot do Azure fornece ferramentas para criar, testar, implantar e gerenciar bots inteligentes em um só lugar. Por meio da estrutura modular e extensível fornecida pelo SDK, os desenvolvedores podem usar modelos para criar bots que fornecem fala, compreensão de linguagem, perguntas e respostas e muito mais.  

## <a name="what-is-a-bot"></a>O que é um bot?
Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões) ou fala. Pode ser uma simples caixa de diálogo de perguntas e respostas, ou um bot sofisticado que permite que as pessoas interajam com os serviços de maneira inteligente usando padrões de correspondência, rastreamento de estado e técnicas de inteligência artificial bem integradas aos serviços de negócios existentes. Confira [estudos de caso](https://azure.microsoft.com/services/bot-service/) de bots.  

## <a name="building-a-bot"></a>Criação de um bot 
Você pode optar por usar seu ambiente de desenvolvimento favorito ou ferramentas de linha de comando para criar seu bot em C# ou Node.js. Nós fornecemos ferramentas para vários estágios de desenvolvimento de bots que você pode usar para construir seu bot para você começar.    

![Visão geral do bot](media/bot-service-overview.png) 

## <a name="plan"></a>Plano 
Antes de escrever código, revise as diretrizes de [design do](bot-service-design-principles.md)  para obter práticas recomendadas e identifique as necessidades do seu bot. Você pode criar um bot simples ou incluir recursos mais sofisticados, como fala, compreensão de linguagem, QnA ou a capacidade de extrair conhecimento de diferentes fontes e fornecer respostas inteligentes.  

> [!TIP] 
>
> Instale o modelo que você precisa:
>  - O construtor de bot do SDK do .NET v3 [modelo VSIX](https://marketplace.visualstudio.com/items?itemName=BotBuilder.BotBuilderV3) 
>  - O construtor de bot v3 SDK Node. js [Yeoman do modelo](https://www.npmjs.com/package/generator-botbuilder) 
>
> Instale as ferramentas:
> - Baixe [ferramentas de CLI](https://github.com/Microsoft/botbuilder-tools) para criar e gerenciar ativos de bot. Essas ferramentas ajudam você a gerenciar o arquivo de configuração do bot, o aplicativo LUIS, a base de conhecimento QnA e muito mais a partir da linha de comando. Você pode encontrar mais detalhes na [Leiame](https://github.com/Microsoft/botbuilder-tools/blob/master/README.md).
> - [Emulador](https://github.com/Microsoft/BotFramework-Emulator/releases) para testar seu bot
>
> Se necessário, use componentes de bot, tais como:  
> - [LUIS](https://www.luis.ai/) para adicionar entendimento de linguagem aos bots
> - [O QnA Maker](https://qnamaker.ai/) responder às perguntas do usuário de uma forma mais natural e conversacional
> - [Fala](https://azure.microsoft.com/services/cognitive-services/speech/) para converter áudio em texto, entender intenção e converter texto de volta para fala  
> - [Ortografia](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir erros de ortografia, reconhecer a diferença entre nomes, nomes de marcas e gírias 
> - [Serviços cognitivos](bot-service-concept-intelligence.md) para vários outros componentes inteligentes 


## <a name="build-your-bot"></a>Crie seu bot 
Seu bot é um serviço da web que implementa uma interface de conversação e se comunica com o serviço de bot. Você pode criar essa solução em vários ambientes e idiomas e oferecemos ferramentas simples para o Visual Studio ou o Yeoman ou diretamente no portal do Azure. Veja abaixo algumas das ferramentas e serviços que você pode usar.

> [!TIP]
>
> Crie um bot usando [SDK](~/dotnet/bot-builder-dotnet-quickstart.md), [portal do Azure](bot-service-quickstart.md), ou use [ferramentas de CLI](~/bot-builder-create-templates.md).
>
> Adicione componentes: 
> - Adicione o modelo de compreensão de idioma [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home). 
> - Adicione [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home) perguntar para responder a usuários de perguntas da base de Conhecimento.  
> - Melhore a experiência do usuário com cartões, fala ou tradução. 
> - Adicione lógica ao seu bot usando o Bot Builder SDK.   

## <a name="test-your-bot"></a>Testar seu bot 
Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado. Antes de publicar, teste seu bot.

> [!TIP] 
>
> - [Testar o bot com o emulador](bot-service-debug-emulator.md)
> - [Bot de teste na Web bate-papo](bot-service-manage-test-webchat.md)

## <a name="publish"></a>Publicar 
Quando estiver pronto, publique seu bot no Azure ou em seu próprio serviço da Web ou data center. Você pode configurar uma implantação contínua que permite desenvolver seu bot localmente e é útil se o bot for colocado em um controle de origem como o GitHub ou o Visual Studio Team Services. Conforme você verifica suas alterações novamente em seu repositório de origem, suas alterações serão implantadas automaticamente no Azure.

> [!Tip]
>
> - [Implantar no Azure](bot-service-build-continuous-deployment.md)

## <a name="connect"></a>Connect          
Conecte seu bot a canais como Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, texto/SMS, Twilio, Cortana e Skype para aumentar as interações e alcançar mais clientes.  
  
> [!TIP]
>
> - [Escolha os canais a ser adicionado](bot-service-manage-channels.md)


## <a name="evaluate"></a>Avaliar 
Use os dados coletados no portal do Azure para identificar oportunidades de melhorar os recursos e o desempenho do seu bot. Você pode obter dados de nível de serviço e de instrumentação, como tráfego, latência e integrações. O Google Analytics também fornece relatórios no nível da conversa sobre dados de usuários, mensagens e canais.

> [!Tip]
>
> - [Reunir as análises](bot-service-manage-analytics.md) 


::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="azure-bot-service"></a>Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

O Serviço de Bot do Azure fornece ferramentas para criar, testar, implantar e gerenciar bots inteligentes em um só lugar. Por meio da estrutura modular e extensível fornecida pelo SDK, os desenvolvedores podem usar modelos para criar bots que fornecem fala, compreensão de linguagem, perguntas e respostas e muito mais.  

## <a name="what-is-a-bot"></a>O que é um bot?
Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões) ou fala. Pode ser uma simples caixa de diálogo de perguntas e respostas, ou um bot sofisticado que permite que as pessoas interajam com os serviços de maneira inteligente usando padrões de correspondência, rastreamento de estado e técnicas de inteligência artificial bem integradas aos serviços de negócios existentes. Confira [estudos de caso](https://azure.microsoft.com/services/bot-service/) de bots.  

## <a name="building-a-bot"></a>Criação de um bot 
Você pode optar por usar seu ambiente de desenvolvimento favorito ou ferramentas de linha de comando para criar seu bot em C#, JavaScript, Java e Python. Nós fornecemos ferramentas para vários estágios de desenvolvimento de bots que você pode usar para construir seu bot para você começar.    

![Visão geral do bot](media/bot-service-overview.png) 

## <a name="plan"></a>Plano 
Antes de escrever código, revise as diretrizes de [design do](bot-service-design-principles.md)  para obter práticas recomendadas e identifique as necessidades do seu bot. Você pode criar um bot simples ou incluir recursos mais sofisticados, como fala, compreensão de linguagem, QnA ou a capacidade de extrair conhecimento de diferentes fontes e fornecer respostas inteligentes. Para começar, você pode usar as [ferramentas da CLI](~/bot-builder-create-templates.md), [Portal do Azure](bot-service-quickstart.md) ou os modelos abaixo.

**Pegue o modelo que você precisa**

| .NET | JavaScript | 
| --- | --- | 
| [Modelo de VSIX](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) | [Modelo yeoman](https://www.npmjs.com/package/generator-botbuilder). Use @preview para obter o modelo de v4. |

**Explorar ou instalar as ferramentas, se necessário**

- Baixe [ferramentas de CLI](https://github.com/Microsoft/botbuilder-tools) para criar e gerenciar ativos de bot. Essas ferramentas ajudam você a gerenciar o arquivo de configuração do bot, o aplicativo LUIS, a base de conhecimento QnA e muito mais a partir da linha de comando. Você pode encontrar mais detalhes na [Leiame](https://github.com/Microsoft/botbuilder-tools/blob/master/README.md).
- [Emulador](https://github.com/Microsoft/BotFramework-Emulator/releases) para testar seu bot
- [LUIS](https://www.luis.ai/) Adicionar idioma natural aos bots
- [O QnA Maker](https://qnamaker.ai/) responder às perguntas do usuário de uma forma mais natural e conversacional


## <a name="build-your-bot"></a>Crie seu bot 
Seu bot é um serviço da web que implementa uma interface de conversação e se comunica com o serviço de bot. Você pode criar essa solução em vários ambientes e idiomas e oferecemos ferramentas simples para o Visual Studio ou o Yeoman ou diretamente no portal do Azure. Veja abaixo algumas das ferramentas e serviços que você pode usar.

Alguns dos muitos componentes disponíveis incluem:

| Componentes | DESCRIÇÃO |
| --- | --- |
| [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home) | Adicionar reconhecimento vocal |
| [O QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home)  | Adicionar uma base de conhecimento para responder a usuários de perguntas perguntar |
| [Ferramenta de expedição](~/v4sdk/bot-builder-tutorial-dispatch.md) | Se estiver usando mais de um modelo, determine de maneira inteligente quando usar qual |
| [Mídia avançada](v4sdk/bot-builder-howto-add-media-attachments.md) | Melhore a experiência do usuário com cartões de mídia ou fala |
| [Fala](https://azure.microsoft.com/services/cognitive-services/speech/) | Converta áudio em texto, entender a intenção e converter texto para fala |
| [Ortografia](https://azure.microsoft.com/services/cognitive-services/spell-check/) | Corrigir erros de ortografia, reconhecer a diferença entre nomes, nomes de marcas e gírias |

> [!NOTE]
> A tabela acima não é uma lista abrangente. Explore os artigos à esquerda, começando com [enviando mensagens](v4sdk/bot-builder-howto-send-messages.md), para mais funcionalidades do bot.

## <a name="test-your-bot"></a>Testar seu bot 
Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado. Antes de publicar, teste seu bot. 

[Testar o bot com o emulador](bot-service-debug-emulator.md) ou [bot de teste na Web bate-papo](bot-service-manage-test-webchat.md).

## <a name="publish"></a>Publicar 
Quando estiver pronto, publique seu bot no Azure ou em seu próprio serviço da Web ou data center. Você pode configurar uma implantação contínua que permite desenvolver seu bot localmente e é útil se o bot for colocado em um controle de origem como o GitHub ou o Visual Studio Team Services. Conforme você verifica suas alterações novamente em seu repositório de origem, suas alterações serão implantadas automaticamente no Azure.

[Implantar no Azure](bot-service-build-continuous-deployment.md) por meio de implantação contínua.

## <a name="connect"></a>Connect          
Conecte seu bot a canais como Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, texto/SMS, Twilio, Cortana e Skype para aumentar as interações e alcançar mais clientes.

[Escolha os canais a serem adicionados](bot-service-manage-channels.md).


## <a name="evaluate"></a>Avaliar 
Use os dados coletados no portal do Azure para identificar oportunidades de melhorar os recursos e o desempenho do seu bot. Você pode obter dados de nível de serviço e de instrumentação, como tráfego, latência e integrações. O Google Analytics também fornece relatórios no nível da conversa sobre dados de usuários, mensagens e canais. 

Explore como [reunir as análises](bot-service-manage-analytics.md).

::: moniker-end
