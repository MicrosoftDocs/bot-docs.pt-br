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
ms.openlocfilehash: 340246868424199f209feaabf9723f9c0f1624b7
ms.sourcegitcommit: 4509747791a57b3098feb2d1705e921a780df351
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91763668"
---
# <a name="configure-nodejs-bot-for-extension"></a>Configurar o bot do Node.js para a extensão

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Este artigo descreve como atualizar um bot para trabalhar com **pipes nomeados**e como habilitar a extensão de serviço de aplicativo de linha direta no recurso de **serviço de Azure app** em que o bot está hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas a seguir, você precisa ter um **bot de aplicativo Web**(seu bot) criado no Azure.

## <a name="enable-direct-line-app-service-extension"></a>Habilitar extensão de serviço de aplicativo de linha direta

Esta seção descreve como habilitar a extensão do serviço de aplicativo de linha direta usando a chave de extensão do serviço de aplicativo da configuração de canal de linha direta do bot.

## <a name="update-nodejs-bot-to-use-direct-line-app-service-extension"></a>Atualizar o bot do Node.js para usar a Extensão do Serviço de Aplicativo do Direct Line

1. O Bot Builder v4.7.0 ou superior é necessário para usar um bot do Node.js com a Extensão do Serviço de Aplicativo do Direct Line.
1. Permita que seu aplicativo use o **pipe nomeado da Extensão do Serviço de Aplicativo do Direct Line**:
    - Atualize o index.js do bot (abaixo da atribuição do adaptador e do bot) para incluir o código a seguir que obtém o nome do serviço de aplicativo do ambiente e instrui o adaptador a se conectar ao pipe nomeado apropriado:
    
    ```Node.js
    
    adapter.useNamedPipe(async (context) => {
        await myBot.run(context);
        },
        process.env.APPSETTING_WEBSITE_SITE_NAME + '.directline'
    );
    ```   

1. Salve o arquivo `index.js`.
1. Atualize o `Web.Config` arquivo para adicionar o `AspNetCore` manipulador e a regra necessários para a extensão de serviço de aplicativo de linha direta para solicitações de serviço:
    - Localize o `Web.Config` arquivo no `wwwroot` diretório do bot e modifique o conteúdo para incluir as seguintes entradas nas `Handlers` `Rules` seções e:
    
    ```XML
    <handlers>      
          <add name="aspNetCore" path="*/.bot/*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    
    <rewrite>
      <rules>
        <!-- Do not interfere with Direct Line App Service Extension requests. (This rule should be as high in the rules section as possible to avoid conflicts.) -->
        <rule name ="DLASE" stopProcessing="true">
          <conditions>
            <add input="{REQUEST_URI}" pattern="^/.bot"/>
          </conditions>
        </rule>
      </rules>
    </rewrite>
    ```

1. **Publique** o bot em seu recurso de bot do aplicativo Web do Azure.

### <a name="enable-bot-direct-line-app-service-extension"></a>Habilitar extensão do serviço de aplicativo de linha direta de bot

1. Na portal do Azure, localize o recurso de **bot do aplicativo Web** .
1. No menu do painel esquerdo, em *Gerenciamento de bot* , clique em **canais** para configurar os canais de serviço de **bot do Azure** do qual o bot aceita mensagens.
1. Se ainda não estiver habilitado, clique no canal de **linha direta** e siga as instruções para habilitar o canal.
1. Na tabela **conectar-se a canais** , clique no link **Editar** na linha da linha direta.
1. Role para baixo até a seção **Chaves de Extensão do Serviço de Aplicativo**.
1. Clique no link **Mostrar** para revelar uma das chaves. Você usará esse valor nas etapas abaixo.

    ![Chaves de extensão do serviço de aplicativo](./media/channels/direct-line-extension-extension-keys.png)

1. No menu do painel esquerdo, na seção *configurações do aplicativo* , clique no item de **configuração** .
1. No painel direito, adicione as seguintes novas configurações:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionKey|<App_Service_Extension_Key>|
    |DIRECTLINE_EXTENSION_VERSION|mais recente|

    Em que *App_Service_Extension_Key* é o valor salvo anteriormente.

1. Se o bot estiver hospedado em um soberanas ou em uma nuvem do Azure restrita (ou seja, você não acessar o Azure por meio do [Portal público](https://portal.azure.com)), também será necessário adicionar a seguinte nova configuração:

    |Nome|Valor|
    |---|---|
    |DirectLineExtensionABSEndpoint|<URL_of_Direct_Line_App_Gateway>|

    Onde *URL_of_Direct_Line_App_Gateway* é específico para a nuvem do Azure em que o bot está hospedado. Para USGov, esse valor é https://directline.botframework.azure.us/v3/extension

1. Ainda dentro da seção de *configuração* , clique na seção configurações **gerais** e ative o **Web Sockets**
1. Clique em **Salvar** para salvar as configurações. Isso reinicia o Serviço de Aplicativo do Azure.

## <a name="confirm-direct-line-app-extension-and-the-bot-are-configured"></a>Confirme se a extensão do aplicativo de linha direta e o bot estão configurados

No navegador, navegue até https://<your_app_service>.azurewebsites.net/.bot.
Se tudo estiver correto, a página retornará este conteúdo JSON: `{"v":"123","k":true,"ib":true,"ob":true,"initialized":true}`. Essas são as informações que você obtém quando **tudo funciona corretamente**, onde

- **v** exibe a versão de build da ASE (Extensão de Serviço de Aplicativo) do Direct Line.
- **k** determina se a ASE do Direct Line pode ler uma chave de Extensão de Serviço de Aplicativo com base na respectiva configuração.
- **initialized** determina se a ASE do Direct Line pode usar a chave de Extensão do Serviço de Aplicativo para baixar os metadados do bot do Serviço de Bot do Azure
- **ib** determina se a ASE do Direct Line pode estabelecer uma conexão de entrada com o bot.
- **ob** determina se a ASE do Direct Line pode estabelecer uma conexão de saída com o bot.

## <a name="troubleshooting"></a>Solução de problemas

- Se os valores *IB* e *OB* exibidos pelo ponto de*extremidade *. bot* forem falsos, isso significa que o bot e a extensão do serviço de aplicativo Direct line não poderão se conectar entre si. 
    1. Verifique se o código para usar pipes nomeados foi adicionado ao bot.
    1. Confirme se o bot é capaz de iniciar e executar de forma alguma. Ferramentas úteis são **testadas no Webchat**, conectando um canal adicional, depuração remota ou registro em log.
    1. Reinicie o **serviço de Azure app** inteiro no qual o bot está hospedado, para garantir uma inicialização limpa de todos os processos.

- Se o valor *inicializado* do **ponto de extremidade. bot** for falso, isso significa que a extensão do serviço de aplicativo de linha direta não pode validar a **chave de extensão do serviço de aplicativo** adicionada às configurações de *aplicativo* do bot acima. 
    1. Confirme se o valor foi inserido corretamente.
    1. Alterne para a **chave de extensão do serviço de aplicativo** alternativa mostrada na página de configuração do canal de **linha direta**do bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar o chat Web com a extensão do serviço de aplicativo de linha direta](./bot-service-channel-directline-extension-webchat-client.md)