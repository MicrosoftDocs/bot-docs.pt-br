---
title: Noções básicas de autenticação do bot Framework – serviço bot
description: Saiba mais sobre as noções básicas de autenticação no bot Framework.
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/07/2020
ms.openlocfilehash: 2a9077e2e9f0e7b22914a0343968a8e80ebeeeba
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417332"
---
# <a name="bot-framework-authentication-basics"></a>Noções básicas de autenticação do bot Framework

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Geralmente, um bot deve acessar recursos protegidos, por exemplo, conta de email, em nome do usuário. Para fazer isso, o bot deve ser **autorizado** com base nas credenciais do usuário. Antes disso, o usuário deve ser **autenticado** primeiro.
Além disso, o próprio bot deve ser uma entidade conhecida, que é deve ser autenticado no contexto do serviço de bot do Azure. Isso acontece antes que o bot seja autorizado a operar em nome do usuário.

Vamos ver se podemos Untangle esse pacote começando com uma visão geral do contexto de autenticação do bot Framework.

![contexto de autenticação de bot.](./media/concept-bot-authentication\bot-auth-context.PNG)

- **Registro do canal de bot**. Quando você registra um bot no Azure, por exemplo, por meio do **registro de canais de bot**, o Azure cria um aplicativo de registro de Active Directory (AD). Este aplicativo tem uma ID do aplicativo ( `MicrosoftAppID` ) e um segredo do cliente ( `MicrosoftAppPassword` ). Você usa esses valores nos arquivos de configuração do bot, conforme descrito abaixo.
Observe que você pode obter resultados semelhantes criando um **bot de aplicativo Web**.

- **Identidade do Azure ad**. O Azure AD (Azure Active Directory) é um serviço de identidade de nuvem que permite que você crie aplicativos que conectam usuários com segurança usando protocolos padrão do setor, como o OAuth2.0. Você cria um aplicativo do AD e usa sua **ID de aplicativo** e **senha** para selecionar um **provedor de identidade** e gerar uma conexão de **autenticação** . Você adiciona essa conexão às configurações de registro do canal de bot. Você também adiciona o nome da conexão nos arquivos de configuração do bot, conforme descrito abaixo.

- **Bot**. Um bot é identificado por sua ID e **senha**do **aplicativo** de registro de canais (ou aplicativo Web). Você adiciona os valores relacionados nos arquivos de configuração do bot ( `appsettings.json` (.net), `.env` (JavaScript), `config.py` (Python)) ou no **Azure Key Vault**. Você também adiciona o nome da conexão aos arquivos.
O bot usa o **token** com base na ID do aplicativo e na senha para acessar recursos protegidos. Além disso, o bot usa o **token** com base na conexão de autenticação para acessar os recursos protegidos do usuário.

## <a name="bot-authentication-and-authorization"></a>Autenticação e autorização de bot

A seguir estão as principais etapas para autenticar um bot e autorizá-lo a acessar os recursos protegidos do usuário:

1. Crie um aplicativo de registro de canal de bot.
1. Adicione a ID do aplicativo de registro e a senha ao arquivo de configuração do bot. Isso permite que o bot seja autenticado para acessar recursos protegidos.
1. Crie um aplicativo do Azure AD para selecionar um provedor de identidade para autenticar o usuário.
1. Crie uma conexão de autenticação e adicione-a às configurações de registro do canal.
1. Adicione o nome da conexão aos arquivos de configuração do bot. Isso permite que o bot seja autorizado a acessar os recursos protegidos do usuário.

Para obter um exemplo completo, consulte [Adicionar autenticação a um bot](bot-builder-authentication.md).

### <a name="best-practices"></a>Práticas recomendadas

- Mantenha o registro do aplicativo do AAD restrito à sua finalidade original de serviço para aplicativo de serviço.
- Crie um aplicativo AAD adicional para qualquer usuário para autenticação de serviço, para um controle mais finito sobre a desabilitação de conexões de autenticação, segredos sem interrupção ou reutilização do aplicativo AAD com outros aplicativos.

Alguns dos problemas encontrados se você também usar o aplicativo de registro do AAD para autenticação são:

- Se o certificado anexado ao registro do aplicativo do AAD precisar ser renovado, ele afetaria os usuários que se autenticaram com outros serviços do AAD usando o certificado.
- Em geral, ele cria um ponto único de falha e controle para todas as atividades relacionadas à autenticação com o bot.

## <a name="related-topics"></a>Tópicos relacionados

Os artigos a seguir fornecem informações detalhadas e exemplos sobre a autenticação do bot Framework. Comece examinando os [tipos de autenticação](bot-builder-concept-authentication-types.md) e, em seguida, [provedores de identidade](bot-builder-concept-identity-providers.md).

> [!div class="mx-tdBreakAll"]
> |Artigo|Descrição|
> |-------------|----------|
> |[Tipos de autenticação <img width=105px/>](bot-builder-concept-authentication-types.md)| Descreve os dois tipos de autenticação da estrutura de bot e os tokens que eles usam.|
> |[Provedores de Identidade](bot-builder-concept-identity-providers.md)| Descreve o uso de provedores de identidade. Eles permitem que você crie aplicativos que conectam usuários com segurança usando protocolos padrão do setor, como o OAuth 2.0.|
> |[Autenticação de usuário](bot-builder-concept-authentication.md)| Descreve a autenticação do usuário e o token relacionado para autorizar um bot a executar tarefas em nome do usuário.|
> |[Logon único](bot-builder-concept-sso.md)| Descreve a autenticação de usuário único para acesso a vários recursos protegidos.|
> |[Registro de canais do bot](../bot-service-quickstart-registration.md)|Mostra como registrar um bot com o serviço de bot do Azure.|
> |[Diretrizes de segurança do Bot Framework](bot-builder-security-guidelines.md)| Descreve a segurança em geral e como ela se aplica ao bot Framework.|
> |[Adicionar autenticação a um bot](bot-builder-authentication.md)| Mostra como criar um registro de canal de bot, conexão de autenticação e preparação do código.|
> |[Adicionar logon único a um bot](bot-builder-authentication-sso.md)| Mostra como adicionar autenticação de logon único a um bot.|
