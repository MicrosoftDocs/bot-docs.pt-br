---
ms.openlocfilehash: b3fcdea923daa96a93b7b9d223354a16878b0ac2
ms.sourcegitcommit: 7e901f5f39a0cfb0d37e532321b90a1dcf4baadd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039779"
---
Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

Logo, é importante incluir seu código compilado e todas as dependências necessárias no arquivo zip a ser implantado no Aplicativo Web, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de compactar os arquivos de projeto, verifique se você está _na_ pasta do projeto. 
> - Para bots C#, será a pasta que tiver o arquivo .csproj. 
> - Para bots JavaScript, será a pasta que tiver o arquivo app.js ou index.js. 
> - Para bots TypeScript, será a pasta que inclui a pasta _src_ (em que os arquivos bot.ts e index.ts estão). 
>
>**Dentro** da pasta do projeto, selecione todos os arquivos e compacte-os. Em seguida, execute o comando ainda na pasta. 
>
> Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.
