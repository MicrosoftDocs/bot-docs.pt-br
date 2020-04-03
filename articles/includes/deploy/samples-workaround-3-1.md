---
ms.openlocfilehash: fa4a49fea5bc2c85459e73fd89ec47a908864eb5
ms.sourcegitcommit: 126c4f8f8c7a3581e7521dc3af9a937493e6b1df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80499830"
---
> [!IMPORTANT]
> Com o lançamento do SDK do Bot Framework 4.8, os exemplos do .NET Bot Framework agora têm como destino o SDK do .NET Core 3.1.
> Nem todos os data centers do Azure estão configurados para criar tais bots.
>
> Consulte o mapa do [.NET Core no Serviço de Aplicativo](https://aspnetcoreon.azurewebsites.net/) para os centros nos quais você pode criar aplicativos do .NET Core 3.1 usando o Kudu. (Todos os centros podem executar aplicativos do .NET Core 3.1.)
>
> Se você estiver implantando um bot direcionado ao SDK do .NET Core 3.1 e estiver implantando em um centro que não pode compilar aplicativos .NET Core 3.1 usando o Kudu, use esta solução alternativa para preparar e compactar seus arquivos de bot. Caso contrário, você pode usar as etapas nas próximas seções.
>
> 1. Execute este comando no diretório que contém a solução ou o arquivo de projeto para o bot.
>
>    ```powershell
>    dotnet publish --configuration Release --runtime win-x86 --self-contained
>    ```
>
> 1. Compacte o conteúdo da pasta **/Release/netcoreapp3.1/publish/** .
