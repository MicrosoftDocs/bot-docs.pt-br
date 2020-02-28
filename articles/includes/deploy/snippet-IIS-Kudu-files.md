---
ms.openlocfilehash: 3a3d1056e9c6f913efbf563131a5ab377a57d2d0
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479242"
---

É necessário preparar os arquivos do projeto para implantar o bot de C#, Javascript ou Typescript. Se estiver implantando um bot de Python, ignore esta etapa.

<!-- **C# bots** -->
##### <a name="c"></a>[C#](#tab/csharp)

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Você deve fornecer o caminho do arquivo .csproj relativo a --code-dir. Isso pode ser feito por meio do argumento --proj-file-path. O comando resolveria --code-dir e --proj-file-path com "./MyBot.csproj"

<!-- **JavaScript bots** -->
##### <a name="javascript"></a>[JavaScript](#tab/javascript)

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Esse comando buscará um arquivo web.config que é necessário para aplicativos Node.js trabalharem com o IIS nos Serviços de Aplicativos do Azure. Certifique-se de que o web.config seja salvo na raiz de seu bot.

<!-- **TypeScript bots** -->
##### <a name="typescript"></a>[TypeScript](#tab/typescript)

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Esse comando funciona da mesma forma que o JavaScript acima, mas para um bot Typescript.

---

> [!NOTE]
>  Para bots C#, o comando `az bot prepare-deploy` gera o arquivo sa `.deployment` na pasta do projeto de bot.
> Para bots JavaScript, o comando gera dois arquivos `web.config` na pasta do projeto.
> Para bots TypeScript, o comando gera dois arquivos `web.config`. Um deles está na pasta do projeto e o outro na pasta **src** dentro da pasta do projeto.


