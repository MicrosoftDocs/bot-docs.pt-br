---
title: Projetar a experiência do usuário | Microsoft Docs
description: Saiba como projetar seu bot para oferecer uma experiência de usuário envolvente, usando controles de usuário avançados, reconhecimento vocal em idioma natural e fala.
keywords: visão geral, design, experiência do usuário, controle de usuário avançado
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 21f5b35e992f792a5667ef05d2708b5a446f8023
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296750"
---
# <a name="design-the-user-experience"></a>Projetar a experiência do usuário

Normalmente, os bots usam uma combinação de **controles de usuário avançados**, **texto e idioma natural** e **fala** para trocar informações com usuários.

## <a name="rich-user-controls"></a>Controles de usuário avançados

**Controles de usuário avançados** são controles de interface do usuário comuns, como botões, imagens, carrosséis e menus que o bot apresenta ao usuário e com os quais o usuário se envolve para informar suas escolhas e intenções. Um bot pode usar uma coleção de controles de interface do usuário para simular um aplicativo ou pode até mesmo ser executado incorporado em um aplicativo. Quando um bot é inserido em um aplicativo ou site, ele pode representar praticamente qualquer controle de interface do usuário, aproveitando as funcionalidades do aplicativo que o está hospedando. 

Há várias décadas, os desenvolvedores de aplicativos e sites dependiam de controles de interface do usuário para permitir que os usuários interajam com seus aplicativos e esses mesmos controles de interface do usuário também podem ser muito eficazes em bots. Por exemplo, os botões são uma ótima maneira de apresentar ao usuário uma escolha simples. Permitir que o usuário informe "Hotéis" clicando em um botão rotulado **Hotéis** é mais fácil e mais rápido do que forçar o usuário a digitar "Hotéis". Isso especialmente se aplica a dispositivos móveis, em que o clique é geralmente preferível à digitação.

## <a name="cards"></a>Cartões

Os cartões permitem apresentar aos usuários uma variedade de mensagens visuais, de áudio e/ou selecionáveis e ajudar no fluxo da conversa. Se um usuário precisar selecionar um item de um conjunto fixo de itens, você poderá exibir um carrossel de cartões, cada um contendo uma imagem, uma descrição de texto e um botão de seleção única. Se um usuário tiver um conjunto de opções para um único item, você poderá apresentar uma única imagem menor e uma coleção de botões com várias opções para escolha. Ele pediu mais informações sobre um assunto? Os cartões podem fornecer informações detalhadas usando a saída de áudio ou de vídeo ou um recibo que forneça detalhes sobre sua experiência de compra. Há uma variedade incrivelmente ampla de usos para cartões para ajudar a guiar a conversa entre o usuário e o bot. O tipo de cartão usado será determinado pelas necessidades do aplicativo. Vamos dar uma olhada nos cartões, em suas ações e em alguns usos recomendados. 

Os cartões do Serviço de Bot da Microsoft são objetos programáveis que contêm coleções padronizadas de controles de usuário avançados que são reconhecidos em uma ampla variedade de canais. A tabela a seguir descreve a lista de cartões disponíveis e sugestões de melhor prática de uso para cada tipo de cartão.

| Tipo de cartão | Exemplo | DESCRIÇÃO |
| ---- | ---- | ---- |
| AdaptiveCard | ![Imagem do Cartão adaptável](~/media/adaptive-card.png) | Um formato de troca de cartão aberto renderizado como um objeto JSON. Normalmente usado para implantação entre canais de cartões. Os cartões se adaptam à aparência de cada canal de host. |
| AnimationCard | ![Imagem de Cartão de animação](~/media/animation-card1.png) | Um cartão que pode reproduzir GIFs animados ou vídeos curtos. |
| AudioCard | ![Imagem de Cartão de áudio](~/media/audio-card.png) | Um cartão que pode reproduzir um arquivo de áudio. |
| HeroCard | ![Imagem de Cartão Hero](~/media/hero-card1.png) | Um cartão que contém uma única imagem grande, um ou mais botões e um texto. Normalmente usado para realçar visualmente uma seleção potencial do usuário. |
| ThumbnailCard | ![Imagem do Cartão em miniatura](~/media/thumbnail-card.png) | Um cartão que contém uma única imagem em miniatura, um ou mais botões e um texto. Normalmente usado para realçar visualmente os botões de uma seleção potencial do usuário. |
| ReceiptCard | ![Imagem de Cartão de recibo](~/media/receipt-card1.png) | Um cartão que permite a um bot fornecer um recibo para o usuário. Normalmente, contém a lista de itens a serem incluídos no recibo, informações fiscais e de totais e outros textos. |
| SignInCard | ![Imagem de Cartão de entrada](~/media/sign-in-card.png) | Um cartão que permite a um bot solicitar a entrada do usuário. Normalmente, contém um texto e um ou mais botões nos quais o usuário pode clicar para iniciar o processo de entrada. |
| SuggestedAction | ![Imagem de Cartão de ações sugeridas](~/media/suggested-actions.png) | Apresenta ao usuário um conjunto de CardActions que representa uma escolha do usuário. Esse cartão desaparecerá após a seleção de uma das ações sugeridas. |
| VideoCard | ![Imagem de Cartão de vídeo](~/media/video-card.png) | Um cartão que pode reproduzir vídeos. Normalmente usado para abrir uma URL e transmitir um vídeo disponível. |
| CardCarousel | ![Imagem de Carrossel de cartões](~/media/card-carousel.png) | Uma coleção horizontalmente rolável de cartões que permite ao usuário exibir facilmente uma série de opções possíveis de escolhas do usuário.|

