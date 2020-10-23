---
title: Visão geral do Assistente Virtual – Serviço de Bot
description: Familiarize-se com o modelo de assistente virtual do bot Framework. Saiba mais sobre os recursos, entenda os princípios de design e veja cenários de exemplo.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 86080cde3f05e8c193a6d6e1a662c0a8cd01b318
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92413598"
---
# <a name="virtual-assistant-overview"></a>Visão geral do Assistente Virtual

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

## <a name="overview"></a>Visão geral

Clientes e parceiros têm uma necessidade significativa de fornecer um assistente de conversação adaptado à sua marca, personalizado para seus usuários e disponibilizado em uma ampla variedade de telas e dispositivos.

Dando continuidade à abordagem de software livre da Microsoft em relação ao SDK do Bot Framework, a solução de Assistente Virtual de código aberto oferece um conjunto de recursos básicos e controle total sobre a experiência do usuário final.

Este modelo incorpora o Enterprise Template anterior, reúne todos os componentes de suporte e práticas recomendadas identificados por meio da construção de experiências de conversação e simplifica bastante a criação de um projeto novo do bot que inclui: tentativas de conversação básicas, integração de expedição, QnA Maker, Application Insights e uma implantação automatizada.

Acreditamos plenamente que nossos clientes devem conduzir e fortalecer os insights de seus clientes e seu relacionamento com eles. Assim, um Assistente Virtual fornece controle total sobre a experiência do usuário para nossos clientes e parceiros com a disponibilização do código de software livre no GitHub. O nome, a voz e a personalidade podem ser alterados para atender às necessidades da organização. Nossa solução de Assistente Virtual simplifica a criação de seu próprio assistente, permitindo que você comece a usá-lo em minutos e evolua usando nossas ferramentas de desenvolvimento de ponta a ponta.

O escopo da funcionalidade do Assistente Virtual é amplo, normalmente oferecendo aos usuários finais vários recursos. Para aumentar a produtividade do desenvolvedor e permitir um ecossistema vibrante de experiências de conversação reutilizáveis, estamos oferecendo aos desenvolvedores exemplos inicias de habilidades de conversação reutilizáveis. Essas habilidades podem ser adicionadas a um aplicativo de conversação para dar vida a uma experiência de conversa específica, por exemplo, para localizar um ponto de interesse, interagir com o calendário, tarefas, emails e muitos outros cenários. As habilidades são totalmente personalizáveis e consistem em modelos de linguagem para várias linguagens, diálogos e códigos.

![Diagrama do Assistente Virtual](./media/enterprise-template/customassistantdiagram.jpg)

## <a name="getting-started"></a>Introdução

