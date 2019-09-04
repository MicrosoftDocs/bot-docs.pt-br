---
title: Testar uma habilidade da Cortana | Microsoft Docs
description: Saiba como testar um bot da Cortana invocando uma habilidade da Cortana.
keywords: SDK do Bot Framework, registrar o bot, Cortana
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/01/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 996063bef2e139292187f125318f63745373e7bb
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70166942"
---
# <a name="test-a-cortana-skill"></a>Testar uma habilidade da Cortana

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]
 
Se você criou uma habilidade da Cortana usando o SDK do Bot Framework, invoque-a na Cortana para testá-la. As instruções a seguir orientam você pelas etapas necessárias para testar a habilidade da Cortana.

## <a name="register-your-bot"></a>Registrar o bot
Se você [criou o bot](~/bot-service-quickstart.md) usando o Serviço de Bot no Azure, isso significa que o bot já está registrado e você pode ignorar esta etapa.

Se você implantou o bot em outro lugar ou se desejar testar o bot localmente, [registre](bot-service-quickstart-registration.md) o bot para conectá-lo à Cortana. Durante o processo de registro, você precisará fornecer o **ponto de extremidade de Mensagens** do bot. Caso você opte por testar o bot localmente, precisará executar um software de túnel, como o [ngrok](http://ngrok.com), para obter um ponto de extremidade para o bot local.

## <a name="get-messaging-endpoint-using-ngrok"></a>Obter o ponto de extremidade de mensagens usando o ngrok

Se estiver executando o bot localmente, obtenha um ponto de extremidade para uso em testes executando um software de túnel, como o [ngrok](https://ngrok.com). Para usar o ngrok para obter um ponto de extremidade, em uma janela de console, digite: 

```cmd
 ngrok.exe http 3979 -host-header="localhost:3979"
``` 

Isso configura e exibe um link de encaminhamento do ngrok que encaminha solicitações ao bot, que é executado na porta 3978. A URL para o link de encaminhamento deve ser semelhante a esta: `https://0d6c4024.ngrok.io`.  Acrescente `/api/messages` ao link para formar uma URL de ponto de extremidade de mensagens neste formato: `https://0d6c4024.ngrok.io/api/messages`. 

Insira esta URL de ponto de extremidade na seção **Configuração** da folha [Configurações](~/bot-service-manage-settings.md) do bot.

## <a name="enable-speech-recognition-priming"></a>Habilitar a desobstrução de reconhecimento de fala
Se o bot usa um aplicativo LUIS (Reconhecimento vocal), lembre-se de associar a ID do aplicativo LUIS ao serviço de bot registrado. Isso ajuda o bot a reconhecer enunciados falados que são definidos no modelo LUIS. Para obter mais informações, confira [Desobstrução de fala](~/bot-service-manage-speech-priming.md).

## <a name="add-the-cortana-channel"></a>Adicionar o canal da Cortana
Para adicionar a Cortana como um canal, siga as etapas listadas em [Conectar um bot à Cortana](bot-service-channel-connect-cortana.md).

## <a name="test-using-web-chat-control"></a>Testar usando o controle de Webchat

Para testar o bot usando o controle de Webchat integrado no Serviço de Bot, clique em **Testar no Webchat** e digite uma mensagem para verificar se o bot está funcionando.

## <a name="test-using-emulator"></a>Testar usando o emulador

Para testar o bot usando o [emulador](~/bot-service-debug-emulator.md), faça o seguinte:

1. Execute o bot.
2. Abra o emulador e preencha as informações necessárias. Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword). 
3. Clique em **Conectar** para conectar o emulador ao bot.
4. Digite uma mensagem para verificar se o bot está funcionando.

## <a name="test-using-cortana"></a>Testar usando a Cortana
Invoque a habilidade da Cortana falando uma frase de invocação para a Cortana. 
1. Abra a Cortana.
2. Abra o Caderno na Cortana e clique em **Sobre mim** para ver qual conta você está usando para a Cortana. Verifique se você está conectado com a mesma conta Microsoft usada para registrar o bot. 
   ![Entrar no Caderno da Cortana](~/media/cortana/cortana-notebook.png)
2. Clique no botão de microfone no aplicativo da Cortana ou na caixa de pesquisa "Pergunte-me alguma coisa" do Windows e diga a [frase de invocação][InvocationNameGuidelines] do bot. A frase de invocação inclui um *nome de invocação*, que identifica exclusivamente a habilidade a ser invocada. Por exemplo, se o nome de invocação de uma habilidade for "Northwind Photo", uma frase de invocação adequada poderá incluir "Solicite à Northwind Photo para..." ou "Diga à Northwind Photo que...".

   Você especifica o *nome de invocação* do bot quando o configura para a Cortana.
   ![Inserir o nome de invocação ao configurar o canal da Cortana](~/media/cortana/cortana-invocation-name-callout.png)

3. Se a Cortana reconhecer sua frase de invocação, o bot será iniciado na tela da Cortana. 

## <a name="troubleshoot"></a>Solucionar problemas

Se a habilidade da Cortana não puder ser iniciada, verifique o seguinte:
* Verifique se você está conectado à Cortana usando a mesma conta Microsoft usada para registrar o bot no Portal do Bot Framework.
* Verifique se o bot está funcionando clicando em **Testar no Webchat** para abrir a janela **Chat** e digitando uma mensagem nela.
* Verifique se o nome de invocação atende às [diretrizes][InvocationNameGuidelines]. Caso o nome de invocação tenha mais de três palavras, seja difícil de pronunciar ou seja semelhante a outras palavras, a Cortana poderá ter dificuldade para reconhecê-lo.
* Se a habilidade usar um modelo LUIS, lembre-se de [habilitar a desobstrução de reconhecimento de fala](~/bot-service-manage-speech-priming.md).

Confira [Habilitar depuração das habilidades da Cortana][Cortana-TestBestPractice] para obter mais dicas de solução de problemas e informações sobre como habilitar a depuração das habilidades no painel da Cortana. 


## <a name="next-steps"></a>Próximas etapas

Depois de testar a habilidade da Cortana e verificar que ela funciona conforme esperado, implante-a em um grupo de testadores beta ou libere-a para o público. Confira [Publicando as Habilidades da Cortana][Cortana-Publish] para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais
* [O Kit de Habilidades da Cortana][CortanaGetStarted]

[CortanaGetStarted]: /cortana/getstarted

[BFPortal]: https://dev.botframework.com/
[CortanaDevCenter]: https://developer.microsoft.com/cortana

[CortanaSpecificEntities]: https://aka.ms/cortana-channel-data
[CortanaAuth]: https://aka.ms/add-auth-cortana-skill

[InvocationNameGuidelines]: https://aka.ms/cortana-invocation-guidelines 


[Cortana-Debug]: https://aka.ms/cortana-enable-debug
[Cortana-TestBestPractice]: https://aka.ms/cortana-test-best-practice
[Cortana-Publish]: /cortana/skills/publish-skill
