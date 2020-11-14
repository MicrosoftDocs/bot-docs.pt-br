---
title: Como os projetos de bot são um serviço de bot estruturado
description: Saiba mais sobre como os projetos de bot são estruturados no SDK do bot Framework. Saiba mais sobre os aspectos comuns do código de bot.
author: JonathanFingold
ms.author: v-jofin
manager: kamrani
ms.topic: how-to
ms.service: bot-service
ms.date: 09/24/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1467e9e1ec4736af72c8deb1b76f6d0686d21951
ms.sourcegitcommit: 36928e6f81288095af0c66776a5ef320ec309c1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94618151"
---
# <a name="understand-the-structure-of-an-echo-bot"></a>Entender a estrutura de um bot de eco

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os modelos e exemplos do bot Framework são escritos para ASP.NET (C#), restify (JavaScript) e aiohttp (Python).
No entanto, os recursos do serviço Web não fazem parte do SDK do bot Framework, mas parte da estrutura da Web que você escolhe usar.

Todos os aplicativos de bot compartilham alguns recursos comuns.

| Recurso | Descrição |
|:-|:-|
| Provisionamento de recursos | O bot como um aplicativo Web precisa criar um serviço Web, um adaptador de bot e um objeto bot. |
| Ponto de extremidade de mensagens | O aplicativo Web precisa implementar um ponto de extremidade de mensagens no qual receber atividades e encaminhar atividades para o adaptador de bot. |
| Adaptador de bot | O adaptador recebe atividades do ponto de extremidade do sistema de mensagens, encaminha-as para o manipulador de folheio do bot e captura quaisquer erros ou exceções que a lógica do bot não detecta. |
| Objeto bot | O objeto bot manipula o raciocínio ou a lógica do bot para a rodada. |

Você pode criar um bot de eco nos modelos, conforme descrito nos guias de início rápido (para [**C#**](../dotnet/bot-builder-dotnet-sdk-quickstart.md), [**JavaScript**](../javascript/bot-builder-javascript-quickstart.md)ou [**python**](../python/bot-builder-python-quickstart.md)), ou você pode copiar um projeto de bot de eco do repositório [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples) .

Os modelos C# e JavaScript têm suporte interno para conexões de streaming. Este artigo aborda os recursos de streaming. Para obter informações sobre conexões de streaming, consulte como [conectar um bot à Direct line Speech](../bot-service-channel-connect-directlinespeech.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre os [conceitos básicos do bot](bot-builder-basics.md).
- Uma cópia do exemplo de **bot de eco** em [**C#**](https://aka.ms/cs-echobot-sample), [**JavaScript**](https://aka.ms/js-echobot-sample)ou [**Python**](https://aka.ms/py-echobot-sample).

## <a name="bot-templates"></a>Modelos de bot

Um bot é um aplicativo Web, e modelos são fornecidos para cada linguagem.

# <a name="c"></a>[C#](#tab/csharp)

A estrutura de bot inclui os modelos VSIX e dotnet.

Os modelos geram um aplicativo Web [ASP.NET MVC Core](https://dotnet.microsoft.com/apps/aspnet/mvc) . Se você examinar os conceitos básicos do [ASP.NET](/aspnet/core/fundamentals/index?view=aspnetcore-3.1&preserve-view=true), verá um código semelhante em arquivos como **Program.cs** e **Startup.cs**. Esses arquivos são necessários para todos os aplicativos Web e não são específicos do bot.

[!INCLUDE [VSIX templates](../includes/vsix-templates-versions.md)]

O **appsettings.jsno** arquivo especifica as informações de configuração para o bot, como a ID do aplicativo e a senha entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para esse bot de eco, você não precisa fazer nada aqui agora; a ID do aplicativo e a senha podem ser deixadas indefinidas no momento.

O arquivo **EchoBot. csproj** especifica dependências e suas versões associadas para o bot. Isso é configurado pelo modelo e seu sistema. Dependências adicionais podem ser instaladas usando o Gerenciador de pacotes NuGet ou o `dotnet add package` comando.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O gerador Yeoman cria um tipo de aplicativo Web [restify](http://restify.com/). Se você examinar o início rápido do restify nos documentos, verá um aplicativo semelhante ao arquivo gerado **index.js**. Alguns dos arquivos de chave gerados pelo modelo são descritos neste artigo. Em alguns arquivos, o código não será copiado, mas você o verá quando executar o bot; é possível consultar o exemplo [echobot Node.js](https://aka.ms/js-echobot-sample).

O **package.jsno** arquivo especifica as dependências e suas versões associadas para o bot. Isso é configurado pelo modelo e seu sistema. As dependências adicionais podem ser instaladas usando o `npm install` .

O arquivo **.env** especifica as informações de configuração para o bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

Para usar o arquivo de configuração **. env** , o bot requer o `dotenv` pacote de NPM. Isso já está incluído como uma dependência na **package.jsno** arquivo.

# <a name="python"></a>[Python](#tab/python)

Os modelos Python CookieCutter criam um aplicativo Web [aiohttp](https://docs.aiohttp.org/) .

O arquivo de **requirements.txt** especifica dependências e suas versões associadas para o bot.  Tudo isso é configurado pelo modelo e seu sistema. Dependências adicionais podem ser instaladas usando `pip install -r requirements.txt`

O arquivo **config.py** especifica as informações de configuração do bot, como o número da porta, a ID do aplicativo e a senha, entre outras coisas. Se estiver usando algumas tecnologias ou este bot em produção, você precisará adicionar suas chaves específicas ou a URL a essa configuração. No entanto, para este bot Eco, você não precisa fazer nada aqui no momento; a ID do aplicativo e a senha podem ser deixadas indefinidas por enquanto.

---

## <a name="resource-provisioning"></a>Provisionamento de recursos

O bot como um aplicativo Web precisa criar um serviço Web, um adaptador de bot e um objeto bot.

Muitos bots também criariam os objetos de gerenciamento de memória e camada de armazenamento para o bot, mas o bot de eco não requer estado.
Outros bots também criaria objetos externos ao objeto bot ou ao adaptador que precisa consumir.

### <a name="c"></a>[C#](#tab/csharp)

No ASP.NET, você registra objetos e métodos de criação de objeto no arquivo **Startup.cs** .
O `ConfigureServices` método carrega os serviços conectados, bem como suas chaves de **appsettings.js** ou Azure Key Vault (se houver), conecta o estado e assim por diante. Aqui, o adaptador e o bot são definidos para serem disponibilizados por meio de injeção de dependência.
Em seguida, o `Configure` método conclui a configuração do seu aplicativo.

`ConfigureServices` e `Configure` são chamados pelo runtime quando o aplicativo é iniciado.

### <a name="javascript"></a>[JavaScript](#tab/javascript)

No restify, você configura o serviço Web e os objetos necessários no arquivo **index.js** . O serviço, o adaptador e o bot são cobertos separadamente nas seções a seguir.

### <a name="python"></a>[Python](#tab/python)

No aiohttp, você configura o serviço Web e os objetos necessários no arquivo **app.py** . O serviço, o adaptador e o bot são cobertos separadamente nas seções a seguir.

---

## <a name="messaging-endpoint"></a>Ponto de extremidade de mensagens

O modelo implementa um serviço Web com um ponto de extremidade de mensagens. O serviço extrai o cabeçalho de autenticação e a carga de solicitação e os encaminha para o adaptador.

Os SDKs do C# e do JavaScript dão suporte a conexões de streaming. Embora o bot de eco não use nenhum dos recursos de streaming, o adaptador no modelo foi projetado para dar suporte a eles.

Cada solicitação de entrada representa o início de uma nova rodada.

### <a name="c"></a>[C#](#tab/csharp)

**BotController.cs de controladores \\**

[!code-csharp[BotController](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/Controllers/BotController.cs?range=11-34&highlight=22)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[create server](~/../botbuilder-samples/samples/javascript_nodejs/02.echo-bot/index.js?range=20-26,62-68&highlight=10-13)]

### <a name="python"></a>[Python](#tab/python)

**app.py**

[!code-python[create server](~/../botbuilder-samples/samples/python/02.echo-bot/app.py?range=64-88&highlight=10)]

---

## <a name="the-bot-adapter"></a>O adaptador de bot

O adaptador recebe atividades do ponto de extremidade do sistema de mensagens, encaminha-as para o manipulador de folheio do bot e captura quaisquer erros ou exceções que a lógica do bot não detecta.

O adaptador permite que você adicione seu próprio manipulador _de erros on Turn_ .

### <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

O adaptador a ser usado é definido no `ConfigureServices` método.

[!code-csharp[adapter](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/Startup.cs?range=30-31)]

**AdapterWithErrorHandler.cs**

[!code-csharp[adapter](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/AdapterWithErrorHandler.cs?range=11-32)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[create adapter](~/../botbuilder-samples/samples/javascript_nodejs/02.echo-bot/index.js?range=15-16,30-57)]

### <a name="python"></a>[Python](#tab/python)

**app.py**

[!code-python[create adapter](~/../botbuilder-samples/samples/python/02.echo-bot/app.py?range=11-15,22-28,30-56,58-59)]

---

## <a name="the-bot-logic"></a>A lógica de bot

O bot de eco usa um _manipulador de atividade_ e implementa manipuladores para os tipos de atividade que ele reconhecerá e reagir, nesse caso, a atualização de _conversa_ e atividades de _mensagem_ .

- Uma atividade de atualização de conversa inclui informações sobre quem ingressou ou saiu da conversa. Para conversas que não são de grupo, o bot e o usuário ingressam na conversa quando ele é iniciado. Para conversas de grupo, uma atualização de conversa é gerada sempre que alguém ingressa ou sai da conversa, seja ele o bot ou um usuário.
- Uma atividade de mensagem representa uma mensagem que o usuário envia para o bot.

O bot de eco aprecia um usuário quando ele ingressa na conversa e retorna todas as mensagens que enviam para o bot.

### <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

O bot a ser usado é definido no `ConfigureServices` método.

[!code-csharp[adapter](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/Startup.cs?range=33-34)]

**Bots\\EchoBot.cs**

[!code-csharp[adapter](~/../botbuilder-samples/samples/csharp_dotnetcore/02.echo-bot/Bots/EchoBot.cs?range=12-31)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[create bot](~/../botbuilder-samples/samples/javascript_nodejs/02.echo-bot/index.js?range=18-19,60)]

**bot.js**

[!code-javascript[bot logic](~/../botbuilder-samples/samples/javascript_nodejs/02.echo-bot/bot.js?range=4-29)]

### <a name="python"></a>[Python](#tab/python)

**app.py**

[!code-python[create bot](~/../botbuilder-samples/samples/python/02.echo-bot/app.py?range=11-15,60-61)]

**bots/bot. py**

[!code-python[create bot](~/../botbuilder-samples/samples/python/02.echo-bot/bots/echo_bot.py?range=4-19)]

---

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Enviar e receber mensagens de texto](bot-builder-howto-send-messages.md)
- Saiba como [enviar mensagens de boas-vindas aos usuários](bot-builder-send-welcome-message.md)
