---
ms.openlocfilehash: 0c28cf506f2701acfc705fdcc67ec6a1e7224ad1
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386026"
---
Neste ponto, estamos prontos para implantar o código no aplicativo Web do Azure. Execute o seguinte comando na linha de comando para executar a implantação usando o serviço de implantação de zip por push do kudu para um aplicativo web.

```cmd
az webapp deployment source config-zip --resource-group "<resource-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Opção   | DESCRIÇÃO |
|:---------|:------------|
| resource-group | O nome do grupo de recursos do Azure que contém o bot. (Esse será o grupo de recursos que você usou ou criou durante a criação do registro do aplicativo para o bot.) |
| Nome | Nome do Aplicativo Web que você usou anteriormente. |
| src  | O caminho para o arquivo compactado que você criou. |
