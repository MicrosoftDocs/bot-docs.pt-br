---
title: Habilitar a fala no canal de Webchat | Microsoft Docs
description: Saiba como habilitar a fala no controle de Webchat para um bot conectado ao canal de Webchat.
keywords: fala, Webchat, voz, microfone, áudio
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: 37e18f49eb55fcb7d0bf94e96051479753eec8aa
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296747"
---
# <a name="how-to-enable-speech-in-web-chat"></a>Como habilitar a fala no Webchat
Habilite uma interface de voz no controle de Webchat. Os usuários interagem com a interface de voz usando o microfone no controle de Webchat.

![Amostra de fala no Webchat](~/media/bot-service-channel-webchat/webchat-sample-speech.png)

Se o usuário digita em vez de falar uma resposta, o Webchat desativa a funcionalidade de fala e o bot fornece apenas uma resposta textual em vez da fala em voz alta. Para habilitar novamente a resposta falada, o usuário pode usar o microfone para responder ao bot na próxima vez. Se o microfone aceitar a entrada, ele será exibido como escuro ou preenchido. Se ele estiver esmaecido, o usuário clicará nele para habilitá-lo.

## <a name="prerequisites"></a>Pré-requisitos

  Antes de executar a amostra, você precisa ter um segredo da Linha Direta ou um token para o bot que deseja executar usando o controle de Webchat. 
  * Confira [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md) para obter informações sobre como obter um segredo da Linha Direta associado ao bot.
  * Confira [Gerar um token da Linha Direta](rest-api/bot-framework-rest-direct-line-3-0-authentication.md) para obter informações sobre como trocar o segredo de um token.

## <a name="customizing-web-chat-for-speech"></a>Personalizando o Webchat para fala
Para habilitar a funcionalidade de fala no Webchat, você precisa personalizar o código JavaScript que invoca o controle de Webchat. Experimente o Webchat habilitado para voz localmente usando as etapas a seguir.

