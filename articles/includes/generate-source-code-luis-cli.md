---
ms.openlocfilehash: cd486fc162bc5ac86f2010cd1b249cc347b0a2b8
ms.sourcegitcommit: d974a0b93f13db7720fcb332f37bf8a404d77e43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2020
ms.locfileid: "90824928"
---
### <a name="generate-a-c-class-for-the-model-results"></a>Gerar uma classe C# para os resultados do modelo

O `luis:generate:cs` comando pode ser usado para gerar um código-fonte C# com rigidez de tipos de um modelo Luis (JSON).

Execute o comando a seguir para criar uma representação. cs de seu modelo LUIS:

```cli
bf luis:generate:cs -i <luis-model-file> -o <output-file-name> --className <class-name>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Generate: CS] [BF-luisgeneratecs] no Leiame do BF CLI LUIS.

### <a name="generate-a-typescript-type-for-the-model-results"></a>Gerar um tipo TypeScript para os resultados do modelo

O `luis:generate:ts` comando pode ser usado para gerar um código-fonte typescript com rigidez de tipos de um modelo Luis (JSON).

Execute o comando a seguir para criar uma representação. TS de seu modelo LUIS:

```cli
bf luis:generate:ts -i <luis-model-file> -o <output-file-name> --className <class-name>
```

Para obter informações adicionais sobre como usar esse comando, consulte [BF Luis: Generate: TS] [BF-luisgeneratets] no Leiame do BF CLI LUIS.