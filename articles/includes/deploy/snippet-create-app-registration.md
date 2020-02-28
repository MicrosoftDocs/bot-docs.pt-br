---
ms.openlocfilehash: a0d110e4975fd3fed943080c5bf4bc71739e877b
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479235"
---
Nesta etapa, você criará um aplicativo do Azure Active Directory, que permitirá:

- O usuário deve interagir com o bot por meio de um conjunto de canais, como *Webchat*.
- A definição de *Configurações de Conexão OAuth* para autenticar um usuário e criar um *token* usado pelo bot para acessar recursos protegidos em nome do usuário.

Para criar um aplicativo do Azure Active Directory, execute o seguinte comando:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Opção   | Descrição |
|:---------|:------------|
| nome de exibição | O nome de exibição do aplicativo. Ele é listado no portal do Azure na lista de recursos gerais e no grupo de recursos ao qual ele pertence.|
| password | A senha, também conhecida como **segredo do cliente**, para o aplicativo. Esta é uma senha que você cria para esse recurso. Ela deve ter pelo menos 16 caracteres, sendo pelo menos um caractere alfabético maiúsculo ou minúsculo e pelo menos um caractere especial.|
| disponível para outros locatários| Indica se o aplicativo pode ser usado de qualquer locatário do Azure AD. Defina isso para habilitar o bot a trabalhar com os canais do Serviço de Bot do Azure.|

O comando acima produz o JSON com a chave `appId`, copie e salva-a.
Você usará essa `appId` e a senha inseridas na etapa de implantação do ARM para atribuir valores aos parâmetros `appId` e `appSecret`, respectivamente.
