---
title: Cenário de bot de Produtividade Empresarial| Microsoft Docs
description: Explore o cenário de bot de Produtividade Empresarial com o Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 0dc869aa1464c086b6596ee83d8e6e488d8a8a55
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296983"
---
# <a name="enterprise-productivity-bot-scenario"></a>Cenário de bot de Produtividade Empresarial
O Bot Empresarial mostra como é possível aumentar a produtividade, integrando um bot com o calendário do Office 365 e outros serviços.

Acessar rapidamente as informações do cliente sem a necessidade de abrir muitas janelas é o objetivo do bot de Produtividade Empresarial. Usando comandos simples de chat, um representante de vendas pode pesquisar um cliente e verificar o próximo compromisso por meio da API do Graph e do Office 365. A partir disso, o representante poderá acessar informações específicas do cliente armazenadas no Dynamics CRM como obter um caso ou criar um novo.

![Diagrama bot Empresarial](~/media/scenarios/bot-service-scenario-enterprise-bot.png)

Aqui é apresentado o fluxo lógico de um bot de Produtividade Empresarial:

1. O funcionário acessa o bot de Produtividade Empresarial.
2. O Azure Active Directory valida a identidade do funcionário.
3. O bot de Produtividade Empresarial é capaz de consultar o calendário do Office 365 do funcionário através do Graph.
4. Usando dados coletados do calendário, o bot acessa informações sobre o caso no Dynamics CRM.
5. As informações são retornadas ao funcionário que pode filtrar os dados sem sair do bot.
6. O Application Insights coleta a telemetria do tempo de execução para ajudar no desenvolvimento e uso do bot.

Baixe ou clone o código-fonte desse bot de exemplo em [Exemplos para cenários comuns do Bot Framework](https://aka.ms/bot/scenarios).

## <a name="sample-bot"></a>Bot de exemplo
Como os bots são acessíveis através de uma variedade de canais, é possível utilizá-los na área de trabalho a partir de um portal corporativo ou pelo Skype, onde quer que você esteja - basta autenticar-se. Com a integração do Azure AD, o bot de Produtividade Empresarial reconhece que se é possível acessá-lo, significa que você foi autenticado pelo Azure AD. A partir disso, será possível pedir ao bot para verificar o próximo compromisso com um cliente específico. O bot obtém essas informações, consultando o Office 365 por meio da API do Graph. Portanto, se houver um compromisso nos próximos sete dias, o bot consultará o CRM procurando por casos recentes para o cliente. O bot responderá o número de casos em aberto ou fechados ou se nenhum caso foi encontrado. Em seguida, será possível pedir ao Bot para listar os casos por tipo e detalhar casos individuais.

## <a name="components-youll-use"></a>Componentes que serão utilizados
O bot de Produtividade Empresarial usa os componentes a seguir:
-   Azure AD para autenticação
-   API do Graph para Office 365
-   Dynamics CRM
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)
O Active Directory do Azure (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. Como um desenvolvedor de Bot, o Azure AD permite que você concentre-se na criação do Bot, tornando-o rápido e simplificando a integração com uma solução de gerenciamento de identidades da mais alta qualidade utilizada por milhões de organizações em todo o mundo. Ao definir um aplicativo do Azure AD, será possível controlar quem terá acesso ao Bot e aos dados expostos, sem implementar seu próprio sistema complexo de autenticação e autorização.

### <a name="graph-api-to-office-365"></a>API do Graph para Office 365
O Microsoft Graph expõe várias APIs do Office 365 e de outros serviços em nuvem da Microsoft por meio de um ponto de extremidade único https://graph.microsoft.com. O Microsoft Graph torna a execução de consultas mais fácil a você e ao bot. A API expõe dados de vários serviços em nuvem da Microsoft, incluindo Exchange Online como parte do Office 365, Azure Active Directory, SharePoint, e muito mais. É possível usar a API para navegar entre entidades e relacionamentos. É possível usar a API dos Bots utilizando os pontos de extremidade de REST ou SDK, bem como de outros aplicativos com suporte nativo Android, iOS, Ruby, UWP, Xamarin e mais.

### <a name="dynamics-crm"></a>Dynamics CRM
O Dynamics CRM é uma plataforma de participação do cliente. Usando APIs do CRM e Bots, é possível compilar Bots interativos avançados que podem acessar os dados avançados armazenados no CRM. A potência do Dynamics CRM está disponível para o Bot criar casos, verificar o status, executar pesquisas de gerenciamento do conhecimento, e muito mais.

### <a name="application-insights"></a>Application Insights
O Application Insights ajuda a obter insights acionáveis por meio de APM (Gerenciamento de Desempenho de Aplicativos) e análise instantânea. Pronto para uso, é possível obter monitoramento de desempenho avançado, alertas eficientes e painéis fáceis de usar para ajudar a garantir que o Bot esteja disponível e executando conforme o esperado. É possível verificar rapidamente se há um problema e, em seguida, realizar uma análise de causa raiz para localizar e corrigir o problema.

## <a name="next-steps"></a>Próximas etapas
Em seguida, saiba mais sobre o cenário de bot de informações.

> [!div class="nextstepaction"]
> [Cenário de bot de informações](bot-service-scenario-informational.md)
