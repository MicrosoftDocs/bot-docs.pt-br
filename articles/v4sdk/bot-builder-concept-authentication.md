---
title: Autenticação de usuário no Serviço de Bot do Azure | Microsoft Docs
description: Saiba mais sobre os recursos de autenticação de usuário no Serviço de Bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b4b71f7679ad6eea283437acb5a8293855219cf2
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299495"
---
# <a name="user-authentication-within-a-conversation"></a>Autenticação de usuário dentro de uma conversa

Para executar determinadas operações em nome de um usuário, como verificação de email, nova referência a um calendário, verificação de status de voo ou realização de pedidos, o bot precisará chamar um serviço externo, como o Microsoft Graph, o GitHub ou o serviço REST de uma empresa.
Cada serviço externo tem uma maneira de proteger essas chamadas. Uma maneira comum de proteger esse tipo de chamada é emitir essas solicitações usando um _token de usuário_ que identifique exclusivamente o usuário naquele serviço externo (também conhecido como um JWT).

Para proteger a chamada para um serviço externo, o bot precisa solicitar que o usuário entre, para que ele possa adquirir o token do usuário desse serviço.
Muitos serviços dão suporte à recuperação de token por meio do protocolo OAuth ou OAuth2.
O Serviço de Bot do Azure fornece cartões e serviços de entrada especializados que funcionam com o protocolo OAuth e gerenciam o ciclo de vida do token. O bot pode usar esses recursos para adquirir um token de usuário.

- Como parte da configuração do bot, uma _configuração de conexão OAuth_ é registrada no recurso Serviço de Bot do Azure no Azure.

    Cada configuração de conexão contém informações sobre o serviço externo ou o provedor de identidade a ser usado, juntamente com uma ID do cliente OAuth válida e um segredo, os escopos do OAuth a serem habilitados e outros metadados de conexão exigidos por esse serviço externo ou provedor de identidade.

- No código do bot, uma configuração de conexão OAuth é usada para ajudar um usuário a entrar e para obter um token de usuário.

Esses serviços estão envolvidos no fluxo de trabalho de entrada.

![Visão geral da autenticação](./media/bot-builder-concept-authentication.png)

## <a name="about-the-bot-framework-token-service"></a>Sobre o serviço de token do Bot Framework

O serviço de token do Bot Framework é responsável por:

- Facilitar o uso do protocolo OAuth com uma ampla variedade de serviços externos.
- Armazenar os tokens com segurança para um bot, um canal, uma conversa e um usuário específicos.
- Gerenciar o ciclo de vida do token, incluindo a tentativa de atualizar tokens.

Por exemplo, um bot que pode verificar os emails recentes de um usuário, usando a API do Microsoft Graph, exigirá um token de usuário do Azure Active Directory. No tempo de design, o desenvolvedor do bot registraria um aplicativo do Azure Active Directory no serviço de token do Bot Framework (por meio do portal do Azure) e, em seguida, definiria uma configuração de conexão OAuth (chamada `GraphConnection`) para o bot. Quando um usuário interagisse com o bot, o fluxo de trabalho seria:

1. O usuário solicita ao bot: "verifique meu email".
1. Uma atividade com essa mensagem é enviada do usuário para o serviço de canal do Bot Framework. O canal de serviço garante que o campo `userid` dentro da atividade tenha sido definido e que a mensagem seja enviada para o bot.

    As IDs de usuário são específicas do canal, como a ID do Facebook ou o número de telefone de SMS do usuário.

1. O bot recebe a atividade de mensagem e determina que a intenção é verificar o email do usuário. Ele faz uma solicitação ao serviço de token do Bot Framework perguntando se ele já tem um token para a UserId da configuração de conexão OAuth `GraphConnection`.
1. Como essa é a primeira vez que esse usuário interage com o bot, o serviço de token do Bot Framework ainda não tem um token para ele e retorna um resultado _NotFound_ para o bot.
1. O bot cria um OAuthCard com um nome de conexão `GraphConnection` e responde ao usuário solicitando que ele entre usando esse cartão.
1. A atividade passa pelo serviço de canal do Bot Framework, que chama o serviço de token do Bot Framework para criar uma URL de entrada do OAuth válida para esta solicitação. Essa URL de entrada é adicionada ao OAuthCard e o cartão é retornado ao usuário.
1. Uma mensagem é apresentada ao usuário solicitando que ele entre clicando no botão de entrada do OAuthCard.
1. Quando o usuário clica no botão de entrada, o serviço de canal abre um navegador da Web e chama o serviço externo para carregar sua página de entrada.
1. O usuário entra nesta página para o serviço externo. Após a conclusão, o serviço externo conclui a troca de protocolo OAuth com o serviço de token do Bot Framework, fazendo com que o serviço externo envie o token de usuário ao serviço de token do Bot Framework. O serviço de token do Bot Framework armazena esse token com segurança e envia uma atividade ao bot com esse token.
1. O bot recebe a atividade com o token e pode usá-lo para fazer chamadas à API do Graph.

## <a name="securing-the-sign-in-url"></a>Protegendo a URL de entrada

Uma consideração importante quando o Bot Framework facilita um logon de usuário é como proteger a URL de entrada. Quando uma URL de entrada é apresentada ao usuário, essa URL é associada a uma ID de conversa e a uma ID de usuário específicas desse bot. Essa URL não deve ser compartilhada, pois isso faria com que uma entrada errada ocorresse para uma conversa de bot específica. Para atenuar os ataques de segurança em relação ao compartilhamento da URL de entrada, é necessário garantir que o computador e a pessoa que clica na URL de entrada seja a pessoa que é _proprietária_ da janela de conversa.

Alguns canais, como a Cortana, o Teams, o Direct Line e o WebChat podem fazer isso sem que o usuário perceba. Por exemplo, o WebChat usa cookies de sessão para garantir que o fluxo de entrada ocorra no mesmo navegador que a conversa do WebChat. No entanto, para outros canais, geralmente é apresentado um _código mágico_ de seis dígitos ao usuário. Isso é semelhante a uma autenticação multifator interna, pois o serviço de token do Bot Framework não liberará o token para o bot, a menos que o usuário conclua a autenticação final, comprovando que a pessoa que entrou tem acesso à experiência de chat ao inserir o código de seis dígitos.

## <a name="azure-activity-directory-application-registration"></a>Registro de aplicativo no Azure Activity Directory

Cada bot que é registrado como um Serviço de Bot do Azure usa uma ID de aplicativo do AD (Azure Activity Directory). É importante **não** reutilizar essa ID de aplicativo e essa senha para a entrada de usuários. A ID do aplicativo do Azure AD do Serviço de Bot do Azure deve proteger a comunicação de serviço a serviço entre o bot e os serviços de canal do Bot Framework. Se você quiser que os usuários entrem no Azure AD, crie um registro de aplicativo do Azure AD separado com as permissões e os escopos apropriados.

## <a name="configure-an-oauth-connection-setting"></a>Definir uma configuração de conexão do OAuth

Para obter mais informações de como registrar e usar uma configuração de conexão OAuth, confira [Adicionar autenticação ao bot](bot-builder-authentication.md).
