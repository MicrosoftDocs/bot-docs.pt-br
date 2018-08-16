---
title: Visualizar os recursos de bot com o Inspetor de Canal | Microsoft Docs
description: Veja o desempenho de vários recursos do Bot Framework em diferentes canais usando o Inspetor de Canal.
keyword: bot, preview features, channel, display, Channel Inspector, Text formatting, markdown, XML
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: def3f811704af2e2a22612ba5755eb5dbd2d8044
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296804"
---
# <a name="preview-bot-features-with-the-channel-inspector"></a>Visualizar os recursos de bot com o Inspetor de Canal

Com o Bot Framework, você pode criar bots com vários recursos, como texto, botões, imagens, cartões avançados exibidos em formato carrossel ou de lista e muito mais. No entanto, cada canal controla como os recursos são renderizados por seus clientes de mensagens.

Mesmo quando um recurso tem suporte de vários canais, cada canal pode renderizar o recurso de um jeito ligeiramente diferente. Em casos nos quais uma mensagem contém um ou mais recursos sem suporte nativo de canal, o canal pode tentar simplificar a renderização do conteúdo da mensagem como texto ou imagem estática, o que pode afetar consideravelmente a aparência da mensagem no cliente. Em alguns casos, talvez um canal não dê suporte algum a um recurso específico. Por exemplo, clientes do GroupMe não podem exibir um indicador de digitação.

## <a name="the-channel-inspector"></a>O Inspetor de Canal

O [Inspetor de Canal][inspector] foi criado para proporcionar uma prévia do desempenho de vários recursos do Bot Framework em diferentes canais. Ao entender como os recursos são renderizados por vários canais, você poderá criar seu bot para oferecer uma experiência de usuário excepcional nos canais onde ele se comunica. O Inspetor de Canal também oferece uma ótima maneira de explorar visualmente e aprender sobre os recursos do Bot Framework.

> [!NOTE]
> Os cartões avançados são um padrão de desenvolvimento para troca de informações do bot com o objetivo de garantir uma exibição consistente entre vários canais. Saiba mais sobre cartões avançados na documentação do [.NET][netcard] ou do [Node.js][nodecard].

## <a name="text-formatting"></a>Formatação de texto

A formatação de texto pode aprimorar suas mensagens de texto visualmente. Além de texto **sem formatação**, seu bot pode enviar mensagens de texto usando a formatação **markdown** ou **xml** para os canais que dão suporte a isso. As tabelas a seguir listam algumas das formatações de texto mais comuns em **markdown** e **xml**. Cada canal pode oferecer suporte a mais ou menos opções de formatação. Verifique o [Inspetor de Canal][inspector] para ver se um recurso que você quer usar tem suporte em um canal que você selecionar.

### <a name="markdown-text-format"></a>Formato de texto markdown

Esses estilos podem ter suporte quando `textFormat` estiver definido como **markdown**:  

