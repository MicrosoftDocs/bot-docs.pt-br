---
title: Autenticação de usuário no Serviço de Bot do Azure – Serviço de Bot
description: Saiba mais sobre os recursos de autenticação de usuário no Serviço de Bot do Azure. Veja como os bots usam conexões OAuth para conectar usuários e acessar recursos online protegidos.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 151a641bfb973ab7674850cef388ab7bf6cd78e5
ms.sourcegitcommit: 4509747791a57b3098feb2d1705e921a780df351
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91763867"
---
# <a name="user-authentication"></a>Autenticação de usuário

Às vezes, um bot deve acessar recursos online protegidos em nome do usuário, como verificação de email, verificação de status de voo ou colocação de um pedido. O usuário deve autorizar o bot a fazer isso em seu nome e, para autorizar o bot, o usuário deve autenticar sua identidade. O **OAuth** é usado para autenticar o usuário e autorizar o bot. Consulte também [tipos de autenticação](bot-builder-concept-authentication-types.md).

Se você quiser atualizar seu conhecimento sobre o OAuth, confira o seguinte:

- [Boa visão geral do OAuth](https://aaronparecki.com/oauth-2-simplified/) mais fácil de seguir do que a especificação formal
- [Especificação OAuth](https://oauth.net/2/)

## <a name="user-authentication-in-a-conversation"></a>Autenticação do usuário na conversa

Para executar determinadas operações em nome de um usuário, como verificação de email, nova referência a um calendário, verificação de status de voo ou realização de pedidos, o bot precisará chamar um serviço externo, como o Microsoft Graph, o GitHub ou o serviço REST de uma empresa.
Cada serviço externo tem um modo de proteger essas chamadas. Um modo comum de emitir essas solicitações é usar um *token de usuário* que identifique exclusivamente o usuário nesse serviço externo (às vezes chamado de JWT [(Token Web JSON](https://jwt.io/introduction/))).

Para proteger a chamada para um serviço externo, o bot precisa solicitar que o usuário entre, para que ele possa adquirir o token do usuário desse serviço.
Muitos serviços dão suporte à recuperação de token por meio do protocolo **OAuth** ou **OAuth2**.

O Serviço de Bot do Azure fornece cartões e serviços de **conexão** especializados que funcionam com o protocolo OAuth e gerenciam o ciclo de vida do token. Um bot pode usar esses recursos para adquirir um token de usuário.

- Como parte da configuração do bot, uma **conexão OAuth** é registrada no recurso Serviço de Bot do Azure no Azure.

    A conexão contém informações sobre o **provedor de identidade** a ser usado, juntamente com uma ID e um segredo do cliente OAuth válidos, os escopos do OAuth a serem habilitados e outros metadados de conexão exigidos por esse provedor de identidade.

- No código do bot, a conexão OAuth é usada para ajudar a conectar o usuário e obter o token dele.

A imagem a seguir mostra os elementos envolvidos no processo de autenticação.

![componentes de autenticação do bot](media/concept-bot-authentication/bot-auth-components.png)

## <a name="about-the-bot-framework-token-service"></a>Sobre o serviço de token do Bot Framework

O serviço de token do Bot Framework é responsável por:

- Facilitar o uso do protocolo OAuth com uma ampla variedade de serviços externos.
- Armazenar os tokens com segurança para um bot, um canal, uma conversa e um usuário específicos.
- Adquirindo tokens de usuário.
    > [!TIP]
    > Se o bot tiver um token de usuário expirado, o bot deverá:
    >    - Desconecte o usuário
    >    - Inicie o fluxo de entrada novamente

Por exemplo, um bot que pode verificar emails recentes de um usuário, usando a API do Microsoft Graph, requer um token de usuário de um **Provedor de Identidade**, neste caso, o **Azure Active Directory**. Em tempo de design, o desenvolvedor do bot executa estas duas etapas importantes:

1. Registra um aplicativo do Azure Active Directory, um Provedor de Identidade, com o Serviço de Token do Bot Framework, por meio do portal do Azure.
1. Configura uma conexão OAuth (chamada, por exemplo, de `GraphConnection`) para o bot.

A imagem a seguir mostra a sequência de tempo da interação do usuário com um bot quando uma solicitação de email é feita usando o serviço do Microsoft Graph.

![sequência de tempo de autenticação do bot](media/concept-bot-authentication/bot-auth-time-sequence.PNG)

1. O usuário faz uma solicitação de email para o bot.
1. Uma atividade com essa mensagem é enviada do usuário para o serviço de canal do Bot Framework. O canal de serviço garante que o campo `userid` dentro da atividade tenha sido definido e que a mensagem seja enviada para o bot.

    > [!NOTE]
    > As IDs de usuário são específicas do canal, como a ID do Facebook ou o número de telefone de SMS do usuário.

1. O bot faz uma solicitação para o serviço de token do Bot Framework perguntando se ele já tem um token para a UserId da conexão OAuth `GraphConnection`.
1. Como essa é a primeira vez que esse usuário interage com o bot, o serviço de token do Bot Framework ainda não tem um token para ele e retorna um resultado *NotFound* para o bot.

    > [!NOTE]
    > Se o token for encontrado, as etapas de autenticação serão ignoradas e o bot poderá fazer a solicitação de email usando o token armazenado.

1. O bot cria um OAuthCard com um nome de conexão `GraphConnection` e responde ao usuário solicitando que ele entre usando esse cartão.
1. A atividade passa pelo serviço de canal do Bot Framework, que chama o serviço de token do Bot Framework para criar uma URL de entrada do OAuth válida para esta solicitação. Essa URL de entrada é adicionada ao OAuthCard e o cartão é retornado ao usuário.
1. Uma mensagem é apresentada ao usuário solicitando que ele entre clicando no botão de entrada do OAuthCard.
1. Quando o usuário clica no botão de entrada, o serviço de canal abre um navegador da Web e chama o serviço externo para carregar sua página de entrada.
1. O usuário entra nesta página para o serviço externo. Em seguida, o serviço externo conclui a troca de protocolo OAuth com o serviço de token do Bot Framework, fazendo com que o serviço externo envie o token de usuário ao serviço de token do Bot Framework. O serviço de token do Bot Framework armazena esse token com segurança e envia uma atividade ao bot com esse token.
1. O bot recebe a atividade com o token e pode usá-lo para fazer chamadas à API do MS Graph.

## <a name="securing-the-sign-in-url"></a>Protegendo a URL de entrada

Uma consideração importante quando o Bot Framework facilita um logon de usuário é como proteger a URL de entrada. Quando uma URL de entrada é apresentada ao usuário, essa URL é associada a uma ID de conversa e a uma ID de usuário específicas desse bot. Essa URL não deve ser compartilhada, pois isso faria com que uma entrada errada ocorresse para uma conversa de bot específica. Para atenuar os ataques de segurança em relação ao compartilhamento da URL de entrada, é necessário garantir que o computador e a pessoa que clica na URL de entrada seja a pessoa que é _proprietária_ da janela de conversa.

Alguns canais, como a Cortana, o Microsoft Teams, o Direct Line e o WebChat, podem fazer isso sem que o usuário perceba. Por exemplo, o WebChat usa cookies de sessão para garantir que o fluxo de entrada ocorra no mesmo navegador que a conversa do WebChat. No entanto, para outros canais, geralmente é apresentado um _código mágico_ de seis dígitos ao usuário. Isso é semelhante a uma autenticação multifator interna, pois o serviço de token do Bot Framework não liberará o token para o bot, a menos que o usuário conclua a autenticação final, comprovando que a pessoa que entrou tem acesso à experiência de chat ao inserir o código de seis dígitos.

> [!IMPORTANT]
> Tenha em mente estas importantes [Considerações de segurança](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#security-considerations).
> Você pode encontrar informações adicionais nesta postagem no blog: [Usar o WebChat com a Autenticação do Serviço de Bot do Azure](https://blog.botframework.com/2018/09/01/using-webchat-with-azure-bot-services-authentication/).

## <a name="next-steps"></a>Próximas etapas

Agora que você conhece a autenticação do usuário, vamos dar uma olhada em como aplicá-la ao bot.

> [!div class="nextstepaction"]
> [Adicionar autenticação a um bot](bot-builder-authentication.md)

## <a name="see-also"></a>Confira também

- [Provedores de Identidade](bot-builder-concept-identity-providers.md)
- [Autenticação do Conector REST](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-4.0&preserve-view=true)
- [Autenticação da linha direta de REST](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0&preserve-view=true)
