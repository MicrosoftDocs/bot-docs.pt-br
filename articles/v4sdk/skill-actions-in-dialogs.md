---
title: Usar diálogos em uma habilidade | Microsoft Docs
description: Saiba como usar diálogos em uma habilidade usando o SDK do Bot Framework.
keywords: habilidades
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/23/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2b90828f2febd41a71701f50ce25eac1b76d1b41
ms.sourcegitcommit: 70587e4f57420ea5a64344761af2e2141984234e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555657"
---
# <a name="use-dialogs-within-a-skill"></a>Usar diálogos em uma habilidade

[!INCLUDE[applies-to](../includes/applies-to.md)]

Este artigo demonstra como criar uma habilidade que dá suporte a várias ações. Ela dá suporte a essas ações por meio de diálogos. O diálogo principal recebe a entrada inicial do consumidor de habilidades e inicia a ação apropriada. Para obter informações sobre como implementar o consumidor de habilidades para o código de exemplo associado, confira como [Consumir uma habilidade usando diálogos](skill-use-skilldialog.md).

Este artigo pressupõe que você já esteja familiarizado com a criação de habilidades.
Para saber como criar um bot de habilidades em geral, confira como [Implementar uma habilidade](skill-implement-skill.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento [básico sobre bots](bot-builder-basics.md), [como os bots skills funcionam](skills-conceptual.md) e como [implementar um skill](skill-implement-skill.md).
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Opcionalmente, uma conta do [LUIS](https://www.luis.ai/). (Para obter mais informações, confira como [Adicionar o reconhecimento de idioma natural ao seu bot](bot-builder-howto-v4-luis.md).)
- Uma cópia da amostra **skillDialog de habilidades** em [**C#** ](https://aka.ms/skills-using-dialogs-cs), [**JavaScript**](https://aka.ms/skills-using-dialogs-js) ou [**Python**](https://aka.ms/skills-using-dialogs-py).

## <a name="about-this-sample"></a>Sobre este exemplo

A amostra **skillDialog de habilidades** inclui projetos para dois bots:

- O _bot raiz do diálogo_, que usa uma classe de _diálogo de habilidades_ para consumir uma habilidade.
- O _bot de habilidades do diálogo_, que usa um diálogo para lidar com atividades provenientes de consumidores de habilidades. Essa habilidade é uma adaptação da amostra de **bot básico**. (Para saber mais sobre o bot básico, confira como [Adicionar o reconhecimento de idioma natural ao seu bot](bot-builder-howto-v4-luis.md).)

Este artigo se concentra em como usar um diálogo em um bot de habilidades para gerenciar várias ações.

### <a name="c"></a>[C#](#tab/cs)

![Diagrama de classe do skill](./media/skill-dialog/dialog-skill-bot-cs.png)

### <a name="javascript"></a>[JavaScript](#tab/js)

![Diagrama de classe do skill](./media/skill-dialog/dialog-skill-bot-js.png)

### <a name="python"></a>[Python](#tab/python)

![Diagrama de classe do skill](./media/skill-dialog/dialog-skill-bot-py.png)

---

Para obter informações sobre o bot do consumidor de habilidades, confira como [Consumir uma habilidade usando diálogos](skill-use-skilldialog.md).

## <a name="resources"></a>Recursos

A autenticação de bot para bot requer que cada bot participante tenha uma appID e uma senha válidas.

Registre tanto o skill quanto o consumidor de skills com o Azure. Você pode usar um Registro de Canais de Bot. Para obter mais informações, veja como [registrar um bot com o Serviço de Bot do Azure](../bot-service-quickstart-registration.md).

Opcionalmente, o bot de habilidades pode usar um modelo LUIS de reserva de voos. Para usar esse modelo, use o arquivo CognitiveModels/FlightBooking.json para criar, treinar e publicar o modelo LUIS.

## <a name="application-configuration"></a>Configuração de aplicativo

1. Adicione a ID do aplicativo e a senha do bot de habilidades.
1. Se você estiver usando o modelo LUIS, adicione a ID do aplicativo, a chave de API e o nome do host da API do LUIS.

### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\appsettings.json**

[!code-json[configuration file](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/appsettings.json?highlight=2-3)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/.env**

[!code-javascript[configuration file](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/.env?highlight=1-2)]

### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/config.py**

[!code-python[configuration file](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/config.py?range=11-22&highlight=2-3)]

---

## <a name="activity-routing-logic"></a>Lógica de roteamento de atividades

A habilidade dá suporte a alguns recursos diferentes. Ele pode reservar um voo ou obter o clima de uma cidade. Além disso, se ele receber uma mensagem fora desses contextos, poderá usar o LUIS para tentar interpretar a mensagem.
O manifesto da habilidade descreve essas ações, os parâmetros de entrada e saída e os pontos de extremidade da habilidade.
Observe que a habilidade pode processar um evento "BookFlight" ou "GetWeather". Ela também pode processar atividades de mensagens.

A habilidade define um diálogo de roteamento de atividades usado para selecionar qual ação deve ser iniciada, com base na atividade de entrada inicial do consumidor de habilidades.
Se ela for fornecida, o modelo LUIS poderá reconhecer as intenções book-flight e get-weather em uma mensagem inicial.

A ação book-flight é um processo de várias etapas, implementado como um diálogo separado. Depois que a ação é iniciada, as atividades de entrada são processadas por esse diálogo. A ação get-weather tem uma lógica de espaço reservado que será substituída em um bot totalmente implementado.

O diálogo de roteamento de atividades inclui um código para:

- [Inicializar o diálogo](#initialize-the-dialog)
- [Processar uma atividade inicial](#process-an-initial-activity)
- [Processar atividades de mensagens](#handle-message-activities)
- [Iniciar uma ação de várias etapas](#begin-a-multi-step-action)
- [Retornar um resultado](#return-a-result)

Os diálogos usados na habilidade são herdados da classe de _diálogo de componentes_. Para obter mais informações sobre diálogos de componente, confira como [Gerenciar a complexidade do diálogo](bot-builder-compositcontrol.md).

### <a name="initialize-the-dialog"></a>Inicializar o diálogo

O diálogo de roteamento de atividades inclui um diálogo filho para a reserva de um voo. O diálogo principal em cascata tem uma etapa que iniciará uma ação com base na atividade inicial recebida.

Ele também aceita um reconhecedor LUIS. Se esse reconhecedor for inicializado, o diálogo o usará para interpretar a intenção de uma atividade de mensagem inicial.

#### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\Dialogs\ActivityRouterDialog.cs**

[!code-csharp[constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=22-34)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/dialogs/activityRouterDialog.js**

[!code-javascript[constructor](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=17-32)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/dialogs/activity_router_dialog.py**

[!code-python[constructor](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=26-36)]

---

### <a name="process-an-initial-activity"></a>Processar uma atividade inicial

Na primeira (e única) etapa do diálogo em cascata principal, a habilidade verifica o tipo de atividade de entrada.

- As atividades de evento são encaminhadas para um manipulador de _atividades no evento_ que inicia a ação apropriada com base no nome do evento.
- As atividades de mensagens são encaminhadas a um manipulador de _atividades de mensagens_ que executa o processamento adicional antes de decidir o que fazer.

Se a habilidade não reconhecer o tipo da atividade de entrada ou o nome do evento, ela enviará uma mensagem de erro e será encerrada.

#### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\Dialogs\ActivityRouterDialog.cs**

[!code-csharp[ProcessActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=36-54)]

[!code-csharp[OnEventActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=56-76)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/dialogs/activityRouterDialog.js**

[!code-javascript[processActivity](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=34-58)]

[!code-javascript[onEventActivity](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=60-88)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/dialogs/activity_router_dialog.py**

[!code-python[process_activity](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=38-61)]

[!code-python[_on_event_activity](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=63-82)]

---

### <a name="handle-message-activities"></a>Processar atividades de mensagens

Se o reconhecedor LUIS estiver configurado, a habilidade chamará o LUIS e iniciará uma ação com base na intenção.
Se o reconhecedor LUIS não estiver configurado ou se não houver suporte para a intenção, a habilidade enviará uma mensagem de erro e será encerrada.

#### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\Dialogs\ActivityRouterDialog.cs**

[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=78-120&highlight=14,27-31)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/dialogs/activityRouterDialog.js**

[!code-javascript[onMessageActivity](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=90-136&highlight=22-23,33-36)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/dialogs/activity_router_dialog.py**

[!code-python[_on_message_activity](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=88-142&highlight=20,37-43)]

---

### <a name="begin-a-multi-step-action"></a>Iniciar uma ação de várias etapas

A ação book-flight inicia um diálogo de várias etapas para obter os detalhes de reserva do usuário.

A ação get-weather não está implementada. Atualmente, ela envia uma mensagem de espaço reservado e, em seguida, é encerrada.

#### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\Dialogs\ActivityRouterDialog.cs**

[!code-csharp[BeginBookFlight](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=138-150)]

[!code-csharp[BeginGetWeather](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/Dialogs/ActivityRouterDialog.cs?range=122-136)]

#### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/dialogs/activityRouterDialog.js**

[!code-javascript[beginBookFlight](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=148-155)]

[!code-javascript[beginGetWeather](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/dialogs/activityRouterDialog.js?range=138-146)]

#### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/dialogs/activity_router_dialog.py**

[!code-python[_begin_book_flight](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=162-172)]

[!code-python[_begin_get_weather](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/dialogs/activity_router_dialog.py?range=144-160)]

---

### <a name="return-a-result"></a>Retornar um resultado

A habilidade inicia um diálogo de reservas para a ação book-flight. Como o diálogo de roteamento de atividades tem apenas uma etapa, quando o diálogo de reserva é encerrado, o diálogo de roteamento de atividades também é encerrado e o resultado do diálogo de reserva se torna o resultado do diálogo de roteamento de atividades.

A ação get-weather simplesmente é encerrada sem definir um valor retornado.

## <a name="canceling-a-multi-step-action"></a>Como cancelar uma ação de várias etapas

O diálogo de reserva e o diálogo de resolvedor de data filho derivam do diálogo base cancel-and-help, que verifica as mensagens do usuário.

- Em "ajuda" ou "?", ele exibe uma mensagem de ajuda e continua o fluxo de conversa no próximo turno.
- Em "cancelar" ou "sair", ele cancela todos os diálogos, o que encerra a habilidade.

Para obter mais informações, confira como [Tratar as interrupções do usuário](bot-builder-howto-handle-user-interrupt.md).

## <a name="service-registration"></a>Registro do serviço

Os serviços necessários para essa habilidade são os mesmos necessários para um bot de habilidades em geral.
Confira como [Implementar uma habilidade](skill-implement-skill.md) para obter uma discussão sobre os serviços necessários.

## <a name="skill-manifest"></a>Manifesto de skills

Um _manifesto de skill_ é um arquivo JSON que descreve as atividades que o skill pode executar, seus parâmetros de entrada e saída e os pontos de extremidade do skill.
O manifesto contém as informações necessárias para acessar o skill por meio de outro bot.

### <a name="c"></a>[C#](#tab/cs)

**DialogSkillBot\wwwroot\manifest\dialogchildbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/csharp_dotnetcore/81.skills-skilldialog/DialogSkillBot/wwwroot/manifest/dialogchildbot-manifest-1.0.json)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**dialogSkillBot/manifest/dialogchildbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/javascript_nodejs/81.skills-skilldialog/dialogSkillBot/manifest/dialogchildbot-manifest-1.0.json)]

### <a name="python"></a>[Python](#tab/python)

**dialog-skill-bot/wwwroot/manifest/dialogchildbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/python/81.skills-skilldialog/dialog-skill-bot/wwwroot/manifest/dialogchildbot-manifest-1.0.json)]

---

O _esquema de manifesto do skill_ é um arquivo JSON que descreve o esquema do manifesto de skill. A versão atual do esquema é [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="test-the-skill-bot"></a>Testar o bot de habilidades

Teste a habilidade no Emulator com o consumidor de habilidades. Para fazer isso, você precisará executar os bots de habilidades e de consumidor de habilidades ao mesmo tempo. Confira como [Usar um diálogo para consumir uma habilidade](skill-use-skilldialog.md) para obter informações sobre como configurar a habilidade.

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente.

1. Execute o bot de habilidades do diálogo e o bot raiz do diálogo localmente no computador. Caso precise de instruções, confira o arquivo LEIAME para obter a amostra em [C#](https://aka.ms/skills-using-dialogs-cs), [JavaScript](https://aka.ms/skills-using-dialogs-js) ou [Python](https://aka.ms/skills-using-dialogs-py).
1. Use o Emulador para testar o bot.
   - Quando você ingressa na conversa pela primeira vez, o bot exibe uma mensagem de boas-vindas e pergunta qual habilidade você deseja chamar. O bot de habilidades desta amostra tem apenas uma habilidade.
   - Selecione **DialogSkillBot**.
1. Em seguida, o bot solicita que você escolha uma ação para a habilidade. Escolha "BookFlight".
   1. A habilidade começa a ação book-flight; responda às solicitações.
   1. Quando a habilidade for concluída, o bot raiz exibirá os detalhes da reserva antes de solicitar novamente a habilidade que você deseja chamar.
1. Selecione **DialogSkillBot** novamente e "BookFlight".
   1. Responda à primeira solicitação e, em seguida, digite "Cancelar" para cancelar a ação.
   1. O bot de habilidades será encerrado sem concluir a ação e o consumidor solicitará a habilidade que você deseja chamar.

## <a name="additional-information"></a>Informações adicionais

- [Usar um diálogo para consumir uma habilidade](skill-use-skilldialog.md) descreve como consumir uma habilidade usando um diálogo de habilidade.
