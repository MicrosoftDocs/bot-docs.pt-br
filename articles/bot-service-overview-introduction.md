---
title: Introdução ao Serviço de Bot do Azure | Microsoft Docs
description: Saiba mais sobre o serviço de bot, um serviço para criar, conectar, testar, implantar, monitorar e gerenciar bots.
keywords: Visão geral, a estrutura de tópicos de Introdução, SDK
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 10/08/2018
ms.openlocfilehash: 750f348437f4e729ecc24317f1849b96c47d743a
ms.sourcegitcommit: a3833dd173a30f3f0c1baa807e690a5778862d64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48864021"
---
::: moniker range="azure-bot-service-3.0"

# <a name="about-azure-bot-service"></a>Sobre o Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

O Serviço de Bot do Azure fornece ferramentas para criar, testar, implantar e gerenciar bots inteligentes em um só lugar. Por meio da estrutura modular e extensível fornecida pelo SDK, os desenvolvedores podem usar modelos para criar bots que fornecem fala, compreensão de linguagem, perguntas e respostas e muito mais.  

## <a name="what-is-a-bot"></a>O que é um bot?
Um bot é um aplicativo com o qual os usuários interagem de maneira conversacional usando texto, gráficos (cartões) ou fala. Pode ser uma simples caixa de diálogo de perguntas e respostas, ou um bot sofisticado que permite que as pessoas interajam com os serviços de maneira inteligente usando padrões de correspondência, rastreamento de estado e técnicas de inteligência artificial bem integradas aos serviços de negócios existentes. 

## <a name="building-a-bot"></a>Criação de um bot 
Você pode optar por usar seu ambiente de desenvolvimento favorito ou ferramentas de linha de comando para criar seu bot em C# ou Node.js. Nós fornecemos ferramentas para vários estágios de desenvolvimento de bots que você pode usar para construir seu bot para você começar.    

![Visão geral do bot](media/bot-service-overview.png) 

## <a name="plan"></a>Plano 
Antes de escrever código, revise as diretrizes de [design do](bot-service-design-principles.md)  para obter práticas recomendadas e identifique as necessidades do seu bot. Você pode criar um bot simples ou incluir recursos mais sofisticados, como fala, compreensão de linguagem, QnA ou a capacidade de extrair conhecimento de diferentes fontes e fornecer respostas inteligentes.  

