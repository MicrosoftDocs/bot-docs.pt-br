---
ms.openlocfilehash: fcf42541da048ecdbf1281c4409817ff4cc4b36c
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360109"
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

<!-- **TPython bots** -->
##### <a name="python"></a>[Python](#tab/Python)

Se você não tiver dependências adicionais, poderá ignorar isso e continuar na etapa 5,2.

Se você estiver usando um Gerenciador de dependências e pacotes, precisará converter sua lista de dependências em um `requirements.txt` arquivo e adicioná-la à pasta que contém `app.py` . Isso é necessário porque a instalação de dependência ocorre no lado do servidor para bots do Python, não localmente como faz para bots em outras linguagens. Os arquivos em `deploymentTemplates` procure `requirements.txt` , e sem o arquivo, suas dependências não serão instaladas. 


---
