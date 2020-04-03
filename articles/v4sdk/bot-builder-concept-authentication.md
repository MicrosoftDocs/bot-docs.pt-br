---
title: Autenticação de usuário no Serviço de Bot do Azure – Serviço de Bot
description: Saiba mais sobre os recursos de autenticação de usuário no Serviço de Bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c6566a0b4d1150b3662734200c3ffd7c62dff0f6
ms.sourcegitcommit: 64b25f796f89e8bb6fa53d3c824b73b8ce4d6ed8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80250035"
---
# <a name="bot-authentication"></a>Autenticação do bot

Há ocasiões em que um bot deve acessar recursos online protegidos em nome do usuário, e o bot deve estar autorizado a fazer isso. A autorização assume a forma de um token de portador. Isso é feito por um conjunto de componentes que faz parte da arquitetura do **Serviço de Bot do Azure** descrita abaixo.

1. **Aplicativo de Registro de Canais de Bot**. Esse é o mecanismo para *integrar* um bot dentro da infraestrutura do Azure, o que permite ao usuário comunicar-se por meio de canais com o bot.
1. **Bot**. O bot pode ser hospedado em qualquer lugar, incluindo o Azure.
1. **Aplicativo do provedor de identidade**. Esse aplicativo é necessário para cada recurso protegido que o bot deve acessar em nome do usuário. É o **provedor de identidade** que permite que o bot *acesse um recurso protegido externo*, como o Office 365 MSGraph. O Azure Active Directory é o provedor de identidade para acessar os recursos protegidos da Microsoft. Existem muitos outros provedores de identidade, por exemplo, o GitHub, para acessar recursos protegidos.

A imagem a seguir mostra a arquitetura do Serviço de Bot do Azure que usa o Azure AD como o provedor de identidade para autenticação.

![Arquitetura do Serviço de Bot do Azure](media/concept-bot-authentication/azure-bot-service-architecture.png)

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


## <a name="azure-activity-directory-in-a-bot"></a>Azure Active Directory em um bot

O Azure Active Directory desempenha uma função dinâmica quando um bot é implantado no Azure, conforme descrito abaixo.

### <a name="bot-registration"></a>Registro do bot

 Quando você registra um bot no Azure, por exemplo, por meio do registro de canais de bot, um aplicativo de registro do Active Directory é criado. Esse aplicativo tem a própria ID de aplicativo (ID do aplicativo) e o segredo do cliente (senha) necessários para configurar o bot para implantação. A ID do aplicativo também é necessária para proteger o serviço para a comunicação de serviço entre o bot e os Serviços de Canal do Bot Framework.

### <a name="bot-authentication"></a>Autenticação do bot

O Azure Active Directory é um provedor de identidade de nuvem que permite conectar com segurança os usuários usando protocolos padrão do setor, como **OAuth2.0**. Para obter mais informações, confira [Provedor de identidade do Azure Active Directory](bot-builder-concept-identity-providers.md#azure-active-directory-identity-provider).


### <a name="next-steps"></a>Próximas etapas

Agora que você sabe a função que o AD desempenha, vamos dar uma olhada em como autenticar um bot.

> [!div class="nextstepaction"]
> [Adicionar autenticação a um bot](bot-builder-authentication.md).

## <a name="see-also"></a>Confira também

- [Provedores de Identidade](bot-builder-concept-identity-providers.md)
- [Autenticação do Conector REST](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-4.0)
- [Autenticação da linha direta de REST](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0)