| Estilo | Markdown | Exemplo |
| ---- | ---- | ---- |
| negrito | \*\*text\*\* | **text** |
| itálico | \*text\* | *text* |
| cabeçalho (1-5) | # H1 | # H1 |
| tachado | \~\~text\~\~ | ~~text~~ |
| régua horizontal | ---- | ---- |
| lista não ordenada | \* texto |  <ul><li>text</li></ul> |
| lista ordenada | 1. texto | 1. texto |
| texto pré-formatado | \`text\` | `text`  |
| blockquote | \> texto | <blockquote>text</blockquote> |
| hiperlink | \[bing](http://www.bing.com) | [bing](http://www.bing.com) |
| link da imagem| !\[pato](http://aka.ms/Fo983c) | ![pato](http://aka.ms/Fo983c) |

> [!NOTE]
> As marcas HTML em **Markdown** não têm suporte para canais de Chat na Web do Microsoft Bot Framework. Se você precisar usar marcas HTML em seu **Markdown**, poderá processá-las em um canal de [Linha Direta](~/bot-service-channel-connect-directline.md) que ofereça suporte a isso. Como alternativa, você pode usar as marcas HTML abaixo configurando `textFormat` como **xml**, e conectar seu bot ao canal do Skype.

### <a name="xml-text-format"></a>Formato de texto XML

Esses estilos podem ter suporte quando `textFormat` estiver definido como **xml**:

|      Estilo      |                       XML                       |                   Exemplo                   |
|-----------------|-------------------------------------------------|---------------------------------------------|
|      negrito       |                 \<b\>texto\</b\>                 |            <strong>text</strong>            |
|     itálico      |                 \<i\>texto\</i\>                 |                <em>text</em>                |
|    sublinhado    |                 \<u\>texto\</u\>                 |                 <u>text</u>                 |
|  tachado  |                 \<s\>texto\</s\>                 |                 <s>text</s>                 |
|    hiperlink    |   \<a href="http://www.bing.com"\>bing\</a\>    |   <a href="http://www.bing.com">bing</a>    |
|    parágrafo    |                 \<p\>texto\</p\>                 |                 <p>text</p>                 |
|   quebra de linha    |                     \<br/\>                     |             linha 1 <br/>linha 2              |
| régua horizontal |                     \<hr/\>                     |                    <hr/>                    |
|  cabeçalho (1-4)   |                \<h1\>texto\</h1\>                |             <h1>Cabeçalho 1</h1>              |
|      código       |           \<code\>bloco de código\</code\>           |           <code>code block</code>           |
|      image      | \<img src="<http://aka.ms/Fo983c>" alt="Pato"\> | <img src="http://aka.ms/Fo983c" alt="Duck"> |

> [!NOTE]
> O `textFormat` **xml** tem suporte apenas do canal do Skype.

## <a name="preview-features-across-various-channels"></a>Visualizar recursos em vários canais

Para ver como um canal renderiza um determinado recurso, acesse o [Inspetor de Canal][inspector], selecione o canal na lista de **Canais** e o recurso na lista **Recursos**. Por exemplo, para ver como o Skype renderiza um Cartão de Destaque, defina **Canal** como *Skype* e **Recurso** como *HeroCard*.

![Inspetor de Canal mostrando o canal Skype e o Cartão de Destaque](~/media/bot-service-channel-inspector.png)

O Inspetor de Canal mostra uma prévia do recurso selecionado, da forma como ele será renderizado pelo canal especificado. A seção **Notas** transmite informações importantes sobre as limitações de mensagem e/ou alterações de exibição. Por exemplo, alguns tipos de cartões avançados dão suporte a apenas uma imagem e a renderização de alguns recursos pode ser simplificada em determinados canais.

> [!NOTE]
> No momento, o Inspetor de Canal dá suporte a esses canais:
> * Cortana
> * Email
> * Facebook
> * GroupMe
> * Kik
> * Skype
> * Skype for Business
> * Margem de atraso
> * sms
> * Equipes da Microsoft
> * Telegram
> * WeChat
> * Chat na Web
>
> É possível adicionar outros canais no futuro.

## <a name="features-that-can-be-previewed"></a>Recursos que permitem uma prévia

No momento, o Inspetor de Canal permite que você tenha uma prévia dos recursos a seguir.

|Recurso | DESCRIÇÃO|
| --- | ----|
| Cartões Adaptáveis | Um cartão que pode conter qualquer combinação de texto, fala, imagens, botões e campos de entrada. |
| Botões| Botões nos quais o usuário pode clicar. Os botões aparecem na tela de conversa com a mensagem a qual pertencem. |
| Carrossel| Uma lista horizontal, compacta e com rolagem dos cartões. Para usar um layout vertical, use Lista.|
| ChannelData| Uma maneira de passar metadados para acessar a funcionalidade específica do canal, além de cartões, texto e anexos.|
| Codesample| Um bloco de texto pré-formatado e com várias linhas projetado para exibir o código de computador.|
| DirectMessage| Uma mensagem enviada a um único membro de uma conversa em grupo.
| Documento| Envie e receba anexos que não são mídias. |
| Emoji| Exiba emoji com suporte.
| GroupChat| O Bot envia mensagens para participar de conversas em grupo. |
| HeroCard| Um anexo formatado que normalmente contendo uma única imagem grande, uma ação de toque e botões (opcional), juntamente com o conteúdo de texto descritivo. |
| Imagens| Exibição de anexos de imagem. |
| Layout de Lista| Uma lista vertical de cartões. Para usar um layout horizontal, rolável, use Carrossel.|
| Localização| Compartilhe o local físico do usuário com o bot. |
| Markdown| Renderiza o texto formatado com Markdown.|
| Membros| Compartilha a lista de membros na conversa com o bot durante um chat em grupo. |
| Cartão de Recebimento| Exiba uma confirmação para o usuário. |
| Cartão de Entrada| Solicite ao usuário credenciais de autenticação.|
| Ações Sugeridas | Ações apresentadas como botões, nos quais o usuário pode tocar para fornecer entrada. |
| Cartão de Miniatura| Um anexo formatado contendo uma única imagem pequena (miniatura), uma ação de toque e botões (opcional), juntamente com o conteúdo de texto descritivo. |
| Digitação| Exibe um indicador de digitação. Isso é útil para informar ao usuário que o bot ainda está funcionando, mas está executando alguma ação em segundo plano.|
| Vídeo| Exibe anexos de vídeo e controles de reprodução.|

## <a name="additional-resources"></a>Recursos adicionais

* [Inspetor de Canal][inspector]
* Cartões avançados em [Node.js][nodecard] e [.NET][netcard]
* Anexos de mídia em [Node.js][nodemedia] e [.NET][netmedia]
* Ações sugeridas em [Node.js][nodebutton] e [.NET][netbutton]

[inspector]: https://docs.botframework.com/en-us/channel-inspector/channels/Skype/

[syntax]: https://daringfireball.net/projects/markdown/syntax

[netcard]: ~/dotnet/bot-builder-dotnet-add-rich-card-attachments.md
[nodecard]: ~/nodejs/bot-builder-nodejs-send-rich-cards.md

[netmedia]: ~/dotnet/bot-builder-dotnet-add-media-attachments.md
[nodemedia]: ~/nodejs/bot-builder-nodejs-send-receive-attachments.md

[netbutton]: ~/dotnet/bot-builder-dotnet-add-suggested-actions.md
[nodebutton]: ~/nodejs/bot-builder-nodejs-send-suggested-actions.md
