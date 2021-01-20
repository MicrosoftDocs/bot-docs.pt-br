---
title: Visão geral do Webchat – Serviço de Bot
description: Familiarize-se com o componente de chat da Web do bot Framework. Saiba como usar e personalizar este componente. Exiba as propriedades disponíveis e outras informações.
keywords: bot framework, webchat, chat, exemplos, react, referência
author: mmiele
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/23/2020
ms.openlocfilehash: bbdafa503fb5f125300571c71f8222e0eff19d5c
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98577298"
---
# <a name="web-chat-overview"></a>Visão geral de Webchat

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Este artigo contém detalhes do componente de [Webchat do Bot Framework](https://github.com/microsoft/BotFramework-WebChat). O componente de Webchat do Bot Framework é um cliente baseado na Web altamente personalizável para o SDK do Bot Framework V4. O SDK do Bot Framework v4 permite aos desenvolvedores modelar a conversa e criar aplicativos de bot sofisticados.

Se você estiver buscando migrar do Webchat v3 para v4, vá diretamente para [a seção de migração](#migrating-from-web-chat-v3-to-v4).

## <a name="how-to-use"></a>Como usar

> [!NOTE]
> Para versões anteriores do Webchat (v3), visite o [branch do Webchat v3](https://github.com/Microsoft/BotFramework-WebChat/tree/v3).

Primeiro, crie um Bot usando o [Serviço de Bot do Azure](https://azure.microsoft.com/services/bot-service/).
Depois que o bot for criado, você precisará [obter o segredo do Webchat do bot](../bot-service-channel-connect-webchat.md#get-your-bot-secret-key) no portal do Azure. Em seguida, use o segredo para [gerar um token](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md) e passá-lo para seu Webchat.

Os exemplos a seguir mostram como adicionar um controle de chat da Web a um site.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>

         // Set style options.
         const styleOptions = {
            botAvatarInitials: 'BF',
            userAvatarInitials: 'WC'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  token: 'YOUR_DIRECT_LINE_TOKEN'
               }),
               userID: 'YOUR_USER_ID',
               username: 'Web Chat User',
               locale: 'en-US',
               styleOptions
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

> `userID`, `username`, `locale`, `botAvatarInitials` e `userAvatarInitials` são todos parâmetros opcionais para passar ao método `renderWebChat`.  Para obter mais informações sobre estilo, consulte [por que styleoptions?](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/02.branding-styling-and-customization/a.branding-web-chat#why-stylesetoptions). Para saber mais sobre as propriedades de chat da Web, consulte a seção [referência da API de chat Web](#web-chat-api-reference) .


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

| Personalização                 | Pacote CDN         | React              |
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
> Para obter informações sobre CDNs (Redes de Distribuição de Conteúdo), consulte [CDNs (Redes de Distribuição de Conteúdo)](/azure/architecture/best-practices/cdn)

## <a name="migrating-from-web-chat-v3-to-v4"></a>Migração de Webchat v3 para v4

Há três caminhos possíveis para a migração de v3 para v4. Primeiro, Compare seu cenário inicial com aquele listado abaixo.

- Para atualizar um controle de chat da Web inserido em um `<iframe>` , consulte a documentação no repositório de chat da Web para o [pacote de inserção](https://github.com/microsoft/BotFramework-WebChat/tree/master/packages/embed#readme).
- Para atualizar um controle de chat da Web que usa pouca ou nenhuma personalização, examine o exemplo de bate-papo da Web [00. Migration/a. v3-to-v4](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/00.migration/a.v3-to-v4) , que descreve o processo.
- Para atualizar uma versão bifurcada do chat Web v3 com muita personalização, consulte as [diretrizes de migração](https://github.com/microsoft/BotFramework-WebChat/blob/master/docs/MIGRATION.md#my-website-is-integrated-with-a-fork-of-web-chat-v3-i-have-implemented-a-lot-of-customization-in-my-version-of-web-chat-and-i-am-concerned-v4-is-not-compatible-with-my-needs)de chat da Web.

## <a name="web-chat-api-reference"></a>Referência da API do Webchat

Há várias propriedades que você pode passar para seu componente React do Webchat (`<ReactWebChat>`) ou o método `renderWebChat()`. Para obter uma breve descrição das propriedades disponíveis, consulte [Web Chat API Reference](https://github.com/microsoft/BotFramework-WebChat/blob/master/docs/API.md#web-chat-api-reference).
Além disso, sinta-se à vontade para examinar o código-fonte começando com [`packages/component/src/Composer.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Composer.js#L378) .

## <a name="browser-compatibility"></a>Compatibilidade de navegador

O Webchat é compatível com as duas versões mais recentes dos navegadores modernos, como Chrome, Microsoft Edge e Firefox.
Se você precisar do Webchat no Internet Explorer 11, confira [pacote ES5](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/01.getting-started/c.es5-bundle) e [demonstração](https://microsoft.github.io/BotFramework-WebChat/01.getting-started/c.es5-bundle/).

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
