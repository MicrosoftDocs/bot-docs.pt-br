---
title: O que é o SDK do Bot Framework? -Serviço bot
description: Saiba mais sobre o serviço de bot do Azure e o SDK do bot Framework para criar, conectar, testar, implantar, monitorar e gerenciar bots.
keywords: Visão geral, a estrutura de tópicos de Introdução, SDK
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: overview
ms.service: bot-service
ms.date: 11/15/2019
ms.openlocfilehash: c2e02e8fef44a2a687b5c28c881082a2a8f58a62
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456324"
---
# <a name="what-is-the-bot-framework-sdk"></a>O que é o SDK do Bot Framework?

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

<!-- Alternate titles:
# About the Bot Framework SDK and the Azure Bot Service
-->

O bot Framework, juntamente com o serviço de bot do Azure, fornece ferramentas para criar, testar, implantar e gerenciar bots inteligentes, tudo em um só lugar. A estrutura de bot inclui um SDK modular e extensível para a criação de bots, bem como ferramentas, modelos e serviços de ia relacionados. Com essa estrutura, os desenvolvedores podem criar bots que usam fala, compreender a linguagem natural, lidar com perguntas e respostas e muito mais.

<!--
    - [Azure] Bot Service - The service that accelerates the process of developing a bot. It provisions a web host with one of five bot templates that can be modified in an integrated environment.
    - bot service - An instance created by the user using Azure Bot Service.
    - [Microsoft] Bot Framework - The comprehensive offering to build and deploy high quality bots for users to enjoy wherever they are talking. Users can start conversations with your bot from any channel that you've configured your bot to work on, such as SMS, Skype, Slack, Facebook, and other popular services.
-->

## <a name="what-is-a-bot"></a>O que é um bot?

Os bots fornecem uma experiência na qual parece que estamos lidando com uma pessoa, ou pelo menos um robô inteligente, e não com uma máquina. Eles podem ser usados para transferir simples tarefas repetitivas, como fazer a reserva para um jantar ou coletar informações de perfil, para sistemas automatizados que não exigem uma intervenção humana direta. Os usuários conversam com um bot usando texto, cartões interativos e fala. Uma interação de bot pode ser uma pergunta e resposta rápidas, ou pode ser uma conversa sofisticada que fornece acesso aos serviços de forma inteligente.

Um bot pode ser considerado como um aplicativo Web que tem uma interface de conversação.
Um usuário se conecta a um bot por meio de um canal, como o Facebook, a margem de atraso ou o Microsoft Teams.

- Os _motivos_ de bot sobre a entrada e executa tarefas relevantes. Isso pode incluir solicitar informações adicionais ao usuário ou acessar serviços em nome do usuário.
- O bot executa o reconhecimento na entrada do usuário para interpretar o que o usuário está solicitando ou dizendo.
- O bot gera respostas para enviar ao usuário para comunicar o que o bot está fazendo ou fez.
- Dependendo de como o bot é configurado e como ele é registrado no canal, os usuários podem interagir com o bot por meio de texto ou fala, e a conversa pode incluir imagens e vídeos.

> [!div class="mx-imgBorder"]
> ![Um bot remoto interage com um usuário em um dispositivo por meio de texto, fala, imagens ou vídeo](./media/architecture/what-is-a-bot.png)

Os bots são muito parecidos com aplicativos Web modernos, vivendo na Internet e usando APIs para enviar e receber mensagens. O que um bot oferece varia muito dependendo do tipo de bot. Um software de bot moderno se baseia em um conjunto de tecnologias e ferramentas para fornecer experiências cada vez mais complexas em uma ampla variedade de plataformas. No entanto, um bot simples pode apenas receber uma mensagem e ecoá-la para o usuário com muito pouco código envolvido.

Os bots podem fazer as mesmas coisas que outros tipos de software podem fazer: ler e gravar arquivos, usar bancos de dados e APIs e realizar tarefas computacionais normais. O que torna os bots únicos é o uso de mecanismos geralmente reservados à comunicação entre seres humanos.

