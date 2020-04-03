---
ms.openlocfilehash: 3e578bcd8301bfb74946eda99d86f5e25eea17b0
ms.sourcegitcommit: 126c4f8f8c7a3581e7521dc3af9a937493e6b1df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80499844"
---

É necessário preparar os arquivos do projeto para implantar o bot de C#, Javascript ou Typescript. Se estiver implantando um bot de Python, ignore esta etapa e continue na etapa 5.2.

<!-- **C# bots** -->
##### <a name="c"></a>[C#](#tab/csharp)

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Você deve fornecer o caminho do arquivo .csproj relativo a --code-dir. Isso pode ser feito por meio do argumento --proj-file-path. O comando resolveria --code-dir e --proj-file-path para "./MyBot.csproj".

Esse comando gera um arquivo `.deployment` na pasta do projeto do bot.

<!-- **JavaScript bots** -->
##### <a name="javascript"></a>[JavaScript](#tab/javascript)

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

O comando gera dois arquivos `web.config` na pasta do projeto. Os aplicativos Node.js precisam do web.config para funcionarem com o IIS nos Serviços de Aplicativos do Azure. Certifique-se de que o web.config seja salvo na raiz de seu bot.

<!-- **TypeScript bots** -->
##### <a name="typescript"></a>[TypeScript](#tab/typescript)

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Esse comando funciona de maneira semelhante ao JavaScript, pois ele gera dois arquivos de `web.config`. Um deles está na pasta do projeto e o outro está na pasta **src** dentro da pasta do projeto.

<!-- **TPython bots** -->
##### <a name="python"></a>[Python](#tab/Python)

Você não precisa preparar seus arquivos de projeto antes de implantar um bot do Python. Prossiga para a etapa 5.2.

---
