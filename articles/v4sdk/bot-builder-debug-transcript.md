---
title: Depurar o bot usando arquivos de transcrição – Serviço de Bot
description: Saiba como usar arquivos de transcrição para depurar bots. Veja como criar e recuperar esses arquivos, que fornecem conjuntos detalhados de interações de usuário e respostas de bot.
keywords: depuração, perguntas frequentes, arquivo de transcrição, emulador
author: DanDev33
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservices: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6f9a75af687e10985c61cc925ebf61cec2ddb219
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414306"
---
# <a name="debug-your-bot-using-transcript-files"></a>Depurar seu bot usando arquivos de transcrição

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Uma das chaves para testar e depurar um bot com sucesso é sua capacidade de registrar e examinar o conjunto de condições que ocorrem durante a execução do bot. Este artigo aborda a criação e uso de um arquivo de transcrição do bot para fornecer um conjunto detalhado de interações do usuário e respostas do bot para teste e depuração.

## <a name="the-bot-transcript-file"></a>O arquivo de transcrição do bot
Um arquivo de transcrição do bot é um arquivo JSON especializado que preserva as interações entre um usuário e seu bot. Um arquivo de transcrição preserva não apenas o conteúdo de uma mensagem, mas também os detalhes da interação como a id de usuário, id do canal, tipo de canal, recursos do canal, a hora do interação, etc. Todas essas informações podem posteriormente ser usadas para ajudar a encontrar e resolver problemas ao testar ou depurar seu bot. 

## <a name="creatingstoring-a-bot-transcript-file"></a>Criar/armazenar um arquivo de transcrição de bot
Este artigo mostra como criar arquivos de transcrição de bot usando o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator) da Microsoft. Os arquivos de transcrição também podem ser criados programaticamente. Leia mais sobre essa abordagem [aqui](./bot-builder-howto-v4-storage.md#blob-transcript-storage). Neste artigo, usaremos o código de exemplo do Bot Framework para [Multi Turn Prompt Bot](https://aka.ms/cs-multi-prompts-sample) que solicita o modo de transporte, o nome e a idade do usuário, mas qualquer código que possa ser acessado usando o Microsoft Bot Framework Emulator pode ser usado para criar um arquivo de transcrição.

Para iniciar esse processo, verifique se o código do bot que você deseja testar está em execução dentro de seu ambiente de desenvolvimento. Inicie o Bot Framework Emulator, selecione o botão _Abrir Bot_ e, em seguida, insira o endereço _localhost: port_ mostrado em seu navegador seguido por "/api/messages", conforme mostrado na imagem abaixo. Agora clique no botão _Conectar_ para conectar o emulador ao seu bot.

![conectar o emulador ao seu código](./media/emulator_open_bot_configuration.png)

Depois de conectar o emulador ao código em execução, teste o código enviando as interações simuladas do usuário ao bot. Neste exemplo passamos o modo de transporte, o nome e a idade do usuário. Depois de inserir todas as interações do usuário que você deseja preservar, use o Bot Framework Emulator para criar e salvar um arquivo de transcrição que contém essa conversa. 

Na guia _Chat ao vivo_ (mostrada abaixo), escolha o botão _Salvar transcrição_. 

![escolher salvar transcrição](./media/emulator_transcript_save.png)

Escolha um local e um nome para o arquivo de transcrição e, em seguida, escolha o botão salvar.

![transcrição salvar como ursula](./media/emulator_transcript_saveas_ursula.png)

Todas as interações do usuário e as respostas do bot inseridas para testar seu código com o emulador agora foram salvas em um arquivo de transcrição que você pode recarregar posteriormente para ajudar a depurar as interações entre um usuário e seu bot.

## <a name="retrieving-a-bot-transcript-file"></a>Recuperar um arquivo de transcrição de bot
Para recuperar um arquivo de transcrição de bot usando o Bot Framework Emulator, selecione _Arquivo_ e _Abrir Transcrição..._ no canto superior esquerdo do emulador, conforme mostrado abaixo. Em seguida, escolha o arquivo de transcrição que você deseja recuperar. (As transcrições também podem ser acessadas de dentro do controle de lista _TRANSCRIÇÕES_ na seção _Recursos_ do emulador) 

Neste exemplo estamos recuperando o arquivo de transcrição denominado “ursula_user.transcript”. Escolher um arquivo de transcrição carregará automaticamente toda a conversa preservada em uma nova guia chamada _Transcrição_.

![recuperar transcrição salva](./media/emulator_transcript_retrieve.png)

## <a name="debug-using-transcript-file"></a>Depurar usando o arquivo de transcrição
Com seu arquivo de transcrição carregado, você agora está pronto para depurar as interações que capturadas entre um usuário e seu bot. Para fazer isso, basta clicar em qualquer evento ou atividade registrada na seção _LOG_ exibida na parte inferior direita do emulador. No exemplo mostrado abaixo, escolhemos a primeira interação do usuário quando ele enviou a mensagem "Olá". Quando fazemos isso, todas as informações no arquivo de transcrição relacionadas a essa interação específica são exibidas na janela _INSPETOR_ do emulador no formato JSON. Ao observar alguns desses valores de baixo para cima, podemos ver:
* O tipo de interação era _mensagem_.
* A hora em que a mensagem foi enviada.
* Que o texto sem formatação enviado continha "Sim".
* Que a mensagem foi enviada ao nosso bot.
* A id e as informações do usuário.
* A id, recursos e informações do canal.

![depurar usando transcrição](./media/emulator_transcript_debug.png)

Esse nível detalhado de informações permite que você siga as interações passo a passo entre a entrada do usuário e a resposta do bot, o que é útil para situações de depuração em que o bot não responde de volta da maneira esperada ou simplesmente não responde ao usuário. Ter esses valores e um registro das etapas até a interação com falha permite percorrer o código, localizar o local onde o bot não responde conforme o esperado e resolver esses problemas.

Usar arquivos de transcrição juntamente com o Bot Framework Emulator é apenas uma das muitas ferramentas que você pode usar para ajudá-lo a testar e depurar o código do bot e as interações do usuário. Para ver mais maneiras de testar e depurar seu bot, confira os recursos adicionais listados abaixo.

## <a name="additional-information"></a>Informações adicionais

Para outras informações sobre teste e depuração confira:

* [Diretrizes para teste e depuração de bot](./bot-builder-testing-debugging.md)
* [Depurar com o Bot Framework Emulator](../bot-service-debug-emulator.md)
* [Solucionar problemas gerais](../bot-service-troubleshoot-bot-configuration.md) e outros artigos de solução de problemas nesta seção.
* [Depurando no Visual Studio](https://docs.microsoft.com/visualstudio/debugger/index)
