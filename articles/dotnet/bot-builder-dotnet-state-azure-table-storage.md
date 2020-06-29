---
title: Gerenciar dados de estado personalizados com o Armazenamento de Tabela do Azure (C# v3) – Serviço de Bot
description: Saiba como salvar e recuperar dados de estado usando o Armazenamento de Tabelas do Azure com o SDK do Bot Framework para .NET
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5fcce457e5365cd54be77812f0b9fd70382a813b
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75797927"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-net"></a>Gerenciar dados de estado personalizados com o Armazenamento de Tabela do Azure para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Neste artigo, você implementará o armazenamento do Azure Table para armazenar e gerenciar os dados de estado do seu bot. O Serviço de Estado do Conector padrão usado pelos bots não é destinado ao ambiente de produção. Você deve usar [Extensões do Azure](https://github.com/Microsoft/BotBuilder-Azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando a plataforma de armazenamento de dados de sua escolha. Aqui estão alguns dos motivos para usar o armazenamento de estado personalizado:
 - Maior rendimento da API de estado (mais controle sobre o desempenho)
 - Baixa latência para geo-distribuição
 - Controle sobre onde os dados são armazenados
 - Acesso aos dados de estado real
 - Store mais de 32kb de dados

## <a name="prerequisites"></a>Pré-requisitos
Você precisará de:
 - [Conta do Microsoft Azure](https://azure.microsoft.com/free/)
 - [Visual Studio 2015 ou posterior](https://www.visualstudio.com/)
 - [Pacote do NuGet do Azure de construtor de bot](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/)
 - [Pacote do NuGet do Autofac Web Api2](https://www.nuget.org/packages/Autofac.WebApi2/)
 - [Emulador do bot Framework](https://emulator.botframework.com/)
 - [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/)
 
## <a name="create-azure-account"></a>Criar conta do Azure
Se você não tiver uma conta do Azure, clique em [aqui](https://azure.microsoft.com/free/) para se inscrever para uma conta gratuita.

## <a name="set-up-the-azure-table-storage-service"></a>Configurar o serviço de armazenamento de tabelas do Azure
1. Depois de efetuar login no portal do Azure, crie um novo serviço de armazenamento do Azure Table clicando em **Novo**. 
2. Pesquise **conta de armazenamento** que implementa a tabela do Azure. 
3. Preencha os campos e clique no botão **Criar** na parte inferior da tela para implantar o novo serviço de armazenamento. Depois que o novo serviço de armazenamento for implantado, ele exibirá recursos e opções disponíveis para você.
4. Selecione a guia **Access keys** à esquerda e copie a string de conexão para uso posterior. Seu bot usará essa cadeia de caracteres de conexão para chamar o serviço de armazenamento para salvar os dados de estado.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet
1. Abra um projeto de bot C# existente ou crie um novo usando o modelo C# Bot no Visual Studio. 
2. Instale os seguintes pacotes NuGet:
   - Microsoft.Bot.Builder.Azure
   - Autofac.WebApi2

## <a name="add-connection-string"></a>Adicionar cadeia de conexão 
Adicione a seguinte entrada no seu arquivo Web.config: 
```XML
  <connectionStrings>
    <add name="StorageConnectionString"
    connectionString="YourConnectionString"/>
  </connectionStrings>
```
Substitua "YourConnectionString" pela cadeia de conexão pelo armazenamento da tabela do Azure salvo anteriormente. Salve o arquivo Web. config.

## <a name="modify-your-bot-code"></a>Modifique o código do bot
No arquivo Global.asax.cs, adicione as seguintes `using` instruções:
```cs
using Autofac;
using System.Configuration;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;
```
No `Application_Start()` método, crie uma instância da `TableBotDataStore` classe. A classe `TableBotDataStore` implementa a interface `IBotDataStore<BotData>`. A interface `IBotDataStore` permite que você substitua a conexão do Serviço de estado do conector padrão.
 ```cs
 var store = new TableBotDataStore(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
 ```
Registre o serviço, conforme mostrado abaixo:
 ```cs
 Conversation.UpdateContainer(
            builder =>
            {
                builder.Register(c => store)
                          .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                          .AsSelf()
                          .SingleInstance();

                builder.Register(c => new CachingBotDataStore(store,
                           CachingBotDataStoreConsistencyPolicy
                           .ETagBasedConsistency))
                           .As<IBotDataStore<BotData>>()
                           .AsSelf()
                           .InstancePerLifetimeScope();

                
            });
 ```
Salve o arquivo Global.asax.cs.

## <a name="run-your-bot-app"></a>Execute seu bot de aplicativo
Execute seu bot no Visual Studio, o código que você adicionou criará a tabela personalizada **botdata** no Azure.

## <a name="connect-your-bot-to-the-emulator"></a>Conectar seu bot no emulador
Neste ponto, seu bot está em execução localmente. Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:
1. Tipo http://localhost:port-number/api/messages na barra de endereços, em que o número da porta corresponde ao número da porta no navegador onde seu aplicativo está sendo executado. Você pode deixar os campos <strong>Microsoft App ID</strong> e <strong>Microsoft App Password</strong> em branco por enquanto. Você receberá essas informações posteriormente, quando você [registrar seu bot](~/bot-service-quickstart-registration.md).
2. Clique em **Conectar**. 
3. Teste seu bot digitando algumas mensagens no emulador. 

## <a name="view-data-in-azure-table-storage"></a>Exibir dados no armazenamento de tabelas do Azure
Para exibir os dados de estado, abra **Storage Explorer** e conecte-se ao Azure usando sua credencial do Portal do Azure ou conecte-se diretamente à tabela usando o nome do armazenamento e a chave de armazenamento e navegue até o nome da tabela.  

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você implementou o armazenamento de tabela do Azure para salvar e gerenciar dados do seu bot. Em seguida, aprenda como modelar o fluxo de conversação usando diálogos.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-dotnet-manage-conversation-flow.md)


## <a name="additional-resources"></a>Recursos adicionais

Se você não estiver familiarizado com os contêineres Inversion of Control e o padrão de Injeção de Dependência usado no código acima, acesse o site [Autofac](http://autofac.readthedocs.io/en/latest/) para obter informações. 

Você também pode baixar uma completa [armazenamento de tabelas do Azure](https://github.com/Microsoft/BotBuilder-Azure/tree/master/CSharp/Samples/AzureTable) exemplo do GitHub.