Examine a documentação [Assistente Virtual e Habilidades](https://aka.ms/bf-solutions-docs) para ver informações mais detalhadas.

## <a name="whats-in-the-box"></a>Conteúdo

O Modelo de Assistente Virtual reúne várias da melhores práticas que identificamos durante a construção de experiências de conversação e automatiza a integração de componentes que consideramos altamente benéficos para desenvolvedores do Bot Framework. Esta seção cobre algumas informações básicas em relação a decisões importantes para ajudar a explicar por que o modelo funciona dessa forma.

Agora o modelo de Assistente Virtual incorpora as funcionalidades do Enterprise Template anterior, incluindo intenções de conversação básicas em vários idiomas, Expedição, QnA e insights de conversa. As funcionalidades a seguir, relacionadas ao assistente, estão atualmente disponíveis. Outras funcionalidades estão sendo planejadas e trabalharemos junto aos nossos clientes e parceiros para a criação de um roteiro.

Recurso | Descrição |
------------ | -------------
Integração | Um exemplo de fluxo de integração que permite ao assistente cumprimentar o usuário e coletar informações iniciais.
Arquitetura de eventos | Eventos no contexto do Assistente Virtual permitem que o aplicativo cliente que está hospedando o Assistente (em um navegador da Web ou em um dispositivo como um carro ou alto-falante) obtenha informações sobre os eventos de usuário ou dispositivo enquanto recebe eventos para executar operações do dispositivo.
Contas vinculadas | Em um cenário de fala, não é prático para um usuário inserir seu nome de usuário e senha para dar suporte a sistemas de comandos de voz. Portanto, uma experiência complementar separada dá ao usuário a oportunidade de entrar e conceder permissão a um Assistente Virtual para recuperar tokens para uso posterior.
Habilitação de habilidades | Todo programador deve desenvolver por conta própria o vasto conjunto de recursos comuns disponíveis atualmente. A solução Assistente Virtual inclui uma nova habilidade que permite que novos recursos sejam conectados a um Assistente Virtual usando somente configurações, além de fornecer um mecanismo de autenticação para que as habilidades solicitem tokens para atividades downstream.
Habilidade de ponto de interesse | A versão prévia da habilidade ponto de interesse (PoI) fornece um modelo de linguagem abrangente para localizar pontos de interesse e solicitar direções. Atualmente, a habilidade fornece integração com o Azure Mapas.
Habilidade de calendário | A versão prévia da habilidade de calendário fornece um modelo de linguagem abrangente para atividades comuns relacionadas a calendário. A habilidade, no momento, está integrada ao Microsoft Graph (Office 365/Outlook.com) e o suporte para APIs do Google chegará em breve.
Habilidade de email | A versão prévia da habilidade de email fornece um modelo de linguagem abrangente para atividades comuns relacionadas a email. A habilidade, no momento, está integrada ao Microsoft Graph (Office 365/Outlook.com) e o suporte para APIs do Google chegará em breve.
Habilidade de tarefas | A versão prévia da Habilidade de tarefas pendentes fornece um modelo de linguagem abrangente para atividades comuns relacionadas a tarefas pendentes. A habilidade, no momento, está integrada ao OneNote e o suporte a Microsoft Graph (outlookTask) chegará em breve.
Integração do dispositivo | Nossos SDKs do Serviço de Bot do Azure (DirectLine), junto com o cartão adaptável e os SDKs de Fala facilitam a integração multiplataforma com os dispositivos. Outros exemplos de integração de dispositivos e plataforma, incluindo o Microsoft Edge, estão planejados.
Agentes de teste | Além do Bot Framework Emulator, um agente de teste baseado em WebChat é fornecido para permitir o teste de cenários mais complexos de autenticação. Um agente de teste simples baseado em console demonstra a abordagem de troca de mensagens a fim de mostrar a facilidade de integração do dispositivo.
Implantação automatizada | Todos os recursos do Azure necessários para o seu Assistente são implantados automaticamente: Registro de bot, Serviço de Aplicativo do Azure, LUIS, QnA Maker, Content Moderator, Cosmos DB, Armazenamento do Azure e Application Insights. Além disso, modelos do LUIS para todas as habilidades, do QnAMaker e de expedição são criados, treinados e publicados para habilitar o teste de imediato.
Modelo de linguagem automotiva | Um modelo de linguagem automotiva que abrange domínios nucleares, como telefone, navegação e controle de recursos no carro, estará disponível em breve.

>[!IMPORTANT]
> A classe de _armazenamento do Cosmos DB_ foi preterida. Os contêineres criados originalmente com CosmosDbStorage não tinham nenhum conjunto de chaves de partição e receberam a chave de partição padrão de _ \/ _partitionKey_.
>
> Contêineres criados com _Cosmos DB armazenamento_ podem ser usados com _Cosmos DB armazenamento particionado_. Para obter mais informações, leia [Particionamento no Azure Cosmos DB](https://aka.ms/azure-cosmosdb-partitioning-overview).
>
> Observe também que, diferentemente do armazenamento de Cosmos DB herdado, o armazenamento particionado Cosmos DB não cria automaticamente um banco de dados dentro de sua conta de Cosmos DB. Você precisa [criar um novo banco de dados manualmente](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal), mas ignorar a criação manual de um contêiner, pois o _CosmosDbPartitionedStorage_ criará o contêiner para você.

## <a name="example-scenarios"></a>Cenários de Exemplo

O Assistente Virtual pode ser usado em diversos cenários de diferentes setores. Alguns cenários de exemplo são mostrados abaixo para fins de referência.

- **Indústria automotiva**: Assistente Pessoal habilitado por voz integrado ao carro que oferece aos usuários finais a capacidade de executar funções tradicionais do carro (por exemplo, navegação, rádio) além dos cenários com foco na produtividade, como remarcar reuniões quando você estiver atrasado ou adicionar itens à sua lista de tarefas, além de experiências proativas em que o carro pode sugerir tarefas com base em eventos, tais como dar partida no motor, ir para casa ou habilitar o modo de piloto automático. Os Cartões Adaptáveis são renderizados na integração da Unidade Principal e Fala, executados por meio de interações Apertar para Falar e Wake Word (palavra de início do Assistente).

- **Hospitalidade**: Assistente Pessoal habilitado por voz integrado a um dispositivo em um quarto de hotel que oferece vários cenários voltados para hospitalidade (por exemplo, estender sua estadia, solicitar check-out tardio, serviço de quarto), incluindo concierge e a capacidade de encontrar restaurantes e atrações locais. A opção de vinculação às suas contas de produtividade permite experiências mais personalizadas, tais como alarmes sugeridos, avisos meteorológicos e aprendizado de padrões entre estadias. Uma evolução da personalização da TV existente nas salas atualmente.

- **Enterprise**: experiências personalizadas de Assistente do Funcionário habilitado por voz e texto e integradas a dispositivos da empresa e a telas de conversa existentes (por exemplo, Teams, WebChat, Slack), permitindo que os funcionários administrem seus calendários, encontrem salas de reunião disponíveis, localizem pessoas com habilidades específicas ou realizem operações de RH.

## <a name="virtual-assistant-principles"></a>Princípios do Assistente Pessoal

### <a name="your-data-your-brand-and-your-experience"></a>Seus dados, sua marca e sua experiência

Todos os aspectos da experiência do usuário final são seus e é você que os controla. Isso inclui a identidade visual, nome, voz, personalidade, respostas e avatar. Os códigos-fonte do Assistente Virtual e das habilidades de suporte são fornecidos integralmente, permitindo que você os ajuste conforme necessário.

O Assistente Virtual será implantado em sua assinatura do Azure. Portanto, todos os dados gerados pelo assistente (perguntas feitas, comportamento do usuário, etc.) estão totalmente contidos em sua assinatura do Azure. Confira a [Nuvem Confiável do Azure para os Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) e, mais especificamente, a [seção do Azure na Central de Confiabilidade](https://www.microsoft.com/TrustCenter/CloudServices/Azure) para obter mais informações.

### <a name="write-it-once-embed-it-anywhere"></a>Gravar uma vez, inserir em qualquer lugar

O Assistente Virtual utiliza a plataforma de inteligência artificial de conversação da Microsoft e, portanto, pode aparecer em qualquer [canal](../bot-service-manage-channels.md) do Bot Framework.

Além disso, é possível inserir experiências na área de trabalho e nos aplicativos móveis (assim como carros, alto-falantes e despertadores) por meio do canal [Direct Line](../rest-api/bot-framework-rest-direct-line-3-0-concepts.md).

### <a name="enterprise-grade-solutions"></a>Soluções com a mesma tecnologia do Enterprise

A solução Assistente Virtual é criada com base no Serviço de Bot do Azure, no Serviço Cognitivo de Reconhecimento Vocal e na Fala Unificada com um amplo conjunto de componentes de suporte do Azure. Isso significa que você se beneficia da [infraestrutura global do Azure](https://azure.microsoft.com/global-infrastructure/), incluindo as certificações ISO 27018, HIPAA, PCI DSS e SOC 1, 2 e 3.

Além disso, o suporte ao Reconhecimento Vocal é fornecido pelo Serviço Cognitivo LUIS, que dá suporte a um amplo conjunto de idiomas [listados aqui](https://docs.microsoft.com/azure/cognitive-services/luis/luis-supported-languages). O [Serviço Cognitivo de Tradução](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) fornece funcionalidades adicionais de Tradução Automática para ampliar ainda mais o alcance do Assistente Virtual.

### <a name="integrated-and-context-aware"></a>Integrado e contextual

Seu assistente virtual pode ser incorporado ao seu dispositivo e ecossistema, permitindo uma experiência verdadeiramente integrada e inteligente. Com esse reconhecimento contextual, experiências mais inteligentes podem ser desenvolvidas para oferecer ainda mais personalização.

### <a name="3rd-party-assistant-integration"></a>Integração com assistentes de terceiros

O Assistente Virtual permite que você crie sua própria experiência exclusiva, mas também pode deixar o trabalho para o Assistente Digital escolhido pelo usuário final, dependendo do tipo de pergunta.

### <a name="flexible-integration"></a>Integração flexível

A arquitetura de nosso Assistente Virtual é flexível e pode ser integrada a outros investimentos que você possa ter feito em funcionalidades de processamento de Fala ou de Linguagem Natural que se integram aos sistemas de back-end e APIs existentes.

### <a name="adaptive-cards"></a>Cartões Adaptáveis

Os [Cartões Adaptáveis](https://adaptivecards.io/) permitem que o Assistente Virtual retorne elementos da experiência do usuário (por exemplo, cartões, imagens, botões) junto com respostas baseadas em texto. Se a tela do dispositivo ou da conversa tiver uma tela, esses Cartões Adaptáveis poderão ser renderizados entre vários dispositivos e plataformas, oferecendo suporte à experiência do usuário quando apropriado. Exemplos de Cartões Adaptáveis podem ser encontrados [aqui](https://adaptivecards.io/samples/), com informações sobre as opções de renderização [nesta](https://docs.microsoft.com/adaptive-cards/rendering-cards/getting-started) documentação.

### <a name="skills"></a>Habilidades

Além do assistente de base, existe um amplo conjunto de funcionalidades comuns que devem ser criadas pelos próprios desenvolvedores. A produtividade é um ótimo exemplo de como cada organização precisa criar modelos de linguagem (LUIS), diálogos (código), integração (código) e geração de linguagem (respostas) para habilitar experiências comuns de calendário, tarefa ou email.

Isso é ainda mais complicado pela necessidade de oferecer suporte a vários idiomas e resulta em uma grande quantidade de trabalho necessária para qualquer organização que esteja criando seu próprio assistente.

Nossa solução Assistente Virtual inclui uma nova funcionalidade de habilidade que permite que novas funcionalidades sejam inseridas em um assistente personalizado somente por meio de configuração.

Todos os aspectos de cada habilidade (modelo de linguagem, diálogos, código de integração e geração de linguagem) são totalmente personalizados por desenvolvedores, já que o código-fonte completo é fornecido no GitHub, juntamente com o Assistente Virtual.

## <a name="next-steps"></a>Próximas etapas

Consulte os [tutoriais](https://aka.ms/bfs-tutorials) para saber como criar e implantar seu Assistente Virtual.
