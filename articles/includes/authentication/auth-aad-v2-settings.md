---
ms.openlocfilehash: a862e8bfbc4396c9e440cbb360fcd9d660c0203e
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717522"
---

<!-- Azure AD v2 settings -->
<!-- Fixed ID -->

| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Your name for the connection\> <img width="300px">|
|**Provedor de Serviço**| Provedor de identidade do Azure AD | `Azure Active Directory v2` |
|**ID do Cliente** | ID do aplicativo do provedor de identidade do Azure AD| \<AAD provider app ID\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade do Azure AD| \<AAD provider app secret\> |
|**ID do locatário** | | \<directory (tenant) ID\> ou `common`. Veja a observação. |
|**Escopos** |Lista separada por espaços das permissões de API que você concedeu ao aplicativo do provedor de identidade do Azure AD| Valores como,,,, `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` e `User.ReadBasic.All` |
|**URL de Troca de Token** |Usado para SSO no Azure AD v2| |
| | |

**Observação**

- Insira a **ID de locatário** registrada para o aplicativo de provedor de identidade do AAD, caso você tenha selecionado uma das seguintes opções:

    - *Somente contas neste diretório organizacional (somente Microsoft – locatário único)*

    - *Contas em qualquer diretório organizacional (diretório do Microsoft AAD – multilocatário)*
- Insira `common` se você tiver selecionado *Contas em qualquer diretório organizacional (Qualquer diretório do AAD – contas multilocatário e pessoais Microsoft, por ex. Skype, Xbox, Outlook.com)* . Caso contrário, o aplicativo do provedor de identidade do AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.
- Os escopos usam uma lista de valores separada por espaços que diferencia maiúsculas de minúsculas.
