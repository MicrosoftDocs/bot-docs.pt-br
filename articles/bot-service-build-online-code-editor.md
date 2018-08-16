---
title: Criar um bot com o editor de código online do Azure | Microsoft Docs
description: Saiba como criar seu bot usando o editor de código online no Serviço de Bot.
keywords: editor de código online, portal do azure, bot de funções
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/08/2018
ms.openlocfilehash: bdb287e26c31a784bf6f53ad1601d586781c5ef3
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296984"
---
# <a name="edit-a-bot-with-online-code-editor"></a>Editar um bot com o editor de código online

Você pode usar o editor de código online para compilar seu bot sem a necessidade de uma IDE. Este tópico mostrará como abrir o código de bot no editor de código online. 

## <a name="edit-bot-source-code-in-online-code-editor"></a>Editar o código-fonte bot no editor de código online

Para editar o código-fonte do bot no editor de código online, faça o seguinte para o tipo específico que você possui.

### <a name="web-app-bot"></a>Bot do aplicativo Web
1. Entre no [portal do Azure](http://portal.azure.com) e abra a folha do bot.
2. Na seção **GERENCIAMENTO DE BOT**, clique em **Compilar**.
3. Clique em **Abrir editor de código online**. Isso abrirá o código do bot em uma nova janela do navegador. 

   ![Abrir editor de código online](~/media/azure-bot-build/open-online-code-editor.png)

   Dependendo do idioma do bot, a estrutura de arquivos no diretório **WWWRoot** será diferente. Por exemplo, se você tiver um bot em C#, o **WWWRoot** pode ter esta aparência:

   ![Estrutura de arquivos em C#](~/media/azure-bot-build/cs-wwwroot-structure.png)

   Se você tiver um bot em Node.js, o **WWWRoot** pode ter esta aparência:

   ![Estrutura de arquivos em Node.js](~/media/azure-bot-build/node-wwwroot-structure.png)

4. Realize alterações de código. Por exemplo, para bots em C#, você pode iniciar com o arquivo **Dialogs/EchoDialog.cs**. Para bots em Node.js, você pode iniciar com o arquivo **App.js**.

   > [!NOTE]
   > Como não é possível fazer alterações de código em arquivos de origem atuais no projeto, não é possível criar novos arquivos de origem usando o editor de código online. Para adicionar novos arquivos de origem para o bot, será necessário [baixar o código-fonte](bot-service-build-download-source-code.md) do projeto, adicionar os arquivos e publicar as alterações de volta no Azure.

5. Salve suas alterações. Para bots em C# bots que estão em um **plano de consumo** e todos os bots em Node.js, o bot é atualizado automaticamente depois que o código-fonte for salvo clicando no botão **Salvar**. 

   ![Estrutura de arquivos em Node.js](~/media/azure-bot-build/node-save-file.png)

   Para bots em C# em um plano do **Serviço de aplicativo**, abra a folha **Console** e envie o comando **build.cmd**. 

   ![Compilar o projeto na folha de console](~/media/azure-bot-build/cs-console-build-cmd.png)
 
   > [!NOTE]
   > Se esse comando não compilar, tente reiniciar o serviço de aplicativo do bot e tente compilar novamente. Para reiniciar o serviço de aplicativo, na folha do bot, clique em **Todas as configurações do serviço de aplicativo** e, em seguida, clique no botão **Reiniciar**.
   > ![Reiniciar um aplicativo Web](~/media/azure-bot-build/open-online-code-editor-restart-appservice.png)

6. Volte ao portal do Azure e clique em **Testar no Webchat** para testar as alterações. Se você já tiver o Webchat aberto para esse bot, clique em **Recomeçar** para ver as novas alterações.

### <a name="functions-bot"></a>Bot de Funções

1. Entre no [portal do Azure](http://portal.azure.com) e abra a folha do bot.
2. Na seção **GERENCIAMENTO DE BOT**, clique em **Compilar**.
3. Clique em **Abrir este bot no Azure Functions**. Isso abrirá o bot com a interface do usuário do <a href="http://go.microsoft.com/fwlink/?linkID=747839" target="_blank">Azure Functions</a>. 
4. Realize alterações de código. Por exemplo, atualize o código de mensagens da função. A captura de tela abaixo mostra o código de mensagens para um Bot de Funções do Node.js.

   ![Editor de código de mensagens do Bot de Funções](~/media/azure-bot-build/functions-messages-code.png)

5. Salve as alterações do código.
6. Volte ao portal do Azure e clique em **Testar no Webchat** para testar as alterações. Se você já tiver o Webchat aberto para esse bot, clique em **Recomeçar** para ver as novas alterações.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como editar o código do bot usando o Editor de códigos Online, também é possível criar o bot localmente usando sua IDE favorita.

> [!div class="nextstepaction"]
> [Baixar o código-fonte do bot](bot-service-build-download-source-code.md)