O serviço de bot do Azure e o bot Framework oferecem:

- O SDK do bot Framework para desenvolvimento de bots
- Ferramentas do Bot Framework para cobrir o fluxo de trabalho de desenvolvimento de bot de ponta a ponta
- Serviço do Bot Framework (BFS) para enviar e receber mensagens e eventos entre bots e canais
- Implantação do bot e configuração do canal no Azure

Além disso, os bots podem usar outros serviços do Azure, como:

- Serviços Cognitivos do Azure para compilar aplicativos inteligentes
- Armazenamento do Azure para a solução de armazenamento em nuvem

<!-- Bot Framework Service - The service that communicates with your bot to send and receive messages and events. -->
<!-- [Microsoft] Azure Storage - The Microsoft Azure service that lets you store binary data and text data in blobs, unstructured non-relational data in tables, and messages for workflow and communication in queues. -->
<!-- [Microsoft] Cognitive Services - The family of services to build apps with powerful algorithms using just a few lines of code, which work across devices and platforms such as iOS, Android, and Windows, are easy to set up and enable natural and contextual interaction with tools that augment users' experiences using the power of machine-based intelligence. Microsoft Translator is part of Cognitive Services. -->

## <a name="building-a-bot"></a>Criação de um bot

O Serviço de Bot do Azure e o Bot Framework oferecem um conjunto integrado de ferramentas e serviços para facilitar esse processo. Escolha seu ambiente de desenvolvimento favorito ou ferramentas de linha de comando para criar seu bot. Os SDKs existem para C#, JavaScript, Typescript e o Python (o SDK para Java está em desenvolvimento). Nós fornecemos ferramentas para vários estágios de desenvolvimento de bot para ajudá-lo a projetar e criar bots.

![Visão geral do bot](media/bot-service-overview.png)

### <a name="plan"></a>Plano

Assim como acontece com qualquer tipo de software, é importante que o processo de criação de um bot bem-sucedido tenha uma compreensão detalhada dos objetivos, processos e necessidades dos usuários. Antes de escrever código, revise as diretrizes de [design do](bot-service-design-principles.md)  para obter práticas recomendadas e identifique as necessidades do seu bot. Você pode criar um bot simples ou incluir recursos mais sofisticados, como fala, compreensão de linguagem natural ou respostas a perguntas.

### <a name="build"></a>Build

