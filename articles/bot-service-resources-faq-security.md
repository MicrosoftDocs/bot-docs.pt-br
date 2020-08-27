---
title: Perguntas frequentes sobre o bot Framework Security and Privacy-serviço bot
description: Perguntas frequentes sobre segurança e privacidade do bot Framework.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/09/2020
ms.openlocfilehash: ff1b0463f5813bdeaf4830257083afab661b1119
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143516"
---
# <a name="security-and-privacy"></a>Segurança e privacidade

<!-- Attention writers!!
     1 - This article contains FAQs regarding Bot Framework security and privacy.
     1 - When you create a new FAQ, please add the related link to the proper section in bot-service-resources-bot-framework-faq.md.-->

## <a name="do-the-bots-registered-with-the-bot-framework-collect-personal-information-if-yes-how-can-i-be-sure-the-data-is-safe-and-secure-what-about-privacy"></a>Os bots registrados no Bot Framework coletam informações pessoais? Em caso afirmativo, como posso ter certeza de que os dados estão seguros e protegidos? E quanto à privacidade?

Cada bot é seu próprio serviço, e os desenvolvedores desses serviços são obrigados a fornecer Termos de Serviço e Políticas de Privacidade de acordo com seu Código de Conduta do Desenvolvedor. Para obter mais informações, confira as [diretrizes de análise do bot](https://docs.microsoft.com/azure/bot-service/bot-service-review-guidelines?view=azure-bot-service-4.0).

## <a name="can-i-host-my-bot-on-my-own-servers"></a>Pode hospedar meu bot em meus próprios servidores?
Sim. Seu bot pode ser hospedado em qualquer lugar na Internet. Em seus próprios servidores, no Azure ou em qualquer outro datacenter. O único requisito é que o bot deve expor um ponto de extremidade HTTPS publicamente acessível.

## <a name="how-do-you-ban-or-remove-bots-from-the-service"></a>Como os bots são vetados ou removidos do serviço?

Os usuários têm um modo para relatar o comportamento inadequado de um bot, que é o cartão de visita do bot no diretório. Os desenvolvedores precisam respeitar os termos de serviço da Microsoft para participar do serviço.

## <a name="which-specific-urls-do-i-need-to-allow-list-in-my-corporate-firewall-to-access-bot-framework-services"></a>Quais URLs específicas eu preciso para permitir-list no meu firewall corporativo para acessar os serviços do bot Framework?

Se você tiver um firewall de saída bloqueando o tráfego do bot para a Internet, você precisará permitir-listar as seguintes URLs nesse firewall:

- `login.botframework.com` (Autenticação do bot)
- `login.microsoftonline.com` (Autenticação do bot)
- `westus.api.cognitive.microsoft.com` (para integração do NLP no Luis.ai)
- `cortanabfchanneleastus.azurewebsites.net` (canal da Cortana)
- `cortanabfchannelwestus.azurewebsites.net` (Canal da Cortana)
- `*.botframework.com` (canais)
- `state.botframework.com` (compatibilidade com versões anteriores)
- `login.windows.net` (Logon do Windows)
- `login.windows.com` (Logon do Windows)
- `sts.windows.net` (Logon do Windows)
- URLs adicionais para canais específicos do Bot Framework

> [!NOTE]
> Você pode usar `<channel>.botframework.com` se preferir não permitir-listar uma URL com um asterisco. `<channel>` é igual a cada canal que seu bot usa, como `directline.botframework.com`, `webchat.botframework.com` e `slack.botframework.com`. Também vale a pena observar o tráfego em seu firewall enquanto testa o bot para verificar se nada mais está sendo bloqueado.

## <a name="can-i-block-all-traffic-to-my-bot-except-traffic-from-the-bot-framework-service"></a>Posso bloquear todo o tráfego para o meu bot, exceto o tráfego do Bot Framework Service?
Os Bot Framework Services são hospedados em datacenters do Azure em todo o mundo e a lista de IPs do Azure está em constante mudança. permitir-listar determinados endereços IP pode funcionar um dia e interromper o próximo à medida que os endereços IP do Azure são alterados.

## <a name="which-rbac-role-is-required-to-create-and-deploy-a-bot"></a>Qual função RBAC é necessária para criar e implantar um bot?

A criação de um bot no portal do Azure requer acesso de colaborador na assinatura ou em um grupo de recursos específico. Um usuário com a função de *Colaborador* em um grupo de recursos pode criar um bot nesse grupo de recursos específico. Um usuário na função de *Colaborador* para uma assinatura pode criar um bot em um grupo de recursos novo ou existente.

Usando a CLI do Azure, uma abordagem de controle de acesso baseado em função pode dar suporte a funções personalizadas. Se você quiser tornar uma função personalizada com permissões mais restritas, o conjunto a seguir permitirá que o usuário crie e implante um bot compatível também com LUIS, QnA Maker e Application Insights.

  "Microsoft.Web/ *", "Microsoft.BotService/* ", "Microsoft.Storage/ *", "Microsoft.Resources/deployments/* ", "Microsoft.CognitiveServices/ *", "Microsoft.Search/searchServices/* ", "Microsoft.Insights/ *", "Microsoft.Insights/components/* "

O LUIS e o QnA Maker exigem permissões de Serviços Cognitivos. O QnA Maker também requer permissões de Pesquisa. Ao criar uma função personalizada, qualquer permissão de *negar* herdada será substituída por permissões de *permitir*.

## <a name="what-keeps-my-bot-secure-from-clients-impersonating-the-bot-framework-service"></a>O que mantém o meu bot protegido de clientes que se passam pelo Bot Framework Service?

1. Todas as solicitações do Bot Framework autênticas são acompanhadas por um token JWT cuja assinatura criptográfica pode ser verificada seguindo o guia de [autenticação](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector). O token foi projetado para que os invasores não possam se passar por serviços confiáveis.
2. O token de segurança que acompanha todas as solicitações feitas para seu bot tem a ServiceUrl codificada dentro dele, o que significa que, mesmo se um invasor obtiver acesso ao token, eles não conseguirá redirecionar a conversa para uma nova ServiceUrl. Isso é imposto por todas as implementações do SDK e documentado em nossos materiais de [referência](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) de autenticação.
3. Se o token de entrada estiver ausente ou se for malformado, o SDK do Bot Framework não irá gerar um token na resposta. Isso limita o dano que poderá ocorrer se o bot for configurado incorretamente.
4. Dentro do bot, você pode verificar manualmente a ServiceUrl fornecida no token. Isso torna o bot mais frágil em caso de alterações de topologia de serviço, por isso que é possível, mas não recomendado.

Observe que essas são as conexões de saída do bot para a Internet. Não há uma lista de nomes DNS ou Endereços IP que o Serviço Bot Framework Connector usará para se comunicar com o bot. Não há suporte para a lista de permissões de endereço IP de entrada.