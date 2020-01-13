---
ms.openlocfilehash: 71b47097da95e2da9d9916557e86061c5a10248e
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491169"
---

É necessário preparar os arquivos do projeto para implantar o bot de C#, Javascript ou Typescript. Se estiver implantando um bot de Python, ignore esta etapa.

<!-- **C# bots** -->
##### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Você deve fornecer o caminho do arquivo .csproj relativo a --code-dir. Isso pode ser feito por meio do argumento --proj-file-path. O comando resolveria --code-dir e --proj-file-path com "./MyBot.csproj"

<!-- **JavaScript bots** -->
##### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Esse comando buscará um arquivo web.config que é necessário para aplicativos Node.js trabalharem com o IIS nos Serviços de Aplicativos do Azure. Certifique-se de que o web.config seja salvo na raiz de seu bot.

<!-- **TypeScript bots** -->
##### <a name="typescripttabtypescript"></a>[TypeScript](#tab/typescript)

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Esse comando funciona da mesma forma que o JavaScript acima, mas para um bot Typescript.

---

> [!NOTE]
>  Para bots C# e bots JavaScript, o comando `az bot prepare-depoloy` deve gerar um arquivo `.deployment` na pasta do projeto do bot.
> Para bots TypeScript, o comando deve gerar dois arquivos `web.config`. Um deles está na pasta do projeto e o outro na pasta **src** dentro da pasta do projeto. 
