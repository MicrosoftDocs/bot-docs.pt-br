---
title: Conectar um bot ao canal de Webchat – Serviço de Bot
description: Saiba como usar o controle de Webchat na página da Web para um bot conectado ao canal de Webchat.
keywords: Webchat, canal de bot, página da Web, chave secreta, iframe, HTML
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/22/2019
ms.openlocfilehash: 168a74969cc427034b826bfb2dfcee652043f647
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124562"
---
# <a name="connect-a-bot-to-web-chat"></a>Conectar um bot ao Webchat

[!INCLUDE [pre-release-label](./includes/pre-release-label.md)]

Quando você [cria um bot](bot-service-quickstart.md) com o serviço de bot de estrutura, o canal de chat da Web é configurado automaticamente para você. O canal de chat da Web inclui o [controle de chat da Web](https://github.com/microsoft/BotFramework-WebChat), que fornece a capacidade para os usuários interagirem com o bot diretamente em uma página da Web.

![Amostra de Webchat](./media/bot-service-channel-webchat/create-a-bot.png)

O canal de chat da Web no portal do bot Framework contém tudo o que você precisa para inserir o controle de chat da Web em uma página da Web. Basta usar o controle de Webchat para inserir a chave secreta do bot e o controle em uma página da Web.

## <a name="web-chat-security-considerations"></a>Considerações de segurança de chat na Web

Quando você usa a autenticação do Serviço de Bot do Azure com o Webchat, há algumas considerações importantes sobre segurança que você precisa ter em mente. Consulte as considerações de [segurança](rest-api/bot-framework-rest-direct-line-3-0-authentication.md#security-considerations).

## <a name="embed-the-web-chat-control-in-a-web-page"></a>Inserir o controle de chat da Web em uma página da Web

A imagem a seguir mostra os componentes envolvidos ao inserir o controle de chat da Web em uma página da Web.

  ![componentes de inserção de bot](./media/bot-service-channel-webchat/webchat-control.png)

> [!NOTE]
> Este artigo pressupõe que você já tenha um bot implantado no Azure. Para obter informações sobre a implantação, consulte [implantar o bot](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp).


### <a name="get-your-bot-secret-key"></a>Obtenha sua chave secreta do bot

1. Abra o bot no [portal do Azure](https://portal.azure.com) e clique na folha **Canais**.

2. Clique em **Editar** no canal de **Webchat**.

    ![Canal de Webchat](./media/bot-service-channel-webchat/bot-service-channel-list.png)

3. Em **Chaves secretas**, clique em **Mostrar** para a primeira chave.

    ![Chave secreta](./media/bot-service-channel-webchat/secret-key.png)

4. Copie a **Chave secreta** e o **Código de inserção**.

5. Clique em **Concluído**.

### <a name="development-embedding-options"></a>Opções de inserção de desenvolvimento

#### <a name="option-1---exchange-your-secret-for-a-token-and-generate-the-embed"></a>Opção 1 – trocar seu segredo para um token e gerar a inserção

Use essa opção se puder executar uma solicitação de servidor para servidor para trocar o segredo do Webchat por um token temporário e se desejar dificultar para outros desenvolvedores inserirem o bot em seus sites.
Embora o uso dessa opção não impeça por completo que outros desenvolvedores insiram o bot em seus sites, essa opção dificulta que eles façam isso.

Para trocar o segredo por um token e gerar a inserção:

1. Emita uma solicitação **GET** para `https://webchat.botframework.com/api/tokens` e passe o segredo do Webchat por meio do cabeçalho `Authorization`. O cabeçalho `Authorization` usa o esquema `BotConnector` e inclui o segredo, conforme mostrado na solicitação de exemplo abaixo.

2. A resposta à solicitação **GET** conterá o token (entre aspas) que pode ser usado para iniciar uma conversa pela renderização do controle de Webchat dentro de um **iframe**. Um token é válido apenas para uma conversa; para iniciar outra conversa, você precisa gerar um novo token.

3. Dentro do **Código de inserção** do `iframe` copiado do canal de Webchat no Portal do Bot Framework (conforme descrito em [Obtenha sua chave secreta do bot](#get-your-bot-secret-key) acima), altere o parâmetro `s=` para `t=` e substitua "YOUR_SECRET_HERE" pelo token.

> [!NOTE]
> Os tokens serão renovados automaticamente antes de expirarem.

##### <a name="example-request"></a>Solicitação de exemplo

```
requestGET https://webchat.botframework.com/api/tokens
Authorization: BotConnector YOUR_SECRET_HERE
```

##### <a name="example-response"></a>Exemplo de resposta

```response
"IIbSpLnn8sA.dBB.MQBhAFMAZwBXAHoANgBQAGcAZABKAEcAMwB2ADQASABjAFMAegBuAHYANwA.bbguxyOv0gE.cccJjH-TFDs.ruXQyivVZIcgvosGaFs_4jRj1AyPnDt1wk1HMBb5Fuw"
```

##### <a name="example-iframe-using-token"></a>iframe de exemplo (usando o token)

```html
<iframe src="https://webchat.botframework.com/embed/YOUR_BOT_ID?t=YOUR_TOKEN_HERE"></iframe>
```

##### <a name="example-html-code"></a>Código HTML de exemplo
```html
<!DOCTYPE html>
<html>
<body>
  <iframe id="chat" style="width: 400px; height: 400px;" src=''></iframe>
</body>
<script>

    var xhr = new XMLHttpRequest();
    xhr.open('GET', "https://webchat.botframework.com/api/tokens", true);
    xhr.setRequestHeader('Authorization', 'BotConnector ' + 'YOUR SECRET HERE');
    xhr.send();
    xhr.onreadystatechange = processRequest;

    function processRequest(e) {
      if (xhr.readyState == 4  && xhr.status == 200) {
        var response = JSON.parse(xhr.responseText);
        document.getElementById("chat").src="https://webchat.botframework.com/embed/<botname>?t="+response
      }
    }

  </script>
</html>

```

#### <a name="option-2---embed-the-web-chat-control-in-your-website-using-the-secret"></a><a id="option-2"></a> Opção 2 – Inserir o controle de Webchat no site usando o segredo

Use essa opção se desejar permitir que outros desenvolvedores insiram o bot em seus sites com facilidade.

> [!WARNING]
> Com essa opção, a chave secreta do canal de chat da Web é exposta na página da Web do cliente. Use essa opção somente para fins de desenvolvimento e não em um ambiente de produção.

Para inserir o bot em uma página da Web especificando o segredo dentro da `iframe` marca, execute as etapas descritas abaixo.

1. Copie o **Código de inserção** `iframe` do canal de Webchat no Portal do Bot Framework (conforme descrito em [Obtenha sua chave secreta do bot](#get-your-bot-secret-key) acima).

2. Dentro desse **Código de inserção**, substitua "YOUR_SECRET_HERE" pelo valor de **Chave secreta** copiado da mesma página.

##### <a name="example-iframe-using-secret"></a>iframe de exemplo (usando o segredo)

```html
<iframe style="height:480px; width:402px" src="https://webchat.botframework.com/embed/YOUR_BOT_ID?s=YOUR_SECRET_HERE"></iframe>
```

  ![Cliente de chat da Web](./media/bot-service-channel-webchat/web-chat-client.png)

<!-- ![Chat control Client](./media/chatwidget-client.png) -->


### <a name="production-embedding--option"></a>Opção de inserção de produção

#### <a name="keep-your-secret-hidden-exchange-your-secret-for-a-token-and-generate-the-embed"></a>Mantenha seu segredo oculto, troque seu segredo para um token e gere a inserção

Essa opção não expõe a chave secreta do canal de chat da Web na página da Web do cliente, pois ela é necessária em um ambiente de produção.

O cliente deve fornecer um token para se comunicar com o bot. Para saber mais sobre as diferenças entre os segredos e os tokens e para entender os riscos associados ao uso de segredos, visite [autenticação de linha direta](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0).

A página da Web do cliente a seguir mostra como usar um token com o Web Chat.

```html

<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
  </head>
  <body>
    <h2>Web Chat bot client using Direct Line</h2>

    <div id="webchat" role="main"></div>

    <script>

     // "styleSet" is a set of CSS rules which are generated from "styleOptions"
     const styleSet = window.WebChat.createStyleSet({
         bubbleBackground: 'rgba(0, 0, 255, .1)',
         bubbleFromUserBackground: 'rgba(0, 255, 0, .1)',

         botAvatarImage: '<your bot avatar URL>',
         botAvatarInitials: 'BF',
         userAvatarImage: '<your user avatar URL>',
         userAvatarInitials: 'WC',
         rootHeight: '100%',
         rootWidth: '30%'
      });

      // After generated, you can modify the CSS rules
      styleSet.textContent = {
         ...styleSet.textContent,
         fontFamily: "'Comic Sans MS', 'Arial', sans-serif",
         fontWeight: 'bold'
      };

      const res = await fetch('https:<YOUR_TOKEN_SERVER/API>', { method: 'POST' });
      const { token } = await res.json();

      window.WebChat.renderWebChat(
        {
          directLine: window.WebChat.createDirectLine({ token }),
          userID: 'WebChat_UserId',
          locale: 'en-US',
          username: 'Web Chat User',
          locale: 'en-US',
          // Passing 'styleSet' when rendering Web Chat
          styleSet
        },
        document.getElementById('webchat')
      );
    </script>
  </body>
</html>

```

Para obter exemplos de como gerar um token, consulte:

- [Demonstração de logon único](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/07.advanced-web-chat-apps/e.sso-on-behalf-of-authentication)
- [Token de linha direta](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/DirectLineTokenSite)

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral de Webchat](~/v4sdk/bot-builder-webchat-overview.md)
- [Personalização de Webchat](~/v4sdk/bot-builder-webchat-customization.md)
- [Habilitar o controle por voz no Webchat](~/bot-service-channel-connect-webchat-speech.md)
- [Usar o Webchat com a extensão de serviço de aplicativo do Direct Line](~/bot-service-channel-directline-extension-webchat-client.md)
- [Conectar um bot ao Direct Line Speech](~/bot-service-channel-connect-directlinespeech.md)
