---
ms.openlocfilehash: 51363283a2789d4d15fb65e625aed18aeb0f01b7
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94599659"
---

É necessário preparar os arquivos do projeto para implantar o bot de C#, Javascript ou Typescript. Consulte as informações do Python se você precisar preparar os arquivos do projeto antes de implantar o bot do Python.

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

Esse comando gera um arquivo `web.config` na pasta do projeto.

<!-- **Python bots** -->
##### <a name="python"></a>[Python](#tab/python)

Se você estiver usando um Gerenciador de dependências e pacotes, precisará converter sua lista de dependências em um `requirements.txt` arquivo e adicioná-la à pasta que contém `app.py` . Isso é necessário porque a instalação de dependência ocorre no lado do servidor para bots do Python, não localmente como faz para bots em outras linguagens. Os arquivos em `deploymentTemplates` procure `requirements.txt` , e sem o arquivo, suas dependências não serão instaladas.

---
