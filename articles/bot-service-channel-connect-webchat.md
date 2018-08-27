---
title: Conectar um bot ao canal de Webchat | Microsoft Docs
description: Saiba como usar o controle de Webchat na página da Web para um bot conectado ao canal de Webchat.
keywords: Webchat, canal de bot, página da Web, chave secreta, iframe, HTML
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: b560f9f43fc596bc8062676136819922d227d37b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296680"
---
# <a name="connect-a-bot-to-web-chat"></a>Conectar um bot ao Webchat
Quando você [cria um bot](bot-service-quickstart.md) com o Serviço de Bot, o canal de Webchat é configurado automaticamente para você. O canal de Webchat inclui o controle de Webchat, que fornece a capacidade de os usuários interagirem com o bot diretamente em uma página da Web.

![Amostra de Webchat](~/media/bot-service-channel-webchat/webchat-sample.png)

O canal de Webchat no Portal do Bot Framework contém tudo o que você precisa para inserir o controle de Webchat em uma página da Web. Basta usar o controle de Webchat para inserir a chave secreta do bot e o controle em uma página da Web.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a id="step-1"></a> Obter a chave secreta do bot

1. Abra o bot no [portal do Azure](http://portal.azure.com) e clique na folha **Canais**.

2. Clique em **Editar** no canal de **Webchat**.  
![Canal de Webchat](~/media/bot-service-channel-webchat/bot-service-channel-list.png)

3. Em **Chaves secretas**, clique em **Mostrar** para a primeira chave.  
![chave secreta](~/media/bot-service-channel-webchat/secret-key.png)

4. Copie a **Chave secreta** e o **Código de inserção**.

5. Clique em **Concluído**.

## <a name="embed-the-web-chat-control-in-your-website"></a>Inserir o controle de Webchat no site

Você pode inserir o controle de Webchat em seu site usando uma das duas opções.

### <a name="option-1---keep-your-secret-hidden-exchange-your-secret-for-a-token-and-generate-the-embed"></a>Opção 1 – Manter o segredo oculto, trocar o segredo por um token e gerar a inserção

Use essa opção se puder executar uma solicitação de servidor para servidor para trocar o segredo do Webchat por um token temporário e se desejar dificultar para outros desenvolvedores inserirem o bot em seus sites. Embora o uso dessa opção não impeça por completo que outros desenvolvedores insiram o bot em seus sites, essa opção dificulta que eles façam isso.

Para trocar o segredo por um token e gerar a inserção:

1. Emita uma solicitação **GET** para `https://webchat.botframework.com/api/tokens` e passe o segredo do Webchat por meio do cabeçalho `Authorization`. O cabeçalho `Authorization` usa o esquema `BotConnector` e inclui o segredo, conforme mostrado na solicitação de exemplo abaixo.

2. A resposta à solicitação **GET** conterá o token (entre aspas) que pode ser usado para iniciar uma conversa pela renderização do controle de Webchat dentro de um **iframe**. Um token é válido apenas para uma conversa; para iniciar outra conversa, você precisa gerar um novo token.

3. Dentro do **Código de inserção** do `iframe` copiado do canal de Webchat no Portal do Bot Framework (conforme descrito na [Etapa 1](#step-1) acima), altere o parâmetro `s=` para `t=` e substitua "YOUR_SECRET_HERE" pelo token. 

> [!NOTE]
> Os tokens serão renovados automaticamente antes de expirarem. 

##### <a name="example-request"></a>Solicitação de exemplo

```requestGET https://webchat.botframework.com/api/tokens Authorization: BotConnector YOUR_SECRET_HERE
```

##### Example response 

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
        document.getElementById("chat").src="https://webchat.botframework.com/embed/lucas-direct-line?t="+response
      }
    }

  </script>
</html>
```

### <a id="option-2"></a> Opção 2 – Inserir o controle de Webchat no site usando o segredo

Use essa opção se desejar permitir que outros desenvolvedores insiram o bot em seus sites com facilidade. 

> [!WARNING]
> Se você usar essa opção, outros desenvolvedores poderão inserir o bot em seus sites apenas copiando o código de inserção.

Para inserir o bot no site especificando o segredo na marcação `iframe`:

1. Copie o **Código de inserção** do `iframe` do canal de Webchat no Portal do Bot Framework (conforme descrito na [Etapa 1](#step-1) acima).

2. Dentro desse **Código de inserção**, substitua "YOUR_SECRET_HERE" pelo valor de **Chave secreta** copiado da mesma página.

##### <a name="example-iframe-using-secret"></a>iframe de exemplo (usando o segredo)

```html
<iframe src="https://webchat.botframework.com/embed/YOUR_BOT_ID?s=YOUR_SECRET_HERE"></iframe>
```

## <a name="style-the-web-chat-control"></a>Alterar o estilo do controle de Webchat

Altere o tamanho do controle de Webchat usando o atributo `style` do `iframe` para especificar `height` e `width`.

```html
<iframe style="height:480px; width:402px" src="... SEE ABOVE ..."></iframe>
```

![Cliente de controle de chat](~/media/chatwidget-client.png)

## <a name="additional-resources"></a>Recursos adicionais

[Baixe o código-fonte](https://github.com/Microsoft/BotFramework-WebChat) do controle de Webchat no GitHub.