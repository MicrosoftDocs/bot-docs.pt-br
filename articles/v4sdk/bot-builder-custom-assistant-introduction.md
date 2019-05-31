---
title: Visão geral do Assistente Personalizado | Microsoft Docs
description: Saiba mais sobre como criar seu próprio Assistente Personalizado
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f4b8243580ee678390177881b136a9016be4a786
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215461"
---
## <a name="custom-assistant-overview"></a>Visão geral do Assistente Personalizado

## <a name="overview"></a>Visão geral

Temos presenciado uma necessidade significativa de clientes e parceiros em fornecer um assistente de conversação adaptado à marca, personalizado para os clientes e disponibilizado em uma ampla variedade de dispositivos e telas de conversação. Dando continuidade à abordagem de software livre da Microsoft em relação ao SDK do Bot Framework, o Assistente Pessoal Personalizado de código aberto oferece controle total sobre a experiência do usuário final desenvolvida com base em um conjunto de recursos básicos. Além disso, a experiência pode incorporar inteligência sobre o usuário final e informações de qualquer dispositivo/ecossistema para oferecer uma experiência realmente integrada e inteligente.

Acreditamos plenamente que nossos clientes devem poder fortalecer seus relacionamentos com os clientes e obter seus insights. Assim, qualquer assistente personalizado oferecerá controle total da experiência do usuário para nossos clientes e parceiros. O nome, a voz e a personalidade podem ser alterados para atender às necessidades da organização. A solução de Assistente Personalizado simplifica a criação de seu próprio assistente, permitindo que você comece a usá-lo em minutos. 

O escopo da funcionalidade do Assistente Pessoal Personalizado é amplo e geralmente oferece aos usuários finais vários recursos. Para aumentar a produtividade do desenvolvedor e permitir um ecossistema vibrante de experiências de conversação reutilizáveis, estamos oferecendo aos desenvolvedores exemplos inicias de habilidades de conversação reutilizáveis. Essas habilidades podem ser adicionadas a um aplicativo de conversação para dar vida a uma experiência de conversa específica, por exemplo, para localizar um ponto de interesse, interagir com o calendário, tarefas, emails e muitos outros cenários. As habilidades são totalmente personalizáveis e consistem em modelos de linguagem para várias linguagens, diálogos e códigos.

No momento estamos executando uma versão prévia e trabalhando com clientes e parceiros inicias em um repositório de software livre para dar vida às primeiras experiências e disponibilizá-las mais amplamente nos próximos meses. 

![Diagrama do Assistente Personalizado](media/enterprise-template/CustomAssistantDiagram.jpg)

## <a name="complete-control-of-the-user-experience"></a>Total controle da experiência do usuário

Todos os aspectos da experiência do usuário final são seus e é você que os controla. Isso inclui a Identidade Visual, Nome, Voz, Personalidade, Respostas e Avatar. Os códigos-fonte do Assistente Personalizado e das habilidades de suporte são fornecidos integralmente, permitindo que você os ajuste conforme a necessidade.

## <a name="complete-ownership-and-control-of-data"></a>Total controle de dados e propriedade

