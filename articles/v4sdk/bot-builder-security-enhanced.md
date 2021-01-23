---
title: Serviço Security-bot de canais do bot Framework
description: Saiba mais sobre possíveis riscos de segurança quando os usuários se conectam a um bot usando os canais permitidos
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/19/2020
ms.openlocfilehash: a50c0b794564c3603e8f0616c45ee4bf1eabbe37
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717482"
---
# <a name="direct-line-enhanced-authentication"></a>Autenticação avançada de linha direta

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo descreve possíveis riscos de segurança quando os usuários se conectam a um bot, por exemplo, usando o controle de [chat da Web](~/bot-service-channel-connect-webchat.md#embed-the-web-chat-control-in-a-web-page) . Além disso, ele mostra a mitigação de soluções usando as [configurações de autenticação avançada](../bot-service-channel-connect-directline.md#configure-settings) de linha direta e o tratamento de *ID de usuário* seguro.

Você deve estar ciente de que há duas identidades de usuário:

- A identidade do usuário do canal. Um invasor pode usá-lo para [representação](#impersonation).
- A identidade do usuário do provedor de identidade que o bot usa para autenticar o usuário. Um invasor pode usá-lo para [falsificação de identidade](#identity-spoofing).

O código neste artigo baseia-se no exemplo: [MVC directize token Controller](https://github.com/microsoft/BotBuilder-Samples/tree/main/experimental/DirectLineTokenSite). Consulte a seção de [exemplo](#example) para obter mais detalhes.

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

## <a name="impersonation"></a>Representação

A representação refere-se à ação de um invasor que faz com que o bot ache que ele é outra pessoa. Por exemplo, no Web Chat, o invasor pode representar outra pessoa **alterando a ID de usuário** da instância de Web Chat.

### <a name="impersonation-mitigation"></a>Mitigação de representação

- Tornar a **ID de usuário inadivinhable**.
- [Conecte um bot à linha direta](../bot-service-channel-connect-directline.md).
- Habilite a opção de [Autenticação avançada](../bot-service-channel-connect-directline.md#configure-settings) do canal de linha direta para permitir que o serviço de bot do Azure detecte e rejeite ainda mais qualquer alteração de ID de usuário. Isso significa que a ID de usuário ( `Activity.From.Id` ) nas mensagens da linha direta para o bot será sempre a mesma que você usou para inicializar o controle de chat da Web.

    > [!NOTE]
    > A linha direta cria um **token** com base no segredo de linha direta e insere o *User.ID* no token.
    > Garante que as mensagens enviadas para o bot tenham esse *User.ID* como o *from.ID* da atividade. Se um cliente enviar uma mensagem para uma linha direta com um *from.ID* diferente, ele será alterado para a **ID inserida no token** antes de encaminhar a mensagem para o bot. Portanto, você não pode usar outra ID de usuário depois que um segredo de canal é inicializado com essa ID.

    Este recurso requer que a ID de usuário comece com `dl_` conforme mostrado abaixo:

    [!code-csharp[specify user ID](~/../botbuilder-samples/experimental/DirectLineTokenSite/Bot_Auth_DL_Secure_Site_MVC/Controllers/HomeController.cs?range=15-50&highlight=9)]

    O token gerado, com base no segredo de linha direta, é usado no controle de chat da Web, conforme mostrado abaixo:

    [!code-csharp[specify token](~/../botbuilder-samples/experimental/DirectLineTokenSite/Bot_Auth_DL_Secure_Site_MVC/Views/Home/Index.cshtml?range=1-16&highlight=11-14)]

## <a name="identity-spoofing"></a>Falsificação de identidade

A falsificação de identidades refere-se à ação de um invasor que assume a identidade de um usuário legítimo e, em seguida, usa essa identidade para realizar uma meta mal-intencionada.

Quando um bot solicita que o usuário do canal a entre em um provedor de identidade, o processo de entrada deve garantir que o usuário a é o único que entra no provedor. Se outro usuário também tiver permissão para entrar no provedor, ele teria acesso ao usuário a recursos por meio do bot.

### <a name="user-identity-spoofing-mitigation"></a>Mitigação de falsificação de identidade do usuário

No controle de chat da Web, há dois mecanismos para garantir que o usuário apropriado esteja conectado.

1. **Código mágico**. No final do processo de entrada, o usuário recebe um código de 6 dígitos gerado aleatoriamente (*código mágico*). O usuário deve digitar esse código na conversa para concluir o processo de entrada. Isso tende a resultar em uma experiência de usuário inadequada. Além disso, ele ainda está suscetível a ataques de phishing. Um usuário mal-intencionado pode induzir outro usuário a se conectar e a obter o código mágico por meio de phishing.

    >[!WARNING]
    > O uso do código mágico foi preterido. Em vez disso, é recomendável usar a abordagem de **Autenticação avançada de linha direta** , descrita abaixo.

1. **Autenticação avançada de linha direta**. Devido aos problemas com a abordagem do *código mágico* , o serviço de bot do Azure removeu sua necessidade. O Serviço de Bot do Azure garante que o processo de conexão só possa ser concluído na **mesma sessão do navegador** que a do próprio Webchat.
Para habilitar essa proteção, você deve iniciar o chat Web com um **token de linha direta** que contém uma **lista de domínios confiáveis que podem hospedar o cliente de chat da Web do bot**. Com as opções de autenticação avançadas, você pode especificar estaticamente a lista de domínios confiáveis (origens confiáveis) na página de configuração de linha direta. Consulte a seção [Configurar Autenticação avançada](../bot-service-channel-connect-directline.md#configure-enhanced-authentication) .

## <a name="example"></a>Exemplo

O código neste artigo baseia-se no exemplo: [MVC directize token Controller](https://github.com/microsoft/BotBuilder-Samples/tree/main/experimental/DirectLineTokenSite).

Para executar o exemplo, execute as seguintes etapas:

1. Se você não tiver um bot, poderá consultar o tutorial [criar um bot básico](bot-builder-tutorial-create-basic-bot.md) para criar um.
1. Conecte o canal de linha direta ao bot. Siga as etapas descritas no artigo [conectar um bot à linha direta](../bot-service-channel-connect-directline.md).
1. Ao conectar o bot à linha direta, habilite a opção de [Autenticação avançada](../bot-service-channel-connect-directline.md#configure-settings) .
1. Copie e armazene com segurança a chave secreta.
1. Por fim, atribua a chave secreta na `HomeController` classe, conforme mostrado abaixo.

    [!code-csharp[cs sample](~/../botbuilder-samples/experimental/DirectLineTokenSite/Bot_Auth_DL_Secure_Site_MVC/Controllers/HomeController.cs?range=15-19&highlight=3-4)]

    O exemplo (aplicativo cliente) usará a chave secreta para solicitar que a linha direta emita um token. Esse token, junto com a ID de usuário, identifica exclusivamente e com segurança o usuário para permitir a comunicação com o bot usando o controle de chat da Web.
