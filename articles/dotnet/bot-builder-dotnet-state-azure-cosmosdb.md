---
title: Gerenciar dados de estado personalizados com o Azure Cosmos DB (C# v3) – Serviço de Bot
description: Saiba como salvar e recuperar dados de estado usando o Azure Cosmos DB com o SDK do Bot Framework para .NET
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 5057b05d598e8ff985a1d0daf5d18fef6c0ffc95
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75797937"
---
# <a name="manage-custom-state-data-with-azure-cosmos-db-for-net"></a>Gerenciar dados de estado personalizados com o Azure Cosmos DB para .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Neste artigo, você implementará o Azure Cosmos DB para armazenar e gerenciar os dados de estado de seu bot. O Serviço de Estado do Conector padrão usado pelos bots não é destinado ao ambiente de produção. Você deve usar [Extensões do Azure](https://github.com/Microsoft/BotBuilder-Azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando a plataforma de armazenamento de dados de sua escolha. Aqui estão alguns dos motivos para usar o armazenamento de estado personalizado:
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
 - [Emulador do bot Framework](~/bot-service-debug-emulator.md)
 
## <a name="create-azure-account"></a>Criar conta do Azure
Se você não tiver uma conta do Azure, clique em [aqui](https://azure.microsoft.com/free/) para se inscrever para uma conta gratuita.

## <a name="set-up-the-azure-cosmos-db-database"></a>Configurar o banco de dados do Azure Cosmos DB
1. Depois de efetuar login no portal do Azure, crie um novo banco de dados do *Azure Cosmos DB* clicando em **Novo**. 
2. Clique em **Bancos de Dados**. 
3. Encontre **do Azure Cosmos DB** e clique em **criar**.
4. Preencha os campos. Para o **API** campo, selecione **SQL (DocumentDB)** . Quando terminar de preencher todos os campos, clique no botão **Criar** na parte inferior da tela para implantar o novo banco de dados. 
5. Depois que o novo banco de dados é implantado, navegue até seu novo banco de dados. Clique em **chaves de acesso** para encontrar chaves e cadeias de caracteres de conexão. Seu bot usará essas informações para chamar o serviço de armazenamento para salvar os dados do estado.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet
1. Abra um projeto de bot em C# existente ou crie um novo usando o modelo de Bot no Visual Studio. 
2. Instale os seguintes pacotes NuGet:
   - Microsoft.Bot.Builder.Azure
   - Autofac.WebApi2

## <a name="add-connection-string"></a>Adicionar cadeia de conexão 
Adicione as seguintes entradas ao arquivo Web.config:
```XML
<add key="DocumentDbUrl" value="Your DocumentDB URI"/>
<add key="DocumentDbKey" value="Your DocumentDB Key"/>
```
Você substituirá o valor por seu URI e Chave Primária encontrada no Azure Cosmos DB. Salve o arquivo Web. config.

## <a name="modify-your-bot-code"></a>Modifique o código do bot
Para usar o armazenamento do **Azure Cosmos DB**, adicione as seguintes linhas de código ao arquivo **Global.asax.cs** de seu bot dentro do método **Application_Start()** .

```cs
using System;
using Autofac;
using System.Web.Http;
using System.Configuration;
using Microsoft.Bot.Connector;
using Microsoft.Bot.Builder.Azure;
using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Dialogs.Internals;

namespace SampleApp
{
    public class WebApiApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            GlobalConfiguration.Configure(WebApiConfig.Register);
            var uri = new Uri(ConfigurationManager.AppSettings["DocumentDbUrl"]);
            var key = ConfigurationManager.AppSettings["DocumentDbKey"];
            var store = new DocumentDbBotDataStore(uri, key);

            Conversation.UpdateContainer(
                        builder =>
                        {
                            builder.Register(c => store)
                                .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                                .AsSelf()
                                .SingleInstance();

                            builder.Register(c => new CachingBotDataStore(store, CachingBotDataStoreConsistencyPolicy.ETagBasedConsistency))
                                .As<IBotDataStore<BotData>>()
                                .AsSelf()
                                .InstancePerLifetimeScope();

                        });

        }
    }
}
```

Salve o arquivo global.asax.cs. Agora você está pronto para testar o bot com o emulador.

## <a name="run-your-bot-app"></a>Execute seu bot de aplicativo
Execute seu bot no Visual Studio, o código que você adicionou criará a tabela personalizada **botdata** no Azure.

## <a name="connect-your-bot-to-the-emulator"></a>Conectar seu bot no emulador
Neste ponto, seu bot está em execução localmente. Em seguida, inicie o emulador e, em seguida, conecte-se ao seu bot no emulador:
1. Tipo http://localhost:port-number/api/messages na barra de endereços, em que o número da porta corresponde ao número da porta no navegador onde seu aplicativo está sendo executado. Você pode deixar os campos <strong>Microsoft App ID</strong> e <strong>Microsoft App Password</strong> em branco por enquanto. Você receberá essas informações posteriormente, quando você [registrar seu bot](~/bot-service-quickstart-registration.md).
2. Clique em **Conectar**. 
3. Teste seu bot digitando algumas mensagens no emulador. 

## <a name="view-state-data-on-azure-portal"></a>Dados de estado de exibição no Portal do Azure
Para exibir os dados de estado, entre no seu portal do Azure e navegue até seu banco de dados. Clique em **Data Explorer (versão prévia)** para verificar se as informações de estado de seu bot estão sendo salvas. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você usou o Cosmos DB para salvar e gerenciar dados do seu bot. Em seguida, aprenda como modelar o fluxo de conversação usando diálogos.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-dotnet-manage-conversation-flow.md)

## <a name="additional-resources"></a>Recursos adicionais
Se você não estiver familiarizado com os contêineres de Inversão de Controle e com o padrão de Injeção de Dependência usado no código acima, visite o site da [Autofac](http://autofac.readthedocs.io/en/latest/) para obter informações. 

Você também pode fazer o download de um [exemplo](https://github.com/Microsoft/BotBuilder-Azure/tree/master/CSharp/Samples/DocumentDb) do GitHub para saber mais sobre como usar o Cosmos DB para gerenciar o estado. 