1. Baixe o [index.html de exemplo](https://aka.ms/web-chat-speech-sample). <!-- this aka.ms link needs to be updated if the sample location changes -->
2. Edite o código em `index.html` de acordo com o tipo de suporte de fala que deseja adicionar. Os tipos de implementações de fala são descritos em [Habilitar serviços de fala](#enable-speech-services). 
3. Inicie um servidor Web. Uma maneira de fazer isso é usar `npm http-server` em um prompt de comando do Node.js.

   * Para instalar `http-server` globalmente para que ele possa ser executado na linha de comando, execute este comando:

     ```
     npm install http-server -g
     ```

   * Para iniciar um servidor Web usando a porta 8000, no diretório que contém `index.html`, execute este comando:

     ```
     http-server -p 8000
     ```
4. Direcione o navegador para `http://localhost:8000/samples?parameters`. Por exemplo, `http://localhost:8000/samples?s=YOURDIRECTLINESECRET` invoca o bot usando um segredo da Linha Direta. Os parâmetros que podem ser definidos na cadeia de caracteres de consulta são descritos na seguinte tabela:

   | Parâmetro | DESCRIÇÃO |
   |-----------|-------------|
   | s | Segredo da Linha Direta. Confira [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md) para obter informações sobre como obter um segredo da Linha Direta. |
   | t | Token da Linha Direta. Confira [Gerar um token da Linha Direta](rest-api/bot-framework-rest-direct-line-3-0-authentication.md) para obter informações sobre como gerar esse token. |
   | domínio | Opcional. A URL de um ponto de extremidade alternativo da Linha Direta.  |
   | webSocket | Opcional. Definido como 'true' para usar o WebSocket para recebimento de mensagens. O padrão é `false`. |
   | userid | Opcional. A ID do usuário do bot.  |
   | Nome de Usuário | Opcional. O nome de usuário do usuário do bot.  |
   | botid | Opcional. ID do bot. |
   | botname | Opcional. Nome do bot. |


## <a name="enable-speech-services"></a>Habilitar os serviços de fala
A personalização permite que você adicione a funcionalidade de fala de uma das seguintes maneiras:

* **Fala fornecida pelo navegador** – use a funcionalidade de fala interna do navegador da Web. No momento, essa funcionalidade só está disponível no navegador Chrome.
* **Usar o serviço de Fala do Bing** – use o serviço de Fala do Bing para fornecer reconhecimento de fala e sintetização de voz. Essa forma de acessar a funcionalidade de fala é compatível com uma variedade de navegadores. Nesse caso, o processamento é concluído em um servidor em vez de no navegador.
* **Criar um serviço de fala personalizado** – você pode criar seus próprios componentes personalizados de reconhecimento de fala e sintetização de voz.

### <a name="browser-provided-speech"></a>Fala fornecida pelo navegador

O código a seguir cria uma instância dos componentes de reconhecedor de fala e sintetização de voz fornecidos com o navegador. Não há suporte para esse método de adição de fala em todos os navegadores. 

> [!NOTE] 
> O Google Chrome dá suporte ao reconhecedor de fala do navegador. No entanto, o Chrome pode bloquear o microfone nos seguintes casos:
> * Se a URL da página que contém o Webchat começa com `http://` em vez de `https://`.
> * Se a URL é um local de arquivo que usa o protocolo `file://`, em vez de `http://localhost:8000`.

[!code-js[Specify speech options to use in-browser speech (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#BrowserSpeech)]

### <a name="bing-speech-service"></a>Serviço de Fala do Bing

O código a seguir cria uma instância dos componentes de reconhecedor de fala e sintetização de voz que usam o serviço de Fala do Bing. O reconhecimento e a geração de fala são executados no servidor. Esse mecanismo é compatível com vários navegadores. 

> [!TIP]
> Use a desobstrução de reconhecimento de fala para melhorar a precisão do reconhecimento de fala do bot se você usar o serviço de Fala do Bing. Para obter mais informações, confira a postagem no blog [Suporte de fala no Bot Framework](https://blog.botframework.com/2017/06/26/Speech-To-Text).

[!code-js[Specify speech options to use the Bing Speech API (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#BingSpeech)]

#### <a name="use-the-bing-speech-service-with-a-token"></a>Usar o serviço de Fala do Bing com um token

Você também tem a opção de habilitar o reconhecimento de fala dos Serviços Cognitivos usando um token. O token é gerado em um back-end seguro usando sua chave de API.

O código de exemplo a seguir mostra como o fetch do token é feito de um back-end seguro para evitar a exposição da chave de API.

[!code-js[Fetch a token to use with the Bing Speech API (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#FetchToken)]

### <a name="custom-speech-service"></a>Serviço de Fala Personalizado

Você também pode fornecer seu próprio reconhecimento de fala personalizado que implementa ISpeechRecognizer ou sua própria sintetização de voz que implementa ISpeechSynthesis. 

[!code-js[Fetch a token to use with a custom speech service (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#CustomSpeechService)]

### <a name="pass-the-speech-options-to-web-chat"></a>Passar as opções de fala para o Webchat

O seguinte código passa as opções de fala para o controle de Webchat:

[!code-js[Pass speech options to Web Chat (JavaScript)](./includes/code/bot-service-channel-connect-webchat-speech.js#PassSpeechOptionsToWebChat)]

## <a name="next-steps"></a>Próximas etapas
Agora que você pode habilitar a interação de voz com o Webchat, saiba como o bot constrói mensagens faladas e ajusta o estado do microfone:
* [Adicionar fala às mensagens (C#)](dotnet/bot-builder-dotnet-text-to-speech.md)
* [Adicionar fala às mensagens (Node.js)](nodejs/bot-builder-nodejs-text-to-speech.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Baixe o código-fonte](https://github.com/Microsoft/BotFramework-WebChat) do controle de Webchat no GitHub.
* A [documentação da API de Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home) fornece mais informações sobre a API de Fala do Bing.

