---
title: Criar um bot do Designer de Conversa | Microsoft Docs
description: Saiba como criar um bot usando o Designer de Conversa.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 1b33d08e56bf8ae473b28cf1cf3a26d8138ce861
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296727"
---
# <a name="create-a-new-conversation-designer-bot"></a>Criar um bot do Designer de Conversa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Surgirão mais detalhes sobre a disponibilidade do Designer de Conversa posteriormente este ano.

Este tutorial apresenta a você instruções passo a passo para a criação de um bot do Designer de Conversa. 

## <a name="prerequisites"></a>Pré-requisitos

- O Designer de Conversa exige uma assinatura do Azure. Você pode começar a usar <a href="https://azure.microsoft.com/en-us/" target="_blank">aqui</a>
- Se você ainda não fez isso, verifique se você entrou no [portal do LUIS](https://luis.ai) pelo menos uma vez depois de te criado uma conta com eles.
- Familiaridade com programação JavaScript. Funções de script personalizadas são escritas em JavaScript.
- Microsoft Edge ou Google Chrome

## <a name="create-a-conversation-designer-bot"></a>Criar um bot do Designer de Conversa

Para criar um bot do Designer de Conversa, siga estas etapas:
1. Acesse https://dev.botframework.com/ e entre. Use o endereço de email fornecido à Microsoft Corporation para participar desta versão de versão prévia privada.
2. Clique em **Criar um bot** no painel de navegação do canto superior direito. 
3. Clique em **Criar** para *Criar um bot com o Designer de Conversa*.
4. Selecione um dos muitos [bots de exemplo](conversation-designer-sample-bots.md) para começar. Clique em **Próximo**. Se você não tiver certeza do **bot de exemplo** que deseja usar, basta escolher aquele que considere o mais próximo de um bot que deseja criar. Mais tarde, você poderá alternar para outro **bot de exemplo**.
5. Preencha todos os campos e clique em **Criar bot** – são necessários cerca de 2 minutos para a conclusão do provisionamento de bot. 

## <a name="bot-provisioning"></a>Provisionamento de bot

Os seguintes recursos do Azure são provisionados automaticamente quando você cria um bot do Designer de Conversa: 

1. Grupo de recursos do Azure com o nome do bot especificado
2. Serviço de Aplicativo do Azure
3. Plano do Serviço de Aplicativo do Azure 
4. Conta de Armazenamento do Azure
5. Application Insights 
6. Assinatura dos Serviços Cognitivos para [LUIS.ai](https://luis.ai). Um aplicativo LUIS é criado com o **Identificador de bot** (além de uma cadeia de caracteres gerada aleatoriamente) como o nome do aplicativo.
7. Aplicativo único da Conta Microsoft. [Saiba mais](https://apps.dev.microsoft.com/#/appList)

## <a name="welcome-message"></a>Mensagem de boas-vindas

Depois que o bot for provisionado, o Designer de Conversa abrirá a página **Criar** do bot. Uma mensagem de boas-vindas será exibida com informações para ajudá-lo a começar. Explore essas opções ou feche a mensagem e comece a trabalhar no bot. Volte para a mensagem de boas-vindas clicando nas reticências (**...**) no painel de navegação do canto superior esquerdo e, em seguida, escolhendo a opção **Boas-vindas...**.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Salvar bot](conversation-designer-save-bot.md)

## <a name="additional-resources"></a>Recursos adicionais
* Saiba mais sobre [tarefas](conversation-designer-tasks.md)
* Saiba mais sobre o [SDK do Construtor de Bot para Node](../nodejs/index.md) 
