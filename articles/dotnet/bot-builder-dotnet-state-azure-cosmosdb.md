---
title: Gerenciar dados de estado personalizados com o Azure Cosmos DB | Microsoft Docs
description: Saiba como salvar e recuperar dados de estado usando o Azure Cosmos DB com o SDK do Construtor de Bot para .NET
author: kaiqb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b99dc4cd9011871d52479ade92968ebb29c8c73f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297282"
---
# <a name="manage-custom-state-data-with-azure-cosmos-db-for-net"></a>Gerenciar dados de estado personalizados com o Azure Cosmos DB para .NET
Neste artigo, você implementará o Azure Cosmos DB para armazenar e gerenciar os dados de estado de seu bot. O Serviço de Estado do Conector padrão usado por bots não se destina ao ambiente de produção. Você deve usar as [Extensões do Azure](https://github.com/Microsoft/BotBuilder-Azure) disponíveis no GitHub ou implementar um cliente de estado personalizado usando uma plataforma de armazenamento de dados de sua escolha. Estes são alguns dos motivos para usar o armazenamento de estado personalizado:
 - Taxa de transferência de API com estado maior (mais controle sobre o desempenho)
 - Latência menor para distribuição geográfica
 - Controle sobre o local de armazenamento dos dados
 - Acesso aos dados de estado real
 - Armazene mais de 32 kb de dados
 
## <a name="prerequisites"></a>Pré-requisitos
Você precisará de:
 - [Conta do Microsoft Azure](https://azure.microsoft.com/en-us/free/)
 - [Visual Studio 2015 ou posterior](https://www.visualstudio.com/)
 - [Pacote NuGet do Construtor de Bot do Azure](https://www.nuget.org/packages/Microsoft.Bot.Builder.Azure/)
 - [Pacote NuGet do Autofac Web Api2](https://www.nuget.org/packages/Autofac.WebApi2/)
 - [Bot Framework Emulator](~/bot-service-debug-emulator.md)
 
## <a name="create-azure-account"></a>Criar conta do Azure
Caso você não tenha uma conta do Azure, clique [aqui](https://azure.microsoft.com/en-us/free/) para se inscrever em uma conta de avaliação gratuita.

## <a name="set-up-the-azure-cosmos-db-database"></a>Configurar o banco de dados do Azure Cosmos DB
1. Após se conectar ao Portal do Azure, crie um novo banco de dados *Azure Cosmos DB* clicando em **Novo**. 
2. Clique em **Bancos de Dados**. 
3. Encontre **Azure Cosmos DB** e clique em **Criar**.
4. Preencha os campos. Para o campo **API**, selecione **SQL (DocumentDB)**. Após preencher todos os campos, clique no botão **Criar** na parte inferior da tela para implantar o novo banco de dados. 
5. Após a implantação do novo banco de dados, navegue até seu novo banco de dados. Clique em **Chaves de Acesso** para encontrar chaves e cadeias de conexão. Seu bot usará essas informações para chamar o serviço de armazenamento para salvar os dados de estado.

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
Você substituirá o valor por seu URI e Chave Primária encontrada no Azure Cosmos DB. Salve o arquivo Web.config.

## <a name="modify-your-bot-code"></a>Modifique o código do seu bot
Para usar o armazenamento do **Azure Cosmos DB**, adicione as seguintes linhas de código ao arquivo **Global.asax.cs** de seu bot dentro do método **Application_Start()**.

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

## <a name="run-your-bot-app"></a>Executar o bot do seu aplicativo
Execute seu bot no Visual Studio, e o código que você adicionou criará a tabela **botdata** personalizada no Azure.

## <a name="connect-your-bot-to-the-emulator"></a>Conectar seu bot ao emulador
Neste ponto, seu bot está em execução localmente. Em seguida, inicie o emulador e conecte-se ao seu bot no emulador:
1. Digite http://localhost:port-number/api/messages na barra de endereços, em que port-number corresponde ao número da porta no navegador onde seu aplicativo está sendo executado. Você pode deixar os campos <strong>ID de Aplicativo da Microsoft</strong> e <strong>Senha de Aplicativo da Microsoft</strong> em branco por enquanto. Você receberá essas informações posteriormente quando [registrar seu bot](~/bot-service-quickstart-registration.md).
2. Clique em **Conectar**. 
3. Teste seu bot digitando algumas mensagens no emulador. 

## <a name="view-state-data-on-azure-portal"></a>Ver dados de estado no Portal do Azure
Para exibir os dados de estado, entre em seu Portal do Azure e navegue até seu banco de dados. Clique em **Data Explorer (versão prévia)** para verificar se as informações de estado de seu bot estão sendo salvas. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você usou o Cosmos DB para salvar e gerenciar dados do seu bot. Em seguida, saiba como modelar o fluxo da conversa usando diálogos.

> [!div class="nextstepaction"]
> [Gerenciar o fluxo da conversa](bot-builder-dotnet-manage-conversation-flow.md)

## <a name="additional-resources"></a>Recursos adicionais
Se você não estiver familiarizado com os contêineres de Inversão de Controle e com o padrão de Injeção de Dependência usado no código acima, visite o site da [Autofac](http://autofac.readthedocs.io/en/latest/) para obter informações. 

Você também pode fazer o download de um [exemplo](https://github.com/Microsoft/BotBuilder-Azure/tree/master/CSharp/Samples/DocumentDb) do GitHub para saber mais sobre como usar o Cosmos DB para gerenciar o estado. 
