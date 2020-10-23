---
title: Inserir um bot em um aplicativo – Serviço de Bot
description: Saiba como inserir bots em aplicativos. Veja como integrar bots a aplicativos móveis nativos, aplicativos móveis baseados na Web, aplicativos de IoT e outros tipos de aplicativos. Exiba o código de exemplo.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/15/2018
ms.openlocfilehash: a6a100537dc2a2a71062a4213978a1f7b0e3334c
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92415134"
---
# <a name="embed-a-bot-in-an-app"></a>Inserir um bot em um aplicativo

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Embora seja mais comum os bots existirem fora dos aplicativos, eles também podem ser integrados aos aplicativos. Por exemplo, você poderia inserir um [bot de conhecimento](~/bot-service-design-pattern-knowledge-base.md) dentro de um aplicativo para ajudar os usuários a encontrarem informações que, de outra forma, poderiam ser difíceis de localizar dentro de estruturas complexas de aplicativos. Você pode inserir um bot dentro de um aplicativo de suporte técnico para atuar como o primeiro respondente às solicitações do usuário. O bot poderia resolver de forma independente problemas simples e [repassar](~/bot-service-design-pattern-handoff-human.md) os problemas mais complexos para um agente humano. 

## <a name="integrating-bot-with-app"></a>Integração do bot com o aplicativo

A maneira de integrar a um aplicativo a um bot varia dependendo do tipo de aplicativo. 

### <a name="native-mobile-app"></a>Aplicativo móvel nativo

Um aplicativo criado em código nativo pode se comunicar com o Bot Framework usando a [Direct Line API][directLineAPI], por meio de REST ou websockets.

### <a name="web-based-mobile-app"></a>Aplicativo móvel baseado na Web

Um aplicativo móvel criado usando a linguagem da Web e estruturas como <a href="https://cordova.apache.org/" target="_blank">Cordova</a> podem se comunicar com o Bot Framework usando os mesmos componentes que um [bot inserido em um site](~/bot-service-design-pattern-embed-web-site.md) usaria, mas encapsulados dentro do shell de um aplicativo nativo.

### <a name="iot-app"></a>Aplicativo IoT

Um aplicativo IoT pode se comunicar com o Bot Framework usando a [Direct Line API][directLineAPI]. Em alguns cenários, também pode usar os <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Serviços Cognitivos da Microsoft</a> para habilitar recursos como fala e reconhecimento de imagem.

### <a name="other-types-of-apps-and-games"></a>Outros tipos de aplicativos e jogos

Outros tipos de aplicativos e jogos podem se comunicar com o Bot Framework usando a [Direct Line API][directLineAPI]. 

## <a name="creating-a-cross-platform-mobile-app-that-runs-a-bot"></a>Criar um aplicativo móvel de plataforma cruzada que executa um bot

Este exemplo de criação de um aplicativo móvel que executa um bot usa <a href="https://www.xamarin.com/" target="_blank">Xamarin</a>, uma ferramenta popular para a criação de aplicativos móveis de plataforma cruzada. 

Primeiro, crie um componente de exibição da Web simples e use-o para hospedar um <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de chat na Web</a>. Em seguida, usando o portal do Azure, adicione o canal de Webchat. 

Em seguida, especifique a URL do chat na Web registrada como a origem para o controle de exibição da Web no aplicativo do Xamarin:

```cs
public class WebPage : ContentPage
{
    public WebPage()
    {
        var browser = new WebView();
        browser.Source = "https://webchat.botframework.com/embed/<YOUR SECRET KEY HERE>";
        this.Content = browser;
    }
}
```

Usando esse processo, você pode criar um aplicativo móvel de plataforma cruzada que renderiza a exibição da Web integrada ao controle de chat na web.

![Canal de apoio](~/media/bot-service-design-pattern-embed-app/xamarin-apps.png)

<!-- TODO: No sample bot available
## Sample code

For a complete sample that shows how to create a cross-platform mobile app that runs a bot (as described in this article), see the <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/capability-BotInApps" target="_blank">Bot in Apps sample</a> in GitHub.
-->

## <a name="additional-resources"></a>Recursos adicionais

- [Direct Line API][directLineAPI]
- <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Serviços Cognitivos da Microsoft</a>

[directLineAPI]: https://docs.botframework.com/restapi/directline3/#navtitle
