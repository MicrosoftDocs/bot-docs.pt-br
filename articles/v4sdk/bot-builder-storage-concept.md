---
title: Estado e armazenamento | Microsoft Docs
description: Descreve como o gerenciador de estado, estado de conversa e estado de usuário estão no SDK do Bot Builder.
keywords: LUIS, estado de conversa, estado de usuário, armazenamento, gerenciar estado
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 02/15/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e25ecec3aec1cdebe3b9eae4bff0d3c434cb610b
ms.sourcegitcommit: 1abc32353c20acd103e0383121db21b705e5eec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42756633"
---
# <a name="state-and-storage"></a>Estado e armazenamento
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Uma chave para obter bom design de bot é acompanhar o contexto de uma conversa para que o bot reconheça aspectos como as respostas às perguntas anteriores.
Dependendo no que o bot for utilizado, talvez seja necessário até mesmo controlar as informações de estado ou repositório por mais tempo que o tempo de vida da conversa.
Um *estado* do bot é uma informação que o bot reconhece para responder apropriadamente às mensagens recebidas. O SDK do Bot Builder fornece classes para armazenar e recuperar dados de estado como um objeto associado a um usuário ou a uma conversa.

* As **propriedades de conversa** ajudam o bot a acompanhar a conversa atual que o bot está tendo com o usuário. Se o bot precisar concluir uma sequência de etapas ou alternar entre tópicos de conversa, será possível usar as propriedades de conversa para gerenciar etapas em uma sequência ou acompanhar o tópico atual. Como as propriedades de conversa refletem o estado da conversa atual, geralmente são apagadas no fim de uma conversa quando o bot recebe uma atividade _fim da conversa_.
* As **propriedades do usuário** podem ser usadas para muitas finalidades como determinar onde a conversa anterior do usuário foi interrompida ou simplesmente cumprimentando um usuário habitual pelo nome. Se você armazenar as preferências de um usuário, poderá usar essas informações para personalizar a conversa no próximo chat. Por exemplo, é possível alertar o usuário sobre um artigo de notícias relacionado a um tópico de interesse ou alertar um usuário quando um compromisso estiver disponível. Será necessário apagá-las se o bot receber uma atividade _excluir dados de usuário_.

É possível usar [Armazenamento](bot-builder-howto-v4-storage.md) para ler de e gravar para armazenamento persistente. Isso permite que o bot execute ações como atualizar recursos compartilhados, gravar RSVPs ou votos, ou ler dados meteorológicos históricos. Da mesma forma que um aplicativo usa armazenamento para atingir os objetivos, o bot pode fazer isso dentro da conversa com o usuário.

<!-- 
*Conversation state* pertains to the current conversation that the user is having with your bot. When the conversation ends, your bot deletes this data.

You can also store *user state* that persists after a conversation ends. For example, if you store a user's preferences, you can use that information to customize the conversation the next time you chat. For example, you might alert the user to a news article about a topic that interests her, or alert a user when an appointment becomes available. 
-->

<!-- You should generally avoid saving state using a global variable or function closures.
Doing so will create issues when you want to scale out your bot. Instead, use the conversation state and user state middleware that the BotBuilder SDK provides --> 


## <a name="types-of-underlying-storage"></a>Tipos de armazenamento subjacente

O SDK fornece middleware de gerenciador de estado de bot para persistir estado de usuário e conversa. O estado pode ser acessado usando o contexto do bot. Esse gerenciador de estado pode o usar Armazenamento de Tabelas do Azure, armazenamento de arquivos ou armazenamento de memória como o armazenamento de dados subjacente. Você também pode criar seus próprios componentes de armazenamento para o bot.

Os bots construídos usando Armazenamento de Tabelas do Azure podem ser projetados para serem sem estado e escalonáveis entre vários nós de computação.

> [!NOTE] 
> O armazenamento de arquivos e memória não será dimensionado nos nós.

## <a name="writing-directly-to-storage"></a>Gravar diretamente para armazenamento

Também é possível usar o SDK do Bot Builder para ler e gravar dados diretamente no armazenamento, sem usar middleware ou contexto do bot. Isso pode ser apropriado para dados que o bot usa, originados de uma fonte fora do fluxo de conversa do bot.

Por exemplo, digamos que o bot permite que o usuário solicite o boletim meteorológico e o bot recupera o boletim meteorológico para uma data específica, lendo-o a partir de um banco de dados externo. O conteúdo do banco de dados de clima não depende das informações do usuário ou do contexto da conversa, portanto, é possível lê-lo diretamente do armazenamento em vez de usar o gerenciador de estado.  Consulte [Como gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md) para um exemplo.

## <a name="next-steps"></a>Próximas etapas

Em seguida, veremos mais detalhadamente como as atividades são processadas e como respondê-las.

> [!div class="nextstepaction"]
> [Processamento de atividade](bot-builder-concept-activity-processing.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Como salvar estado](bot-builder-howto-v4-state.md)
- [Como gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md)
