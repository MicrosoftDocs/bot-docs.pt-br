---
title: Como executar o bot do SDK V3 do .NET no SDK V4 | Microsoft Docs
description: Saiba como converter um bot de 3.x para 4.0 usando o pacote NuGet clássico.
keywords: migração, bot clássico, converter v3, v3 para v4
author: v-royhar
ms.author: v-royhar
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 4/25/18
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a808076e4865a181802b85cfc24ce342dbf23cba
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296725"
---
# <a name="how-to-run-net-sdk-v3-bots-in-sdk-40"></a>Como executar bots do SDK V3 do .NET no SDK 4.0

O pacote NuGet **Microsoft.Bot.Builder.Classic** facilita a migração dos bots da versão 3.x para a versão 4.0 do Microsoft Bot Framework.

**OBSERVAÇÃO:** esse processo funciona apenas para bots do **Aplicativo Web ASP.NET (.NET Framework)**. Isso não funciona em bots do **Aplicativo Web ASP.NET Core**.

## <a name="the-process"></a>O processo

O processo é relativamente simples:

- Adicione o pacote NuGet **Microsoft.Bot.Builder.Classic** ao projeto.
    - Talvez você também precise atualizar o pacote NuGet **Autofac**.
- Atualize os namespaces **Microsoft.Bot.Builder.Classic**.
- Invoque os diálogos usando **Conversation.SendAsync()** baseado no **ITurnContext** 4.0.

### <a name="add-the-microsoftbotbuilderclassic-nuget-package"></a>Adicionar o pacote NuGet Microsoft.Bot.Builder.Classic

Para adicionar o pacote NuGet **Microsoft.Bot.Builder.Classic**, use a opção **Gerenciar Pacotes NuGet** para adicionar o pacote.

### <a name="update-the-namespaces"></a>Atualizar os namespaces

Para atualizar os namespaces, exclua uma das seguintes instruções `using` encontradas:

```csharp
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
using Microsoft.Bot.Builder.FormFlow;
using Microsoft.Bot.Builder.Scorables;
```

Em seguida, adicione as seguintes instruções `using` em seu lugar:

```csharp
using Microsoft.Bot.Builder.Adapters;
using Microsoft.Bot.Builder.Classic.Dialogs;
using Microsoft.Bot.Builder.Classic.Dialogs.Internals;
using Microsoft.Bot.Builder.Classic.FormFlow;
using Microsoft.Bot.Builder.Classic.Scorables;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Schema;
```

Se você tiver uma linha `[BotAuthentication]`, exclua a linha ou comente-a.

### <a name="invoke-your-3x-dialog"></a>Invocar o diálogo 3.x

Para invocar o diálogo 3.x, você ainda usa `Conversation.SendAsync`, mas agora é usado um **ITurnContext** 4.0 em vez de uma **Activity**.

```csharp
// invoke a Classic V3 IDialog 
await Conversation.SendAsync(turnContext, () => new EchoDialog());
```

Se você não tiver o objeto **ITurnContext**, mas tiver um objeto **Activity**, obtenha o objeto **ITurnContext** desta forma:

```csharp
BotFrameworkAdapter adapter = new BotFrameworkAdapter("", "");

await adapter.ProcessActivity(this.Request.Headers.Authorization?.Parameter,
        activity,
        async (context) =>
        {
            // Do something with context here. For example, the body of your Post() method may go here.
        });
```

## <a name="fix-assembly-conflicts"></a>Corrigir conflitos entre assemblies

Os bots criados com o Pacote NuGet clássico terão conflitos com seus assemblies. Isso será exibido na janela Lista de Erros do Visual Studio após um build.

### <a name="if-you-see-warning-found-conflicts-between-different-versions-of-the-same-dependent-assembly"></a>Se for exibido "Aviso: foram encontrados conflitos entre diferentes versões do mesmo assembly dependente"

Se você encontrar um aviso que começa com o seguinte texto: **Foram encontrados conflitos entre diferentes versões do mesmo assembly dependente**:

- Clique duas vezes na mensagem de aviso. Uma caixa de diálogo será exibida com a pergunta: "Deseja corrigir esses conflitos adicionando registros de redirecionamento de associação ao arquivo de configuração de aplicativo?"
- Clique em "Sim".
- Compile o projeto novamente.

### <a name="if-you-see-error-missing-method-exception-on-startup"></a>Se for exibido "Erro: exceção de método ausente na inicialização"

Há um bug no .NET Standard que parece ocorrer quando você executa um projeto .NET 4.6 mais antigo e atualiza para 4.6.1 e, em seguida, tenta usar uma biblioteca .NET Standard com ele. Basicamente, existem dois assemblies System.Net.Http diferentes que tentam realizar a troca dinâmica. A solução alternativa é adicionar um redirecionamento de associação ao web.config de System.Net.Http. 

Se você receber esse erro, adicione o seguinte ao arquivo web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Net.Http" publicKeyToken="B03F5F7F11D50A3A" culture="neutral" />
    <bindingRedirect oldVersion="0.0.0.0-4.2.0.0" newVersion="4.2.0.0" />
</dependentAssembly>
```

Para obter mais detalhes sobre esse problema, confira [O System.Net.Http v4.2.0.0 está sendo copiado/carregado das ferramentas do MSBuild nº 25773](https://github.com/dotnet/corefx/issues/25773).

## <a name="sample-of-a-converted-bot"></a>Amostra de um bot convertido

Para um bot que já foi convertido, confira a amostra [EchoBot-Classic](https://github.com/Microsoft/botbuilder-dotnet/tree/master/samples/Microsoft.Bot.Samples.EchoBot-Classic), que apresenta um bot 3.x convertido para trabalhar com 4.0.

## <a name="limitations"></a>Limitações
O Microsoft.Bot.Builder.Classic é apenas uma biblioteca do .NET 4.61; ele não funciona no .NET Core.
