---
ms.openlocfilehash: dd7af40d773d2b2664df1cb9ffc3184f6555a822
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97759294"
---
<!-- Include under "Prerequisites" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

- [Tempo de execução de ASP.NET Core 3,1](https://dotnet.microsoft.com/download)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [ASP.NET Core](https://docs.microsoft.com/aspnet/core/) e [programação assíncrona em C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)

### <a name="templates"></a>Modelos

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

- [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads)
- [Modelos de SDK do bot Framework v4 para Visual Studio](https://aka.ms/bot-vsix)

Para adicionar os modelos de bot ao Visual Studio, baixe e instale os [modelos SDK do bot Framework v4 para o arquivo VSIX do Visual Studio](https://aka.ms/bot-vsix) .

[!INCLUDE [.NET VSIX templates info](../../../includes/vsix-templates-versions.md)]

# <a name="visual-studio-code--command-line"></a>[Visual Studio Code/linha de comando](#tab/vc+cl)

Os modelos do .NET Core ajudarão você a criar rapidamente novos bots de AI de conversa usando o bot Framework v4. A partir de maio de 2020, esses modelos e o código que eles geram exigem o .NET Core 3,1.

Para instalar os modelos do bot Framework:

1. Abra uma janela do console.

1. Baixe e instale [SDK do .NET Core Baixe](https://dotnet.microsoft.com/download) a versão 3,1 ou superior.
1. Você pode usar esse comando para determinar qual versão da interface de linha de comando do .NET Core está instalada.

   ```cmd
   dotnet --version
   ```

1. Instale os 3 modelos de C# da estrutura de bot: os modelos de bot de eco, núcleo e vazio.

   ```cmd
   dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
   dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
   dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
   ```

1. Verifique se os modelos foram instalados corretamente.

   ```cmd
   dotnet new --list
   ```

> [!NOTE]
> As etapas de instalação acima instalarão todos os 3 modelos do bot Framework. Você não precisa instalar todos os 3 modelos e pode instalar apenas aqueles que serão usados. Este artigo utiliza o modelo de _bot de eco_ .

---
