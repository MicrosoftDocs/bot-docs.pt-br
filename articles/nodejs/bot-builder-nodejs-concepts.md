---
title: Principais conceitos no SDK do Bot Framework para Node.js | Microsoft Docs
description: Entenda os principais conceitos e ferramentas para criar e implementar bots de conversação disponíveis no SDK do Bot Framework para Node.js.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a3cff9a77de098ee524334183ba891068f176b6e
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464782"
---
# <a name="key-concepts-in-the-bot-framework-sdk-for-nodejs"></a>Principais conceitos no SDK do Bot Framework para Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-concepts.md)
> - [Node.js](../nodejs/bot-builder-nodejs-concepts.md)

Este artigo apresenta os principais conceitos no SDK do Bot Framework para Node.js. Para obter uma introdução ao Bot Framework, consulte [visão geral do Bot Framework](../overview-introduction-bot-framework.md).

## <a name="connector"></a>Conector
O Bot Framework Connector é um serviço que conecta seu bot a vários *canais*, que são clientes como [Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create), Skype, Facebook, Slack e SMS. 

O Conector facilita a comunicação entre o bot e o usuário, transmitindo mensagens do bot para o canal e do canal para o bot. A lógica do seu bot é hospedada como um serviço web que recebe mensagens dos usuários através do serviço Connector, e as respostas do seu bot são enviadas para o Connector usando HTTPS POST. 

O SDK do Bot Framework para Node.js fornece as classes [UniversalBot][UniversalBot] and [ChatConnector][ChatConnector] para configurar o bot para enviar e receber mensagens através do Conector do Bot Framework. O `UniversalBot` classe formulários o cérebro do seu bot. É responsável por gerenciar todas as conversas que seu bot tem com um usuário. A classe `ChatConnector` conecta seu bot ao Serviço de Conector Bot Framework.
Veja um exemplo que demonstra o uso dessas classes em [Criar um bot com o SDK do Bot Framework para o Node.js](bot-builder-nodejs-quickstart.md).

O Connector também normaliza as mensagens que o bot envia aos canais para que você possa desenvolver seu bot de maneira independente de plataforma. Normalizar uma mensagem envolve convertê-la do esquema do Bot Framework no esquema do canal. Nos casos em que o canal não suporta todos os aspectos do esquema da estrutura, o Conector tentará converter a mensagem em um formato suportado pelo canal. Por exemplo, se o bot enviar uma mensagem que contenha um cartão com botões de ação para o canal SMS, o Conector poderá renderizar o cartão como uma imagem e incluir as ações como links no texto da mensagem. O [Channel Inspector][ChannelInspector] é uma ferramenta da Web que mostra como o Conector renderiza mensagens em vários canais.

O `ChatConnector` requer que um endpoint da API seja configurado em seu bot. Com o SDK do Node.js, isso geralmente é feito instalando o módulo `restify` Node.js. Bots também podem ser criados para o console usando o [ConsoleConnector][ConsoleConnector], que não requer um ponto de extremidade de API.

## <a name="messages"></a>Mensagens

As mensagens podem consistir em texto a ser exibido, texto a ser falado, anexos, rich cards e ações sugeridas. Você usa o método [session.send][SessionSend] para enviar mensagens em resposta a uma mensagem do usuário. Seu bot pode chamar `send` quantas vezes ele gosta em resposta a uma mensagem do usuário. Para obter um exemplo que demonstra tudo isso, consulte [responder a mensagens de usuário][RespondMessages].

Para obter um exemplo que demonstra como enviar uma placa gráfica avançada que contém botões interativos que o usuário clica para iniciar uma ação, consulte [adicionar cartões ricos mensagens](bot-builder-nodejs-send-rich-cards.md). Para obter um exemplo que demonstra como enviar e receber anexos, consulte [enviar anexos](bot-builder-nodejs-send-receive-attachments.md). Para um exemplo que demonstra como enviar uma mensagem que especifica o texto a ser falado pelo seu bot em um canal habilitado para fala, consulte [Adicionar fala às mensagens](bot-builder-nodejs-text-to-speech.md). Para obter um exemplo que demonstra como enviar as ações sugeridas, consulte [enviar ações sugeridas](bot-builder-nodejs-send-suggested-actions.md).

## <a name="dialogs"></a>Diálogos
Caixas de diálogo ajudam você a organizar a lógica de conversação no seu bot e é fundamentais para a [Projetando o fluxo da conversa](../bot-service-design-conversation-flow.md). Para obter uma introdução para as caixas de diálogo, consulte [gerenciar uma conversa com caixas de diálogo](bot-builder-nodejs-dialog-manage-conversation.md).

