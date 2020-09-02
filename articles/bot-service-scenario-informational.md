---
title: Cenário de bot de informações – Serviço de Bot
description: Saiba mais sobre o cenário de bot de informações. Veja como esse bot usa serviços cognitivas QnA Maker e Azure Search para consultar um banco de dados SQL e responder a perguntas.
author: BrianRandell
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: aea39a87cd0bdfee482e976ee9029fd5c5d295fe
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360869"
---
# <a name="information-bot-scenario"></a>Cenário de Bot de Informações

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Este Bot de Informações pode responder perguntas definidas em um conjunto de conhecimentos ou em perguntas frequentes usando o QnA Maker dos Serviços Cognitivos e responder perguntas mais abertas usando o Azure Search.

Frequentemente, as informações são arquivadas em armazenamentos de dados estruturados, como SQL Server, que podem facilmente ser exibidas por meio de pesquisa. Imagine pesquisar o status do pedido de um cliente por meio de comandos de conversa simples. Com o QnA Maker dos Serviços Cognitivos, o usuário recebe um conjunto de opções válidas de pesquisa, por exemplo, pesquisa de um cliente, analisar o pedido de mais recente do cliente etc. Com o formato do QnA definido, o usuário pode fazer perguntas respaldadas no Azure Search, o qual pode procurar dados armazenados em um Banco de Dados SQL.

![O diagrama de bot de Informações](~/media/scenarios/bot-service-scenario-informational-bot.png)

Este é o fluxo lógico de um bot de Informações:

1. O funcionário inicia o bot de Informações.
2. O Azure Active Directory valida a identidade do funcionário.
3. O funcionário pode perguntar ao bot quais tipos de consultas são compatíveis.
4. Os Serviços Cognitivos retornam um bot de perguntas frequentes criado com o QnA Maker.
5. O funcionário define uma consulta válida.
6. O bot envia a consulta ao Azure Search, que retorna as informações sobre os dados do aplicativo.
7. O Application Insights coleta a telemetria do runtime para ajudar o desenvolvimento com o desempenho e uso do bot.

## <a name="sample-bot"></a>Bot de exemplo
O exemplo de Bot, escrito em C#, é executado no Microsoft Azure e trabalha com dados indexados pelo Azure Search a partir de uma instância de Banco de Dados SQL. O Bot expõe uma lista de perguntas que podem ser feitas, com informações sobre como formular a pergunta (a resposta) usando os Serviços Cognitivos: QnA Maker. Em seguida, o usuário do Bot pode digitar uma consulta que procura os dados por meio do Azure Search em uma área ampla ou específica do banco de dados indexado. O exemplo fornece um banco de dados simples com informações sobre clientes e pedidos. O Application Insights controla o uso do Bot e ajuda você a monitorar o Bot em caso de exceções. O Bot é publicado como um aplicativo do Azure AD, assim você pode restringir quem tem acesso às informações.

Baixe ou clone o código-fonte desse bot de exemplo em [Exemplos para cenários comuns do Bot Framework](https://aka.ms/abs-scenarios).

## <a name="components-youll-use"></a>Componentes que serão utilizados
O bot de Informações usa os seguintes componentes:
-   Azure AD para autenticação
-   Serviços Cognitivos: QnA Maker
-   Azure Search
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)
O Active Directory do Azure (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. Como um desenvolvedor de Bot, o Azure AD permite que você concentre-se na criação do Bot, tornando-o rápido e simplificando a integração com uma solução de gerenciamento de identidades da mais alta qualidade utilizada por milhões de organizações em todo o mundo. Ao definir um aplicativo do Azure AD, será possível controlar quem terá acesso ao Bot e aos dados expostos, sem implementar seu próprio sistema complexo de autenticação e autorização.

### <a name="cognitive-services-qna-maker"></a>Serviços Cognitivos: QnA Maker
QnA Maker dos Serviços Cognitivos ajuda você a fornecer uma fonte de dados de perguntas frequentes, a qual seus usuários podem consultar de seu Bot. Ao abordar grandes quantidades de informações armazenadas em sistemas diferentes, pode ser útil ajudar os usuários a filtrar a fonte e o conjunto de informações. Um único Banco de Dados SQL pode ter quantidades enormes de informações e, ao receber uma pesquisa de formulário livre, responde com muitas informações. Começando com o QnA Maker, você pode definir um roteiro para os usuários do Bot, para que eles saibam como fazer perguntas inteligentes que possam ser recuperadas por meio do Azure Search.

### <a name="azure-search"></a>Azure Search
O Azure Search é um serviço de pesquisa de nuvem para aplicativos que permite a execução rápida de índices de pesquisa. Como ele está em execução no Microsoft Azure, você pode ampliar e reduzir conforme suas demandas de uso. Você pode relacionar os resultados da pesquisa às metas do negócio com mais controle sobre a classificação da pesquisa e sobre a exibição de dados ocultos em seus bancos de dados.

### <a name="application-insights"></a>Application Insights
O Application Insights ajuda a obter insights práticos pelo APM (gerenciamento de desempenho de aplicativo) e por análises instantâneas. Pronto para uso, é possível obter monitoramento de desempenho avançado, alertas eficientes e painéis fáceis de usar para ajudar a garantir que o Bot esteja disponível e executando conforme o esperado. Veja rapidamente se há algum problema e faça uma análise da causa raiz para encontrar e corrigi-lo.

## <a name="next-steps"></a>Próximas etapas
Em seguida, saiba mais sobre o cenário de bot de Internet das Coisas.

> [!div class="nextstepaction"]
> [Cenários de bot de IoT (Internet das Coisas)](bot-service-scenario-internet-things.md)
