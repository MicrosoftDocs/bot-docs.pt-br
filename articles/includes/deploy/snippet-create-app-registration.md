---
ms.openlocfilehash: 28f49af1610f3c154e76d1cfcdb1333f08374f6c
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386050"
---
Registrar o aplicativo significa que você poderá usar o Microsoft Azure AD para autenticar usuários e solicitar acesso a recursos de usuários. Seu bot precisará de um aplicativo registrado no Azure que o permita acessar o Bot Framework Service para enviar e receber mensagens autenticadas. Para criar o registro de um aplicativo por meio da CLI do Azure, execute o seguinte comando:

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| nome de exibição | O nome de exibição do aplicativo. |
| Senha | Senha do aplicativo, também conhecida como 'segredo do cliente'. A senha deve ter pelo menos 16 caracteres, sendo pelo menos um caractere alfabético maiúsculo ou minúsculo e pelo menos um caractere especial.|
| disponível para outros locatários| Indica se o aplicativo pode ser usado em qualquer locatário do Azure AD. Defina essa opção como `true` para permitir que o bot funcione com os canais do Serviço de Bot do Azure.|

O comando acima gera JSON com a chave `appId`, salva o valor dessa chave para a implantação do ARM, onde ela será usada para o parâmetro `appId`. A senha fornecida será usada ao parâmetro `appSecret`.

> [!NOTE] 
> Se quiser usar um Registro do aplicativo existente, você poderá usar o comando:
> ``` cmd
> az bot create --kind webapp --resource-group "<name-of-resource-group>" --name "<name-of-web-app>" --appid "<existing-app-id>" --password "<existing-app-password>" --lang <Javascript|Csharp>
> ```
