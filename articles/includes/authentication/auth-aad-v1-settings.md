---
ms.openlocfilehash: 2413184fe88dcf5560599be63b4ee85667f680bd
ms.sourcegitcommit: 64b25f796f89e8bb6fa53d3c824b73b8ce4d6ed8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80250205"
---
<!-- Azure AD v1 settings -->
<!-- Fixed ID -->
| **Propriedade** | **Descrição** | **Valor** |
|---|---|---|
|**Nome** | O nome da sua conexão | \<Seu nome para a conexão\> <img width="300px">|
|**Provedor de Serviço**| Provedor de identidade do Azure AD | `Azure Active Directory` |
|**ID do Cliente** | ID do aplicativo do provedor de identidade do Azure AD| \<ID do aplicativo do provedor do AAD\> |
|**Segredo do cliente** | Segredo do aplicativo do provedor de identidade do Azure AD| \<Segredo do aplicativo do provedor do AAD\> |
|**Tipo de Concessão** | | `authorization_code` |
|**URL de logon** | | `https://login.microsoftonline.com` |
|**ID do locatário** | | <ID do diretório (locatário)> ou `common`. Veja a observação.|
|**URL de recursos** | | `https://graph.microsoft.com/` |
|**Escopos** | | <leave it blank> |
|**URL de Troca de Token** |Usado para SSO no Azure AD v2| |
| | |


**Observação**

- Insira a **ID de locatário** registrada para o aplicativo de provedor de identidade do AAD, caso você tenha selecionado uma das seguintes opções:

    - *Somente contas neste diretório organizacional (somente Microsoft – locatário único)*

    - *Contas em qualquer diretório organizacional (diretório do Microsoft AAD – multilocatário)*
- Insira `common` se você tiver selecionado *Contas em qualquer diretório organizacional (Qualquer diretório do AAD – contas multilocatário e pessoais Microsoft, por ex. Skype, Xbox, Outlook.com)* . Caso contrário, o aplicativo do provedor de identidade do AAD verificará o locatário cuja ID foi selecionada e excluirá as contas MS pessoais.
