---
ms.openlocfilehash: 1b6539ab58110824d0ba389ac83e49f9733b6853
ms.sourcegitcommit: 2f66efadbbbda16fab3258a9d03f4e56821ab412
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85073697"
---
Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

É importante incluir seu código compilado com todas as dependências necessárias no arquivo zip a ser implantado, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de zipar os arquivos do projeto, verifique se você está na pasta do projeto do bot.
> - Para bots C#, será a pasta que tiver o arquivo .csproj.
> - Para bots JavaScript, será a pasta que tiver o arquivo app.js ou index.js.
> - Para bots TypeScript, será a pasta que inclui a pasta _src_ (em que os arquivos bot.ts e index.ts estão).
> - Para bots Python, será a pasta que tem o arquivo app.py.
> 
> **Dentro** da pasta do projeto, certifique-se de selecionar todos os arquivos e pastas antes de executar o comando para criar o arquivo zip. Isso criará um arquivo zip na pasta do projeto. Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.