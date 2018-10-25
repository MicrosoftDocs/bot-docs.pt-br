---
title: Baixar e reimplantar o código-fonte do bot | Microsoft Docs
description: Saiba como baixar e publicar um Serviço de Bot.
keywords: baixar código-fonte, reimplantar, implantar, arquivo zip, publicar
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 09/26/2018
ms.openlocfilehash: afb1c4a0e766df7ac2d122b3c7ca4e7959871dbb
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997273"
---
# <a name="download-and-redeploy-bot-code"></a>Baixar e reimplantar o código do bot
O Serviço de Bot do Azure permite que você baixe todo o projeto de origem para o seu bot, assim você pode trabalhar localmente usando seu IDE preferido. Após a atualização do código, publique suas alterações no portal do Azure. Mostraremos como fazer o download do código usando o portal do Azure e a cli `az`. Também falaremos sobre a reimplantação do código do bot atualizado usando o Visual Studio e a ferramenta de cli `az`. Escolha o modelo que funcione melhor para você.

## <a name="prerequisites"></a>Pré-requisitos
- Instalar a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)
- Instalar a extensão az botservice usando o comando `az extension add -n botservice`

### <a name="download-code-using-the-azure-portal"></a>Baixar o código usando o portal do Azure
Para baixar o código do [portal do Azure](https://portal.azure.com), faça o seguinte:
1. Abra a folha do bot.
1. Na seção **Gerenciamento do bot**, clique em **Compilar**.
1. Em **Baixar o código-fonte**, clique em **Baixar arquivo zip**.
1. Aguarde até que o Azure prepare seu URI de download e, em seguida, clique em **Baixar arquivo zip** na notificação.
1. Salve e extraia o arquivo .zip em um diretório local.

Se você tiver um bot C#, atualize o arquivo `appsettings.json` para incluir informações sobre o arquivo .bot, conforme mostrado abaixo:

```
{
  "botFilePath": "yourbasicBot.bot",
  "botFileSecret": "ukxxxxxxxxxxxs="
}
```
O `botFilePath` faz referência ao nome de seu bot, basta substituir "yourbasicBot.bot" pelo nome do seu bot. Para obter a chave `botFileSecret`, consulte o artigo [Criptografia do arquivo do bot](https://aka.ms/bot-file-encryption) sobre como gerar uma chave para o seu bot.


Se você tiver um bot de node.js, adicione um arquivo `.env` com as seguintes entradas:
```
botFilePath=yourbasicBot.bot
botFileSecret=ukxxxxxxxxxxxxs=
```

Em seguida, faça alterações às suas fontes editando arquivos de origem existentes ou adicionando novos ao seu projeto. Teste seu código usando o Emulador. Quando você estiver pronto para reimplantar o código modificado no portal do Azure, siga as instruções abaixo.

### <a name="publish-code-using-visual-studio"></a>Publicar seu código usando o Visual Studio
1. No Visual Studio, clique com o botão direito no nome do seu projeto e clique em **Publicar...**. A janela **Publicar** será aberta.

![Publicação no Azure](~/media/azure-bot-build/azure-csharp-publish.png)

2. Selecione o perfil do seu projeto.
3. Copie a senha listada no arquivo _publish.cmd_ em seu projeto.
4. Clique em **Publicar**.
5. Após solicitação, insira a senha que você copiou na etapa 3.   

Após a configuração do seu projeto, as alterações do projeto serão publicadas no Azure. 

Em seguida, vamos ver como fazer download e reimplantar o código usando a cli `az`.

### <a name="download-code-using-azure-cli"></a>Baixar o código usando a CLI do Azure

Primeiro, faça logon portal do Azure usando a ferramenta az cli.

```azcli
az login
```

Você será solicitado com um código de autenticação temporário exclusivo. Para entrar, use um navegador da web e visite o [logon do dispositivo](https://microsoft.com/devicelogin) da Microsoft, e cole o código fornecido pela CLI para continuar.

Para baixar o código usando a cli `az`, use o seguinte comando:
```azcli
az bot download --name "my-bot-name" --resource-group "my-resource-group"`
```
Após o download do código, faça o seguinte:
- Para o bot em C#, atualize o arquivo appsettings.json para incluir informações sobre o arquivo .bot, conforme mostrado abaixo:

```
{
  "botFilePath": "yourbasicBot.bot",
  "botFileSecret": "ukxxxxxxxxxxxs="
}
```

- Para o bot node.js, adicione um arquivo .env com as seguintes entradas:

```
botFilePath=yourbasicBot.bot
botFileSecret=ukxxxxxxxxxxxxs=
```

Em seguida, faça alterações às suas fontes editando arquivos de origem existentes ou adicionando novos ao seu projeto. Teste seu código usando o Emulador. Quando você estiver pronto para reimplantar o código modificado no portal do Azure, siga as instruções abaixo.

### <a name="login-to-azure-cli-by-running-the-following-command"></a>Faça logon na CLI do Azure executando os seguintes comandos.
Você pode ignorar esta etapa se já estiver conectado.

```azcli
az login
```
Você será solicitado com um código de autenticação temporário exclusivo. Para entrar, use um navegador da web e visite o [logon do dispositivo](https://microsoft.com/devicelogin) da Microsoft, e cole o código fornecido pela CLI para continuar.

### <a name="publish-code-using-azure-cli"></a>Publicar o código usando a CLI do Azure
Para publicar o código de volta no Azure usando a cli `az`, use o seguinte comando:
```azcli
az bot publish --name "my-bot-name" --resource-group "my-resource-group" --code-dir <path to directory> 
```

Você pode usar a opção `code-dir` para indicar qual diretório usar. Se isso não for fornecido, o comando `az bot publish` usará o diretório local para publicar.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como fazer upload das alterações de volta no Azure, configure a implantação contínua para o bot.

> [!div class="nextstepaction"]
> [Configurar a implantação contínua](bot-service-build-continuous-deployment.md)