> [!TIP]
> Crie uma conta do [Azure](https://portal.azure.com). 

## <a name="build-your-bot"></a>Crie seu bot 
Seu bot é um serviço da web que implementa uma interface de conversação e se comunica com o serviço de bot. Você pode criar essa solução em vários ambientes e idiomas e oferecemos ferramentas simples para o Visual Studio ou o Yeoman ou diretamente no portal do Azure. Veja abaixo algumas das ferramentas e serviços que você pode usar.

> [!TIP]
> Crie um bot usando o [portal do Azure](bot-service-quickstart.md). Se necessário, adicione componentes, como: 
> - Reconhecimento vocal [LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/home). 
> - Base de conhecimento do [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/home) para responder a perguntas dos usuários.  

## <a name="test-your-bot"></a>Testar seu bot 
Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado. Antes de publicar, teste seu bot.

> [!TIP] 
> - Testar o bot no [Webchat](bot-service-manage-test-webchat.md) ou testar seu bot localmente com o [emulador](bot-service-debug-emulator.md)

## <a name="publish"></a>Publicar 
Quando estiver pronto, publique seu bot no Azure ou em seu próprio serviço da Web ou data center. Você pode configurar uma implantação contínua que permite desenvolver seu bot localmente e é útil se o bot for colocado em um controle de origem como o GitHub ou o Visual Studio Team Services. Conforme você verifica suas alterações novamente em seu repositório de origem, suas alterações serão implantadas automaticamente no Azure.

> [!Tip]
> - [Baixar e reimplantar o código no Azure](bot-service-build-download-source-code.md)

## <a name="connect"></a>Connect          
Conecte seu bot a canais como Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, texto/SMS, Twilio, Cortana e Skype para aumentar as interações e alcançar mais clientes.  
  
> [!TIP]
> - [Escolha os canais a ser adicionado](bot-service-manage-channels.md)


## <a name="evaluate"></a>Avaliar 
Use os dados coletados no portal do Azure para identificar oportunidades de melhorar os recursos e o desempenho do seu bot. Você pode obter dados de nível de serviço e de instrumentação, como tráfego, latência e integrações. O Google Analytics também fornece relatórios no nível da conversa sobre dados de usuários, mensagens e canais.

> [!Tip]
> - [Reunir as análises](bot-service-manage-analytics.md) 


::: moniker-end

::: moniker range="azure-bot-service-4.0"

# <a name="about-azure-bot-service"></a>Sobre o Serviço de Bot do Azure

[!INCLUDE [pre-release-label](includes/pre-release-label.md)]

O Serviço de Bot do Azure fornece ferramentas para criar, testar, implantar e gerenciar bots inteligentes em um só lugar. Por meio do uso da estrutura modular e extensível fornecida pelo SDK, pelas ferramentas, pelos modelos e serviços de inteligência artificial, os desenvolvedores podem criar bots que utilizam fala, compreendem linguagem natural, lidam com perguntas e respostas e muito mais.

## <a name="what-is-a-bot"></a>O que é um bot?
Os bots fornecem uma experiência na qual parece que estamos lidando com uma pessoa, ou pelo menos um robô inteligente, e não com uma máquina. Eles podem ser usados para transferir simples tarefas repetitivas, como fazer a reserva para um jantar ou coletar informações de perfil, para sistemas automatizados que não exigem uma intervenção humana direta. Os usuários conversam com um bot usando texto, cartões interativos e fala. Uma interação de bot pode ser uma pergunta e resposta rápidas, ou pode ser uma conversa sofisticada que fornece acesso aos serviços de forma inteligente.

Os bots são muito semelhantes aos aplicativos Web modernos, residindo na Internet e usando APIs para enviar e receber mensagens. O que um bot oferece varia muito dependendo do tipo de bot. Um software de bot moderno se baseia em um conjunto de tecnologias e ferramentas para fornecer experiências cada vez mais complexas em uma ampla variedade de plataformas. No entanto, um bot simples pode apenas receber uma mensagem e ecoá-la para o usuário com muito pouco código envolvido. 

Os bots podem fazer as mesmas coisas que outros tipos de software podem fazer: ler e gravar arquivos, usar bancos de dados e APIs e realizar tarefas computacionais normais. O que torna os bots únicos é o uso de mecanismos geralmente reservados à comunicação entre seres humanos. 

Geralmente, um bot é formado pelos seguintes componentes:
* Um servidor Web, na maioria dos casos um que esteja disponível na Internet pública
* O SDK e as ferramentas do Bot Builder que fornecem uma interface para o desenvolvimento de bots
* Serviços Cognitivos do Azure 
* Armazenamento do Azure

## <a name="building-a-bot"></a>Criação de um bot 

O Serviço de Bot do Azure oferece um conjunto integrado de ferramentas e serviços para facilitar esse processo. Escolha seu ambiente de desenvolvimento favorito ou ferramentas de linha de comando para criar seu bot em C#, JavaScript ou Typescript. (Java e Python também estarão disponíveis em breve!) Nós fornecemos ferramentas para vários estágios de desenvolvimento de bots que você pode usar para construir seu bot para você começar.

![Visão geral do bot](media/bot-service-overview.png) 

### <a name="plan"></a>Plano
Assim como acontece com qualquer tipo de software, é importante que o processo de criação de um bot bem-sucedido tenha uma compreensão detalhada dos objetivos, processos e necessidades dos usuários. Antes de escrever código, revise as diretrizes de [design do](bot-service-design-principles.md)  para obter práticas recomendadas e identifique as necessidades do seu bot. Você pode criar um bot simples ou incluir recursos mais sofisticados, como fala, compreensão de linguagem natural ou respostas a perguntas.

### <a name="build"></a>Compilação
Seu bot é um serviço Web que implementa uma interface de conversação e se comunica com o Serviço do Bot Framework para enviar e receber mensagens e eventos. Você pode criar bots em quantos ambientes e idiomas desejar. Você pode iniciar o desenvolvimento de bot no [portal do Azure](bot-service-quickstart.md) ou usar modelos de [[C#](dotnet/bot-builder-dotnet-sdk-quickstart.md) | [JavaScript](javascript/bot-builder-javascript-quickstart.md)] para o desenvolvimento local.

Como parte do Serviço de Bot do Azure, oferecemos componentes adicionais que você pode usar para estender a funcionalidade do seu bot

| Recurso | DESCRIÇÃO | Link |
| --- | --- | --- |
| Adicionar processamento de idioma natural | Habilite seu bot para entender a linguagem natural, entender os erros de ortografia, usar a fala e reconhecer a intenção do usuário | Como usar o [LUIS](~/v4sdk/bot-builder-howto-v4-luis.md) 
| Responder a perguntas | Adicionar uma base de conhecimento para responder a perguntas que os usuários fazem de forma mais natural e conversacional | Como usar o [QnA Maker](~/v4sdk/bot-builder-howto-qna.md) 
| Gerenciar vários modelos | Se você estiver usando mais de um modelo, como LUIS e QnA Maker, determine de forma inteligente quando usar qual durante a conversa do seu bot | Ferramenta de [expedição](~/v4sdk/bot-builder-tutorial-dispatch.md)|
| Adicionar botões e cartões | Aprimore a experiência do usuário com mídia diferente de texto, como gráficos, menus e cartões | Como [adicionar cartões](v4sdk/bot-builder-howto-add-media-attachments.md) |

> [!NOTE]
> A tabela acima não é uma lista abrangente. Explore os artigos à esquerda, começando com [enviando mensagens](~/v4sdk/bot-builder-howto-send-messages.md), para mais funcionalidades do bot.

Além disso, fornecemos ferramentas de linha de comando para ajudá-lo a criar, gerenciar e testar os ativos do bot. Essas ferramentas podem gerenciar um arquivo de configuração do bot, configurar aplicativos LUIS, criar uma base de conhecimento de QnA, simular uma conversa e muito mais. Você pode encontrar mais detalhes no arquivo [Leiame](https://aka.ms/botbuilder-tools-readme) das ferramentas de linha de comando.

Você também tem acesso a vários [exemplos](https://github.com/microsoft/botbuilder-samples) que demonstram muitos dos recursos disponíveis por meio do SDK. Eles são excelentes para desenvolvedores que buscam um ponto de partida com mais recursos.

### <a name="test"></a>Teste 
Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado. Antes de publicar, teste seu bot. Fornecemos várias maneiras de testar bots antes que eles sejam liberados para uso:

- Teste seu bot localmente com o [emulador](bot-service-debug-emulator.md). O emulador do Bot Framework é um aplicativo autônomo que não só fornece uma interface de chat, mas também ferramentas de depuração e para fazer perguntas para ajudar a entender como e porque seu bot faz o que ele faz.  O emulador pode ser executado localmente juntamente com seu aplicativo de bot em desenvolvimento. 
 
- Teste o bot na [Web](bot-service-manage-test-webchat.md). Uma vez configurado no portal do Azure, seu bot também pode ser acessado através de uma interface de chat da Web. A interface de chat da Web é uma ótima maneira de conceder acesso ao bot para testadores e outras pessoas que não têm acesso direto ao código de execução do bot.

### <a name="publish"></a>Publicar 
Quando estiver pronto para disponibilizar seu bot na Web, publique seu bot no [Azure](bot-builder-howto-deploy-azure.md) ou em seu próprio serviço Web ou data center. A primeira etapa para o bot ganhar vida em seu site, ou dentro de canais de chat, é ter um endereço na Internet pública.

### <a name="connect"></a>Connect          
Conecte seu bot a canais como Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, texto/SMS, Twilio, Cortana e Skype. O Bot Framework faz a maior parte do trabalho necessário para enviar e receber mensagens de todas essas diferentes plataformas. Seu aplicativo bot recebe um fluxo de mensagens unificado e normalizado, independentemente do número e tipo de canais aos quais ele está conectado. Para obter informações sobre como adicionar canais, confira o tópico [canais](bot-service-manage-channels.md).

### <a name="evaluate"></a>Avaliar 
Use os dados coletados no portal do Azure para identificar oportunidades de melhorar os recursos e o desempenho do seu bot. Você pode obter dados de nível de serviço e de instrumentação, como tráfego, latência e integrações. O Google Analytics também fornece relatórios no nível da conversa sobre dados de usuários, mensagens e canais. Para obter mais informações, confira [como reunir as análises](bot-service-manage-analytics.md).


## <a name="next-steps"></a>Próximas etapas
Confira estes [estudos de caso](https://azure.microsoft.com/services/bot-service/) de bots ou clique no link abaixo para criar um bot.
> [!div class="nextstepaction"]
> [Criar um bot](bot-service-quickstart.md)

::: moniker-end
