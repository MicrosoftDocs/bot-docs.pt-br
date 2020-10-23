---
title: Personalização do Webchat – Serviço de Bot
description: Saiba como personalizar o Webchat do Bot Framework. Exiba o código que adiciona anexos e modifica a fonte, a cor, o tamanho do contêiner e o avatar do bot.
keywords: bot framework, webchat, chat, exemplos, react, referência
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/07/2019
ms.openlocfilehash: 44adcaa02a42c0e65bfd1ccdf1f1a819ca747299
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92413536"
---
# <a name="web-chat-customization"></a>Personalização de Webchat

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo fornece detalhes sobre como personalizar os exemplos de Webchat para se ajustarem ao seu bot.

## <a name="integrate-web-chat-into-your-website"></a>Integrar o Webchat ao site

Siga as instruções na [página de visão geral](bot-builder-webchat-overview.md) para integrar o controle de Webchat ao site.

## <a name="customizing-styles"></a>Personalizar estilos

A versão mais recente do controle de Webchat fornece opções de personalização avançada: você pode alterar cores, tamanhos, posicionamento de elementos, adicionar elementos personalizados e interagir com a página da Web de hospedagem. Abaixo estão vários exemplos de como personalizar os elementos da interface do usuário do Webchat.

Você pode encontrar a lista completa de todas as configurações que você pode modificar com facilidade no Webchat no [arquivo `defaultStyleOptions.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/defaultStyleOptions.js).

Essas configurações gerarão um _conjunto de estilos_, que é um conjunto de regras de CSS aprimoradas com [glamour](https://github.com/threepointone/glamor). Você pode encontrar a lista completa de estilos CSS gerados no conjunto de estilos no [arquivo `createStyleSet.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/createStyleSet.js).

## <a name="set-the-size-of-the-web-chat-container"></a>Definir o tamanho do contêiner de Webchat

Agora é possível ajustar o tamanho do contêiner de Webchat usando `styleSetOptions`. O exemplo a seguir tem uma cor da tela de fundo de `body``paleturquoise` para mostrar o contêiner de Webchat (seção com a tela de fundo branca).

```js
…
<head>
  <style>
    html, body { height: 100% }
    body {
      margin: 0;
      background-color: paleturquoise;
    }

    #webchat {
      height: 100%;
      width: 100%;
    }
  </style>
</head>
<body>
  <div id="webchat" role="main"></div>
  <script>
    (async function () {
    window.WebChat.renderWebChat({
      directLine: window.WebChat.createDirectLine({ token }),
      styleOptions: {
        rootHeight: '100%',
        rootWidth: '50%'
      }
    }, document.getElementById('webchat'));
    })()
  </script>
…
```

Este é o resultado:

<img alt="Web Chat with root height and root width set" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/rootHeightWidth.png" width="600"/>

## <a name="change-font-or-color"></a>Alterar fonte ou cor

Em vez de usar a cor da tela de fundo padrão e as fontes usadas dentro de bolhas de chat, você pode personalizá-las de acordo com o estilo da página da Web de destino. O snippet de código a seguir permite que você altere a cor da tela de fundo das mensagens do usuário e do bot.

<img alt="Screenshot with custom style options" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-style-options.png" width="396" />

Se precisar fazer alguma mudança simples de estilo, você poderá defini-lo por meio de `styleOptions`. Opções de estilo são conjuntos de estilos predefinidos que você pode modificar diretamente e com base nos quais o Webchat calculará a folha de estilos inteira.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         const styleOptions = {
            bubbleBackground: 'rgba(0, 0, 255, .1)',
            bubbleFromUserBackground: 'rgba(0, 255, 0, .1)'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),

               // Passing 'styleOptions' when rendering Web Chat
               styleOptions
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

## <a name="change-the-css-manually"></a>Alterar o CSS manualmente

Além de cores, você pode modificar as fontes usadas para renderizar mensagens:

<img alt="Screenshot with custom style set" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-style-set.png" width="396" />

Para aplicação de estilo mais aprofundada, você também pode modificar o conjunto de estilos manualmente definindo as regras de CSS diretamente.

> Já que as regras de CSS são rigidamente vinculadas à estrutura da árvore de DOM, há possibilidade de que essas regras precisem ser atualizadas para funcionarem com a versão mais recente do Webchat.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         // "styleSet" is a set of CSS rules which are generated from "styleOptions"
         const styleSet = window.WebChat.createStyleSet({
            bubbleBackground: 'rgba(0, 0, 255, .1)',
            bubbleFromUserBackground: 'rgba(0, 255, 0, .1)'
         });

         // After generated, you can modify the CSS rules
         styleSet.textContent = {
            ...styleSet.textContent,
            fontFamily: "'Comic Sans MS', 'Arial', sans-serif",
            fontWeight: 'bold'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),

               // Passing 'styleSet' when rendering Web Chat
               styleSet
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

## <a name="change-the-avatar-of-the-bot-within-the-dialog-box"></a>Alterar o avatar do bot dentro da caixa de diálogo

A versão mais recente do Webchat dá suporte a avatares, os quais você pode personalizar configurando `botAvatarInitials` e `userAvatarInitials` na propriedade `styleOptions`.

