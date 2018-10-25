---
title: Publicar um Serviço de Bot do controle de origem ou do Visual Studio | Microsoft Docs
description: Saiba como publicar um bot do Serviço de Bot uma vez do Visual Studio ou continuamente do controle de origem.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 12/13/2017
ms.openlocfilehash: 38b26ed5a50409de64518562faabf532f45c857e
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999143"
---
# <a name="publish-a-bot-to-bot-service"></a>Publicar um bot no Serviço de Bot

Depois de atualizar o código-fonte do bot em C#, é possível publicá-lo em um bot em execução no Serviço de Bot, usando o Visual Studio. Também é possível publicar o código-fonte do bot em C# ou Node.js gravado em qualquer ambiente de desenvolvimento integrado (IDE) automaticamente cada vez que você verificar um arquivo de origem em um serviço de controle de fonte.


## <a name="publish-a-bot-on-app-service-plan-from-the-online-code-editor"></a>Publicar um bot no plano do Serviço de Aplicativo a partir do editor de código online

Caso você não tenha configurado a implantação contínua, é possível modificar os arquivos de origem no editor de código online. Para implantar o código-fonte modificado, siga estas etapas.

4. Clique no ícone Abrir Console.  
    ![Ícone do Console](~/media/azure-bot-service-console-icon.png)
2. Na janela Console, digite **build.cmd** e pressione a tecla enter.


## <a name="publish-c-bot-on-app-service-plan-from-visual-studio"></a>Publicar o bot em C# no plano do Serviço de Aplicativo a partir do Visual Studio 

Para configurar a publicação do Visual Studio usando o arquivo `.PublishSettings`, execute as seguintes etapas:

1. No portal do Azure, clique no Serviço de Bot, clique na guia **BUILD** e, em seguida, clique em **Baixar arquivo zip**.
3. Extraia o conteúdo do arquivo zip baixado para uma pasta local.
4. No Explorer, localize o arquivo de Solução do Visual Studio (.sln) para o bot e clique duas vezes nele.
4. No Visual Studio, clique em **Exibir** e, em seguida, clique em **Gerenciador de Soluções**.
5. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e depois clique em **Publicar...** A janela Publicar será aberta. 
6. N janela Publicar, clique em **Criar novo perfil**, clique em **Importar perfil** e, em seguida, clique em **OK**.
7. Navegue até a pasta do projeto, depois até a pasta **PostDeployScripts**, selecione o arquivo que termina com `.PublishSettings` e, em seguida, clique em **Abrir**.

Você agora configurou a publicação para este projeto. Para publicar o código-fonte local no Serviço de Bot, clique no projeto,depois clique em **Publicar...** e, em seguida, clique no botão **Publicar**. 

## <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua

Por padrão, o Serviço de Bot permite desenvolver o bot diretamente no navegador usando o editor do Azure, sem qualquer necessidade de um editor local ou controle do código-fonte. No entanto, o editor do Azure não permite que você gerencie arquivos dentro do aplicativo (por exemplo, adicionar arquivos, renomear arquivos ou excluir arquivos). Se você quiser ter a capacidade de gerenciar os arquivos dentro do aplicativo, é possível configurar a implantação contínua e usar o ambiente de desenvolvimento integrado (IDE) e o sistema de controle do controle do código-fonte da sua escolha (por exemplo, Visual Studio Team, GitHub ou Bitbucket). Com a implantação contínua configurada, todas as alterações de código que você confirmar no controle do código-fonte serão implantadas automaticamente no Azure. Após configurar a implantação contínua, é possível [depurar o bot localmente](bot-service-debug-bot.md).