Os cartões permitem que você projete o bot uma vez para que ele funcione em uma variedade de canais. No entanto, nem todos os tipos de cartão são totalmente compatíveis com todos os canais disponíveis. As tabelas a seguir apresentam uma amostragem do suporte atual para os tipos de cartão em uma seleção de canais. Para obter as últimas informações sobre o suporte de canal, não se esqueça de usar o [Inspetor de Canal](bot-service-channel-inspector.md) para ver a aparência e a função de cartões específicos em seu canal de interesse. Para obter informações detalhadas sobre os cartões adaptáveis, veja também o <a href="http://adaptivecards.io/visualizer/">Visualizador de Cartões Adaptáveis</a>.

A tabela abaixo mostra o suporte de canal para os tipos de cartão geralmente usados em um carrossel de cartões.

| Canal | Adaptável | Hero | Miniatura | Carrossel |
| ---- | ---- | ---- | ---- | ---- |
| Cortana | ✔ | ✔ | ✔ | ✔ |
| Email do O365 | ✔ | ✔ | ✔ | ✔ |
| Facebook | - | ✔ | ✔ | ✔ |
| Kik | ✔ | ✔ | ✔ | ✔ |
| Skype | ✔ | ✔ | ✔ | ✔ |
| Margem de atraso | ✔ | ✔ | ✔ | ✔ |
| sms | ✔ | ✔ | ✔ | ✔ |
| Teams | ✔ | ✔ | ✔ | ✔ |
| Webchat | ✔ | ✔ | ✔ | ✔ |

A tabela a seguir mostra o suporte de canal para tipos adicionais de cartões do Serviço de Bot da Microsoft.

| Canal | Vídeo | Receipt | SignIn | SuggestedAction |
| ---- | ---- | ---- | ---- | ---- |
| Cortana | ✔ | ✔ | ✔ | - |
| Email do O365 | ✔ | ✔ | ✔ | ✔ |
| Facebook | ✔ | ✔ | ✔ | ✔ |
| Kik | ✔ | ✔ | ✔ | ✔ |
| Skype | ✔ | ✔ | ✔ | ✔ |
| Margem de atraso | ✔ | ✔ | ✔ | ✔ |
| sms | ✔ | ✔ | - | ✔ |
| Teams | - | ✔ | ✔ | - |
| Webchat | ✔ | ✔ | ✔ | ✔ |

✔ = compatível

Encontre instruções detalhadas para a adição de cartões ao bot nestas seções [Adicionar anexos de mídia de cartão avançado](v4sdk/bot-builder-howto-add-media-attachments.md) e [Adicionar ações sugeridas às mensagens](v4sdk/bot-builder-howto-add-suggested-actions.md).

