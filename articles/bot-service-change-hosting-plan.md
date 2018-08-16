---
title: Migrar um bot de C# de um Plano de Consumo para um Plano do Serviço de Aplicativo | Microsoft Docs
description: Migrar um bot de C# de Serviço de Bot de um Plano de Hospedagem de Consumo para um Plano de Hospedagem de Serviço de Aplicativo.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 1ba574de619e482b6248d33bcb805080c0ea72d0
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296822"
---
# <a name="change-the-hosting-plan-for-your-bot-service"></a>Alterar o plano de hospedagem para seu serviço de bot

Este tópico explica como você pode migrar um bot de script do C# com um Plano de Consumo para um bot de C# com um Plano do Serviço de Aplicativo. 

## <a name="advantages-of-a-bot-on-an-app-service-plan"></a>Vantagens de um bot em um Plano do Serviço de Aplicativo

Os bots em um Plano do Serviço de Aplicativo são executados como aplicativos Web do Azure. Os bots de aplicativo Web fazem coisas que bots de Plano de Consumo não conseguem:

- Um bot de aplicativo Web pode adicionar definições de rota personalizadas.
- Um bot de aplicativo Web pode habilitar um servidor Websocket. 
- Um bot que usa um Plano de Hospedagem de Consumo tem as mesmas limitações que todo o código em execução no Azure Functions. Para saber mais, veja <a target='_blank' href='/azure/azure-functions/functions-scale'>Consumo do Azure Functions e planos de Serviço de Aplicativo</a>.

## <a name="download-your-existing-bot-source"></a>Baixar o código-fonte do bot existente

Siga estas etapas para baixar o código-fonte do seu bot existente:

1. Dentro de seu bot do Azure, clique na guia **Configurações** e expanda a seção **Implantação contínua**.  
2. Clique no botão azul para baixar o arquivo zip que contém o código-fonte para o bot.  
    ![Baixar o arquivo zip do bot](~/media/continuous-deployment-consumption-download.png)
3. Extraia o conteúdo do arquivo zip baixado para uma pasta local. 


## <a name="create-a-bot-template"></a>Criar um modelo de bot

O serviço de bot tem os mesmos modelos para o Plano de Consumo e bots de Plano do Serviço de Aplicativo. Para migrar de um bot de Plano de Consumo, crie um novo bot de Plano do Serviço de Aplicativo no Serviço de Bot, com base no mesmo modelo. O código subjacente pode diferir entre os dois tipos de hospedagem para o mesmo modelo, mas o novo aplicativo Web tem estrutura e recursos de configuração semelhantes que seu bot existente usa.

## <a name="download-the-new-bot-source"></a>Baixar o novo código-fonte do bot

Siga estas etapas para baixar o código-fonte do novo bot:

1. No seu bot do Azure, clique na guia **CONSTRUIR**, encontre a seção **Baixar código-fonte** e, em seguida, clique em **Baixar o arquivo zip**. 
2. Extraia o conteúdo do arquivo zip baixado para a pasta local.

## <a name="add-source-files-to-new-solution"></a>Adicionar arquivos de origem à nova solução

Alguns arquivos .csx podem compilar e ser executados como arquivos .cs na nova solução. Crie um arquivo .cs para cada arquivo .csx em sua solução, exceto `run.csx`. Você irá migrar a lógica do `run.csx` manualmente. Em seus arquivos .cs, você deverá adicionar uma declaração de classe e a declaração de namespace opcional.

## <a name="migrate-runcsx-logic-into-your-project"></a>Migrar a lógica do run.csx para o seu projeto

Os projetos de script do C# têm um método `Run` em que diferentes valores de `ActivityTypes` são manipulados. Importe a sua lógica de manipulação de atividade para o método `MessageController.Post`, no `MessageController.cs`.

## <a name="remove-compiler-keywords"></a>Remover palavras-chave do compilador

Os arquivos de script do C# podem incluir um módulo de referência usando a palavra-chave `#r`. Remova essas linhas e também adicione-as como referências ao projeto do Visual Studio. Além disso, remova palavras-chave `#load`, que inserem outros arquivos de código-fonte em uma compilação de arquivo. Em vez disso, adicione todos os arquivos do `.csx` ao seu projeto como código-fonte do `.cs`.

## <a name="add-references-from-projectjson"></a>Adicionar referências do project.json

Se seu bot de Plano de Consumo adicionar as referências de NuGet em seu arquivo do `project.json`, adicione essas referências a sua nova solução do Visual Studio clicando com o botão direito do mouse no projeto no painel Gerenciador de Soluções e, em seguida, clicando em **Adicionar Referência**.

### <a name="add-references-that-were-implicit"></a>Adicionar referências que estavam implícitas

Um bot de Serviço de Bot em um Plano de Consumo inclui implicitamente essas referências em todos os arquivos de origem .csx. O código-fonte migrado para arquivos de origem .cs podem ter que adicionar referências explícitas para essas classes:

- `TraceWriter` no pacote NuGet `Microsoft.Azure.WebJobs` que fornece os tipos de namespace `Microsoft.Azure.WebJobs.Host`. 
- gatilhos de temporizador no pacote NuGet `Microsoft.Azure.WebJobs.Extensions`
- `Newtonsoft.Json`, `Microsoft.ServiceBus` e outros assemblies referenciados automaticamente
- `System.Threading.Tasks` e outros namespaces importados automaticamente

Para obter outras diretrizes, veja *Como converter em arquivos de classe* em <a target='_blank' href='https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/'>Publicação em uma biblioteca de classes do .NET como um aplicativo de funções</a>.

## <a name="debug-your-new-bot"></a>Depurar seu novo bot

Um bot em um Plano do Serviço de Aplicativo é muito mais fácil do que um bot em um Plano de Consumo para depurar localmente. Você pode usar [o emulador](bot-service-debug-emulator.md) para depurar o código migrado localmente.

## <a name="publish-from-visual-studio-or-set-up-continuous-deployment"></a>Publicar no Visual Studio ou configurar implantação contínua

Por fim, publique seu código-fonte migrado para o Serviço de Bot importando seu arquivo `.PublishSettings` e clicando em **Publicar** ou [configurando a implantação contínua](bot-service-debug-bot.md).