> [!NOTE]
> Se você habilitar a implantação contínua para o bot, é necessário verificar as alterações de código no serviço de controle do código-fonte. Caso você queira editar novamente o código no editor do Azure, é necessário [desabilitar a implantação contínua](#disable-continuous-deployment).

É possível habilitar a implantação contínua do aplicativo de bot executando as etapas a seguir.

## <a name="set-up-continuous-deployment-for-a-bot-on-an-app-service-plan"></a>Configurar a implantação contínua de um bot no plano do Serviço de Aplicativo

Esta seção descreve como habilitar a implantação contínua para um bot que você criou usando o Serviço de Bot que tenha um plano de hospedagem do Serviço de Aplicativo.

1. No portal do Azure, encontre o bot do Azure, clique na guia **BUILD** e depois localize a seção **Implantação contínua do controle do código-fonte**.
2. Para o Visual Studio Online ou GitHub, forneça um token de acesso emitido por você nesses sites. Será efetuado o pull do código-fonte do Azure para o repositório do código-fonte.
3. Para outros sistemas de controle do código-fonte, selecione **outros** e siga as etapas exibidas. 
3. Clique em **Habilitar**.  

### <a name="create-an-empty-repository-and-download-bot-source-code"></a>Criar um repositório vazio e baixar o código-fonte do bot

Siga estas etapas se você quiser usar um serviço de controle do código-fonte *que não seja* o Visual Studio Online ou o Github. Visual Studio Online e Github efetuarão o pull do código-fonte para o bot a partir do Azure, de modo que os usuários desses dois serviços possem ignorar estas etapas.

3. Para um bot em um plano do Serviço de Aplicativo, localize a página do bot no Azure, clique na guia **BUILD**, depois localize a seção **Baixar código-fonte** e, em seguida, clique em **Baixar arquivo zip**.
1. Criar um repositório vazio dentro de um dos sistemas de controle do código-fonte com suporte do Azure.

    ![Sistema de controle do código-fonte](~/media/continuous-integration-sourcecontrolsystem.png)

3. Extraia o conteúdo do arquivo zip baixado na pasta local em que você planeja sincronizar a origem da implantação.
4. Clique em **Configurar** e siga as etapas exibidas. 

## <a name="set-up-continuous-deployment-for-a-bot-on-a-consumption-plan"></a>Configurar a implantação contínua de um bot no plano de Consumo 

Escolha a origem da implantação do bot e conecte o repositório. 

1. Dentro do portal do Azure, no bot do Azure, clique na guia **SETTINGS** e, em seguida, clique em **Configurar** para expandir a seção **Implantação contínua**.  
2. Siga as etapas e clique na caixa de seleção para confirmar que você está pronto. 
3. Clique em **Configurar**, selecione a origem da implantação que corresponda ao sistema de controle do código-fonte em que você criou anteriormente o repositório vazio e conclua as etapas para conectar.   


## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua 

Quando você desabilita a implantação contínua, o serviço de controle do código-fonte continua a funcionar, mas as alterações no check-in não são automaticamente publicadas no Azure. Para desabilitar a implantação contínua, faça o seguinte:

1. Se o bot tiver um plano de hospedagem do Serviço de Aplicativo, no portal do Azure, encontre o bot do Azure, clique na guia **BUILD** e depois localize a seção **Implantação contínua do controle do código-fonte**, *ou...* 
2. Se o bot tiver um plano de Consumo, clique na guia **Configurações**, expanda a seção **Implantação contínua** e, em seguida, clique em **Configurar**.
3. No painel **Implantações**, selecione o serviço de controle do código-fonte em que a implantação contínua está habilitada e depois clique em **Desconectar**.  


## <a name="additional-resources"></a>Recursos adicionais

Para saber como depurar o bot localmente depois de configurar a implantação contínua, consulte [Depurar um bot do Serviço de Bot](bot-service-debug-bot.md).

Este artigo destacou os recursos específicos de implantação contínua do Serviço de Bot. Para obter informações sobre a implantação contínua com relação aos Serviços de Aplicativo do Azure, consulte <a href="https://azure.microsoft.com/en-us/documentation/articles/app-service-continuous-deployment/" target="_blank">Implantação Contínua no Serviço de Aplicativo do Azure</a>.