Ao projetar o bot, não descarte automaticamente elementos comuns de interface do usuário como não sendo "inteligentes o suficiente". Conforme abordado [anteriormente](~/bot-service-design-principles.md#designing-a-bot), o bot deve ser projetado para resolver o problema do usuário da maneira mais eficaz, rápida e fácil possível. Evite a tentação de começar incorporando o reconhecimento vocal em idioma natural, pois ele costuma ser desnecessário e introduz uma complexidade não justificada.

> [!TIP]
> Comece usando os controles mínimos de interface do usuário que permitam ao bot resolver o problema do usuário e adicione outros elementos posteriormente, caso esses controles não sejam mais suficientes.


## <a name="text-and-natural-language-understanding"></a>Reconhecimento vocal em idioma natural e de texto

Um bot pode aceitar uma entrada de **texto** dos usuários e tentar analisar essa entrada usando a correspondência de expressão regular ou APIs de **reconhecimento vocal em idioma natural**, como o <a href="https://www.luis.ai" target="_blank">LUIS</a>. Dependendo do tipo de entrada fornecida pelo usuário, o reconhecimento vocal em idioma natural pode ou não ser uma boa solução.

Em alguns casos, um usuário pode estar **respondendo a uma pergunta muito específica**. Por exemplo, se o bot pergunta, "Qual é o seu nome?", o usuário pode responder com um texto que especifique apenas o nome, "Julio", ou com uma frase, "Meu nome é Julio".

Fazer perguntas específicas reduz o escopo das possíveis respostas que o bot pode razoavelmente receber, o que reduz a complexidade da lógica necessária para analisar e compreender a resposta. Por exemplo, considere a seguinte pergunta ampla em aberto: "Como você está se sentindo?". É uma tarefa muito complexa compreender as muitas permutações possíveis de respostas potenciais a uma pergunta como essa.

Por outro lado, perguntas específicas, como "Você está sentindo dor? sim/não" e "Em que local está sentindo dor? peito/cabeça/braço/perna", provavelmente gerarão respostas mais específicas que um bot pode analisar e compreender, sem a necessidade de implementar o reconhecimento vocal em idioma natural. 

> [!TIP]
> Sempre que possível, faça perguntas específicas que não exigirão funcionalidades de reconhecimento vocal em idioma natural ao analisar a resposta. Isso simplificará o bot e aumentará o sucesso de que o bot compreenda o usuário.

  
Em outros casos, um usuário pode estar **digitando um comando específico**. Por exemplo, um bot de DevOps que permite aos desenvolvedores gerenciar máquinas virtuais pode ser criado para aceitar comandos específicos, como "/STOP VM XYZ" ou "/START VM XYZ". Projetar um bot para aceitar comandos específicos como esses gera uma boa experiência do usuário, pois a sintaxe é fácil de aprender e o resultado esperado de cada comando é claro. Além disso, o bot não exigirá funcionalidades de reconhecimento vocal em idioma natural, pois a entrada do usuário pode ser analisada com facilidade usando expressões regulares. 

> [!TIP]
> Projetar um bot para exigir comandos específicos do usuário geralmente oferece uma boa experiência do usuário, ao mesmo tempo que elimina a necessidade da funcionalidade de reconhecimento vocal em idioma natural.

  
No caso de um bot *de base de dados de conhecimento* ou de um bot de *perguntas e respostas*, um usuário pode estar fazendo **perguntas gerais**. Por exemplo, imagine um bot que possa responder a perguntas com base no conteúdo de milhares de documentos. O <a href="https://qnamaker.ai" target="_blank">QnA Maker</a> e o <a href="https://azure.microsoft.com/en-us/services/search/" target="_blank">Azure Search</a> são tecnologias que foram criadas especificamente para esse tipo de cenário. Para obter mais informações, confira [Projetar bots de conhecimento](bot-service-design-pattern-knowledge-base.md).

> [!TIP]
> Se estiver criando um bot que responderá perguntas com base em dados estruturados ou não estruturados de bancos de dados, páginas da Web ou documentos, considere o uso de tecnologias que foram projetadas especificamente para abordar esse cenário, em vez de tentar resolver o problema com o reconhecimento vocal em idioma natural.

  
Em outros cenários, um usuário pode estar **digitando solicitações simples com base em idioma natural**. Por exemplo, um usuário pode digitar "Quero uma pizza de pepperoni" ou "Há algum restaurante vegetariano aberto agora a 3 milhas de minha casa?". APIs de reconhecimento vocal em idioma natural como o [LUIS.ai](https://www.luis.ai) são uma excelente opção para cenários como esse. 

Usando as APIs, o bot pode extrair os principais componentes do texto do usuário para identificar a intenção do usuário. Ao implementar funcionalidades de reconhecimento vocal em idioma natural no bot, defina expectativas realistas para o nível de detalhes que os usuários provavelmente fornecerão na entrada. 

![como os usuários se comunicam](~/media/bot-service-design-user-experience/buy-house.png)

> [!TIP]
> Ao criar modelos de idioma natural, não presuma que os usuários fornecerão todas as informações necessárias na consulta inicial. Projete o bot para solicitar especificamente as informações necessárias, guiando o usuário para fornecer essas informações fazendo uma série de perguntas, se necessário. 

  
## <a name="speech"></a>Fala

Um bot pode usar a entrada e/ou a saída de **fala** para se comunicar com os usuários. Nos casos em que um bot foi projetado para dar suporte a dispositivos que não têm teclado ou monitor, a fala é o único meio de se comunicar com o usuário. 

## <a name="choosing-between-rich-user-controls-text-and-natural-language-and-speech"></a>Escolhendo entre controles de usuário avançados, texto, idioma natural e fala

Assim como as pessoas se comunicam entre si usando uma combinação de gestos, vozes e símbolos, os bots podem se comunicar com os usuários usando uma combinação de controles de usuário avançados, texto (às vezes, incluindo o idioma natural) e fala. Esses métodos de comunicação podem ser usados em conjunto; você não precisa escolher um em detrimento do outro. 

Por exemplo, imagine um "bot de culinária" que ajuda os usuários com receitas, em que o bot pode fornecer instruções reproduzindo um vídeo ou exibindo uma série de imagens para explicar o que precisa ser feito. Alguns usuários talvez prefiram virar páginas da receita ou fazer perguntas ao bot usando a fala enquanto preparam uma receita. Outros podem preferir tocar na tela de um dispositivo em vez de interagir com o bot por meio da fala. Ao projetar o bot, incorpore os elementos da experiência do usuário que dão suporte às maneiras que os usuários provavelmente vão preferir interagir com o bot, considerando os casos de uso específicos para os quais se destina o suporte. 

