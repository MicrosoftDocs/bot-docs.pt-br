---
title: Importar e exportar um bot de Designer de Conversa | Microsoft Docs
description: Saiba como importar e exportar bots de Designer de Conversa.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: ed055cb0f75d148e6a0dd3248366851901d9a675
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297421"
---
# <a name="export-and-import-a-conversation-designer-bot"></a>Exportar e importar um bot de Designer de Conversa

Os bots no Designer de Conversa podem ser exportados para o computador como um arquivo .zip. Isso permite que você faça backup do bot. Posteriormente, será possível restaurar o bot para um estado anterior usando qualquer um dos arquivos .zip exportados. 

## <a name="export-a-conversation-designer-bot"></a>Exportar um bot do Designer de Conversa

A exportação permite que você salve o estado atual do bot do Designer de Conversa no computador local. 

Para exportar um bot do Designer de Conversa, faça o seguinte:
1. No canto superior direito do painel de navegação, clique nas reticências (**...**).
2. Clique em **Exportar**. O servidor reunirá as informações necessárias e retornará com uma opção para salvar o arquivo .zip.
3. Salve o arquivo .zip exportado no computador local.

O bot agora está salvo em um arquivo .zip no computador. Posteriormente, será possível restaurar o bot para esse estado, [importando-o](#import-a-conversation-designer-bot) de volta para o bot.

## <a name="import-a-conversation-designer-bot"></a>Importar um bot de Designer de Conversa

A importação permite restaurar o bot de Designer de Conversa para um estado anterior. A importação substituirá o bot atual pelo bot de importação. Se você não quiser perder o que atualmente há no bot atual, certifique-se de [exportar](#export-a-conversation-designer-bot) o bot atual antes de realizar uma operação de importação.

Para importar um bot de Designer de Conversa, faça o seguinte:
1. No canto superior direito do painel de navegação, clique nas reticências (**...**).
2. Clique em **Importar**. 
3. Se você quiser salvar o trabalho no bot atual, escolha a opção **Fazer backup e importar**. Essa opção salvará o bot atual no computador local e, em seguida, solicitará a localização do arquivo .zip para importar. Caso contrário, escolha **Importar sem fazer backup** .

O bot agora é importado.

> [!NOTE]
> É possível importar bots que são exportados pelo Designer de Conversa.

## <a name="import-a-luis-app-into-a-conversation-designer-bot"></a>Importar um aplicativo de LUIS para um bot de Designer de Conversa

Se você tiver um aplicativo de LUIS e quiser usá-lo no bot de Designer de Conversa, será possível importar o aplicativo de LUIS para o bot de Designer de Conversa. Conceitualmente, esse processo exige que você exporte o aplicativo de LUIS e o bot de Designer de Conversa e, em seguida, troque o conteúdo do arquivo **luis.model** do bot com o arquivo **luis.json**. Em seguida, importe as alterações de volta para o bot de Designer de Conversa. Essencialmente, substituindo as intenções de LUIS no bot com a do aplicativo de LUIS. Devido a isso, é recomendável que você realize essa operação de importação antes de começar a personalizar as intenções de LUIS do bot, caso contrário, todo o trabalho será substituído por essa operação de importação.

> [!NOTE]
> Se você estiver fazendo alterações em um aplicativo de [LUIS](https://luis.ai) associado ao bot (cada bot de Designer de Conversa tem um aplicativo de LUIS correspondente), não será necessário executar essas etapas. Em vez disso, tudo o que você precisa fazer é entrar no bot de Designer de Conversa e clicar em [**Salvar**](conversation-designer-save-bot.md).

Para importar o aplicativo de LUIS para o bot de Designer de Conversa, faça o seguinte:

1. A partir de [LUIS.ai](https://luis.ai), abra o aplicativo de LUIS e clique em **CONFIGURAÇÕES**.
2. Escolha as **Versões** do aplicativo que você quer usar e clique no ícone de ação **{ }**. Essa ação baixará o arquivo **luis.json** para o computador local. 
3. No Designer de Conversa, [crie um novo bot](conversation-designer-create-bot.md#create-a-conversation-designer-bot) ou abra um bot existente.
4. [Exporte](#export-a-conversation-designer-bot) o bot. Isso exportará o bot como um arquivo .zip para o computador.
5. Navegue até o arquivo .zip exportado e extraia-o.
6. Abra o arquivo **luis.model** em um editor de texto e substitua o conteúdo desse arquivo pelo conteúdo do arquivo **luis.json**. Salve o arquivo.
7. Compacte a pasta do bot de Designer de Conversa.
8. [Importe](#import-a-conversation-designer-bot) o bot de volta para o bot de Designer de Conversa.

O bot agora poderá usar as novas intenções de LUIS que você acabou de importar.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Tarefas](conversation-designer-tasks.md)
