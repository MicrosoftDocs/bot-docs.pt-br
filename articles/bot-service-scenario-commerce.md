---
title: Cenário de bot comercial| Microsoft Docs
description: Explore o cenário de bot Comercial com o Bot Framework.
author: BrianRandell
ms.author: v-brra
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b809e98ec971abaac98fd33c4fb2c285baca898f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297325"
---
# <a name="commerce-bot-scenario"></a>Cenário de bot Comercial
O cenário de [bot Comercial](bot-service-scenario-commerce.md) descreve um bot que substitui as interações tradicionais de email e telefone normalmente usadas por serviços de recepção de um hotel. O bot aproveita os Serviços Cognitivos para processar melhor as solicitações do cliente por meio de voz e texto com contexto obtido pela integração com serviços de back-end.

![O diagrama do aplicativo bot](~/media/scenarios/bot-service-scenario-commerce-bot.png)

Este é o fluxo lógico de um bot Comercial que funciona como recepcionista de um hotel:

1. O cliente usa o aplicativo móvel do hotel.
2. O usuário autentica usando o Azure AD B2C.
3. O usuário solicita as informações usando o Bot de Aplicativo personalizado. 
4. Os Serviços Cognitivos ajudam a processar a solicitação de idioma natural.
5. A resposta é revisada pelo cliente, que pode refinar a pergunta usando uma conversa natural.
6. Assim que o usuário ficar satisfeito com os resultados, o Bot de Aplicativo atualiza a reserva do cliente.
7. O Application Insights coleta a telemetria do tempo de execução para ajudar o desenvolvimento com o desempenho e uso do bot.

## <a name="sample-bot"></a>Exemplo de bot
O exemplo de bot Comercial foi desenvolvido com base no serviço de recepcionista de um hotel fictício. Os clientes acessam o Bot, escrito em C#, após a autenticação do Azure AD B2C em um hotel por meio do aplicativo móvel de serviços para membros da rede. A cadeia armazena as reservas em um Banco de Dados SQL. Um cliente pode usar perguntas com frases naturais como "Quanto custa para alugar um guarda-sol para a minha estadia". O Bot, por sua vez, tem contexto sobre o hotel e a duração da estadia do hóspede. Além disso, o serviço LUIS (Serviço Inteligente de Reconhecimento Vocal) facilita para o bot a obtenção de contexto, mesmo de uma frase simples como "guarda-sol". O Bot fornece a resposta e, em seguida, pode se oferecer para reservar um guarda-sol para o hóspede, fornecendo opções sobre o número de dias e tipo de guarda-sol. Assim que o Bot tiver todos os dados necessários, ele fará a reserva. O hóspede também pode usar a própria voz para fazer a mesma solicitação.

Você pode baixar ou clonar o código-fonte deste exemplo de bot em [Exemplos de cenários comuns do Bot Framework](https://aka.ms/bot/scenarios).

## <a name="components-youll-use"></a>Componentes que você usará
O bot Comercial usa os seguintes componentes:
-   Azure AD para autenticação
-   Serviços Cognitivos: LUIS
-   Application Insights

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)
O Active Directory do Azure (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. Como desenvolvedor de Bots, o Azure AD permite que você se concentre na criação de seu Bot, tornando-o rápido e simplificando a integração com uma solução de gerenciamento de identidade da mais alta qualidade usada por milhões de empresas em todo o mundo. O Azure AD dá suporte a um conector de B2C, permitindo que você identifique pessoas que usam IDs externas, como Google, Facebook ou uma conta da Microsoft. O Azure AD poupa você da responsabilidade de gerenciar as credenciais do usuário e, em vez disso, permite que se concentre na solução de seu Bot sabendo que você pode correlacionar o usuário do Bot com os dados corretos expostos por seu aplicativo.

### <a name="cognitive-services-luis"></a>Serviços Cognitivos: LUIS
Como membro da família de tecnologias de Serviços Cognitivos, o LUIS (Serviço Inteligente de Reconhecimento Vocal) traz a potência do aprendizado de máquina para seus aplicativos. Atualmente, o LUIS dá suporte a várias linguagens que permitem ao seu Bot entender o que uma pessoa deseja. Ao integrar-se ao LUIS, você expressa a intenção e define as entidades que seu Bot entende. Em seguida, você ensina seu Bot a entender essas intenções e entidades treinando-o com declarações de exemplo. Você tem a capacidade de ajustar sua integração usando as listas de frase e recursos de regex para que seu Bot flua o melhor possível para suas necessidades de conversa específicas.

### <a name="application-insights"></a>Application Insights
O Application Insights ajuda você a obter insights práticos através do APM (gerenciamento de desempenho de aplicativo) e análises instantâneas. Você conta, imediatamente, com o monitoramento de desempenho, alertas avançados e painéis simples de usar para ajudar a garantir que seu Bot esteja disponível e com o desempenho esperado. Veja rapidamente se há algum problema e faça uma análise da causa raiz para encontrar e corrigi-lo.

## <a name="next-steps"></a>Próximas etapas
Em seguida, saiba mais sobre o cenário de bot de Habilidades da Cortana.

> [!div class="nextstepaction"]
> [Cenário de bot de Habilidades da Cortana](bot-service-scenario-cortana-skill.md)
