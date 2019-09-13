---
ms.openlocfilehash: 80cc69ca3d863a4f96f1dd241e6471074cc9976f
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386038"
---
Com o uso da [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) não configurada para implantar o código de seu bot, o comportamento do Aplicativo Web/Kudu será o seguinte:

_O Kudu assume por padrão que as implantações a partir de arquivos zip estão prontas para serem executadas e não exigem etapas de compilação adicionais durante a implantação, como npm install ou dotnet restore/dotnet publish._

Logo, é importante incluir seu código compilado e todas as dependências necessárias no arquivo zip a ser implantado no Aplicativo Web, caso contrário, seu bot não funcionará como esperado.

> [!IMPORTANT]
> Antes de compactar os arquivos de projeto, verifique se você está _na_ pasta do projeto. 
> - Para bots C#, será a pasta que tiver o arquivo .csproj. 
> - Para bots JS, será a pasta que tiver o arquivo app.js ou index.js. 
>
>**Dentro** da pasta do projeto, selecione todos os arquivos e compacte-os. Em seguida, execute o comando ainda na pasta. 
>
> Se o local da pasta raiz estiver incorreto, o **bot não será executado no portal do Azure**.
