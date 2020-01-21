---
title: Visão geral do Webchat – Serviço de Bot
description: Saiba como configurar o Webchat do Bot Framework.
keywords: bot framework, webchat, chat, exemplos, react, referência
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/07/2019
ms.openlocfilehash: e04ba2abe8d92246149cb6b7bfa185826448d71e
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791148"
---
# <a name="web-chat-overview"></a>Visão geral de Webchat

Este artigo contém detalhes do componente de [Webchat do Bot Framework](https://github.com/microsoft/BotFramework-WebChat). O componente de Webchat do Bot Framework é um cliente baseado na Web altamente personalizável para o SDK do Bot Framework V4. O SDK do Bot Framework v4 permite aos desenvolvedores modelar a conversa e criar aplicativos de bot sofisticados.

Se você estiver buscando migrar do Webchat v3 para v4, vá diretamente para [a seção de migração](#migrating-from-web-chat-v3-to-v4).

## <a name="how-to-use"></a>Como usar

> [!NOTE]
> Para versões anteriores do Webchat (v3), visite o [branch do Webchat v3](https://github.com/Microsoft/BotFramework-WebChat/tree/v3).

Primeiro, crie um Bot usando o [Serviço de Bot do Azure](https://azure.microsoft.com/services/bot-service/).
Depois que o bot for criado, você precisará [obter o segredo do Webchat do bot](../bot-service-channel-connect-webchat.md#get-your-bot-secret-key) no portal do Azure. Em seguida, use o segredo para [gerar um token](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md) e passá-lo para seu Webchat.

Aqui está como você pode adicionar um controle de Webchat ao seu site:

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  token: 'YOUR_DIRECT_LINE_TOKEN'
               }),
               userID: 'YOUR_USER_ID',
               username: 'Web Chat User',
               locale: 'en-US',
               botAvatarInitials: 'WC',
               userAvatarInitials: 'WW'
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

> `userID`, `username`, `locale`, `botAvatarInitials` e `userAvatarInitials` são todos parâmetros opcionais para passar ao método `renderWebChat`. Para saber mais sobre as propriedades de Webchat, examine a seção [Referência da API de Webchat](#web-chat-api-reference) deste artigo.
> ![Captura de tela do Webchat](https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/weatherquery.png.jpg)

### <a name="integrate-with-javascript"></a>Integrar com o JavaScript

O Webchat é projetado para integrar-se ao seu site existente usando JavaScript ou React. A integração com o JavaScript permitirá fazer alterações de estilo e personalizações. Para obter mais informações, consulte o artigo [Integrar o Webchat ao site](https://aka.ms/integrate-webchat-into-site).

Você pode usar o pacote completo e típico do Webchat, que contém a maioria dos recursos normalmente usados.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  token: 'YOUR_DIRECT_LINE_TOKEN'
               }),
               userID: 'YOUR_USER_ID'
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

Consulte o exemplo de funcionamento do [pacote completo do Webchat](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.a.getting-started-full-bundle).

### <a name="integrate-with-react"></a>Integrar ao React

Para personalização completa, você pode usar o [React](https://reactjs.org/) para recompor componentes do Webchat.

Para instalar o build de produção do npm, execute `npm install botframework-webchat`.

```jsx
import { DirectLine } from 'botframework-directlinejs';
import React from 'react';
import ReactWebChat from 'botframework-webchat';

export default class extends React.Component {
  constructor(props) {
    super(props);

    this.directLine = new DirectLine({ token: 'YOUR_DIRECT_LINE_TOKEN' });
  }

  render() {
    return (
      <ReactWebChat directLine={ this.directLine } userID='YOUR_USER_ID' />
      element
    );
  }
}
```

> Você também pode executar `npm install botframework-webchat@master` para instalar um build de desenvolvimento que esteja sincronizado com o branch `master` do GitHub do Webchat.

Veja um exemplo funcional do [Webchat renderizado por meio do React](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.a.host-with-react/).

> [!TIP]
> Se ainda não tiver usado o React e o JSX, você poderá encontrar treinamento na página de [Introdução](https://reactjs.org/docs/getting-started.html) do React.


## <a name="customize-web-chat-ui"></a>Personalizar a interface do usuário do Webchat

O Webchat é projetado para ser personalizável sem criação de fork do código-fonte. A tabela a seguir descreve quais tipos de personalizações você pode obter quando está importando o Webchat de maneiras diferentes. Esta lista não é exaustiva.

|                               | Pacote CDN         | React              |
| ----------------------------- | ------------------ | ------------------ |
| Alterar cores                 | :heavy_check_mark: | :heavy_check_mark: |
| Alterar tamanhos                  | :heavy_check_mark: | :heavy_check_mark: |
| Atualizar/substituir estilos CSS     | :heavy_check_mark: | :heavy_check_mark: |
| Escutar eventos              | :heavy_check_mark: | :heavy_check_mark: |
| Interagir com a página da Web de hospedagem | :heavy_check_mark: | :heavy_check_mark: |
| Atividades de renderização personalizadas      |                    | :heavy_check_mark: |
| Anexos de renderização personalizados     |                    | :heavy_check_mark: |
| Adicionar novos componentes de interface do usuário         |                    | :heavy_check_mark: |
| Recompor toda a interface do usuário        |                    | :heavy_check_mark: |

Veja mais sobre [personalização de Webchat](https://github.com/Microsoft/BotFramework-WebChat/blob/master/SAMPLES.md) para saber mais a esse respeito.

> [!NOTE] 
> Para obter informações sobre CDNs (Redes de Distribuição de Conteúdo), consulte [CDNs (Redes de Distribuição de Conteúdo)](https://aka.ms/CDN-best-practices)

## <a name="migrating-from-web-chat-v3-to-v4"></a>Migração de Webchat v3 para v4

Há três caminhos possíveis para a migração de v3 para v4. Em primeiro lugar, compare seu cenário de início:

### <a name="my-current-website-integrates-web-chat-using-an-iframe-element-obtained-from-azure-bot-services-i-want-to-upgrade-to-v4"></a>Meu site atual integra o Webchat usando um elemento `<iframe>` obtido dos Serviços de Bot do Azure. Desejo fazer uma atualização para o v4.

Desde maio de 2019, estamos distribuindo a v4 para sites que integram o Webchat usando o elemento `<iframe>`. Consulte [a documentação de inserção](https://github.com/Microsoft/BotFramework-WebChat/tree/master/packages/embed) para obter informações sobre a integração do Webchat usando o `<iframe>`.

### <a name="my-website-is-integrated-with-web-chat-v3-and-uses-customization-options-provided-by-web-chat-no-customization-at-all-or-very-little-of-my-own-customization-that-was-not-available-with-web-chat"></a>Meu site é integrado com o Webchat v3 e usa as opções de personalização fornecidas pelo Webchat, nenhuma personalização em absoluto ou um nível muito pequeno de personalização de minha autoria, que não estava disponível com o Webchat.

Siga a implementação do exemplo [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration) para converter sua página da Web de Webchat v3 para v4.

### <a name="my-website-is-integrated-with-a-fork-of-web-chat-v3-i-have-implemented-a-lot-of-customization-in-my-version-of-web-chat-and-i-am-concerned-v4-is-not-compatible-with-my-needs"></a>Meu site está integrado com um fork do Webchat v3. Implementei muita personalização na minha versão do Webchat e estou preocupado que a v4 não seja compatível com minhas necessidades.

Uma das coisas favoritas da nossa equipe sobre a v4 do Webchat é a capacidade de adicionar personalização **sem a necessidade de criar fork do Webchat**. Embora isso crie uma sobrecarga adicional para os usuários de v3 que criaram fork do Webchat anteriormente, faremos nosso melhor para dar suporte aos clientes que fizerem a mudança. Use as seguintes sugestões:

-  Dê uma olhada na implementação do exemplo [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration). Esse é um ótimo ponto de partida para colocar o Webchat em funcionamento.
-  Em seguida, leia a [lista de exemplos](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples) para comparar seus requisitos de personalização com aqueles com os quais o Webchat já é compatível. Esses exemplos são compostos de recursos comumente solicitados para o Webchat.
-  Se um ou mais dos seus recursos não está disponível nos exemplos, consulte nossos [problemas abertos e fechados](https://github.com/Microsoft/BotFramework-WebChat/issues?utf8=%E2%9C%93&q=is%3Aissue+), [Rótulo de exemplos](https://github.com/Microsoft/BotFramework-WebChat/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3ASample) e [Rótulo de suporte à migração](https://github.com/Microsoft/BotFramework-WebChat/issues?q=is%3Aissue+migrate+label%3A%22Migration+Support%22) para pesquisar por solicitações de exemplo e/ou suporte à personalização de um recurso que você está procurando. Adicionar seu comentário a problemas em aberto ajudará a equipe de priorizar as solicitações que têm alta demanda, e nós encorajamos a participação em nossa comunidade.
-  Se você não encontrou o recurso na lista de solicitações em aberto, fique à vontade para [criar sua própria solicitação](https://github.com/Microsoft/BotFramework-WebChat/issues/new). Assim como no item acima, outros clientes adicionando comentários ao seu problema em aberto também ajudarão a priorizar quais recursos são mais frequentemente necessários entre usuários do Webchat.
-  Por fim, se você precisa de seu recurso assim que possível, [solicitações de pull](https://github.com/Microsoft/BotFramework-WebChat/compare) para o Webchat são bem-vindas. Se você tem experiência de codificação suficiente para implementar o recurso por conta própria, apreciamos muito o suporte adicional! Criar o recurso por conta própria significa que ele fica disponível para uso em Webchat mais rapidamente e que outros clientes que buscam um recurso igual ou semelhante podem usar sua contribuição.
-  Confira o restante deste `README` para saber mais sobre a v4.

## <a name="samples-list"></a>Lista de exemplos

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Exemplo&nbsp;Nome&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Descrição                                                                                                                                                                                                                         | Link                                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| [`01.a.getting-started-full-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.a.getting-started-full-bundle)                                                                                       | Apresenta a inserção do Webchat de uma CDN e demonstra um Webchat simples e completo. Isso inclui os Cartões Adaptáveis, Serviços Cognitivos e dependências de Markdown-It.                                                            | [Demonstração do pacote completo](https://microsoft.github.io/BotFramework-WebChat/01.a.getting-started-full-bundle)                               |
| [`01.b.getting-started-es5-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.b.getting-started-es5-bundle)                                                                                         | Apresenta a versão completa da inserção do Webchat, com compatibilidade com versões anteriores para navegadores ES5 usando o ponyfill ES5 do Webchat.                                                                                                                | [Demonstração do pacote ES5](https://microsoft.github.io/BotFramework-WebChat/01.b.getting-started-es5-bundle)                                 |
| [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration)                                                                                           | Demonstra como migrar do seu bot de Webchat v3 para v4.                                                                                                                                                                        | [Demonstração de migração](https://microsoft.github.io/BotFramework-WebChat/01.c.getting-started-migration)                                   |
| [`02.a.getting-started-minimal-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/02.a.getting-started-minimal-bundle)                                                                                 | Apresenta a CDN minimizada, com apenas as dependências básicas. Isso NÃO inclui os Cartões Adaptáveis, dependências de Serviços Cognitivos ou dependências de Markdown-It.                                                                      | [Demonstração de pacote mínimo](https://microsoft.github.io/BotFramework-WebChat/02.a.getting-started-minimal-bundle)                         |
| [`02.b.getting-started-minimal-markdown`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/02.b.getting-started-minimal-markdown)                                                                             | Demonstra como adicionar a CDN para a dependência de Markdown-It sobre o pacote mínimo.                                                                                                                                            | [Demonstração mínima com Markdown](https://microsoft.github.io/BotFramework-WebChat/02.b.getting-started-minimal-markdown)                |
| [`03.a.host-with-react`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.a.host-with-react)                                                                                                               | Demonstra como criar um componente React que hospeda o Webchat completo.                                                                                                                                                 | [Demonstração de host com React](https://microsoft.github.io/BotFramework-WebChat/03.a.host-with-react)                                       |
| [`03.b.host-with-Angular`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.b.host-with-angular)                                                                                                           | Demonstra como criar um componente Angular que hospeda o Webchat completo.                                                                                                                                              | [Demonstração de host com Angular](https://stackblitz.com/github/omarsourour/ng-webchat-example)                                              |
| [`04.a.display-user-bot-initials-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/04.a.display-user-bot-initials-styling)                                                                           | Demonstra como exibir as iniciais de ambos os participantes do Webchat.                                                                                                                                                                | [Demonstração de iniciais de bot](https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/04.a.display-user-bot-initials-styling/)  |
| [`04.b.display-user-bot-images-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/04.b.display-user-bot-images-styling)                                                                               | Demonstra como exibir as imagens e iniciais de ambos os participantes do Webchat.                                                                                                                                                     | [Demonstração de imagens do usuário](https://microsoft.github.io/BotFramework-WebChat/04.b.display-user-bot-images-styling)                           |
| [`05.a.branding-webchat-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.a.branding-webchat-styling)                                                                                             | Apresenta a capacidade de definir o estilo do Webchat para corresponder à sua marca. Esse método de definição de estilo personalizado não será interrompido após atualizações do Webchat.                                                                                                   | [Demonstração de Webchat com identidade visual](https://microsoft.github.io/BotFramework-WebChat/05.a.branding-webchat-styling)                            |
| [`05.b.idiosyncratic-manual-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.b.idiosyncratic-manual-styling/)                                                                                    | Demonstra como fazer alterações de estilo manuais e é uma maneira mais complicada e demorada para personalizar o estilo do Webchat. Estilos manuais podem ser interrompidos após atualizações do Webchat.                                                | [Demonstração de características de estilo idiossincráticas](https://microsoft.github.io/BotFramework-WebChat/05.b.idiosyncratic-manual-styling)                    |
| [`05.c.presentation-mode-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.c.presentation-mode-styling)                                                                                           | Demonstra como configurar o Modo de Apresentação, que exibe o histórico do chat, mas não mostra a caixa de envio e desabilita a interatividade de Cartões Adaptáveis.                                                                         | [Demonstração do Modo de Apresentação](https://microsoft.github.io/BotFramework-WebChat/05.c.presentation-mode-styling)                           |
| [`05.d.hide-upload-button-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.d.hide-upload-button-styling)                                                                                         | Demonstra como ocultar o botão de upload de arquivos por meio da definição de estilo.                                                                                                                                                                            | [Demonstração de ocultar o botão de upload](https://microsoft.github.io/BotFramework-WebChat/05.d.hide-upload-button-styling)                         |
| [`06.a.cognitive-services-bing-speech-js`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.a.cognitive-services-bing-speech-js)                                                                           | Apresenta a capacidade de conversão de fala em texto e texto em fala, usando a API de Fala do Bing dos Serviços Cognitivos (preterida) e o JavaScript.                                                                                                      | [Demonstração de Fala do Bing com JavaScript](https://microsoft.github.io/BotFramework-WebChat/06.a.cognitive-services-bing-speech-js)                 |
| [`06.b.cognitive-services-bing-speech-react`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.b.cognitive-services-bing-speech-react)                                                                     | Apresenta a capacidade de conversão de fala em texto e texto em fala, usando a API de Fala do Bing dos Serviços Cognitivos (preterida) e o React.                                                                                                           | [Demonstração de Fala do Bing com React](https://microsoft.github.io/BotFramework-WebChat/06.b.cognitive-services-bing-speech-react)           |
| [`06.c.cognitive-services-speech-services-js`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.c.cognitive-services-speech-services-js)                                                                   | Apresenta a capacidade de conversão de fala em texto e texto em fala, usando a API dos Serviços de Fala dos Serviços Cognitivos.                                                                                                                                  | [Demonstração dos Serviços de Fala com JavaScript](https://microsoft.github.io/BotFramework-WebChat/06.c.cognitive-services-speech-services-js)         |
| [`06.d.speech-web-browser`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.d.speech-web-browser)                                                                                                         | Demonstra como implementar conversão de texto em fala usando a API de Fala da Web baseada em navegador do Webchat. (link para o W3C padrão no exemplo)                                                                                                    | [Demonstração da API de Fala da Web](https://microsoft.github.io/BotFramework-WebChat/06.d.speech-web-browser)                                     |
| [`06.e.cognitive-services-speech-services-with-lexical-result`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.e.cognitive-services-speech-services-with-lexical-result)                                 | Demonstra como usar o resultado lexical da API de Serviços de Fala dos Serviços Cognitivos.                                                                                                                                                 | [Demonstração do resultado lexical](https://microsoft.github.io/BotFramework-WebChat/06.e.cognitive-services-speech-services-with-lexical-result) |
| [`06.f.hybrid-speech`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.f.hybrid-speech)                                                                                                                   | Demonstra como usar tanto a API de Fala da Web baseada em navegador para conversão de fala em texto quanto a API dos Serviços de Fala dos Serviços Cognitivos para conversão de texto em fala.                                                                                        | [Demonstração de fala híbrida](https://microsoft.github.io/BotFramework-WebChat/06.f.hybrid-speech)                                           |
| [`07.a.customization-timestamp-grouping`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/07.a.customization-timestamp-grouping)                                                                             | Demonstra como personalizar os carimbos de data/hora, mostrando ou ocultando os carimbos de data/hora e alterando o agrupamento das mensagens por hora.                                                                                                             | [Demonstração de agrupamento de carimbos de data/hora](https://microsoft.github.io/BotFramework-WebChat/07.a.customization-timestamp-grouping)                   |
| [`07.b.customization-send-typing-indicator`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/07.b.customization-send-typing-indicator)                                                                       | Demonstra como enviar atividade de digitação quando o usuário começa a digitar na caixa de envio.                                                                                                                                                | [Demonstração de indicador de digitação pelo usuário](https://microsoft.github.io/BotFramework-WebChat/07.b.customization-send-typing-indicator)             |
| [`08.customization-user-highlighting`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/08.customization-user-highlighting)                                                                                   | Demonstra como personalizar o estilo de atividades com base na proveniência da mensagem ser do usuário ou do bot.                                                                                                                      | [Demonstração de realce de usuário](https://microsoft.github.io/BotFramework-WebChat/08.customization-user-highlighting)                       |
| [`09.customization-reaction-buttons`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/09.customization-reaction-buttons/)                                                                                    | Apresenta a capacidade de criar componentes personalizados para Webchat que são exclusivos para as necessidades do seu bot. Este tutorial demonstra a capacidade de adicionar o emoji de reação, tal como: thumbsup: e: thumbsdown:, a atividades de conversação. | [Demonstração de botões de reação](https://microsoft.github.io/BotFramework-WebChat/09.customization-reaction-buttons)                         |
| [`10.a.customization-card-components`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/10.a.customization-card-components)                                                                                   | Demonstra como criar anexos de cartão de atividades personalizadas, neste caso, cartões de repositório do GitHub.                                                                                                                                  | [Demonstração de componentes de cartão](https://microsoft.github.io/BotFramework-WebChat/10.a.customization-card-components)                         |
| [`10.b.customization-password-input`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/10.b.customization-password-input)                                                                                     | Demonstra como criar uma atividade personalizada para entrada de senha.                                                                                                                                                                      | [Demonstração de entrada de senha](https://microsoft.github.io/BotFramework-WebChat/10.b.customization-password-input)                           |
| [`11.customization-redux-actions`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/11.customization-redux-actions)                                                                                           | Tutorial avançado: Demonstra como incorporar o middleware redux em seu aplicativo de Webchat enviando ações do redux por meio do bot. Este exemplo demonstra o estilo manual com base nas atividades entre o bot e o usuário.             | [Demonstração de ações do Redux](https://microsoft.github.io/BotFramework-WebChat/11.customization-redux-actions)                               |
| [`12.customization-minimizable-web-chat`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/12.customization-minimizable-web-chat)                                                                             | Tutorial avançado: Demonstra como adicionar a interface de Webchat ao seu site como uma caixa de chat do tipo mostrar/ocultar minimizável.                                                                                                              | [Demonstração de Webchat minimizável](https://microsoft.github.io/BotFramework-WebChat/12.customization-minimizable-web-chat)                 |
| [`13.customization-speech-ui`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/13.customization-speech-ui)                                                                                                   | Tutorial avançado: Demonstra como personalizar totalmente os principais componentes do seu bot, neste caso a fala, que substitui completamente a interface do usuário de transcrição baseada em texto e, em vez disso, mostra um botão de fala simples com a resposta do bot.      | [Demonstração da interface do usuário de fala](https://microsoft.github.io/BotFramework-WebChat/13.customization-speech-ui)                                       |
| [`14.customization-piping-to-redux`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/14.customization-piping-to-redux)                                                                                       | Tutorial avançado: Demonstra como direcionar atividades do bot para seu próprio repositório do Redux e usar seu bot para controlar sua página por meio do Redux e de atividades do bot.                                                                          | [Demonstração de direcionamento para o Redux](https://microsoft.github.io/BotFramework-WebChat/14.customization-piping-to-redux)                           |
| [`15.a.backchannel-piggyback-on-outgoing-activities`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.a.backchannel-piggyback-on-outgoing-activities)                                                     | Tutorial avançado: Demonstra como adicionar dados personalizados a todas as atividades de saída.                                                                                                                                                | [Demonstração de acúmulo de backchannel](https://microsoft.github.io/BotFramework-WebChat/15.a.backchannel-piggyback-on-outgoing-activities) |
| [`15.b.incoming-activity-event`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.b.incoming-activity-event)                                                                                               | Tutorial avançado: Demonstra como encaminhar todas as atividades de entrada a um evento de JavaScript para processamento adicional.                                                                                                                | [Demonstração de atividade de entrada](https://microsoft.github.io/BotFramework-WebChat/15.b.incoming-activity-event)                             |
| [`15.c.programmatic-post-activity`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.c.programmatic-post-activity)                                                                                         | Tutorial avançado: Demonstra como enviar uma mensagem de forma programática.                                                                                                                                                             | [Demonstração de postagem programática](https://microsoft.github.io/BotFramework-WebChat/15.c.programmatic-post-activity)                       |
| [`15.d.backchannel-send-welcome-event`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.d.backchannel-send-welcome-event)                                                                                 | Tutorial avançado: Demonstra como enviar eventos de boas-vindas com funcionalidades de cliente como o idioma do navegador.                                                                                                                        | [Demonstração de evento de boas-vindas](https://microsoft.github.io/BotFramework-WebChat/15.d.backchannel-send-welcome-event)                          |
| [`16.customization-selectable-activity`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/16.customization-selectable-activity)                                                                               | Tutorial avançado: Demonstra como adicionar um comportamento de clique personalizado para cada atividade.                                                                                                                                                  | [Demonstração de atividade selecionável](https://microsoft.github.io/BotFramework-WebChat/16.customization-selectable-activity)                   |
| [`17.chat-send-history`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/17.chat-send-history)                                                                                                               | Tutorial avançado: Demonstra a capacidade de salvar a entrada do usuário e permitir que o usuário retroceda pelas mensagens enviadas anteriores.                                                                                                      | [Demonstração de histórico de envio de chat](https://microsoft.github.io/BotFramework-WebChat/17.chat-send-history)                                     |
| [`18.customization-open-url`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/18.customization-open-url)                                                                                                     | Tutorial avançado: Demonstra como personalizar o comportamento de abrir URL.                                                                                                                                                             | [Demonstração de personalizar abrir URL](https://microsoft.github.io/BotFramework-WebChat/18.customization-open-url)                               |
| [`19.a.single-sign-on-for-enterprise-apps`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps)                                                                         | Demonstra como usar o logon único para aplicativos empresariais usando OAuth                                                                                                                                                              | [Demonstração de logon único usando OAuth](https://microsoft.github.io/BotFramework-WebChat/19.a.single-sign-on-for-enterprise-apps)         |
 

## <a name="web-chat-api-reference"></a>Referência da API do Webchat

Há várias propriedades que você pode passar para seu componente React do Webchat (`<ReactWebChat>`) ou o método `renderWebChat()`. Fique à vontade examinar o código-fonte começando com [`packages/component/src/Composer.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Composer.js#L378). Abaixo, uma breve descrição das propriedades disponíveis.

| Propriedade                   | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `activityMiddleware`       | Uma cadeia de middleware, modelada segundo o [middleware do Redux](https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6), que permite que o desenvolvedor adicione novos componentes de DOM ao DOM de atividades existente atualmente. A assinatura do middleware é a seguinte: `options => next => card => children => next(card)(children)`.                                                                                                                                                                                                                                           |
| `activityRenderer`         | A versão "nivelada" de `activityMiddleware`, semelhante ao conceito de [aprimorador de repositório](https://github.com/reduxjs/redux/blob/master/docs/Glossary.md#store-enhancer) no Redux.                                                                                                                                                                                                                                                                                                                                                                                                                |
| `adaptiveCardHostConfig`   | Passar uma configuração de host de Cartões Adaptáveis personalizada. Verifique sua Configuração de Host com a versão dos Cartões Adaptáveis que está sendo usada. Para obter mais informações, consulte [Configuração de Host personalizada](https://github.com/microsoft/BotFramework-WebChat/issues/2034#issuecomment-501818238).                                                                                                                                                                                                                                                                                                                                    |
| `attachmentMiddleware`     | Uma cadeia de middleware que permite que o desenvolvedor adicione seus próprios elementos HTML personalizados em anexos. A assinatura é a seguinte: `options => next => card => next(card)`.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `attachmentRenderer`       | A versão "nivelada" de `attachmentMiddleware`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `cardActionMiddleware`     | Uma cadeia de middleware que permite ao desenvolvedor modificar as ações de cartão, tais como Cartões Adaptáveis ou ações sugeridas. A assinatura do middleware é a seguinte: `cardActionMiddleware: () => next => ({ cardAction, getSignInUrl }) => next(cardAction)`                                                                                                                                                                                                                                                                                                                                           |
| `createDirectLine`         | Um método de fábrica para instanciar o objeto Direct Line. Os usuários do Azure Governamental devem usar `createDirectLine({ domain: 'https://directline.botframework.azure.us/v3/directline', token });` para alterar o ponto de extremidade. A lista completa de parâmetros são: `conversationId`, `domain`, `fetch`, `pollingInterval`, `secret`, `streamUrl`, `token`, `watermark` `webSocket`.                                                                                                                                                                                                                         |
| `createStore`              | Uma cadeia de middleware que permite ao desenvolvedor modificar as ações de armazenamento. A assinatura do middleware é a seguinte: `createStore: ({}, ({ dispatch }) => next => action => next(cardAction)`                                                                                                                                                                                                                                                                                                                                                                                                |
| `directLine`               | Especificar o objeto DirectLine com o token DirectLine.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `disabled`                 | Desabilite a interface do usuário (ou seja, para o modo de apresentação) do Webchat.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `grammars`                 | Especifique uma lista de gramática de Fala (Serviços de Fala dos Serviços Cognitivos ou Fala do Bing).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `groupTimeStamp`           | Altere as configurações padrão para agrupamentos de carimbos de data/hora.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `locale`                   | Indica o idioma padrão do Webchat. Códigos com quatro letras (como `en-US`) são altamente recomendados.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `renderMarkdown`           | Altere o objeto de renderizador de Markdown padrão.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `sendTypingIndicator`      | Exibir um sinal de digitação do usuário ao bot para indicar que o usuário não está ocioso.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `store`                    | Especificar um repositório personalizado, por exemplo, para a adição de uma atividade programática ao bot.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `styleOptions`             | Objeto que armazena valores de personalização para seu estilo de Webchat. Para obter uma lista completa das opções de estilo padrão (atualizada com frequência), consulte o arquivo [defaultStyleOptions.js](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/defaultStyleOptions.js).                                                                                                                                                                                                                                                                              |
| `styleSet`                 | A maneira não recomendada de substituir estilos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `userID`                   | Especificar uma userID. Há duas maneiras para especificar o `userID`: em propriedades ou no token ao gerar a chamada de token (`createDirectLine()`). Se ambos os métodos forem usados para especificar a ID do usuário, a propriedade userID do token será usada e um `console.warn` será exibido durante o runtime. Se o `userID` é fornecido por meio de objetos, mas tem `'dl'` como prefixo, por exemplo, `'dl_1234'`, o valor é lançado e um novo `ID` é gerado. Se `userID` não for especificado, ele usará por padrão uma ID de usuário aleatória. Não é recomendado que vários usuários compartilhem a mesma ID de usuário, pois o estado de usuário deles será compartilhado. |
| `username`                 | Especificar um nome de usuário.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `webSpeechPonyFillFactory` | Especificar o objeto de Fala da Web para conversão de texto em fala e de fala em texto.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
## <a name="browser-compatibility"></a>Compatibilidade de navegador
O Webchat é compatível com as duas versões mais recentes dos navegadores modernos, como Chrome, Edge e Firefox.
Se você precisa do Webchat no Internet Explorer 11, consulte a [Demonstração de pacote do ES5](https://microsoft.github.io/BotFramework-WebChat/01.b.getting-started-es5-bundle).

No entanto, observe que:
- O Webchat não é compatível com versões do Internet Explorer anteriores à 11
- A personalização, conforme mostrada em exemplos não ES5, não é compatível com o Internet Explorer. Já que o IE11 não é um navegador moderno, ele não é compatível com ES6. Muitos exemplos que usam funções de seta e promessas modernas precisariam ser convertidas manualmente em ES5.  Se você precisa de muita personalização em seu aplicativo, é altamente recomendável desenvolver seu aplicativo para um navegador moderno, como o Google Chrome ou o Edge.
- O Webchat não tem nenhum plano de dar suporte a exemplos no IE11 (ES5).
   - Para clientes que desejam reescrever manualmente nossos outros exemplos para trabalhar no IE11, é recomendável estudar a conversão de código do ES6 + para ES5 usando polyfills e transpilers como [`babel`](https://babeljs.io/docs/en/next/babel-standalone.html).

## <a name="how-to-test-with-web-chats-latest-bits"></a>Como testar as últimas novidades do Webchat

*O teste de recursos não liberados só está disponível atualmente por meio de empacotamento MyGet.*

Se você quiser testar um recurso ou correção de bug que ainda não foi lançado, você deverá apontar seu pacote do Webchat para o feed diário do Webchat, em vez do feed npmjs oficial.

No momento, você pode acessar as novidades diárias do Webchat assinando nosso feed do MyGet. Para fazer isso, você precisará atualizar o registro em seu projeto. **Essa alteração é reversível e nossas instruções incluem como reverter para voltar a assinar a versão oficial**.

### <a name="subscribe-to-latest-bits-on-mygetorg"></a>Assinar as últimas novidades no `myget.org`

Para fazer isso, você pode adicionar seus pacotes e, em seguida, alterar o registro do seu projeto.

1. Adicione suas dependências de projeto que não sejam de Webchat.
1. No diretório de raiz do seu projeto, crie um arquivo `.npmrc`
1. Adicione a seguinte linha ao seu arquivo: `registry=https://botbuilder.myget.org/F/botframework-webchat/npm/`
1. Adicione o Webchat a suas dependências de projeto `npm i botframework-webchat --save`
1. Observe que, no seu `package-lock.json`, os registros para os quais você está apontando agora são MyGet. O projeto de Webchat tem proxy de origem upstream habilitado, o que redirecionará os pacotes não MyGet para `npmjs.com`.

### <a name="re-subscribe-to-official-release-on-npmjscom"></a>Assinar novamente a versão oficial em `npmjs.com`
Voltar a assinar a versão original exige que você redefina seu registro.

1. Excluir seu `.npmrc file`
1. Exclua o `package-lock.json` raiz
1. Remova o diretório `node_modules`
1. Reinstale os pacotes com `npm i`
1. Observe que, no `package-lock.json`, os registros estão apontando para https://npmjs.com/ novamente.


## <a name="contributing"></a>Participante

Consulte nossa [Página de contribuição](https://github.com/Microsoft/BotFramework-WebChat/tree/master/.github/CONTRIBUTING.md) para obter detalhes sobre como criar o projeto e nossas diretrizes de repositório para solicitações de pull.

Este projeto adotou o [Código de Conduta de Software Livre da Microsoft](https://opensource.microsoft.com/codeofconduct/).
Para saber mais, confira as [Perguntas Frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate [opencode@microsoft.com](mailto:opencode@microsoft.com) com perguntas ou comentários adicionais.

## <a name="reporting-security-issues"></a>Relatar problemas de segurança

Bugs e problemas de segurança devem ser relatados em particular, por email, para o MSRC (Microsoft Security Response Center) em [secure@microsoft.com](mailto:secure@microsoft.com). Você deverá receber uma resposta em até 24 horas. Se por alguma razão isso não ocorrer, faça um contato posterior via email para garantir que tenhamos recebido sua mensagem original. Mais informações, incluindo a chave [PGP do MSRC](https://technet.microsoft.com/security/dn606155), podem ser encontradas no [Microsoft Security Response Center](https://technet.microsoft.com/security/default).

Copyright (c) Microsoft Corporation. Todos os direitos reservados.
