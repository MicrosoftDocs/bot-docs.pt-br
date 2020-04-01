---
title: Implementar um consumidor de skills | Microsoft Docs
description: Saiba como implementar um consumidor de skills usando o SDK do Bot Framework.
keywords: habilidades
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/22/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9211f3f1df98ef5972571c1c36ecdeefc27639bf
ms.sourcegitcommit: 308e6df385b9bac9c8d60f8b75eabc813b823c38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519985"
---
# <a name="implement-a-skill-consumer"></a>Implementar um consumidor de skills

[!INCLUDE[applies-to](../includes/applies-to.md)]

Você pode usar skills para estender outro bot.
Um _skill_ é um bot que pode executar um conjunto de tarefas para outro bot e que usa um manifesto para descrever sua interface.
Um _bot raiz_ é um bot voltado para o usuário que pode invocar um ou mais skills. Um bot raiz é um tipo de _consumidor de skills_.

- Um consumidor de skills pode usar a validação de declarações para gerenciar quais skills ou usuários podem acessá-lo.
- Um consumidor de skills pode usar vários skills.
- Os desenvolvedores que não têm acesso ao código-fonte do skill podem usar as informações do manifesto do skill para criar um consumidor de skills.

Este artigo demonstra como implementar um consumidor de skills que usa o skill de eco para ecoar a entrada do usuário. Para obter um exemplo de manifesto de skill e informações sobre como implementar o skill de eco, consulte como [implementar um skill](skill-implement-skill.md).

## <a name="prerequisites"></a>Prerequisites

