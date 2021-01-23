---
ms.openlocfilehash: 867efd810b0d3de8e4331791dbc09878cd7a9e48
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717523"
---
<!-- Generic Oauth2 provider settings -->
<!-- Fixed ID -->

| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Your name for the connection\> <img width="300px">|
| **Provedor de Serviço**| Provedor de identidade | Na lista suspensa, selecione **OAuth 2 Genérico** |
|**ID do Cliente** | ID do aplicativo do provedor de identidade| \<provider ID\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade| <segredo do provedor\> |
|**URL de Autorização** | | https://login.microsoftonline.com/common/oauth2/v2.0/authorize |
|*Cadeia de Consulta da URL de Autorização* | | *?client_id={ClientId}&response_type=code&redirect_uri={RedirectUrl}&scope={Scopes}&state={State}* |
|**URL do Token** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Corpo do Token* | Corpo a ser enviado para a troca de tokens | *code={Code}&grant_type=authorization_code&redirect_uri={RedirectUrl}&client_id={ClientId}&client_secret={ClientSecret}* |
|**URL de Atualização** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Atualizar o Modelo do Corpo* | Corpo a ser enviado com a atualização do token | *refresh_token={RefreshToken}&redirect_uri={RedirectUrl}&grant_type=refresh_token&client_id={ClientId}&client_secret={ClientSecret}* |
|**Escopos** | Lista separada por vírgulas das permissões de API que você concedeu anteriormente para o aplicativo de autenticação do Azure AD | Valores como,,,, `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` e `User.ReadBasic.All`|
