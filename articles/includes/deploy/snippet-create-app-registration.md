---
ms.openlocfilehash: 9ab522b3c8c715079e24756e4558a77cf38a64d1
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073669"
---
Nesta etapa, você criará um registro de aplicativo do Azure, que permitirá:

- O usuário deve interagir com o bot por meio de um conjunto de canais, como *Webchat*.
- A definição de *Configurações de Conexão OAuth* para autenticar um usuário e criar um *token* usado pelo bot para acessar recursos protegidos em nome do usuário.

#### <a name="31-create-the-azure-application-registration"></a>3.1 Criar o registro de aplicativo do Azure
Para criar um registro de aplicativo do Azure, execute o seguinte comando:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Opção   | Descrição |
|:---------|:------------|
| nome de exibição | O nome de exibição do aplicativo. Ele é listado no portal do Azure na lista de recursos gerais e no grupo de recursos ao qual ele pertence.|
| password | A senha, também conhecida como **segredo do cliente**, para o aplicativo. Esta é uma senha que você cria para esse recurso. Ela deve ter pelo menos 16 caracteres, sendo pelo menos um caractere alfabético maiúsculo ou minúsculo e pelo menos um caractere especial.|
| disponível para outros locatários| Indica se o aplicativo pode ser usado de qualquer locatário do Azure AD. Defina isso para habilitar o bot a trabalhar com os canais do Serviço de Bot do Azure.|

#### <a name="32-record-the-appid-and-appsecret-values"></a>3.2 Registrar os valores appId e appSecret
Você precisará gravar dois valores depois de executar o comando na etapa anterior (etapa [3.1](#31-create-the-azure-application-registration)):
- O `password` criado na etapa anterior. 
- O `appId` que você pode encontrar no JSON de saída. 

Copie e salve os valores `appId` e `password`. Você precisará usá-los na etapa de implantação do ARM para atribuir valores aos parâmetros `appId` e `appSecret`, respectivamente.