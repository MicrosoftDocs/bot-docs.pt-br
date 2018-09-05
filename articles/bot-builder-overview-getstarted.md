---
title: Começar a criar bots com o Construtor de Bot | Microsoft Docs
description: Comece a criar bots avançados com o Construtor de Bot.
author: robstand
ms.author: kamrani
manager: kamrani
ms.topic: get-started-article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 3ee7843e64dfa95427ebcb132740eab3db281ffc
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904009"
---
# <a name="develop-bots-with-bot-builder"></a>Desenvolver bots com o Construtor de Bot



O Construtor de Bot fornece um SDK, bibliotecas, amostras e ferramentas para ajudá-lo a criar e depurar bots. Quando você cria um bot com o Serviço de Bot, o bot tem o suporte do SDK do Construtor de Bot. Use também o SDK do Construtor de Bot para criar um bot do zero usando o C# ou o Node.js. O Construtor de Bot inclui o Bot Framework Emulator para testar os bots e o Inspetor de Canal para visualizar a experiência do usuário do bot em canais diferentes.

Caso deseje criar um bot usando uma linguagem de programação de sua preferência, use a API REST.

## <a name="bot-builder-sdk-for-net"></a>Construtor de bot do SDK para .NET
O SDK do Construtor de Bot para .NET aproveita o C# para fornecer uma maneira conhecida para os desenvolvedores do .NET escreverem bots. É uma estrutura avançada para a criação de bots que podem manipular interações de forma livre e conversas mais guiadas, nas quais o usuário seleciona um entre valores possíveis. 

O [Início Rápido do .NET](~/dotnet/bot-builder-dotnet-quickstart.md) orientará você pela criação de um bot com o SDK do Construtor de Bot para .NET.

O SDK do Construtor de Bot para .NET está disponível como um [pacote NuGet](https://www.nuget.org/packages/Microsoft.Bot.Builder/).

> [!IMPORTANT]
> O SDK do Construtor de Bot para .NET exige o .NET Framework 4.6 ou mais recente. Caso esteja adicionando o SDK a um projeto existente direcionado a uma versão inferior do .NET Framework, você precisará atualizar o projeto para direcioná-lo ao .NET Framework 4.6 primeiro.

Para instalar o SDK em um projeto existente do Visual Studio, conclua as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet...**.
2. Na guia **Procurar**, digite "Microsoft.Bot.Builder" na caixa de pesquisa.
3. Selecione **Microsoft.Bot.Builder** na lista de resultados, clique em **Instalar** e aceite as alterações.

Baixe também o [código-fonte](https://github.com/Microsoft/BotBuilder/tree/master/CSharp) do SDK do Construtor de Bot para .NET no GitHub.

### <a name="visual-studio-project-templates"></a>Modelos de projeto do Visual Studio
Baixe os modelos de projeto do Visual Studio para acelerar o desenvolvimento de bot.

* [Modelo de bot para o Visual Studio][bot-template] para desenvolvimento de bots com o C#
* [Modelo de habilidade da Cortana para o Visual Studio][cortana-template] para o desenvolvimento de habilidades da Cortana com o C#

> [!TIP]
> <a href="/visualstudio/ide/how-to-locate-and-organize-project-and-item-templates" target="_blank">Saiba mais</a> sobre como instalar modelos de projeto do Visual Studio 2017.

## <a name="bot-builder-sdk-for-nodejs"></a>Construtor de bot do SDK para Node.js
O SDK do Construtor de Bot para Node.js fornece uma maneira conhecida para os desenvolvedores do Node.js escreverem bots. Use-o para criar uma ampla variedade de interfaces do usuário de conversa, de prompts simples a conversas de forma livre. O SDK do Construtor de Bot para Node.js usa o Restify, uma estrutura popular de criação de serviços Web, para criar o servidor Web do bot. O SDK também é compatível com o Express. 

O [Início Rápido do Node.js](~/nodejs/bot-builder-nodejs-quickstart.md) orientará você pela criação de um bot com o SDK do Construtor de Bot para Node.js. 

O SDK do Construtor de Bot para Node.js está disponível como um pacote npm. Para instalar o SDK do Construtor de Bot para Node.js e suas dependências, primeiro crie uma pasta para o bot, navegue para ele e execute o seguinte comando do **npm**:

```nodejs
npm init
```

Em seguida, instale o SDK do Construtor de Bot para Node.js e os módulos do <a href="http://restify.com/" target="_blank">Restify</a> executando os seguintes comandos do **npm**:

```nodejs
npm install --save botbuilder
npm install --save restify
```

Baixe também o [código-fonte](https://github.com/Microsoft/BotBuilder/tree/master/Node) do SDK do Construtor de Bot para Node.js no GitHub.

## <a name="rest-api"></a>API REST

Crie um bot com qualquer linguagem de programação usando a API REST do Bot Framework. O [Início Rápido da REST](rest-api/bot-framework-rest-connector-quickstart.md) orientará você pela criação de um bot com a REST.

Há três APIs REST no Bot Framework:

 - A [API REST do Bot Connector][connectorAPI] permite que o bot envie e receba mensagens de canais configurados no [Portal do Bot Framework](https://dev.botframework.com/). 

- A [API REST de Estado do Bot][stateAPI] permite que o bot armazene e recupere o estado associado às conversas realizadas por meio da API REST do Bot Connector.

- A [API REST de Linha Direta][directLineAPI] permite que você conecte seu próprio aplicativo, como um aplicativo cliente, controle de Webchat ou aplicativo móvel, diretamente a um único bot.

## <a name="bot-framework-emulator"></a>Bot Framework Emulator
O Bot Framework Emulator é um aplicativo da área de trabalho que permite testar e depurar bots, local ou remotamente. Usando o emulador, você pode conversar com o bot e inspecionar as mensagens enviadas e recebidas pelo bot. [Saiba mais sobre o Bot Framework Emulator](~/bot-service-debug-emulator.md) e [baixe o emulador](http://emulator.botframework.com) para sua plataforma.

## <a name="bot-framework-channel-inspector"></a>Inspetor de Canal do Bot Framework
Veja rapidamente a aparência dos recursos de bot em canais diferentes com o [Inspetor de Canal](bot-service-channel-inspector.md) do Bot Framework.

[bot-template]: http://aka.ms/bf-bc-vstemplate
[cortana-template]: https://aka.ms/bf-cortanaskill-template


[connectorAPI]: https://docs.botframework.com/en-us/restapi/connector/#navtitle
 
[stateAPI]: https://docs.botframework.com/en-us/restapi/state/#navtitle

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle
