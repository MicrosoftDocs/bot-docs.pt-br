---
title: Perguntas frequentes do bot Framework-serviço de bot de ecossistema
description: Perguntas frequentes sobre o ecossistema do bot Framework.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/08/2020
ms.openlocfilehash: 529ec82ed8f00136b2fa3272fef62d58efb8d07e
ms.sourcegitcommit: 7bf72623d9abf15e1444e8946535724f500643c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88143565"
---
# <a name="ecosystem"></a>Ecossistema

<!-- Attention writers!!
     1 - This article contains FAQs regarding Bot Framework ecosystem.
     1 - When you create a new FAQ, please add the related link to the proper section in bot-service-resources-bot-framework-faq.md.-->

## <a name="how-do-i-enable-the-emulator-to-connect-to-localhost-while-behind-a-corporate-proxy"></a>Como fazer habilitar o emulador para se conectar ao localhost enquanto estiver atrás de um proxy corporativo?

Ao desenvolver um bot em um ambiente corporativo, normalmente, o proxy bloqueará a conexão, a menos que você especifique que ela deve ser ignorada para o `localhost` . Para fazer isso em seu computador local, você deve definir a seguinte variável de ambiente:

```cmd
    NO_PROXY=localhost
```

Para saber mais, consulte [Definir as configurações de proxy](bot-service-debug-emulator.md#configure-proxy-settings).

## <a name="when-will-you-add-more-conversation-experiences-to-the-bot-framework"></a>Quando vocês adicionarão mais experiências de conversa ao Bot Framework?

Estamos planejando fazer melhorias contínuas no Bot Framework, incluindo canais adicionais, mas não podemos fornecer um prazo no momento.
Se você quiser um canal específico adicionado à estrutura, [informe-nos] [suporte].

## <a name="i-have-a-communication-channel-id-like-to-be-configurable-with-bot-framework-can-i-work-with-microsoft-to-do-that"></a>Tenho um canal de comunicação que desejo tornar configurável com o Bot Framework. Posso trabalhar com a Microsoft para fazer isso?

Não fornecemos um mecanismo geral para os desenvolvedores adicionarem novos canais à estrutura de bot, mas você pode conectar seu bot ao seu aplicativo por meio da [API de linha direta] [DirectLineAPI]. Se você for um desenvolvedor de um canal de comunicação e quiser trabalhar conosco para habilitar seu canal na estrutura de bot [gostaríamos de ouvir de você] [suporte].

## <a name="if-i-want-to-create-a-bot-for-microsoft-teams-what-tools-and-services-should-i-use"></a>Se eu quiser criar um bot para o Microsoft Teams, quais ferramentas e serviços deverei usar?

O Bot Framework foi projetado para criar, conectar e implantar bots escalonáveis, dinâmicos e de alto desempenho e alta qualidade no Teams e em muitos outros canais. O SDK pode ser usado para criar bots compatíveis com texto/SMS, imagem, botão e cartão (que constituem a maior parte das interações de bot hoje nas experiências de conversa), bem como interações de bot específicas do Teams, tais como experiências avançadas de áudio e vídeo.

Caso você já tenha um bot excelente, mas queira alcançar o público-alvo do Teams, o bot poderá ser conectado com facilidade ao Teams (ou a qualquer canal compatível) por meio do Bot Framework para a API REST (desde que ele tenha um ponto de extremidade REST acessível pela Internet).

## <a name="how-do-i-create-a-bot-that-uses-the-us-government-data-center"></a>Como fazer para criar um bot que usa o data center do Governo dos EUA?

Há duas etapas principais necessárias para criar um bot que usa um data center do Governo dos EUA.

1. Adicione uma configuração "provedor de canal" em seu appsettings.json (ou as Configurações do Serviço de Aplicativo). Isso precisa ser definido especificamente para esta constante de nome/valor: ChannelService = "https://botframework.azure.us". Um exemplo de uso de appsetting.json é mostrado abaixo.

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  "ChannelService": "https://botframework.azure.us"
}
```

1. Se estiver usando o .NET Core, você precisará adicionar um ConfigurationChannelProvider em seu arquivo startup.cs. O modo como você faz isso varia de acordo com qual versão do SDK você está usando.

- Para a 4.3 e versões superiores, no método ConfigureServices, você precisará criar uma instância de ConfigurationChannelProvider. Ao usar a classe BotFrameworkHttpAdapter, você injeta isso como singleton na coleção de serviços desta maneira:

```csharp
services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();
```

- Para versões anteriores à 4.3, no método ConfigureServices, encontre o método AddBot. Ao definir as opções, verifique se você adicionou:

```csharp
options.ChannelProvider = new ConfigurationChannelProvider();
```

Você pode encontrar mais informações sobre os serviços do governo [aqui](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#azure-bot-service)

## <a name="what-is-the-direct-line-channel"></a>O que é o canal da Linha Direta?

Linha Direta é uma API REST que permite que você adicione o bot ao serviço, ao aplicativo móvel ou à página da Web.

Você pode escrever um cliente para a API de Linha Direta em qualquer linguagem. Basta codificar para o [protocolo de linha direta] [DirectLineAPI], gerar um segredo na página de configuração de linha direta e conversar com o bot de onde o código reside.

A Linha Direta é adequada para:

- Aplicativos móveis no iOS, no Android e no Windows Phone e outros
- Aplicativos da área de trabalho no Windows, no OSX e muito mais
- Páginas da Web em que você precisa de mais personalização do que o [canal de bate-papo de Internet incorporável] ofertas
- Aplicativos de serviço a serviço

## <a name="what-are-the-steps-to-configure-web-chat-and-direct-line-for-azure-government"></a>Quais são as etapas para configurar o chat da Web e a linha direta para o Azure governamental?

As etapas para configurar o chat da Web e a linha direta para o Azure governamental são semelhantes às usadas para o Azure público. No Azure governamental, você define o [domínio](https://github.com/microsoft/BotFramework-WebChat/blob/master/packages/bundle/src/createDirectLine.js#L6) para a URL do Azure governamental porque o [domínio padrão](https://github.com/microsoft/BotFramework-DirectLineJS/blob/master/src/directLine.ts#L456) se aplica ao Azure público, não ao Azure governamental. Observe que a URL pública do Azure ( `https://webchat.botframework.com/v3/directline` ) é diferente da URL do Azure governamental ( `https://webchat.botframework.azure.us/v3/directline` ) para o chat da Web e a configuração de linha direta.

O exemplo a seguir mostra como definir o domínio para a URL do Azure governamental:

```html
<body>
    <div id="webchat" role="main"></div>
    <script>
      window.WebChat.renderWebChat(
        {
          directLine: window.WebChat.createDirectLine({
          token: 'YOUR_TOKEN_SECRET',
          domain: 'https://webchat.botframework.azure.us/v3/directline'
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

```
Saiba mais nos documentos a seguir:
* [Conectar um bot ao Webchat](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat?view=azure-bot-service-4.0)
* [Conectar um bot à Linha Direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directline?view=azure-bot-service-4.0)
* Para uma abordagem programática para trocar seu segredo para um token, use o trecho de código fornecido [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat?view=azure-bot-service-4.0#production-embedding--option) e ajuste as URLs do Azure público para o Azure governamental.


## <a name="how-does-the-bot-framework-relate-to-cognitive-services"></a>Como o Bot Framework está relacionado aos Serviços Cognitivos?

O Bot Framework e os [Serviços Cognitivos](https://www.microsoft.com/cognitive) são criados com base em anos de pesquisa e uso em produtos populares da Microsoft. Essas funcionalidades permitem a todas as organizações aproveitar o poder dos dados, da nuvem e da inteligência a fim de criar os próprios sistemas inteligentes que revelam novas oportunidades, aumentar a velocidade dos negócios e liderar os setores nos quais elas atendem a seus clientes.

## <a name="what-are-the-possible-machine-readable-resolutions-of-the-luis-built-in-date-time-duration-and-set-entities"></a>Quais são as possíveis resoluções legíveis por computador da data, hora, duração interna do LUIS e conjunto de entidades?

Para obter uma lista de exemplos, consulte a seção [Entidades criadas previamente](/azure/cognitive-services/LUIS/luis-reference-prebuilt-entities) da documentação do LUIS.

## <a name="how-can-i-use-more-than-the-maximum-number-of-luis-intents"></a>Como posso usar mais que o número máximo de intenções de LUIS?

Você pode considerar dividir o modelo e chamar o serviço de LUIS em série ou paralelo.

## <a name="how-can-i-use-more-than-one-luis-model"></a>Como é possível usar mais de um modelo de LUIS?

Tanto o SDK do Bot Framework para Node.js quanto o SDK do Bot Framework para .NET dão suporte a chamada de vários modelos de LUIS a partir de um único diálogo de intenção de LUIS. Tenha em mente as seguintes advertências:

* O uso de vários modelos de LUIS assume que os modelos de LUIS possuem conjuntos de intenções não sobrepostos.
* O uso de vários modelos de LUIS pressupõe que as pontuações de diferentes modelos sejam comparáveis para selecionar a "melhor intenção combinada" em vários modelos.
* Usar vários modelos de LUIS significa que, se uma intenção corresponder a um modelo, também corresponderá fortemente à intenção "nenhum" dos outros modelos. Evite selecionar a intenção "nenhum" nessa situação, já que o SDK do Bot Framework para Node.js reduzirá automaticamente a pontuação para "nenhum" para evitar esse problema.

## <a name="where-can-i-get-more-help-on-luis"></a>Onde é possível obter mais ajuda sobre o LUIS?

- [Introdução ao LUIS (Serviço Inteligente de Reconhecimento Vocal) - Serviços Cognitivos da Microsoft](https://www.youtube.com/watch?v=jWeLajon9M8) (vídeo)
- [Sessão de Aprendizado Avançado para LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://www.youtube.com/watch?v=39L0Gv2EcSk) (vídeo)
- [Documentação do LUIS](/azure/cognitive-services/luis/)
- [Fórum do Serviço Inteligente de Reconhecimento Vocal](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