- Conhecimento [básico sobre bots](bot-builder-basics.md), [como os bots skills funcionam](skills-conceptual.md) e como [implementar um skill](skill-implement-skill.md).
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Uma cópia do exemplo de **bot para bot de skills simples** em [**C#** ](https://aka.ms/skills-simple-bot-to-bot-csharp), [**JavaScript**](https://aka.ms/skills-simple-bot-to-bot-js) ou [**Python**](https://aka.ms/skills-simple-bot-to-bot-python).

## <a name="about-this-sample"></a>Sobre este exemplo

O exemplo **bot para bot de skills simples** inclui projetos para dois bots:

- O _bot skill de eco_, que implementa o skill.
- O _bot raiz simples_, que implementa um bot raiz que consome o skill.

Este artigo se concentra no bot raiz, que inclui a lógica de suporte em seus objetos de bot e adaptador e inclui objetos usados para trocar atividades com um skill. Eles incluem:

- Um cliente de skills, usado para enviar atividades a um skill.
- Um manipulador de skills, usado para receber atividades de um skill.
- Um alocador de ID de conversa de skill, usado pelo cliente e manipulador de skill para converter entre a referência de conversa usuário-raiz e a referência de conversa raiz-skill.

### <a name="c"></a>[C#](#tab/cs)

![Diagrama de classe de consumidor de skills](./media/skills-simple-root-cs.png)

### <a name="javascript"></a>[JavaScript](#tab/js)

![Diagrama de classe de consumidor de skills](./media/skills-simple-root-js.png)

### <a name="python"></a>[Python](#tab/python)

![Diagrama de classe de consumidor de skills](./media/skills-simple-root-python-2.png)

---

Para obter informações sobre o bot de skill de eco, consulte como [implementar um skill](skill-implement-skill.md).

## <a name="resources"></a>Recursos

A autenticação de bot para bot requer que cada bot participante tenha uma appID e uma senha válidas.

Registre tanto o skill quanto o consumidor de skills com o Azure. Você pode usar um Registro de Canais de Bot. Para obter mais informações, veja como [registrar um bot com o Serviço de Bot do Azure](../bot-service-quickstart-registration.md).

## <a name="application-configuration"></a>Configuração de aplicativo

1. Adicione a ID do aplicativo e a senha do bot raiz.
1. Adicione a URL do ponto de extremidade ao qual os skills devem responder ao consumidor de skills.
1. Adicione uma entrada para cada skill que o consumidor de skills usará. Cada entrada inclui:
   - Uma ID que o consumidor de skills usará para identificar cada skill.
   - A ID do aplicativo do skill.
   - O ponto de extremidade de mensagens do skill.

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\appsettings.json**

Adicione a ID do aplicativo e a senha do bot raiz ao arquivo appsettings.json. Além disso, adicione a ID do aplicativo para o bot skill de eco à matriz `BotFrameworkSkills`.

[!code-csharp[configuration file](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/appsettings.json)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**echo-skill-bot/.env**

Adicione a ID do aplicativo e a senha do bot raiz ao arquivo .env. Além disso, adicione a ID do aplicativo para o bot skill de eco.

[!code-javascript[configuration file](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/.env)]

### <a name="python"></a>[Python](#tab/python)

**simple_root_bot/config.py**

Adicione a ID do aplicativo e a senha do bot raiz ao arquivo .env. Além disso, adicione a ID do aplicativo para o bot skill de eco.

[!code-python[configuration file](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/config.py?range=16-29)]

---

## <a name="skills-configuration"></a>Configuração de skills

Este exemplo lê informações de cada skill no arquivo de configuração em uma coleção de objetos _skills_.

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\SkillsConfiguration.cs**

[!code-csharp[skills configuration](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/SkillsConfiguration.cs?range=14-38)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/skillsConfiguration.js**

[!code-javascript[skills configuration](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/skillsConfiguration.js?range=7-33)]


### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/config.py**

[!code-python[skills configuration](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/config.py?range=32-36)]

---

## <a name="conversation-id-factory"></a>Alocador de ID de conversa

Ele cria a ID de conversa para uso com o skill e pode recuperar a ID de conversa do usuário original da ID da conversa de skills.

O alocador de ID de conversa deste exemplo é compatível com um cenário simples em que:

- O bot raiz é projetado para consumir um skill específico.
- O bot raiz tem apenas uma conversa ativa com um skill de cada vez.

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\SkillConversationIdFactory.cs**

[!code-csharp[Conversation ID factory](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/SkillConversationIdFactory.cs?range=17-40)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/skillConversationIdFactory.js**

[!code-javascript[Conversation ID factory](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/skillConversationIdFactory.js?range=10-29)]

### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/skill_conversation_id_factory.py**

[!code-python[Conversation ID factory](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/skill_conversation_id_factory.py?range=9-47)]

---

Para compatibilidade com cenários mais complexos, crie seu alocador de ID de conversa de maneira que:

- O método _criar ID de conversa de skills_ obtém ou gera a ID de conversa de skill apropriada.
- O método _obter referência de conversa_ obtenha a conversa de usuário correta.

## <a name="skill-client-and-skill-handler"></a>Cliente de skills e manipulador de skills

O consumidor de skills usa um cliente de skills para encaminhar atividades ao skill.
O cliente usa as informações de configuração de skills e o alocador de ID de conversa para fazer isso.

O consumidor de skills usa um manipulador de skills para receber atividades de um skill.
O manipulador usa o alocador de ID de conversa, a configuração de autenticação e um provedor de credenciais para fazer isso e também tem dependências no manipulador de atividade e no adaptador do bot raiz

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\Startup.cs**

[!code-csharp[skill client and handler](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Startup.cs?range=42-43)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/index.js**

[!code-javascript[skill client](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=107-108)]

[!code-javascript[skill handler](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=132)]

### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/app.py**

[!code-python[skill client](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=58)]

[!code-python[skill handler](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=120-122)]

---

O tráfego HTTP proveniente do skill entrará no ponto de extremidade da URL de serviço que o consumidor de skills anuncia ao skill. Use um manipulador de ponto de extremidade específico da linguagem para encaminhar o tráfego para o manipulador de skills.

O manipulador de skills padrão:

- Usa um objeto de configuração de autenticação para executar a autenticação de bot para bot e a validação de declarações.
- Usa o alocador de ID de conversa para converter da conversa consumidor-skill de volta para a conversa do usuário raiz.
- Gera uma mensagem proativa para que o consumidor de skills possa restabelecer um contexto de usuário raiz e encaminhar atividades para o usuário.

## <a name="activity-handler-logic"></a>Lógica do manipulador de atividades

É importante saber que a lógica do consumidor de skills deve:

- Lembrar-se de que há skills ativos e encaminhar atividades de para eles, conforme apropriado.
- Observar quando um usuário faz uma solicitação que deve ser encaminhada a um skill e iniciar o skill.
- Procurar uma atividade `endOfConversation` em qualquer skill ativo, para observar quando ela é concluída.
- Se apropriado, adicionar lógica para permitir que o usuário ou o consumidor de skills cancele um skill que ainda não foi concluído.
- Salvar o estado antes de fazer a chamada para um skill, uma vez que qualquer resposta pode voltar a uma instância diferente do consumidor de skills. (balanceamento de carga etc.)

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\Bots\RootBot.cs**

O bot raiz tem dependências do estado da conversa, das informações de skills, do cliente de skills e da configuração geral. O ASP.NET fornece esses objetos por meio de injeção de dependência.
O bot raiz também define um acessador de propriedade de estado de conversa para controlar qual skill está ativo.

[!code-csharp[Root bot dependencies](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Bots/RootBot.cs?range=21-55)]

Este exemplo tem um método auxiliar para encaminhar atividades a um skill. Ele salva o estado da conversa antes de invocar o skill e verifica se a solicitação HTTP foi bem-sucedida.

[!code-csharp[Send to skill](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Bots/RootBot.cs?range=134-148)]

É importante observar que o bot raiz inclui a lógica para encaminhar atividades para a habilidade, iniciar a habilidade mediante solicitação do usuário e interromper a habilidade quando ela é concluída.

[!code-csharp[message/end-of-conversation handlers](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Bots/RootBot.cs?range=57-121)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/rootBot.js**

O bot raiz tem dependências do estado da conversa, das informações de skills e do cliente de skills.
O bot raiz também define um acessador de propriedade de estado de conversa para controlar qual skill está ativo.

[!code-javascript[Root bot dependencies](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/rootBot.js?range=7-30)]

Este exemplo tem um método auxiliar para encaminhar atividades a um skill. Ele salva o estado da conversa antes de invocar o skill e verifica se a solicitação HTTP foi bem-sucedida.

[!code-javascript[Send to skill](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/rootBot.js?range=119-131)]

É importante observar que o bot raiz inclui a lógica para encaminhar atividades para a habilidade, iniciar a habilidade mediante solicitação do usuário e interromper a habilidade quando ela é concluída.

[!code-javascript[message/end-of-conversation handlers](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/rootBot.js?range=32-94)]

### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/bots/root_bot.py**

O bot raiz tem dependências do estado da conversa, das informações de skills, do cliente de skills e da configuração geral.
O bot raiz também define um acessador de propriedade de estado de conversa para controlar qual skill está ativo.

[!code-python[Root bot dependencies](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/bots/root_bot.py?range=23-37)]

Este exemplo tem um método auxiliar para encaminhar atividades a um skill. Ele salva o estado da conversa antes de invocar o skill e verifica se a solicitação HTTP foi bem-sucedida.

[!code-python[Send to skill](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/bots/root_bot.py?range=111-124)]

É importante observar que o bot raiz inclui a lógica para encaminhar atividades para a habilidade, iniciar a habilidade mediante solicitação do usuário e interromper a habilidade quando ela é concluída.

[!code-python[Handled activities](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/bots/root_bot.py?range=39-100)]

---

## <a name="on-turn-error-handler"></a>Manipulador de erro em ciclo

Quando ocorre um erro, o adaptador limpa o estado da conversa para redefinir a conversa com o usuário e evitar a persistência de um estado de erro.

É uma boa prática enviar uma atividade _fim da conversa_ para qualquer skill ativo antes de limpar o estado da conversa no consumidor de skills. Isso permite que os skills liberem os recursos associados à conversa consumidora de skills antes que o consumidor de skills libere a conversa.

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\AdapterWithErrorHandler.cs**

Neste exemplo, a lógica de erro de ciclo é dividida entre alguns métodos auxiliares.

[!code-csharp[On turn error](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/AdapterWithErrorHandler.cs?range=40-120)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/index.js**


[!code-javascript[On turn error](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=34-87)]

### <a name="python"></a>[Python](#tab/python)

**app.py**

[!code-python[On turn error](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=62-115)]

---

## <a name="skills-endpoint"></a>Ponto de extremidade de skills

O bot define um ponto de extremidade que encaminha as atividades de skills de entrada para o manipulador de skills do bot raiz.


### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\Controllers\SkillController.cs**

[!code-csharp[skill endpoint](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Controllers/SkillController.cs?range=15-23)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/index.js**


[!code-javascript[skill endpoint](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=133-134)]

### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/app.py**

[!code-python[skill endpoint](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=144)]

---

## <a name="service-registration"></a>Registro do serviço

Inclui um objeto de configuração de autenticação com qualquer validação de declarações, além de todos os objetos adicionais.

### <a name="c"></a>[C#](#tab/cs)

**SimpleRootBot\Startup.cs**

[!code-csharp[services](~/../botbuilder-samples/samples/csharp_dotnetcore/80.skills-simple-bot-to-bot/SimpleRootBot/Startup.cs?range=22-53)]

### <a name="javascript"></a>[JavaScript](#tab/js)

**simple-root-bot/index.js**

[!code-javascript[services](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=27-31)]

[!code-javascript[services](~/../botbuilder-samples/samples/javascript_nodejs/80.skills-simple-bot-to-bot/simple-root-bot/index.js?range=95-134)]

### <a name="python"></a>[Python](#tab/python)

**simple-root-bot/app.py**

[!code-python[services](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=35-58)]

[!code-python[services](~/../botbuilder-samples/samples/python/80.skills-simple-bot-to-bot/simple-root-bot/app.py?range=118-144)]

---

## <a name="test-the-root-bot"></a>Testar o bot raiz

Você pode testar o consumidor de skills no Emulador como se fosse um bot normal. No entanto, você precisa executar os bots skills e consumidores de skill ao mesmo tempo.
Consulte como [implementar um skill](skill-implement-skill.md) para obter informações sobre como configurar o skill.

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente

1. Execute o bot skill de eco e o bot raiz simples localmente em seu computador. Se você precisar de instruções, consulte o arquivo LEIAME para os exemplos de [C#](https://aka.ms/skills-simple-bot-to-bot-csharp), [JavaScript](https://aka.ms/skills-simple-bot-to-bot-js) ou [Python](https://aka.ms/skills-simple-bot-to-bot-python).
1. Use o Emulador para testar o bot, conforme mostrado abaixo. Observe que quando você envia uma mensagem `end` ou `stop` para o skill, ele envia ao bot raiz uma atividade `endOfConversation`, além da mensagem de resposta. A propriedade _código_ da atividade `endOfConversation` indica que o skill foi concluído com êxito.

![testar o consumidor de skills](media/skills-simple-consumer-test.png)

## <a name="additional-information"></a>Informações adicionais

Aqui estão algumas coisas a serem consideradas ao implementar um bot raiz mais complexo.

### <a name="to-allow-the-user-to-cancel-a-multi-step-skill"></a>Para permitir que o usuário cancele um skill de várias etapas

O bot raiz deve verificar a mensagem do usuário antes de encaminhá-la para o skill ativo. Se o usuário quiser cancelar o processo atual, o bot raiz poderá enviar uma atividade `endOfConversation` para o skill, em vez de encaminhar a mensagem.

### <a name="to-exchange-data-between-the-root-and-skill-bots"></a>Para trocar dados entre os bots skills e raiz

Para enviar parâmetros para o skill, o consumidor de skills pode definir a propriedade _valor_ nas mensagens que ele envia para o skill. Para receber valores retornados do skill, o consumidor de skills deve verificar a propriedade _valor_ quando o skill envia uma atividade `endOfConversation`.

### <a name="to-use-multiple-skills"></a>Para usar vários skills

- Se um skill estiver ativo, o bot raiz precisará determinar qual skill está ativo e encaminhar a mensagem do usuário para o skill correto.
- Se nenhum skill estiver ativo, o bot raiz precisará determinar qual skill deve ser iniciado, se houver algum, com base no estado do bot e na entrada do usuário.
- Se você quiser permitir que o usuário alterne entre vários skills simultâneos, o bot raiz precisará determinar a quais dos skills ativos o usuário está pretendendo interagir antes de encaminhar a mensagem do usuário.

<!--
## Next steps

TBD: Claims validation? Skill manifest?

> [!div class="nextstepaction"]
> [Add claims validation](skill-add-claims-validation.md)
-->