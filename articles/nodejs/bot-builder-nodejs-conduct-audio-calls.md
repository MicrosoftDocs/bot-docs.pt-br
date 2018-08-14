---
title: Realizar chamadas de voz | Microsoft Docs
description: Saiba como realizar chamadas de voz com o Skype em um bot usando o Node.js
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: fbfe65526335b7a8797ab229871472d540735e20
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296666"
---
# <a name="support-audio-calls-with-skype"></a>Dar suporte a chamadas de voz com o Skype

O Skype dá suporte a um recurso avançado chamado Bots de Chamada.  Quando ele está habilitado, os usuários podem fazer uma chamada de voz ao bot e interagir com ele usando o IVR (Resposta Interativa de Voz).  O SDK do Construtor de Bot para Node.js inclui um [SDK de Chamada][calling_sdk] especial que os desenvolvedores podem usar para adicionar recursos de chamada ao chatbot.   

O SDK de Chamada é muito semelhante ao [SDK de Chat][chat_sdk]. Eles têm classes semelhantes, constructos comuns e você pode até mesmo usar o SDK de Chat para enviar uma mensagem para o usuário com quem você está em uma chamada.  Os dois SDKs foram projetados para execução lado a lado, mas embora eles sejam semelhantes, existem algumas diferenças significativas e você geralmente deve evitar combinar classes das duas bibliotecas.  

## <a name="create-a-calling-bot"></a>Criar um bot de chamada
O código de exemplo a seguir mostra como o "Olá, Mundo" de um bot de chamada é muito semelhante a de um chatbot normal. 

```javascript
var restify = require('restify');
var calling = require('botbuilder-calling');

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log(`${server.name} listening to ${server.url}`); 
});

// Create calling bot
var connector = new calling.CallConnector({
    callbackUrl: 'https://<your host>/api/calls',
    appId: '<your bots app id>',
    appPassword: '<your bots app password>'
});
var bot = new calling.UniversalCallBot(connector);
server.post('/api/calls', connector.listen());

// Add root dialog
bot.dialog('/', function (session) {
    session.send('Watson... come here!');
});
```

> [!NOTE]
> Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

Atualmente, o emulador não dá suporte a testes de bots de chamada. Para testar o bot, você precisará percorrer a maioria das etapas necessárias para publicar o bot.  Também precisará usar um cliente do Skype para interagir com o bot. 

### <a name="enable-the-skype-channel"></a>Habilitar o canal do Skype
[Registre o bot](../bot-service-quickstart-registration.md) e habilite o canal do Skype. Você precisará fornecer um ponto de extremidade de mensagens quando registrar o bot. É recomendável que você emparelhe o bot de chamada com um chatbot e, portanto, o ponto de extremidade do chatbot é o que você normalmente coloca nesse campo.  Se estiver apenas registrando um bot de chamada, basta colar o ponto de extremidade de chamada no campo.  

Para habilitar o recurso de chamada real, você precisará acessar o canal do Skype referente ao bot e ativar o recurso de chamada. Em seguida, você verá um campo no qual copiar o ponto de extremidade de chamada. Use o link HTTPS do ngrok para a parte de host do ponto de extremidade de chamada.

Durante o registro do bot, você receberá uma ID do aplicativo e uma senha que deverá colar nas configurações do conector no bot Olá, Mundo. Você também precisará copiar o link de chamada completo e colá-lo na configuração de callbackUrl.

### <a name="add-bot-to-contacts"></a>Adicionar o bot aos contatos
Na página de registro do bot no portal do desenvolvedor, você verá um botão **Adicionar ao Skype** ao lado do canal do Skype para o bot. Clique no botão para adicionar o bot à sua lista de contatos do Skype.  Depois de fazer isso, você (e qualquer pessoa para quem você fornecer o link de ingresso) poderá se comunicar com o bot.

### <a name="test-your-bot"></a>Testar o bot
Teste o bot usando um cliente do Skype. Você deverá observar o ícone de chamada acender quando clicar na entrada de contato do bot (talvez você precise pesquisar o bot para vê-lo).  Podem ser necessários alguns minutos até que o ícone de chamada seja aceso, caso você tenha adicionado uma chamada a um bot existente.  

Se você pressionar o botão de chamada, ele deverá discar para o bot e você deverá ouvi-lo falar “Watson… venha aqui!” e, em seguida, desligar.

## <a name="calling-basics"></a>Noções básicas de chamadas
As classes [UniversalCallBot](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.universalcallbot) e [CallConnector](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callconnector) permitem que você crie um bot de chamada quase da mesma maneira que criaria um chatbot. Adicione diálogos ao bot que são essencialmente idênticos aos [diálogos de chat](bot-builder-nodejs-manage-conversation-flow.md). Adicione [cascatas](bot-builder-nodejs-prompts.md) ao bot. Em seguida, adicione um objeto de sessão, a classe [CallSession](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession), que contém os métodos [answer()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#answer), [hangup()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#hangup) e [reject()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#reject) adicionados para gerenciar a chamada atual. Em geral, você não precisa se preocupar com esses métodos de controle de chamada, pois CallSession tem uma lógica para gerenciar automaticamente a chamada para você. A sessão responderá à chamada automaticamente se você executar uma ação, como enviar uma mensagem ou chamar um prompt interno. Ela também desligará/rejeitará a chamada automaticamente se você chamar [endConversation()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#endconversation) ou se ela detectar que você parou de fazer perguntas ao chamador (você não chamou um prompt interno).

Outra diferença entre bots de chamada e chatbots é que, embora os chatbots costumem enviar mensagens, cartões e teclados para um usuário, um bot de chamada cuida das Ações e dos Resultados. Os bots de chamada do Skype devem criar [fluxos de trabalho](http://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iworkflow) que são compostos por uma ou mais [ações](http://docs.botframework.com/en-us/node/builder/calling-reference/interfaces/_botbuilder_d_.iaction).  Isso é outra coisa com a qual você não precisa se preocupar muito na prática, já que o SDK do Construtor de Bot de chamada gerenciará grande parte disso tudo para você. O método [CallSession.send()](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.callsession#send) permite que você passe ações ou cadeias de caracteres que ele transformará em [PlayPromptActions](http://docs.botframework.com/en-us/node/builder/calling-reference/classes/_botbuilder_d_.playpromptaction).  A sessão contém uma lógica de envio em lote automático para combinar várias ações em um único fluxo de trabalho que é enviado para o serviço de chamada, de modo que você possa chamar send() com segurança várias vezes.  Além disso, você deve depender dos [prompts](bot-builder-nodejs-prompts.md) internos do SDK para coletar entradas do usuário conforme ele processa todos os resultados.  

[calling_sdk]: http://docs.botframework.com/en-us/node/builder/calling-reference/modules/_botbuilder_d_
[chat_sdk]: http://docs.botframework.com/en-us/node/builder/chat-reference/modules/_botbuilder_d_