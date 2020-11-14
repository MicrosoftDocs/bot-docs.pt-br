---
ms.openlocfilehash: 5e052abb0eda1834cccd5b230843d473f49e9a96
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94596869"
---
<!-- Include under "Prerequisites" header in the files:
bot-builder-tutorial-create-basic-bot.md and bot-builder-dotnet-sdk-quickstart.md -->

- [.NET Core 3.1](https://dotnet.microsoft.com/download)
- [Emulador do bot Framework](https://aka.ms/bot-framework-emulator-readme)
- Conhecimento de [ASP.Net Core](https://docs.microsoft.com/aspnet/core/) e [programação assíncrona em C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)

### <a name="templates"></a>Modelos

# <a name="visual-studio"></a>[Visual Studio](#tab/vs)

- [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads)
- [Modelo de SDK do Bot Framework v4 para C#](https://aka.ms/bot-vsix)

Para adicionar os modelos de bot ao Visual Studio, baixe e instale os [modelos SDK do bot Framework v4 para o arquivo VSIX do Visual Studio](https://aka.ms/bot-vsix) .

[!INCLUDE [dotnet vsix templates info](~/includes/vsix-templates-versions.md)]

# <a name="visual-studio-code--command-line"></a>[Visual Studio Code/linha de comando](#tab/vc+cl)

Os modelos do .NET Core ajudarão você a criar rapidamente novos bots de AI de conversa usando o bot Framework v4. A partir de maio de 2020, esses modelos e o código que eles geram exigem o .NET Core 3,1.

Para instalar os modelos do bot Framework:

1. Abra uma janela do console.

1. Instale [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 3,1 ou superior.
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
> As etapas de instalação acima instalarão todos os 3 modelos do bot Framework. Você não precisa instalar todos os 3 modelos e pode instalar apenas aqueles que serão usados. Este artigo utiliza o modelo de bot de eco.

---