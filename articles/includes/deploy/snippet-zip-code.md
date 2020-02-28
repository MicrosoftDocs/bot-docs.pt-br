---
ms.openlocfilehash: 71eb85da7a18757806bb5d782723d270f8db0ac2
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479237"
---
Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

É importante incluir seu código compilado com todas as dependências necessárias no arquivo zip a ser implantado, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de compactar os arquivos de projeto, verifique se você está _na_ pasta do projeto.
> - Para bots C#, será a pasta que tiver o arquivo .csproj.
> - Para bots JavaScript, será a pasta que tiver o arquivo app.js ou index.js.
> - Para bots TypeScript, será a pasta que inclui a pasta _src_ (em que os arquivos bot.ts e index.ts estão).
> - Para bots Python, será a pasta que tem o arquivo app.py.

>**Dentro** da pasta do projeto, selecione todos os arquivos e pastas que você deseja incluir no arquivo zip antes de executar o comando para criar esse arquivo. Isso criará um único arquivo zip contendo todos os arquivos e pastas selecionados.
> Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.
