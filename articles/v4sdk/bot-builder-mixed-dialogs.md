---
title: Criar um bot usando diálogos adaptáveis, de componente e em cascata – Serviço de Bot
description: Saiba como gerenciar um fluxo de conversa com diálogos convencionais e adaptáveis no SDK do Bot Framework.
keywords: fluxo de conversa, diálogos, diálogos de componente, diálogos personalizados, diálogos em cascata, diálogos adaptáveis
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/26/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 84d60fb20e24be7d4eef7ebe261ac4fd184f4655
ms.sourcegitcommit: 5add21ad3daf0ce894612a22b951b98350961720
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420283"
---
# <a name="create-a-bot-using-adaptive-component-waterfall-and-custom-dialogs"></a>Criar um bot usando diálogos adaptáveis, de componente, em cascata e personalizados

[!INCLUDE[applies-to](../includes/applies-to.md)]

Todos os diálogos derivam de uma classe de _diálogo_ base.
Se você usar o _gerenciador de diálogos_ para executar o diálogo raiz, todas as classes de diálogo poderão trabalhar juntas.
Este artigo mostra como usar diálogos de componente, em cascata, personalizados e adaptáveis em um só bot.

Ele se concentra no código que permite que esses diálogos funcionem juntos. Confira as [informações adicionais](#additional-information) para obter artigos que abordam cada tipo de diálogo mais detalhadamente.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento das [noções básicas sobre bots][bot-basics], do [gerenciamento de estado][concept-state], da [biblioteca de diálogos][about-dialogs] e dos [diálogos adaptáveis][about-adaptive-dialogs].
- Uma cópia da amostra de **caixa de diálogo em cascata ou personalizada com adaptável** em [**C#** ][cs-sample], [**JavaScript** (versão prévia)][js-sample]

### <a name="preliminary-steps-to-add-an-adaptive-dialog-to-a-bot"></a>Etapas preliminares para adicionar um diálogo adaptável a um bot

Você precisará seguir as etapas descritas abaixo para adicionar um diálogo adaptável a um bot.
Essas etapas são abordadas mais detalhadamente em como [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].

#### <a name="c"></a>[C#](#tab/csharp)

1. Atualize todos os pacotes NuGet do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `Microsoft.Bot.Builder.Dialogs.Adaptive` ao seu projeto de bot.
1. Atualize o adaptador de bot para adicionar o armazenamento e os objetos de estado de conversa e usuário a cada contexto de turno.
1. Use um gerenciador de diálogos no código do bot para iniciar ou continuar o diálogo raiz em cada turno.

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Atualize todos os pacotes npm do Bot Builder para a versão 4.9.x.
1. Adicione o pacote `botbuilder-dialogs-adaptive` ao seu projeto de bot.
1. No manipulador no turno do bot:
   1. Crie um gerenciador de diálogos.
   1. Defina as propriedades de armazenamento e de estado do usuário e da conversa do gerenciador de diálogos.
   1. Use o gerenciador de diálogos para iniciar ou continuar o diálogo raiz.

---

## <a name="about-the-sample"></a>Sobre o exemplo

A título de ilustração, esta amostra combina vários tipos de diálogos juntos em um só bot.
Ela não demonstra as melhores práticas de criação do fluxo de conversa.
A amostra:

- Define uma classe de diálogo de _preenchimento de slot_ personalizada.
- Cria um diálogo de componente raiz:
  - Um diálogo em cascata gerencia o fluxo de conversa de nível superior.
  - Juntos, um diálogo adaptável e dois diálogos de preenchimento de slot personalizados gerenciam o restante do fluxo de conversa.

> [!div class="mx-imgBorder"]
> ![fluxo de diálogo](media/adaptive-mixed-dialogs-flow.png)

Os diálogos personalizados de preenchimento de slot aceitam uma lista de propriedades (slots a serem preenchidos). Cada diálogo personalizado solicitará os valores ausentes até que todos os slots sejam preenchidos.
A amostra _associa_ uma propriedade ao diálogo adaptável para permitir que o diálogo adaptável também preencha os slots.

Este artigo se concentra em como os vários tipos de diálogos funcionam juntos.
Para obter informações sobre como configurar o bot para usar diálogos adaptáveis, confira como [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].
Para obter mais informações sobre como usar diálogos adaptáveis para coletar a entrada do usuário, confira [Sobre as entradas em diálogos adaptáveis][about-input-dialogs].

## <a name="the-custom-slot-filling-dialogs"></a>Os diálogos personalizados de preenchimento de slot

Um diálogo personalizado é qualquer diálogo derivado de uma das classes de diálogos no SDK e que substitua um ou mais métodos de diálogos básicos: _iniciar diálogo_, _continuar diálogo_, _retomar diálogo_ ou _encerrar diálogo_.

Ao criar o diálogo de preenchimento de slot, forneça uma lista de definições para os _slots_ que o diálogo preencherá.
O diálogo substitui os métodos iniciar, continuar e retomar diálogo para solicitar de maneira iterativa que o usuário preencha cada slot.
Quando todos os slots são preenchidos, o diálogo é encerrado e retorna as informações coletadas.

Cada definição de slot inclui o nome da solicitação de diálogo com a qual as informações serão coletadas.

O diálogo raiz cria dois diálogos de preenchimento de slot, um para coletar o nome completo do usuário e outro para coletar o endereço. Ele também cria a _solicitação de texto_ que esses dois diálogos usam para preencher os slots.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\SlotDetails.cs**

A classe `SlotDetails` descreve as informações a serem coletadas e a solicitação com a qual ela será coletada.

[!code-csharp[slot details](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotDetails.cs?range=9-39)]

**Dialogs\SlotFillingDialog.cs**

A classe `SlotFillingDialog` é derivada da classe `Dialog` base.

Ela acompanha os valores coletados, qual slot é solicitado por último e os detalhes dos slots a serem preenchidos.

[!code-csharp[slot-filling fields and constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotFillingDialog.cs?range=23-37)]

A lógica principal para coletar informações ausentes está no método auxiliar `RunPromptAsync`. Quando todas as informações forem coletadas, ela encerrará o diálogo e retornará as informações.

[!code-csharp[slot-filling RunPromptAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/SlotFillingDialog.cs?range=121-151&highlight=23-24,28-29)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/slotDetails.js**

A classe `SlotDetails` descreve as informações a serem coletadas e a solicitação com a qual ela será coletada.

[!code-javascript[slot details](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotDetails.js?range=4-26)]

**dialogs/slotFillingDialog.js**

A classe `SlotFillingDialog` estende a classe `Dialog` base.

[!code-javascript[slot-filling constants](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=7-12)]

[!code-javascript[slot-filling constructor](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=15-30)]

A lógica principal para coletar informações ausentes está no método auxiliar `RunPromptAsync`.
Ela acompanha os valores coletados, qual slot é solicitado por último e os detalhes dos slots a serem preenchidos.
Quando todas as informações forem coletadas, ela encerrará o diálogo e retornará as informações.

[!code-javascript[slot-filling runPrompt](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/slotFillingDialog.js?range=84-109&hihglight=19-20,22-23)]

---

Para obter mais informações sobre a implementação de diálogos personalizados, confira a discussão do diálogo _cancelar e ajudar_ em como [Tratar as interrupções do usuário][interruptions-how-to].

## <a name="the-root-component-dialog"></a>O diálogo de componente raiz

O diálogo raiz:

- Define todos os slots a serem preenchidos, para si mesmo, para os dois diálogos de preenchimento de slot e para o diálogo adaptável.
- Cria um acessador de propriedade de estado do usuário no qual as informações coletadas serão salvas.
- Cria o diálogo adaptável, os dois diálogos de preenchimento de slot, um diálogo em cascata e as solicitações a serem usadas com os diálogos de preenchimento de slot e em cascata.
- Define o diálogo em cascata como o diálogo inicial a ser executado quando o componente for iniciado pela primeira vez.

O diálogo em cascata agregará todas as informações coletadas e salvará o estado do usuário.

Os diálogos em cascata e adaptável são descritos nas seções a seguir.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

A classe `RootDialog` é um `ComponentDialog`. Ela define a propriedade de estado do usuário na qual as informações coletadas serão salvas.

[!code-csharp[class and constructor opening](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=25-32)]

Seu construtor cria todas as caixas de diálogo de que precisa, incluindo uma caixa de diálogo adaptável `adaptiveSlotFillingDialog`.

Em seguida, ele adiciona todas essas caixas de diálogo ao seu conjunto de caixas de diálogo.

[!code-csharp[add dialogs plus closing](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=124-142)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

A classe `RootDialog` estende `ComponentDialog`. Ela define a propriedade de estado do usuário na qual as informações coletadas serão salvas.

[!code-javascript[class and constructor opening](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=16-23)]

Seu construtor cria todas as caixas de diálogo de que precisa, incluindo uma caixa de diálogo adaptável `adaptiveSlotFillingDialog`.

Em seguida, ele adiciona todas essas caixas de diálogo ao seu conjunto de caixas de diálogo.

[!code-javascript[add dialogs plus closing](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=89-111)]

---

## <a name="the-waterfall-dialog"></a>O diálogo em cascata

O diálogo em cascata contém três etapas:

1. Inicia o diálogo de preenchimento de slot "fullname", que coletará e retornará o nome completo do usuário.
1. Registra o nome do usuário e inicia o diálogo adaptável, que coletará o restante das informações do usuário.
1. Grava as informações do usuário no acessador de propriedade de estado do usuário e resume para o usuário as informações coletadas.

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

[!code-csharp[Waterfall steps](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=162-202)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

[!code-javascript[Waterfall steps](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=113-145)]

---

## <a name="the-adaptive-dialog"></a>O diálogo adaptável

O diálogo adaptável define um gatilho que é executado quando o diálogo é iniciado. O gatilho executará estas ações:

1. Usa um diálogo de entrada para solicitar a idade do usuário.
1. Usa um diálogo de entrada para solicitar o tamanho do calçado do usuário.
1. Inicia o diálogo de preenchimento de slot "address" para coletar o endereço do usuário.
1. Define o valor do resultado do gatilho e é encerrado.

Como nenhuma outra ação será colocada na fila, o diálogo adaptável também será encerrado e retornará esse valor do resultado.

O diálogo adaptável usa um gerador de linguagem para formatar o texto e incluir os valores do bot e do estado do diálogo. (Confira como usar [Geradores em diálogos adaptáveis][lg-in-adaptive] para obter mais informações.)

### <a name="c"></a>[C#](#tab/csharp)

**Dialogs\RootDialog.cs**

[!code-csharp[adaptive dialog and triggers](~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive/Dialogs/RootDialog.cs?range=60-122&highlight=1-10)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**dialogs/rootDialog.js**

[!code-javascript[adaptive dialog and triggers](~/../botbuilder-samples/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive/dialogs/rootDialog.js?range=39-87&highlight=1-10)]

---

## <a name="to-test-the-bot"></a>Para testar o bot

1. Se ainda não tiver feito isso, instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Execute o exemplo localmente em seu computador.
1. Inicie o emulador, conecte-se ao bot e responda às solicitações: nome e sobrenome, tamanho do calçado, rua, cidade e CEP.
1. O bot exibirá as informações coletadas.
1. Envie ao bot qualquer mensagem para iniciar o processo novamente.

## <a name="additional-information"></a>Informações adicionais

Para obter mais informações sobre como usar cada tipo de diálogo, confira:

| Tipo de diálogo | Artigo
|:-|:-
| Diálogos adaptáveis e de entrada | [Criar um bot usando diálogos adaptáveis][basic-adaptive-how-to].
| Diálogos de componente | [Gerenciar a complexidade da caixa de diálogo][component-how-to]
| Diálogos personalizados | [Manipular interrupções do usuário][interruptions-how-to]
| Diálogos de solicitação e em cascata | [Implementar o fluxo de conversa sequencial][basic-dialog-how-to]

<!--
## Next steps
> [!div class="nextstepaction"]
> [TBD](tbd.md)
-->

<!-- Footnote-style links -->

[bot-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[about-dialogs]: bot-builder-concept-dialog.md
[about-adaptive-dialogs]: bot-builder-adaptive-dialog-Introduction.md
[about-input-dialogs]: bot-builder-concept-adaptive-dialog-inputs.md

[lg-in-adaptive]: bot-builder-concept-adaptive-dialog-generators.md

[basic-adaptive-how-to]: bot-builder-dialogs-adaptive.md
[basic-dialog-how-to]: bot-builder-dialog-manage-conversation-flow.md
[component-how-to]: bot-builder-compositcontrol.md
[interruptions-how-to]: bot-builder-howto-handle-user-interrupt.md

[cs-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/04.waterfall-or-custom-dialog-with-adaptive
[js-sample]: https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/javascript_nodejs/04.waterfall-or-custom-dialog-with-adaptive