## <a name="actions"></a>Ações
Você vai querer projetar seu bot para ser capaz de lidar com interrupções, como solicitações de cancelamento ou ajuda a qualquer momento durante o fluxo da conversa. O SDK do Bot Framework para Node.js fornece manipuladores de mensagens globais que disparam ações, como o cancelamento ou a invocação de outras caixas de diálogo. Veja [Manipular ações de usuário](bot-builder-nodejs-dialog-actions.md) para obter exemplos de como usar [manipuladores triggerAction][triggerAction].
<!--[Handling cancel](bot-builder-nodejs-manage-conversation-flow.md#handling-cancel), [Confirming interruptions](bot-builder-nodejs-manage-conversation-flow.md#confirming-interruptions) and-->


## <a name="recognizers"></a>Reconhecedores
Quando os usuários solicitam seu bot algo, como "help" ou "encontrar notícias", seu bot precisa entender o que está solicitando o usuário e, em seguida, execute a ação apropriada. Você pode criar seu bot para reconhecer intenções com base na entrada do usuário e associá-lo a ações. 

Você pode usar o reconhecedor de expressões regulares interno que o SDK do Bot Framework fornece, chamar um serviço externo, como a API do LUIS, ou implementar um reconhecedor personalizado para determinar a intenção do usuário. Veja [Reconhecer a intenção do usuário](bot-builder-nodejs-recognize-intent-messages.md) para exemplos que demonstram como adicionar reconhecedores ao seu bot e usá-los para acionar ações.


## <a name="saving-state"></a>Salvar estado

Uma chave para um bom projeto de bot é rastrear o contexto de uma conversa, para que seu bot se lembre de coisas como a última pergunta que o usuário fez. Bots construídos usando o SDK do Bot Framework são projetados para serem sem estado para que possam ser escalonados facilmente a fim de serem executados em vários nós de computação. O Bot Framework fornece um sistema de armazenamento que armazena os dados do bot, para que o serviço da web do bot possa ser dimensionado. Por causa disso, você geralmente deve evitar salvar o estado usando uma variável global ou fechamento de função. Isso criará problemas quando você quiser expandir seu bot. Em vez disso, use as seguintes propriedades do [objeto][Session] do seu bot para salvar dados relativos a um usuário ou conversa:

* **userData** armazena informações globalmente para o usuário em todas as conversas.
* **conversationData** armazena informações globalmente para uma única conversa. Esses dados são visíveis para todos dentro da conversa, portanto, tenha cuidado ao armazenar dados nessa propriedade. Ele é habilitado por padrão e você pode desabilitá-lo usando a configuração [persistConversationData][PersistConversationData] do bot.
* **privateConversationData** armazena informações globalmente para uma única conversa, mas ele são privados dados específicos do usuário atual. Esses dados abrangem todos os diálogos, por isso é útil para armazenar o estado temporário que você deseja limpar quando a conversa termina.
* **dialogData** persiste informações para uma única instância de diálogo. Isso é essencial para armazenar informações temporárias entre as etapas de uma [cascata](bot-builder-nodejs-dialog-waterfall.md) em um diálogo.

Para exemplos que demonstram como usar essas propriedades para armazenar e recuperar dados, consulte [Gerenciar dados de estado](bot-builder-nodejs-state.md).

## <a name="natural-language-understanding"></a>Compreensão de idioma natural

O Bot Builder permite que você use o LUIS para adicionar entendimento de idioma natural ao seu bot usando a classe [LuisRecognizer][LuisRecognizer]. Você pode adicionar uma instância de um **LuisRecognizer** que faz referência a seu modelo de linguagem de programação publicada e, em seguida, adicione manipuladores para realizar ações em resposta às declarações do usuário. Para ver o LUIS em ação Assista o tutorial de 10 minutos a seguir:

* [Tutorial do Microsoft LUIS][LUISVideo] (vídeo)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Visão geral de caixas de diálogo](bot-builder-nodejs-dialog-overview.md)



[PersistConversationData]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iuniversalbotsettings.html#persistconversationdata
[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html
[ChatConnector]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.chatconnector.html
[ConsoleConnector]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.consoleconnector.html

[ChannelInspector]: ../bot-service-channel-inspector.md

[Session]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html
[SessionSend]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#send

[triggerAction]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction
[waterfall]: bot-builder-nodejs-prompts.md

[RespondMessages]:bot-builder-nodejs-use-default-message-handler.md

[LUISRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer
[LUISVideo]: https://vimeo.com/145499419
