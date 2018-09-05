---
title: Baixar e reimplantar o código-fonte do bot | Microsoft Docs
description: Saiba como baixar e publicar um Serviço de Bot.
keywords: baixar código-fonte, reimplantar, implantar, arquivo zip, publicar
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: b77e096d28f51f605db9c49d36e796553f9293ef
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42756546"
---
# <a name="download-and-redeploy-bot-source-code"></a>Baixar e reimplantar o código-fonte do bot

O Serviço de Bot permite que você baixe todo o projeto de código-fonte para o bot. Isso permite que você trabalhe em seu bot localmente usando um IDE de sua escolha. Após concluir as alterações, você pode publicar suas alterações de volta no Azure. 

Este tópico mostrará como baixar o código-fonte do seu bot e publicar as alterações de volta no Azure. 

## <a name="download-bot-source-code"></a>Fazer download do código-fonte do bot

Para desenvolver seu bot localmente, faça o seguinte:

1. No Portal do Azure e abra a folha do bot.
2. Na seção **GERENCIAMENTO DE BOT**, clique em **Compilar**.
3. Clique em **Baixar arquivo zip**. 

   ![Fazer download do código-fonte](~/media/azure-bot-build/download-zip-file.png)

4. Extraia o arquivo .zip para um diretório local.
5. Navegue até a pasta extraída e abra os arquivos de código-fonte em seu IDE favorito.
6. Faça alterações em seus arquivos de código-fonte. Edite os arquivos de código-fonte existentes ou adicione novos arquivos ao seu projeto.

Quando estiver pronto, publique o código-fonte de volta no Azure.

## <a name="publish-node-bot-source-code-to-azure"></a>Publicar o código-fonte de bot em Node no Azure

Para instalar esses pacotes, navegue até o diretório do seu projeto em um prompt de comando e execute os seguintes comandos do NPM.

**Observação:** esses pacotes só precisam ser adicionados uma vez.

```console
npm install --save fs
npm install --save path
npm install --save request
npm install --save zip-folder
```

Agora você está pronto para publicar seu projeto no Microsoft Azure. Para publicar seu projeto no Microsoft Azure, execute o seguinte comando NPM no prompt de comando:

```console
npm run azure-publish
```

> [!NOTE]
> Se você encontrar um erro após esse comando NPM, talvez seja necessário adicionar `"scripts": {"azure-publish": "node publish.js"}` ao seu arquivo `package.json` e executá-lo novamente.

## <a name="publish-c-bot-source-code-to-azure"></a>Publicar o código-fonte de bot em C# no Azure

A publicação de um código em C# no Azure usando o Visual Studio é um processo de duas etapas: primeiro, você precisará definir as configurações de publicação. Depois, publique suas alterações.

Para configurar a publicação no Visual Studio, faça o seguinte:

1. No Visual Studio, clique em **Gerenciador de Soluções**.
2. Clique com o botão direito do mouse no nome do seu projeto e clique em **Publicar...**. A janela **Publicar** será aberta.
3. Clique em **Criar novo perfil**, clique em **Importar perfil** e clique em **OK**.
4. Navegue até a pasta do seu projeto, depois até a pasta **PostDeployScripts** e selecione o arquivo que termina com **.PublishSettings**. Clique em **Abrir**.

Seu projeto está configurado para publicar as alterações no Azure.

Após a configuração do seu projeto, você poderá publicar o código-fonte do seu bot novamente no Azure fazendo o seguinte:

1. No Visual Studio, clique em **Gerenciador de Soluções**.
2. Clique com o botão direito do mouse no nome do seu projeto e clique em **Publicar...**.
3. Clique no botão **Publicar** para publicar suas alterações no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como compilar seu bot localmente, configure a implantação contínua para o bot.

> [!div class="nextstepaction"]
> [Configurar a implantação contínua](bot-service-build-continuous-deployment.md)
