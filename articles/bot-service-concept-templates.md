---
title: Modelos de serviço de bot | Microsoft Docs
description: Aprenda sobre os diferentes modelos que você pode usar quando cria um bot com o Bot Service.
author: robstand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: f1cfa6de6dbc0b1dc6ed0408f405a91493741a93
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297631"
---
# <a name="bot-service-templates"></a>Modelos de serviço de bot

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

O serviço de bot inclui cinco modelos para ajudá-lo a começar a construir bots. Esses modelos fornecem um bot totalmente funcional pronto para ajudá-lo a começar rapidamente. Quando você [cria um bot](bot-service-quickstart.md), escolhe um modelo e o idioma do SDK para seu bot.

Cada modelo fornece um ponto de partida baseado em um recurso central para um bot. 

## <a name="basic-bot"></a>Bot básico
Para criar um bot que usa caixas de diálogo para responder à entrada do usuário, escolha o modelo básico. O modelo **Básico** cria um bot com o conjunto mínimo de arquivos e código para começar. O bot ecoa de volta para o usuário, seja lá o que for que digitar. Você pode usar esse modelo para começar a criar fluxo de conversas no seu bot.

## <a name="form-bot"></a>Bot de formulário
Para criar um bot que coleta a entrada de um usuário por meio de uma conversa guiada, escolha o modelo **Form**. Um bot de formulário é projetado para coletar um conjunto específico de informações do usuário. Por exemplo, um bot projetado para obter um pedido de sanduíche do usuário precisaria coletar informações como tipo de pão, escolha de coberturas, tamanho do sanduíche e assim por diante.

Os bots criados com o modelo Form na linguagem C# usam [FormFlow](dotnet/bot-builder-dotnet-formflow.md) para gerenciar formulários, e bots criados com o modelo Form na linguagem Node.js usam [waterfalls](nodejs/bot-builder-nodejs-dialog-waterfall.md) para gerenciar formulários.

## <a name="language-understanding-bot"></a>Bot de compreensão de idioma
Para criar um bot que usa modelos de linguagem natural para entender a intenção do usuário, escolha o modelo **vocal**. Este modelo aproveita o <a href="https://www.luis.ai" target="_blank">Compreensão de Linguagem (LUIS)</a> para fornecer uma compreensão do idioma natural.

Se um usuário enviar uma mensagem como "receba notícias sobre empresas de realidade virtual", seu bot poderá usar o LUIS para interpretar o significado da mensagem. Usando o LUIS, você pode implantar rapidamente um endpoint HTTP que interpretará a entrada do usuário em termos da intenção que ele transmite (localizar notícias) e as principais entidades que estão presentes (empresas de realidade virtual). O LUIS permite que você especifique o conjunto de intenções e entidades que são relevantes para o seu aplicativo e, em seguida, orienta você durante o processo de criação de um aplicativo de compreensão de idioma.

Quando você cria um bot usando o modelo de compreensão de idioma, o serviço de bot cria um aplicativo LUIS correspondente que está vazio (isto é, que sempre retorna `None`). Para atualizar seu modelo de aplicativo LUIS para que ele seja capaz de interpretar a entrada do usuário, você deve entrar no <a href="https://www.luis.ai" target="_blank">LUIS</a>, clicar em **Meus aplicativos**, selecionar o aplicativo que o serviço criou para você, e, em seguida, crie intenções, especifique entidades e treine o aplicativo.

## <a name="question-and-answer-bot"></a>Bot de perguntas e respostas
Para criar um bot que destile dados semi-estruturados como pares de perguntas e respostas em respostas distintas e úteis, escolha o modelo **Pergunta e Resposta**. Este modelo aproveita o <a href="https://qnamaker.ai">QnA Maker</a> serviço para analisar as perguntas e fornecer respostas. 

Ao criar um bot com o modelo de pergunta e resposta, você deve fazer login no <a href="https://qnamaker.ai">Criador do QnA</a> e criar um novo serviço QnA. Esse serviço QnA fornecerá a ID da base de conhecimento e a chave de assinatura que você pode usar para atualizar seus [valores de Configurações de aplicativo](bot-service-manage-settings.md) para **QnAKnowldegebasedId** e **QnASubscriptionKey**. Quando esses valores estiverem definidos, seu bot poderá responder a quaisquer perguntas que o serviço QnA tenha em sua base de conhecimento.

## <a name="proactive-bot"></a>Bot proativo
Para criar um bot que possa enviar mensagens proativas ao usuário, escolha o **Modelo proativo**. Normalmente, cada mensagem que um bot envia ao usuário está diretamente relacionada à entrada anterior do usuário. Em alguns casos, porém, um bot pode precisar enviar as informações do usuário que não estão diretamente relacionadas à mensagem mais recente do usuário. Esses tipos de mensagens são chamados **mensagens proativas**. Mensagens proativas podem ser útil em uma variedade de cenários. Por exemplo, se um bot define um timer ou um lembrete, ele precisa notificar o usuário quando chegar a hora. Ou, se um bot recebe uma notificação sobre um evento externo, ele pode precisar se comunicar essas informações ao usuário. 

Quando você cria um bot usando o modelo proativo, vários recursos do Azure são automaticamente criados e adicionados ao seu grupo de recursos. Por padrão, esses recursos do Azure já estão configurados para habilitar um cenário muito simple de mensagens proativo. 

| Recurso | DESCRIÇÃO |
|----|----|
| Armazenamento do Azure | Usado para criar a fila. |
| Aplicativo de Funções do Azure | Uma `queueTrigger` Função do Azure que é acionada sempre que houver uma mensagem na fila. Ele se comunica com o Serviço de Bot usando [Linha Direta](https://docs.microsoft.com/bot-framework/rest-api/bot-framework-rest-direct-line-3-0-concepts). Essa função usa a associação de bot para enviar a mensagem como parte da carga do gatilho. Nossa função de exemplo encaminha a mensagem do usuário como-está na fila.
| Serviço de Bot | Seu bot. Contém a lógica que recebe a mensagem do usuário, adiciona a mensagem à fila do Azure, recebe acionadores da Função do Azure e envia de volta a mensagem recebida por meio da carga útil do acionador. |

O diagrama a seguir mostra como os eventos acionados funcionam quando você cria um bot usando o modelo Proactive.

![Visão geral do exemplo Proactive Bot](~/media/bot-proactive-diagram.png)

O processo começa quando o usuário envia uma mensagem ao seu bot através dos servidores do Bot Framework (etapa 1).

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece os diferentes modelos, saiba mais sobre os Serviços Cognitivos para usar nos bots.

> [!div class="nextstepaction"]
> [Serviços cognitivos para bots](bot-service-concept-intelligence.md)
