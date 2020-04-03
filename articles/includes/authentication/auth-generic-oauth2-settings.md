---
ms.openlocfilehash: 5f37468f6c0bd61c54cd1eef66f432c6044978fe
ms.sourcegitcommit: 64b25f796f89e8bb6fa53d3c824b73b8ce4d6ed8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80250195"
---
<!-- Generic Oauth2 provider settings -->
<!-- Fixed ID -->

| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Seu nome para a conexão\> <img width="300px">|
| **Provedor de Serviço**| Provedor de identidade | Na lista suspensa, selecione **OAuth 2 Genérico** |
|**ID do Cliente** | ID do aplicativo do provedor de identidade| \<ID do provedor\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade| <segredo do provedor\> |
|**URL de Autorização** | | https://login.microsoftonline.com/common/oauth2/v2.0/authorize |
|*Cadeia de Consulta da URL de Autorização* | | *?client_id={ClientId}&response_type=code&redirect_uri={RedirectUrl}&scope={Scopes}&state={State}* |
|**URL do Token** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Corpo do Token* | Corpo a ser enviado para a troca de tokens | *code={Code}&grant_type=authorization_code&redirect_uri={RedirectUrl}&client_id={ClientId}&client_secret={ClientSecret}* |
|**URL de Atualização** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Atualizar o Modelo do Corpo* | Corpo a ser enviado com a atualização do token | *refresh_token={RefreshToken}&redirect_uri={RedirectUrl}&grant_type=refresh_token&client_id={ClientId}&client_secret={ClientSecret}* |
|**Escopos** | Lista separada por vírgulas das permissões de API que você concedeu anteriormente para o aplicativo de autenticação do Azure AD | Valores como `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` `User.ReadBasic.All`|
