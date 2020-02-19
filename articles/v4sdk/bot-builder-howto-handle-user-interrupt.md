---
title: Manipular interrupções do usuário – Serviço de Bot
description: Saiba como manipular as interrupções do usuário e orientar o fluxo da conversa.
keywords: interromper, interrupções, mudando o tópico, interrupção
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/24/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0cad95ccb7b83334e514d604da086172320867d8
ms.sourcegitcommit: e5bf9a7fa7d82802e40df94267bffbac7db48af7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441704"
---
# <a name="handle-user-interruptions"></a>Manipular interrupções do usuário

[!INCLUDE[applies-to](../includes/applies-to.md)]

O tratamento de interrupções é um aspecto importante de um bot robusto. Os usuários nem sempre seguirão o fluxo de conversa que você definiu, passo a passo. Eles podem tentar fazer uma pergunta no meio do processo ou simplesmente querer cancelá-lo em vez de concluí-lo. Neste tópico, exploraremos algumas maneiras comuns de lidar com as interrupções dos usuários no seu bot.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre [noções básicas de bots][concept-basics], [gerenciamento de estado][concept-state], a [biblioteca de diálogos][concept-dialogs] e como [reutilizar diálogos][component-dialogs].
- Uma cópia do exemplo principal de bot em [**CSharp**][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-this-sample"></a>Sobre este exemplo

O exemplo usado neste artigo traz um bot de reserva de voo que utiliza caixas de diálogo para obter informações do voo do usuário. A qualquer momento durante a conversa com o bot, o usuário pode solicitar _ajudar_ ou _cancelar_ comandos e gerar uma interrupção. Há dois tipos de interrupções que abordamos aqui:

- **De nível de turno**: ignorar o processamento no nível do turno, mas deixando a caixa de diálogo na pilha com as informações que foram fornecidas. No próximo turno, continuar de onde você parou.
- **De nível de caixa de diálogo**: cancelar o processamento completamente para que o bot comece tudo novamente.

## <a name="define-and-implement-the-interruption-logic"></a>Como definir e implementar a lógica de interrupção

Primeiramente, definimos e implementamos as interrupções de _ajuda_ e _cancelamento_.

# <a name="c"></a>[C#](#tab/csharp)

Para usar as caixas de diálogo, instale o pacote do NuGet, **Microsoft.Bot.Builder.Dialogs**.

**Dialogs\CancelAndHelpDialog.cs**

Começamos com a implementação da classe `CancelAndHelpDialog` para lidar com as interrupções do usuário.

[!code-csharp[Class signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=12)]

Na classe `CancelAndHelpDialog`, o método `OnContinueDialogAsync` chama o método `InerruptAsync` para verificar se o usuário interrompeu o fluxo normal. Se o fluxo for interrompido, os métodos da classe base são chamados; caso contrário, o valor retornado de `InterruptAsync` será retornado.

[!code-csharp[Overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=22-31)]

Se o usuário digitar "help", o método `InterrupAsync` enviará uma mensagem e, em seguida, chama `DialogTurnResult (DialogTurnStatus.Waiting)` para indicar que a caixa de diálogo principal está aguardando uma resposta do usuário. Dessa forma, o fluxo de conversa é interrompido durante somente um turno e, no próximo turno, continuamos de onde paramos.

Se o usuário digitar "cancelar", ele chamará `CancelAllDialogsAsync` em seu contexto interno de caixa de diálogo, e isso limpa sua pilha de caixa de diálogo fazendo com que ele seja fechado com um status de cancelamento e nenhum valor de resultado. Para `MainDialog` (mostrado posteriormente), aparecerá que a caixa de diálogo de reserva foi finalizada e não retornou resultados, da mesma forma quando o usuário decide não confirmar a reserva.

[!code-csharp[Interrupt](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=33-56)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para usar as caixas de diálogo, instale o pacote de npm **botbuilder-dialogs**.

**dialogs/cancelAndHelpDialog.js**

Começamos com a implementação da classe `CancelAndHelpDialog` para lidar com as interrupções do usuário.

[!code-javascript[Class signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=11)]

Na classe `CancelAndHelpDialog`, o método `onContinueDialog` chama o método `interrupt` para verificar se o usuário interrompeu o fluxo normal. Se o fluxo for interrompido, os métodos da classe base são chamados; caso contrário, o valor retornado de `interrupt` será retornado.

[!code-javascript[Overrides](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=12-18)]

Se o usuário digitar "help", o método `interrupt` envia uma mensagem e, em seguida, retorna um objeto `{ status: DialogTurnStatus.waiting }` para indicar que a caixa de diálogo principal está aguardando uma resposta do usuário. Dessa forma, o fluxo de conversa é interrompido durante somente um turno e, no próximo turno, continuamos de onde paramos.

Se o usuário digitar "cancelar", ele chamará `cancelAllDialogs` em seu contexto interno de caixa de diálogo, e isso limpa sua pilha de caixa de diálogo fazendo com que ele seja fechado com um status de cancelamento e nenhum valor de resultado. Para `MainDialog` (mostrado posteriormente), aparecerá que a caixa de diálogo de reserva foi finalizada e não retornou resultados, da mesma forma quando o usuário decide não confirmar a reserva.

[!code-javascript[Interrupt](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=20-39)]

## <a name="python"></a>[Python](#tab/python)

Para usar caixas de diálogo, instale o pacote de `botbuilder-dialogs` e verifique se o arquivo de exemplo `requirements.txt` contém a referência apropriada, como `botbuilder-dialogs>=4.5.0`.
Para obter mais informações sobre como instalar os pacotes, consulte o repositório de exemplos no arquivo [LEIAME](https://github.com/microsoft/botbuilder-python).
> [!NOTE]
> Fazer `pip install botbuilder-dialogs` também instalará `botbuilder-core`, `botbulder-connector` e `botbuilder-schema`.

**dialogs/cancel-and-help-dialog.py**

Começamos com a implementação da classe `CancelAndHelpDialog` para lidar com as interrupções do usuário.

[!code-python[class signature](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/cancel_and_help_dialog.py?range=14)]

Na classe `CancelAndHelpDialog`, o método `on_continue_dialog` chama o método `interrupt` para verificar se o usuário interrompeu o fluxo normal. Se o fluxo for interrompido, os métodos da classe base são chamados; caso contrário, o valor retornado de `InterruptAsync` será retornado.

[!code-python[dialog](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/cancel_and_help_dialog.py?range=18-23)]

Se o usuário digitar *help* ou *?* , o método `interrupt` enviará uma mensagem e, em seguida, chamará `DialogTurnResult(DialogTurnStatus.Waiting)` para indicar que a caixa de diálogo na parte superior da pilha está aguardando uma resposta do usuário. Dessa forma, o fluxo de conversa é interrompido durante somente um turno e, no próximo turno, continuamos de onde paramos.

Se o usuário digitar *cancelar* ou *encerrar*, ele chamará `cancel_all_dialogs()` em seu contexto de caixa de diálogo interno, o que limpa sua pilha de caixa de diálogo e faz com que ele saia com um status cancelado e nenhum valor de resultado. Para `MainDialog`, mostrada posteriormente, aparecerá que a caixa de diálogo de reserva foi finalizada e não retornou resultados, da mesma forma quando o usuário decide não confirmar a reserva.

[!code-python[interrupt](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/cancel_and_help_dialog.py?range=25-47)]

---

## <a name="check-for-interruptions-each-turn"></a>Como verificar se há interrupções em cada turno

Agora que falamos sobre como a classe para lidar com interrupções funciona, vamos voltar e ver o que acontece quando o bot recebe uma nova mensagem do usuário.

# <a name="c"></a>[C#](#tab/csharp)

**Dialogs\MainDialog.cs**

Assim que a nova atividade de mensagem chega, o bot executa o `MainDialog`. O `MainDialog` pergunta ao usuário no que pode ajudar. Então, ele inicia o `BookingDialog` no método `MainDialog.ActStepAsync`, com uma chamada para `BeginDialogAsync`, conforme mostrado abaixo.

[!code-csharp[ActStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=58-101&highlight=6,26)]

Em seguida, no método `FinalStepAsync` da classe `MainDialog`, a caixa de diálogo de reserva é encerrada e a reserva é considerada finalizada ou cancelada.

[!code-csharp[FinalStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=130-150)]

O código em `BookingDialog` não é mostrado aqui, porque não está diretamente relacionado com a manipulação da interrupção. Ele é usado para solicitar os detalhes de reserva aos usuários. Você pode encontrar esse código em **Dialogs\BookingDialogs.cs**.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Assim que a nova atividade de mensagem chega, o bot executa o `MainDialog`. O `MainDialog` pergunta ao usuário no que pode ajudar. Então, ele inicia o `bookingDialog` no método `MainDialog.actStep`, com uma chamada para `beginDialog`, conforme mostrado abaixo.

[!code-javascript[Act step](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=71-115&highlight=6,27)]

Em seguida, no método `finalStep` da classe `MainDialog`, a caixa de diálogo de reserva é encerrada e a reserva é considerada finalizada ou cancelada.

[!code-javascript[Final step](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=142-159)]

O código em `BookingDialog` não é mostrado aqui, porque não está diretamente relacionado com a manipulação da interrupção. Ele é usado para solicitar os detalhes de reserva aos usuários. Você pode encontrar esse código em **dialogs/bookingDialogs.js**.

## <a name="python"></a>[Python](#tab/python)

**dialogs/main_dialog.py**

Assim que a nova atividade de mensagem chega, o bot executa o `MainDialog`. O `MainDialog` pergunta ao usuário no que pode ajudar. Então, ele inicia o `bookingDialog` no método `MainDialog.act_step`, com uma chamada para `begin_dialog`, conforme mostrado abaixo.

[!code-python[act step](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/main_dialog.py?range=63-100&highlight=4-5,20)]

Em seguida, no método `final_step` da classe `MainDialog`, a caixa de diálogo de reserva é encerrada e a reserva é considerada finalizada ou cancelada.

[!code-python[final step](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/main_dialog.py?range=102-118)]

---

## <a name="handle-unexpected-errors"></a>Como lidar com erros inesperados

Em seguida, vamos lidar com exceções sem tratamento que podem ocorrer.

# <a name="c"></a>[C#](#tab/csharp)

**AdapterWithErrorHandler.cs**

Em nosso exemplo, o manipulador do `OnTurnError` do adaptador recebe as exceções geradas pela lógica de turno do seu bot. Se uma exceção for lançada, o manipulador excluirá o estado de conversa da conversa atual para impedir que o bot fique preso em um loop de erro causado por estar em estado inválido.

[!code-csharp[AdapterWithErrorHandler](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/AdapterWithErrorHandler.cs?range=19-50)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

Em nosso exemplo, o manipulador do `onTurnError` do adaptador recebe as exceções geradas pela lógica de turno do seu bot. Se uma exceção for lançada, o manipulador excluirá o estado de conversa da conversa atual para impedir que o bot fique preso em um loop de erro causado por estar em estado inválido.

[!code-javascript[AdapterWithErrorHandler](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/index.js?range=35-57)]

## <a name="python"></a>[Python](#tab/python)

**adapter_with_error_handler.py**

Em nosso exemplo, o manipulador do `on_error` do adaptador recebe as exceções geradas pela lógica de turno do seu bot. Se uma exceção for lançada, o manipulador excluirá o estado de conversa da conversa atual para impedir que o bot fique preso em um loop de erro causado por estar em estado inválido.

[!code-python[adapter_with_error_handler](~/../botbuilder-samples/samples/python/13.core-bot/adapter_with_error_handler.py?range=16-56)]

---

## <a name="register-services"></a>Serviços de registro

# <a name="c"></a>[C#](#tab/csharp)

**Startup.cs**

Por fim, em `Startup.cs`, o bot é criado como transitório e, em cada turno, uma nova instância do bot é criada.

[!code-csharp[Add transient bot](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Startup.cs?range=43-44)]

Para referência, aqui estão as definições de classe que são usadas na chamada para criar o bot acima.

[!code-csharp[MainDialog signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=17)]
[!code-csharp[DialogAndWelcomeBot signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=16)]
[!code-csharp[DialogBot signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Bots/DialogBot.cs?range=18)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

Por fim, em `index.js`, o bot é criado.

[!code-javascript[Create bot](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/index.js?range=78-81)]

Para referência, aqui estão as definições de classe que são usadas na chamada para criar o bot acima.

[!code-javascript[MainDialog signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=11)]
[!code-javascript[DialogAndWelcomeBot signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/bots/dialogAndWelcomeBot.js?range=8)]
[!code-javascript[DialogBot signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/bots/dialogBot.js?range=6)]

## <a name="python"></a>[Python](#tab/python)

**app.py** Por fim, em `app.py`, o bot é criado.

[!code-python[create bot](~/../botbuilder-samples/samples/python/13.core-bot/app.py?range=45-49)]

Para referência, aqui estão as definições de classe que são usadas na chamada para criar o bot.

[!code-python[main dialog](~/../botbuilder-samples/samples/python/13.core-bot/dialogs/main_dialog.py?range=20)]

[!code-python[dialog and welcome](~/../botbuilder-samples/samples/python/13.core-bot/bots/dialog_and_welcome_bot.py?range=21)]

[!code-python[dialog](~/../botbuilder-samples/samples/python/13.core-bot/bots/dialog_bot.py?range=9)]

---

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte ao seu bot e envie mensagens conforme mostrado a seguir.

<!--![test dialog prompt sample](~/media/emulator-v4/test-dialog-prompt.png)-->

## <a name="additional-information"></a>Informações adicionais

- O [exemplo de autenticação](https://aka.ms/logout) mostra como lidar com o logoff que usa um padrão semelhante ao mostrado aqui para lidar com interrupções.

- Você deverá enviar uma resposta padrão em vez de não realizar nenhuma ação e deixar o usuário se perguntando o que está acontecendo. A resposta padrão deve informar o usuário quais comandos são reconhecidos pelo bot, de modo que o usuário possa voltar para um tópico adequado.

- Em qualquer momento durante o turno, a propriedade _respondido_ do contexto do turno indica se o bot enviou uma mensagem ao usuário dessa vez. Antes do fim do turno, seu bot deve enviar alguma mensagem para o usuário, mesmo que seja um simples reconhecimento pelas informações inseridas.

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[using-luis]: bot-builder-howto-v4-luis.md
[using-qna]: bot-builder-howto-qna.md

[simple-flow]: bot-builder-dialog-manage-conversation-flow.md
[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-core-sample
[js-sample]: https://aka.ms/js-core-sample
[python-sample]: https://aka.ms/bot-core-python-sample-code
