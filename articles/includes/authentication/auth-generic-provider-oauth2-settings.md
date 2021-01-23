---
ms.openlocfilehash: 3e868b13955ad1cba870f887a6158ba2f5258eea
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717524"
---
<!-- Oauth 2 generic provider settings -->
<!-- Fixed ID -->

| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Your name for the connection\> <img width="300px">|
| **Provedor de Serviço**| Provedor de identidade | Na lista suspensa, selecione **Provedor Genérico OAuth 2** |
|**ID do Cliente** | ID do aplicativo do provedor de identidade| \<provider ID\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade| <segredo do provedor\> |
|*Delimitador da Lista de Escopo*|O caractere a ser usado entre os valores de escopo (geralmente um espaço ou uma vírgula) | *,* \<enter comma\> |
|**Modelo de URL de Autorização** || https://login.microsoftonline.com/common/oauth2/v2.0/authorize |
|*Cadeia de Consulta da URL de Autorização* |A cadeia de caracteres de consulta a ser acrescentada à URL de autorização, modelada com parâmetros desejados: {ClientId} {ClientSecret} {RedirectUrl} {Scopes} {State}| *?client_id={ClientId}&response_type=code&redirect_uri={RedirectUrl}&scope={Scopes}&state={State}* |
|**Modelo de URL do Token** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Modelo de cadeia de consulta da URL do token* | Corpo a ser enviado para a troca de tokens |*?* \<enter question mark\>|
|*Modelo de Corpo do Token* | Corpo a ser enviado para a troca de tokens | *code={Code}&grant_type=authorization_code&redirect_uri={RedirectUrl}&client_id={ClientId}&client_secret={ClientSecret}* |
|**Atualizar o Modelo de URL** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Atualizar o modelo da cadeia de consulta da URL* |A cadeia de caracteres de consulta a ser acrescentada à URL de atualização, modelada com parâmetros desejados: {ClientId} {ClientSecret} {RedirectUrl} {Scopes} {State} |*?* \<enter question mark\>|
|*Atualizar o Modelo do Corpo* | Corpo a ser enviado com a atualização do token | *refresh_token={RefreshToken}&redirect_uri={RedirectUrl}&grant_type=refresh_token&client_id={ClientId}&client_secret={ClientSecret}* |
|**Escopos** | Lista separada por vírgulas das permissões de API que você concedeu anteriormente para o aplicativo de autenticação do Azure AD | Valores como,,,, `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` e `User.ReadBasic.All`|