<img alt="Screenshot with avatar initials" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-avatar-initials.png" width="396" />

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         const styleOptions = {
            botAvatarInitials: 'BF',
            userAvatarInitials: 'WC'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),
               styleOptions
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

Na propriedade `styleOptions` do Webchat, adicionamos `botAvatarInitials` e `userAvatarInitials`:

```js
botAvatarInitials: 'BF',
userAvatarInitials: 'WC'
```

`botAvatarInitials` definirá o texto dentro do avatar no lado esquerdo. Se ele for definido como um valor false, o avatar no lado do bot ficará oculto. Em contraste, `userAvatarInitials` definirá o texto do avatar no lado direito.

## <a name="custom-rendering-activity-or-attachment"></a>Renderização personalizada de atividade ou anexo

Com a versão mais recente do Webchat, você também pode renderizar atividades ou anexos que não são imediatamente compatíveis com o Webchat. Renderizações de atividades e anexos são enviadas por meio de um pipeline personalizável modelado conforme o [middleware do Redux](https://redux.js.org/api/applymiddleware). O pipeline é flexível o suficiente para que você possa realizar facilmente as seguintes tarefas:

-  Decorar atividades/anexos existentes
-  Adicionar novas atividades/anexos
-  Substituir as atividades/anexos existentes (ou removê-los)
-  Fazer uma ligação em cascata de middleware

### <a name="show-github-repository-as-an-attachment"></a>Mostrar o repositório GitHub como um anexo

Se quiser exibir um conjunto de cartões de repositório do GitHub, você poderá criar um componente React para o repositório GitHub e adicioná-lo como um middleware ao anexo.

<img alt="Screenshot with custom GitHub repository attachment" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-github-repository-attachment.png" width="396" />

```jsx
import ReactWebChat from 'botframework-webchat';
import ReactDOM from 'react-dom';

// Create a new React component that accept render a GitHub repository attachment
const GitHubRepositoryAttachment = props => (
   <div
      style={{
         fontFamily: "'Calibri', 'Helvetica Neue', Arial, sans-serif",
         margin: 20,
         textAlign: 'center'
      }}
   >
      <svg
         height="64"
         viewBox="0 0 16 16"
         version="1.1"
         width="64"
         aria-hidden="true"
      >
         <path
            fillRule="evenodd"
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
         />
      </svg>
      <p>
         <a
            href={`https://github.com/${encodeURI(props.owner)}/${encodeURI(
               props.repo
            )}`}
            target="_blank"
         >
            {props.owner}/<br />
            {props.repo}
         </a>
      </p>
   </div>
);

// Creating a new middleware pipeline that will render <GitHubRepositoryAttachment> for specific type of attachment
const attachmentMiddleware = () => next => card => {
   switch (card.attachment.contentType) {
      case 'application/vnd.microsoft.botframework.samples.github-repository':
         return (
            <GitHubRepositoryAttachment
               owner={card.attachment.content.owner}
               repo={card.attachment.content.repo}
            />
         );

      default:
         return next(card);
   }
};

ReactDOM.render(
   <ReactWebChat
      // Prepending the new middleware pipeline
      attachmentMiddleware={attachmentMiddleware}
      directLine={window.WebChat.createDirectLine({ token })}
   />,
   document.getElementById('webchat')
);
```

O exemplo completo pode ser encontrado em [exemplo de componentes de cartão de personalização](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/10.a.customization-card-components).

Nesse exemplo, estamos adicionando um novo componente do React chamado `GitHubRepositoryAttachment`:

```jsx
const GitHubRepositoryAttachment = props => (
   <div
      style={{
         fontFamily: "'Calibri', 'Helvetica Neue', Arial, sans-serif",
         margin: 20,
         textAlign: 'center'
      }}
   >
      <svg
         height="64"
         viewBox="0 0 16 16"
         version="1.1"
         width="64"
         aria-hidden="true"
      >
         <path
            fillRule="evenodd"
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
         />
      </svg>
      <p>
         <a
            href={`https://github.com/${encodeURI(props.owner)}/${encodeURI(
               props.repo
            )}`}
            target="_blank"
         >
            {props.owner}/<br />
            {props.repo}
         </a>
      </p>
   </div>
);
```

Em seguida, criamos um middleware que renderizará o novo componente do React quando o bot enviar um anexo de tipo de conteúdo `application/vnd.microsoft.botframework.samples.github-repository`. Caso contrário, ele continuará no middleware, chamando `next(card)`.

```jsx
const attachmentMiddleware = () => next => card => {
   switch (card.attachment.contentType) {
      case 'application/vnd.microsoft.botframework.samples.github-repository':
         return (
            <GitHubRepositoryAttachment
               owner={card.attachment.content.owner}
               repo={card.attachment.content.repo}
            />
         );

      default:
         return next(card);
   }
};
```

A atividade enviada do bot é semelhante à seguinte:

```json
{
   "type": "message",
   "from": {
      "role": "bot"
   },
   "attachmentLayout": "carousel",
   "attachments": [
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-WebChat"
         }
      },
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-Emulator"
         }
      },
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-DirectLineJS"
         }
      }
   ]
}
```