Seu bot é um serviço Web que implementa uma interface de conversação e se comunica com o Serviço do Bot Framework para enviar e receber mensagens e eventos. O Bot Framework Service é um dos componentes do Serviço de Bot do Azure e do Bot Framework. Você pode criar bots em quantos ambientes e idiomas desejar. Você pode iniciar o desenvolvimento de bot no [portal do Azure](bot-service-quickstart.md) ou usar modelos de [[C#](dotnet/bot-builder-dotnet-sdk-quickstart.md) | [JavaScript](javascript/bot-builder-javascript-quickstart.md) | [Python](python/bot-builder-python-quickstart.md)] para o desenvolvimento local.

Como parte do Serviço de Bot do Azure e do Bot Framework, oferecemos componentes adicionais que você pode usar para estender a funcionalidade do seu bot:

| Recurso | Descrição | Link |
| --- | --- | --- |
| Adicionar processamento de idioma natural | Habilite seu bot para entender a linguagem natural, entender os erros de ortografia, usar a fala e reconhecer a intenção do usuário | Como usar o [LUIS](~/v4sdk/bot-builder-howto-v4-luis.md)
| Responder a perguntas | Adicionar uma base de conhecimento para responder a perguntas que os usuários fazem de forma mais natural e conversacional | Como usar o [QnA Maker](~/v4sdk/bot-builder-howto-qna.md)
| Gerenciar vários modelos | Se você estiver usando mais de um modelo, como LUIS e QnA Maker, determine de forma inteligente quando usar qual durante a conversa do seu bot | Ferramenta de [expedição](~/v4sdk/bot-builder-tutorial-dispatch.md)|
| Adicionar botões e cartões | Aprimore a experiência do usuário com mídia diferente de texto, como gráficos, menus e cartões | Como [adicionar cartões](v4sdk/bot-builder-howto-add-media-attachments.md) |

> [!NOTE]
> A tabela acima não é uma lista abrangente. Explore os artigos à esquerda, começando com [enviando mensagens](~/v4sdk/bot-builder-howto-send-messages.md), para mais funcionalidades do bot.

Além disso, fornecemos ferramentas de linha de comando para ajudá-lo a criar, gerenciar e testar os ativos do bot. Essas ferramentas podem configurar aplicativos LUIS, criar uma base de conhecimento de QnA, construir modelos para despacho entre componentes, simular uma conversa e muito mais. Você pode encontrar mais detalhes no arquivo [Leiame](https://aka.ms/botbuilder-tools-readme) das ferramentas de linha de comando.

Você também tem acesso a vários [exemplos](https://github.com/microsoft/botbuilder-samples) que demonstram muitos dos recursos disponíveis por meio do SDK. Eles são excelentes para desenvolvedores que buscam um ponto de partida com mais recursos.

### <a name="test"></a>Teste
Os bots estão aplicativos complexos, com muitas partes diferentes, trabalhem em conjunto. Como qualquer outro aplicativo complexo, isso pode levar a alguns erros interessantes ou fazer com que seu bot se comporte de maneira diferente do esperado. Antes de publicar, teste seu bot. Fornecemos várias maneiras de testar bots antes que eles sejam liberados para uso:

- Teste seu bot localmente com o [emulador](bot-service-debug-emulator.md). O Bot Framework Emulator é um aplicativo autônomo que não só fornece uma interface de chat, mas também ferramentas de depuração e de interrogação para ajudar a entender como e por que seu bot faz o que ele faz.  O emulador pode ser executado localmente junto com seu aplicativo de bot no desenvolvimento.

- Teste o bot na [Web](bot-service-manage-test-webchat.md). Uma vez configurado no portal do Azure, seu bot também pode ser acessado através de uma interface de chat da Web. A interface de chat da Web é uma ótima maneira de conceder acesso ao bot para testadores e outras pessoas que não têm acesso direto ao código de execução do bot.

- [Teste de unidade](https://docs.microsoft.com/azure/bot-service/unit-test-bots) seu bot com a atualização de julho do SDK do Bot Framework.

### <a name="publish"></a>Publicar

Quando estiver pronto para disponibilizar seu bot na Web, publique seu bot no [Azure](bot-builder-howto-deploy-azure.md) ou em seu próprio serviço Web ou data center. A primeira etapa para o bot ganhar vida em seu site, ou dentro de canais de chat, é ter um endereço na Internet pública.

### <a name="connect"></a>Conectar

Conecte seu bot a canais como Facebook, Messenger, Kik, Slack, Microsoft Teams, Telegram, texto/SMS, Twilio e Cortana. O Bot Framework faz a maior parte do trabalho necessário para enviar e receber mensagens de todas essas diferentes plataformas. Seu aplicativo bot recebe um fluxo de mensagens unificado e normalizado, independentemente do número e tipo de canais aos quais ele está conectado. Para obter informações sobre como adicionar canais, confira o tópico [canais](bot-service-manage-channels.md).

### <a name="evaluate"></a>Avaliar

Use os dados coletados no portal do Azure para identificar oportunidades de melhorar os recursos e o desempenho do seu bot. Você pode obter dados de nível de serviço e de instrumentação, como tráfego, latência e integrações. O Google Analytics também fornece relatórios no nível da conversa sobre dados de usuários, mensagens e canais. Para obter mais informações, confira [como reunir as análises](bot-service-manage-analytics.md).

## <a name="next-steps"></a>Próximas etapas

Confira estes [estudos de caso](https://azure.microsoft.com/services/bot-service/) de bots ou clique no link abaixo para criar um bot.
> [!div class="nextstepaction"]
> [Criar um bot](bot-service-quickstart.md)
