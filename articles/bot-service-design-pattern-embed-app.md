---
title: Inserir um bot em um aplicativo | Microsoft Docs
description: Saiba como criar um bot que será inserido em outro aplicativo.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 3264388cf253bb949eabe3be04fdaebabdc36b99
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296812"
---
# <a name="embed-a-bot-in-an-app"></a>Inserir um bot em um aplicativo

Embora seja mais comum os bots existirem fora dos aplicativos, eles também podem ser integrados aos aplicativos. Por exemplo, você poderia inserir um [bot de conhecimento](~/bot-service-design-pattern-knowledge-base.md) dentro de um aplicativo para ajudar os usuários a encontrarem informações que, de outra forma, poderiam ser difíceis de localizar dentro de estruturas complexas de aplicativos. Você pode inserir um bot dentro de um aplicativo de suporte técnico para atuar como o primeiro respondente às solicitações do usuário. O bot poderia resolver de forma independente problemas simples e [repassar](~/bot-service-design-pattern-handoff-human.md) os problemas mais complexos para um agente humano. 

## <a name="integrating-bot-with-app"></a>Integração do bot com o aplicativo

A maneira de integrar a um aplicativo a um bot varia dependendo do tipo de aplicativo. 

### <a name="native-mobile-app"></a>Aplicativo móvel nativo

Um aplicativo criado em código nativo pode se comunicar com o Bot Framework usando a [API de Linha Direta][directLineAPI], por meio de REST ou websockets.

### <a name="web-based-mobile-app"></a>Aplicativo móvel baseado na Web

Um aplicativo móvel criado usando a linguagem da Web e estruturas como <a href="https://cordova.apache.org/" target="_blank">Cordova</a> podem se comunicar com o Bot Framework usando os mesmos componentes que um [bot inserido em um site](~/bot-service-design-pattern-embed-web-site.md) usaria, mas encapsulados dentro do shell de um aplicativo nativo.

### <a name="iot-app"></a>Aplicativo IoT

Um aplicativo IoT pode se comunicar com o Bot Framework usando a [API de Linha Direta][directLineAPI]. Em alguns cenários, também pode usar os <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Serviços Cognitivos da Microsoft</a> para habilitar recursos como fala e reconhecimento de imagem.

### <a name="other-types-of-apps-and-games"></a>Outros tipos de aplicativos e jogos

Outros tipos de aplicativos e jogos podem se comunicar com o Bot Framework usando a [API de Linha Direta][directLineAPI]. 

## <a name="creating-a-cross-platform-mobile-app-that-runs-a-bot"></a>Criar um aplicativo móvel de plataforma cruzada que executa um bot

Este exemplo de criação de um aplicativo móvel que executa um bot usa <a href="https://www.xamarin.com/" target="_blank">Xamarin</a>, uma ferramenta popular para a criação de aplicativos móveis de plataforma cruzada. 

Primeiro, crie um componente de exibição da Web simples e use-o para hospedar um <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de chat na Web</a>. Depois, usando o Portal do Bot Framework, use [TODO](~/bot-service-manage-channels.md) no canal de Chat na Web. 

![Definições de configuração do bot](~/media/bot-service-design-pattern-embed-app/webchat-channel.png)

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

## <a name="sample-code"></a>Exemplo de código

Confira um exemplo completo que mostra como criar um aplicativo móvel de plataforma cruzada que executa um bot (conforme descrito neste artigo) em <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/capability-BotInApps" target="_blank">Exemplo de Bot em aplicativos</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- [API de Linha Direta][directLineAPI]
- <a href="https://www.microsoft.com/cognitive-services/" target="_blank">Serviços Cognitivos da Microsoft</a>

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle