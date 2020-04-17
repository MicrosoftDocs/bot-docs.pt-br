---
ms.openlocfilehash: aa609e588ab1cf914a9a978228a879d3f3102e19
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80250225"
---

<!-- Azure AD v2 settings -->
<!-- Fixed ID -->

| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Seu nome para a conexão\> <img width="300px">|
|**Provedor de Serviço**| Provedor de identidade do Azure AD | `Azure Active Directory v2` |
|**ID do Cliente** | ID do aplicativo do provedor de identidade do Azure AD| \<ID do aplicativo do provedor do AAD\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade do Azure AD| \<Segredo do aplicativo do provedor do AAD\> |
|**ID do locatário** | | \<ID do diretório (locatário)\> ou `common`. Consultar a observação |
|**Escopos** |Lista separada por espaços das permissões de API que você concedeu ao aplicativo do provedor de identidade do Azure AD| Valores como `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` `User.ReadBasic.All` |
|**URL de Troca de Token** |Usado para SSO no Azure AD v2| |
| | |

**Observação**

- Insira a **ID de locatário** registrada para o aplicativo de provedor de identidade do AAD, caso você tenha selecionado uma das seguintes opções:

    - *Somente contas neste diretório organizacional (somente Microsoft – locatário único)*

    - *Contas em qualquer diretório organizacional (diretório do Microsoft AAD – multilocatário)*
- Insira `common` se você tiver selecionado *Contas em qualquer diretório organizacional (Qualquer diretório do AAD – contas multilocatário e pessoais Microsoft, por ex. Skype, Xbox, Outlook.com)* . Caso contrário, o aplicativo do provedor de identidade do AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.
- Os escopos usam uma lista de valores separada por espaços que diferencia maiúsculas de minúsculas.
