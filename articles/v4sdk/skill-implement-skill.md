---
title: Implementar um skill | Microsoft Docs
description: Saiba como implementar um skills usando o SDK do Bot Framework.
keywords: habilidades
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/08/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 06eb6ce1241f2d01bc55dd890739b7764fac1992
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92417422"
---
# <a name="implement-a-skill"></a>Implementar um skill

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Você pode usar skills para estender outro bot.
Um _skill_ é um bot que pode executar um conjunto de tarefas para outro bot.

- A interface de um skill é descrita por um manifesto. Os desenvolvedores que não têm acesso ao código-fonte do skill podem usar as informações do manifesto para criar um consumidor de skill.
- Um skill pode usar a validação de declarações para gerenciar quais bots ou usuários podem acessá-lo.

Este artigo demonstra como implementar um skill que ecoa a entrada do usuário.

<!-- I haven't discussed passing values back-and-forth mid conversation. That could be the basis of another article. -->

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento de [conceitos básicos de bot](bot-builder-basics.md) e [skills](skills-conceptual.md).
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Uma cópia do exemplo de **bot para bot de skills simples** em [**C#** ](https://aka.ms/skills-simple-bot-to-bot-csharp), [**JavaScript**](https://aka.ms/skills-simple-bot-to-bot-js) ou [**Python**](https://aka.ms/skills-simple-bot-to-bot-python).

## <a name="about-this-sample"></a>Sobre este exemplo

O exemplo **bot para bot de skills simples** inclui projetos para dois bots:

- O _bot skill de eco_, que implementa o skill.
- O _bot raiz simples_, que implementa um bot raiz que consome o skill.

Este artigo se concentra no skill, que inclui a lógica de suporte em seu bot e adaptador.

### <a name="c"></a>[C#](#tab/cs)

![Diagrama de classe Csharp de qualificações](./media/skills-simple-skill-cs.png)

### <a name="javascript"></a>[JavaScript](#tab/javascript)

![Diagrama de classe js de qualificações](./media/skills-simple-skill-js.png)

### <a name="python"></a>[Python](#tab/python)

![Diagrama de classe do Python de habilidades](./media/skills-simple-skill-python.png)

---

Para obter informações sobre o bot raiz simples, consulte como [Implementar um consumidor de skills](skill-implement-consumer.md).

## <a name="resources"></a>Recursos

A autenticação de bot para bot requer que cada bot participante tenha uma ID do aplicativo e uma senha válidas.

Para poder testar o skill como um bot voltado para o usuário, registre o skill com o Azure. Você pode usar um Registro de Canais de Bot. Para obter mais informações, veja como [registrar um bot com o Serviço de Bot do Azure](../bot-service-quickstart-registration.md).

## <a name="application-configuration"></a>Configuração de aplicativo

Adicione a ID do aplicativo do skill e a senha ao arquivo de configuração do skill.

A matriz de _chamadores permitidos_ pode restringir quais consumidores de skills podem acessar o skill.
Deixe essa matriz vazia para aceitar chamadas de qualquer consumidor de skills.

### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\appsettings.json**

Adicione a ID do aplicativo e a senha do skill ao arquivo appsettings.json.

[!code-csharp[configuration file](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/appsettings.json)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/.env**

Adicione a ID do aplicativo e a senha do skill ao arquivo .env.

[!code-javascript[configuration file](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/.env)]

### <a name="python"></a>[Python](#tab/python)

Adicione a ID do aplicativo e a senha do skill ao arquivo config.py.

**config.py**

[!code-python[configuration file](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/config.py?range=12-17)]

---

## <a name="activity-handler-logic"></a>Lógica do manipulador de atividades

### <a name="to-accept-input-parameters"></a>Para aceitar parâmetros de entrada

O consumidor de skills pode enviar informações para o skill. Uma maneira de aceitar essas informações é aceitá-las por meio da propriedade _valor_ em mensagens de entrada. Outra maneira é manipular atividades de evento e invocação.

O skill neste exemplo não aceita parâmetros de entrada.

### <a name="to-continue-or-complete-a-conversation"></a>Para continuar ou concluir uma conversa

Quando o skill envia uma atividade, o consumidor de skills deve encaminhar a atividade para o usuário.

No entanto, você precisa enviar uma atividade `endOfConversation` quando o skill for concluído; caso contrário, o consumidor de skills continuará a encaminhar as atividades do usuário para o skill.
Opcionalmente, use a propriedade _valor_ da atividade para incluir um valor retornado e use a propriedade _código_ da atividade para indicar por que o skill está terminando.

#### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\Bots\EchoBot.cs**

[!code-csharp[Message handler](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/Bots/EchoBot.cs?range=13-31)]

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/bot.js**

[!code-javascript[Message handler](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/bot.js?range=10-26)]

#### <a name="python"></a>[Python](#tab/python)

**echo-skill-bot/bots/echo_bot.py**

[!code-python[Message handler](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/bots/echo_bot.py?range=9-27)]

---

### <a name="to-cancel-the-skill"></a>Para cancelar o skill

Para skills de vários ciclos, você também aceitará atividades `endOfConversation` de um consumidor de skills, para permitir que o consumidor cancele a conversa atual.

A lógica desse skill não é alterada de ciclo em ciclo. Se você implementar um skill que aloque recursos de conversa, adicione o código de limpeza de recurso ao manipulador de fim de conversa.

#### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\Bots\EchoBot.cs**

[!code-csharp[End-of-conversation handler](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/Bots/EchoBot.cs?range=33-39)]

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/bot.js**

 Use o método `onUnrecognizedActivityType` para adicionar uma lógica de fim de conversa. No manipulador, verifique se `type` da atividade não reconhecida é igual a `endOfConversation`.

[!code-javascript[End-of-conversation handler](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/bot.js?range=28-35)]

#### <a name="python"></a>[Python](#tab/python)

**echo-skill-bot/bots/echo_bot.py**

[!code-python[End-of-conversation handler](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/bots/echo_bot.py?range=29-33)]

---

## <a name="claims-validator"></a>Validador de declarações

Este exemplo usa uma lista de chamadores permitidos para validação de declarações. A lista é definida no arquivo de configuração do skill e é lida no objeto do validador quando ele é criado.

Você deve adicionar um _validador de declarações_ à configuração de autenticação. As declarações são avaliadas após o cabeçalho de autenticação. O código de validação deve gerar um erro ou uma exceção para rejeitar a solicitação. Há muitos motivos pelos quais você pode rejeitar uma solicitação autenticada de outra forma. Por exemplo:

- O skill faz parte de um serviço pago. Usuários que não estão no banco de dados não devem ter acesso.
- O skill é proprietário. Somente determinados consumidores de skills podem chamar o skill.

> [!IMPORTANT]
> Se você não fornecer um validador de declarações, seu bot gerará um erro ou exceção ao receber uma atividade do consumidor de habilidades.

<!--TODO Need a link for more information about claims and claims-based validation.-->

### <a name="c"></a>[C#](#tab/cs)

Derive um validador de declarações da classe `ClaimsValidator`. Ele emitirá um `UnauthorizedAccessException` para rejeitar uma solicitação de entrada. Observe que o método `IConfiguration.Get` retornará NULL se o valor no arquivo de configuração for uma matriz vazia.

**EchoSkillBot\Authentication\AllowedCallersClaimsValidator.cs**

[!code-csharp[Claims validator](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/Authentication/AllowedCallersClaimsValidator.cs?range=17-59)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

Defina um método de validação de declarações que gere um erro para rejeitar uma solicitação de entrada.

**echo-skill-bot/authentication/allowedCallersClaimsValidator.js**

[!code-javascript[Claims validator](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/authentication/allowedCallersClaimsValidator.js?range=11-31)]

### <a name="python"></a>[Python](#tab/python)

Defina um método de validação de declarações que gere um erro para rejeitar uma solicitação de entrada.

**echo-skill-bot/authentication/allowed_callers_claims_validator.py**

[!code-python[Claims validator](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/authentication/allowed_callers_claims_validator.py?range=8-44&highlight=28-33)]

---

## <a name="skill-adapter"></a>Adaptador de skill

Quando ocorre um erro, o adaptador do skill deve limpar o estado da conversa para o skill e também deve enviar uma atividade `endOfConversation` para o consumidor de skills. Use a propriedade _código_ da atividade para sinalizar que o skill foi encerrado devido a um erro.

### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\SkillAdapterWithErrorHandler.cs**

[!code-csharp[Error handler](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/SkillAdapterWithErrorHandler.cs?range=20-75)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/index.js**

[!code-javascript[Error handler](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/index.js?range=40-82)]

### <a name="python"></a>[Python](#tab/python)

**echo-skill-bot/adapter_with_error_handler.py**

[!code-python[Error handler](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/adapter_with_error_handler.py?range=23-77&highlight=43-49)]

---

## <a name="service-registration"></a>Registro do serviço

O _adaptador do Bot Framework_ usa um objeto de _configuração de autenticação_ (definido quando o adaptador é criado) para validar o cabeçalho de autenticação em solicitações de entrada.

Este exemplo adiciona validação de declarações à configuração de autenticação e usa o _adaptador de habilidade com o manipulador de erros_ descrito na seção anterior.

### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\Startup.cs**

[!code-csharp[Configuration](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/Startup.cs?range=28-32)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/index.js**

[!code-javascript[configuration](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/index.js?range=32-38)]

<!--C# & JS snippets checked 1/14-->
### <a name="python"></a>[Python](#tab/python)

**echo-skill-bot/app.py**

[!code-python[configuration](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/app.py?range=19-30)]

---

## <a name="skill-manifest"></a>Manifesto de skills

Um _manifesto de skill_ é um arquivo JSON que descreve as atividades que o skill pode executar, seus parâmetros de entrada e saída e os pontos de extremidade do skill.
O manifesto contém as informações necessárias para acessar o skill por meio de outro bot.

### <a name="c"></a>[C#](#tab/cs)

**EchoSkillBot\wwwroot\manifest\echoskillbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/EchoSkillBot/wwwroot/manifest/echoskillbot-manifest-1.0.json)]

### <a name="javascript"></a>[JavaScript](#tab/javascript)

**echo-skill-bot/manifest/echoskillbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/echo-skill-bot/manifest/echoskillbot-manifest-1.0.json)]

### <a name="python"></a>[Python](#tab/python)

**echo_skill_bot/wwwroot/manifest/echoskillbot-manifest-1.0.json**

[!code-json[Manifest](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/echo-skill-bot/wwwroot/manifest/echoskillbot-manifest-1.0.json)]

---

O _esquema de manifesto do skill_ é um arquivo JSON que descreve o esquema do manifesto de skill. A versão atual do esquema é [2.1.0](https://schemas.botframework.com/schemas/skills/v2.1/skill-manifest.json).

## <a name="test-the-skill"></a>Testar o skill

Neste ponto, você pode testar o skill no Emulador como se fosse um bot normal. No entanto, para testá-lo como um skill, você precisaria [implementar um consumidor de skills](skill-implement-consumer.md).

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o bot skill de eco localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para os exemplos de [C#](https://aka.ms/skills-simple-bot-to-bot-csharp), [JavaScript](https://aka.ms/skills-simple-bot-to-bot-js) ou [Python](https://aka.ms/skills-simple-bot-to-bot-python).
1. Use o Emulador para testar o bot, conforme mostrado abaixo. Observe que quando você envia uma mensagem "fim" ou "parar" para o skill, ele envia uma atividade `endOfConversation` além da mensagem de resposta. O skill envia a atividade `endOfConversation` para indicar que o skill foi concluído.

![testar o skill de eco](media/skills-simple-skill-test.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implementar uma habilidade para Power Virtual Agents](skill-pva.md)
