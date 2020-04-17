---
ms.openlocfilehash: 4678bd83d9f95e44531127a1bdbdd1ff94a9f742
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77479241"
---
Neste ponto, estamos prontos para implantar o código no aplicativo Web do Azure. Execute o seguinte comando na linha de comando para executar a implantação usando o serviço de implantação de zip por push do kudu para um aplicativo web.

```cmd
az webapp deployment source config-zip --resource-group "<resource-group-name>" --name "<name-of-web-app>" --src <project-zip-path>
```

| Opção   | Descrição |
|:---------|:------------|
| resource-group | O nome do grupo de recursos do Azure que contém o bot. (Esse será o grupo de recursos que você usou ou criou durante a criação do registro do aplicativo para o bot.) |
| name | Nome do Aplicativo Web que você usou anteriormente. |
| src  | O caminho para o arquivo de projeto compactado que você criou. |

> [!NOTE]
> A conclusão dessa etapa pode levar alguns minutos.
> Além disso, pode levar mais alguns minutos entre a conclusão da implantação e a disponibilidade do bot para teste.