O Assistente Personalizado que será implantado na assinatura do Azure. Portanto, todos os dados gerados pelo seu assistente (perguntas feitas, comportamento do usuário e outros) ficam inteiramente restritos à sua assinatura do Azure. Confira mais especificamente a [Nuvem Confiável do Azure para os Serviços Cognitivos](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) e a [seção do Azure na Central de Confiabilidade](https://www.microsoft.com/en-us/TrustCenter/CloudServices/Azure) para obter mais informações.

## <a name="your-assistant-anywhere"></a>Seu assistente em qualquer lugar...

O Assistente Personalizado utiliza a plataforma de inteligência artificial de conversação da Microsoft e, portanto, pode aparecer em qualquer [canal](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0) do Bot Framework, por exemplo, WebChat, FaceBook Messenger, Skype, etc. Além disso, pelo canal [Linha Direta](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts?view=azure-bot-service-4.0), podemos inserir experiências na área de trabalho e nos aplicativos móveis, incluindo dispositivos como Carros, Alto-falantes, Despertadores, etc.

## <a name="built-on-enterprise-grade-technology"></a>Baseado na mesma tecnologia do Enterprise

A solução Assistente Personalizado é criada com base no Serviço de Bot do Azure, no Serviço Cognitivo de Reconhecimento Vocal, na Fala Unificada e em um amplo conjunto de componentes de suporte do Azure, o que significa que você se beneficia da [infraestrutura global do Azure](https://azure.microsoft.com/en-gb/global-infrastructure/).

Além disso, o suporte ao Reconhecimento Vocal é fornecido pelo Serviço Cognitivo LUIS, que dá suporte a um amplo conjunto de idiomas [listados aqui](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-supported-languages). O [Serviço Cognitivo de Tradução](https://azure.microsoft.com/en-us/services/cognitive-services/translator-text-api/) fornece recursos adicionais de Tradução Automática para ampliar ainda mais o alcance de seu Assistente Personalizado.

## <a name="integrated-and-context-aware"></a>Integrado e contextual

O Assistente Personalizado pode ser integrado ao dispositivo e ao ecossistema, permitindo uma experiência totalmente integrada e inteligente. Com esse reconhecimento contextual, experiências mais inteligentes podem ser desenvolvidas para oferecer ainda mais personalização.

## <a name="3rd-party-assistant-integration"></a>Integração com assistentes de terceiros

O Assistente Personalizado permite que você crie sua própria experiência exclusiva, mas também deixa o trabalho para o Assistente Digital escolhido pelo usuário final, dependendo do tipo de pergunta.

## <a name="flexible-integration"></a>Integração flexível

A arquitetura do Assistente Personalizado é flexível e pode ser integrada a outros investimentos que você possa ter feito em recursos de processamento da Fala ou de Linguagem Natural e, claro, a seus sistemas de back-end e APIs existentes.

## <a name="adaptive-cards"></a>Cartões Adaptáveis

Os [Cartões Adaptáveis](https://adaptivecards.io/) permitem que o Assistente Personalizado retorne elementos da Experiência do usuário (por exemplo, Cartões, Imagens, Botões) juntamente com respostas de texto. Se a tela do dispositivo ou da conversa tiver uma tela, esses Cartões Adaptáveis podem ser renderizados entre vários dispositivos e plataformas, oferecendo suporte à Experiência do usuário quando for o caso. Exemplos de Cartões Adaptáveis podem ser encontrados [aqui](https://adaptivecards.io/samples/), com informações sobre as opções de renderização [nesta](https://docs.microsoft.com/en-us/adaptive-cards/rendering-cards/getting-started) documentação.


## <a name="skills"></a>Habilidades

Além do assistente de base, existe um amplo conjunto de recursos comuns que devem ser criados pelos próprios desenvolvedores. A produtividade é um ótimo exemplo de como cada organização precisa criar Modelos de linguagem (LUIS), Diálogos (Código), Integração (Código) e Geração de linguagem (Respostas) para habilitar cenários comuns como Ponto de interesse, Email, Calendário ou Tarefas.

Isso fica ainda mais complicado pela necessidade de dar suporte a vários idiomas e resultados em uma grande quantidade de trabalho exigida de uma organização que esteja criando seu próprio assistente.

A solução Assistente Personalizado inclui um novo recurso de habilidade que permite que novos recursos sejam inseridos em um assistente personalizado somente por meio de configuração. 

Todos os aspectos de cada habilidade (Modelo de linguagem, Diálogos, Código de integração e Geração de linguagem) são totalmente personalizados por desenvolvedores, já que o código-fonte completo é fornecido no GitHub, juntamente com o Assistente Personalizado.

## <a name="example-scenarios"></a>Cenários de Exemplo

O Assistente Personalizado abrange vários cenários do setor. Cenários de exemplo são mostrados abaixo para fins de referência:

- Indústria automotiva: Assistente Pessoal habilitado por voz integrado ao carro que oferece aos usuários finais a capacidade de executar funções tradicionais do carro (por exemplo, navegação, rádio) além dos cenários com foco na produtividade, como remarcar reuniões quando você estiver atrasado ou adicionar itens à sua lista de tarefas, além de experiências proativas em que o carro pode sugerir tarefas com base em eventos, como dar partida no motor, ir para casa ou habilitar o modo de piloto automático. Os Cartões Adaptáveis são renderizados na integração da Unidade Principal e Fala, executados por meio de interações Apertar para Falar e Wake Word (palavra de início do Assistente).

- Hospitalidade: Assistente Pessoal habilitado para voz integrado a um dispositivo de quarto de hotel que fornece uma ampla variedade de cenários voltados para hospitalidade (por exemplo, estender sua estadia, solicitação de check-out tardio, serviço de quarto), incluindo concierge e a possibilidade de encontrar restaurantes e atrações locais. A opção de vinculação com suas contas de produtividade permitem experiências mais personalizadas, como alarmes sugeridos, avisos meteorológicos e aprendizado de padrões entre estadias. Uma evolução da personalização da TV existente nas salas atualmente.

- Empresas: experiências personalizadas de Assistente do Funcionário habilitado por voz e texto e integradas a dispositivos da empresa e a telas de conversa existentes (por exemplo, Teams, WebChat, Slack), permitindo que os funcionários administrem seus calendários, encontrem salas de reunião disponíveis, localizem pessoas com habilidades específicas ou realizem operações de RH. 

## <a name="getting-started"></a>Introdução

No momento estamos executando uma versão prévia e trabalhando com clientes e parceiros inicias em um repositório de software livre para dar vida às primeiras experiências e disponibilizá-las mais amplamente nos próximos meses. Para registrar seu interesse e começar a usar, preencha [este formulário](https://aka.ms/customassistantpreviewform) e entraremos em contato com você.

