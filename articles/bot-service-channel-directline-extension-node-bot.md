---
title: Bot do Node.js com a Extensão do Serviço de Aplicativo do Direct Line
titleSuffix: Bot Service
description: Habilite o bot do Node.js para trabalhar com a Extensão do Serviço de Aplicativo do Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: dev
ms.date: 01/15/2020
ms.openlocfilehash: 025b3db49f82643d0942518ff87810714389b54c
ms.sourcegitcommit: df2b8d4e29ebfbb9e8a10091bb580389fe4c34cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256437"
---
# <a name="configure-nodejs-bot-for-extension"></a>Configurar o bot do Node.js para a extensão

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados** e como habilitar a extensão de serviço de aplicativo do Direct Line no recurso de **Serviço de Aplicativo do Azure** em que o bot está hospedado.  

## <a name="prerequisites"></a>Prerequisites

Para executar as etapas descritas a seguir, você precisa ter o recurso do **Serviço de Aplicativo do Azure** e o **Serviço de Aplicativo** relacionado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

Esta seção descreve como habilitar a extensão de serviço de aplicativo do Direct Line usando chaves da configuração do canal de seu bot e o recurso do **Serviço de Aplicativo do Azure** em que o bot está hospedado.

## <a name="update-nodejs-bot-to-use-direct-line-app-service-extension"></a>Atualizar o bot do Node.js para usar a Extensão do Serviço de Aplicativo do Direct Line

1. O Bot Builder v4.7.0 ou superior é necessário para usar um bot do Node.js com a Extensão do Serviço de Aplicativo do Direct Line.
1. Como atualizar um bot existente usando a v4.x do SDK
    1. No diretório raiz do bot, execute `npm install --save botbuilder` para atualizar para os pacotes mais recentes.
1. Permita que seu aplicativo use o **pipe nomeado da Extensão do Serviço de Aplicativo do Direct Line**:
    - Atualize o index.js do bot (abaixo da atribuição do adaptador e do bot) para incluir:
    
    ```Node.js
    
    adapter.useNamedPipe(async (context) => {
        await myBot.run(context);
    });
    ```

1. Salve o arquivo `index.js`.
1. Atualize o arquivo `Web.Config` padrão para adicionar o manipulador `AspNetCore` necessário para a Extensão do Serviço de Aplicativo do Direct Line para solicitações de serviço:
    - Localize o arquivo `Web.Config` no diretório `wwwroot` do bot e substitua o conteúdo padrão por:
    
    ```XML
    
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.webServer>
        <handlers>      
          <add name="aspNetCore" path="*/.bot/*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
          <add name="iisnode" path="*" verb="*" modules="iisnode" />
        </handlers>
       </system.webServer>
    </configuration>
    ```
    
1. Abra o arquivo `appsettings.json` e insira os seguintes valores:
    1. `"MicrosoftAppId": "<secret Id>"`
    1. `"MicrosoftAppPassword": "<secret password>"`

    Os valores são a **appID** e o **appSecret** associados ao grupo de registro do serviço.

1. **Publique** o bot em seu Serviço de Aplicativo do Azure.

### <a name="gather-your-direct-line-app-service-extension-keys"></a>Reúna suas chaves de Extensão do Serviço de Aplicativo do Direct Line

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. No portal do Azure, localize o recurso do **Serviço de Bot do Azure**
1. Clique em **Canais** para configurar os canais do bot
1. Se ele ainda não estiver habilitado, clique no canal **Direct Line** para habilitá-lo. 
1. Se ele já estiver habilitado, na tabela Conectar-se a canais, clique no link **Editar** na linha do Direct Line.
1. Role para baixo até a seção **Chaves de Extensão do Serviço de Aplicativo**. 
1. Clique no link **Mostrar** para revelar uma das chaves e, em seguida, copie seu valor.

![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

### <a name="enable-the-direct-line-app-service-extension"></a>Habilitar a Extensão de Serviço de Aplicativo do Direct Line

1. Em seu navegador, navegue até o [portal do Azure](https://portal.azure.com/)
1. Na portal do Azure, localize a página do recursos **Serviço de Aplicativo do Azure** para o aplicativo Web em que o bot está ou será hospedado
1. Clique em **Configuração**. Na seção *Configurações do aplicativo*, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key_From_Section_1>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

1. Na seção *Configuração*, clique na seção de configurações **Geral** e ative os **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

### <a name="confirm-direct-line-app-extension-and-the-bot-are-initialized"></a>Confirme se a Extensão do Aplicativo do Direct Line e o bot foram inicializados

1. No navegador, navegue até https://<your_app_service>.azurewebsites.net/.bot. Se tudo estiver correto, a página retornará este conteúdo JSON: `{"k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando **tudo funciona corretamente**, onde

    - **k** determina se a ASE (Extensão do Serviço de Aplicativo) do Direct Line pode ler uma chave de extensão do Serviço de Aplicativo com base na configuração. 
    - **initialized** determina se a ASE do Direct Line pode usar a chave de Extensão do Serviço de Aplicativo para baixar os metadados do bot do Serviço de Bot do Azure
    - **ib** determina se a ASE do Direct Line pode estabelecer uma conexão de entrada com o bot.
    - **ob** determina se a ASE do Direct Line pode estabelecer uma conexão de saída com o bot. 
