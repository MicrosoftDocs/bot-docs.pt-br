---
title: Converter um bot v3 do JavaScript em uma habilidade
description: Mostra como converter bots v3 do JavaScript em habilidades e consumi-los em um bot v4 do JavaScript.
keywords: JavaScript, migração de bots, habilidades, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 325cc124fc54274b5b27b187831bb6bc6d3d5f32
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95452090"
---
# <a name="convert-a-javascript-v3-bot-to-a-skill"></a>Converter um bot v3 do JavaScript em uma habilidade

[!INCLUDE [applies-to-v4](../../includes/applies-to-v4-current.md)]

Este artigo descreve como converter dois exemplos de bot v3 do JavaScript em habilidades e criar um consumidor de habilidades v4 que possa acessar essas habilidades.
Para converter um bot v3 do .NET em uma habilidade, confira como [Converter um bot v3 do .NET em uma habilidade](net-v3-as-skill.md).
Para migrar um bot do JavaScript de v3 para v4, confira como [Migrar um bot v3 do JavaScript para um bot v4](conversion-javascript.md).

## <a name="prerequisites"></a>Pré-requisitos

- Visual Studio Code.
- Node.js.
- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para testar as habilidades, você precisará do emulador do bot Framework e das cópias locais dos bots:
  - A habilidade de eco do JavaScript v3: [**Skills/v3-skill-bot**](https://aka.ms/v3-js-echo-skill).
  - A habilidade de reserva do JavaScript v3: [**Skills/v3-booking-bot-skill**](https://aka.ms/v3-js-booking-skill).
  - O consumidor de habilidades de exemplo do JavaScript v4: [**Skills/v4-root-bot**](https://aka.ms/js-simple-root-bot).

## <a name="about-the-bots"></a>Sobre os bots

Neste artigo, cada bot v3 é criado para atuar como uma habilidade. Um consumidor de habilidades v4 está incluído para que você possa testar os bots convertidos como habilidades.

- O **v3-skill-bot** ecoa de volta as mensagens recebidas. Como uma habilidade, ela _é concluída_ quando recebe uma mensagem "fim" ou "parar". O bot a ser convertido se baseia em um bot v3 mínimo.
- O **v3-booking-bot-skill** permite que o usuário reserve um voo ou um hotel. Como uma habilidade, ele enviará informações coletadas de volta ao pai quando terminar.

Além disso, um consumidor de habilidades v4, o **v4-root-bot**, demonstra como consumir as habilidades e permite testá-las.

Para usar o consumidor de habilidades para testar as habilidades, todos os três bots precisam estar em execução ao mesmo tempo. Os bots podem ser testados localmente usando o Bot Framework Emulator, com cada bot usando uma porta local diferente.

## <a name="create-azure-resources-for-the-bots"></a>Criar recursos do Azure para os bots

A autenticação de bot para bot requer que cada bot participante tenha uma ID do aplicativo e uma senha válidas.

1. Crie um Registro de Canais de Bot para os bots conforme necessário.
1. Registre a ID e a senha do aplicativo para cada uma delas.

## <a name="conversion-process"></a>Processo de conversão

Para converter um bot existente em um bot de habilidade, são necessárias apenas algumas etapas, conforme descrito nas próximas seções. Para obter informações mais detalhadas, confira [sobre habilidades](../skills-conceptual.md).

- Atualize o arquivo `.env` do bot para definir a ID do aplicativo e a senha do bot e para adicionar uma propriedade _root bot app ID_.
- Adicionar validação de declarações. Isso restringirá o acesso à habilidade para que somente os usuários ou seu bot raiz possa acessar a habilidade. Confira a seção [informações adicionais](#additional-information) para obter mais informações sobre a validação de declarações padrão e personalizadas.
- Modifique o controlador de mensagens do bot para manipular as atividades `endOfConversation` do bot raiz.
- Modifique o código de bot para retornar uma atividade `endOfConversation` quando a habilidade for concluída.
- Sempre que a habilidade for concluída, se ela tiver o estado da conversa ou mantiver recursos, deverá apagar o estado da conversa e liberar os recursos.
- Ou adicione um arquivo de manifesto.
  Como um consumidor de skills não tem necessariamente acesso ao código do skill, use um manifesto de skills para descrever as atividades que o skill pode receber e gerar, seus parâmetros de entrada e saída e os pontos de extremidade do skill.
  O esquema no manifesto atual é [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="convert-the-echo-bot"></a>Converter o bot de eco

Confira [Skills/v3-skill-bot](https://aka.ms/v3-js-echo-skill) para ver um exemplo de um bot de eco v3 que foi convertido em uma habilidade básica.

1. Crie um projeto simples de bot v3 do JavaScript e importe os módulos necessários.

   **v3-skill-bot/app.js**

   [!code-javascript[require statements](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=5-7)]

1. Defina o bot para ser executado localmente na porta 3979.

   **v3-skill-bot/app.js**

   [!code-javascript[Setup server and port](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=9-13)]

1. No arquivo de configuração, adicione a ID e a senha do aplicativo do bot de eco. Além disso, adicione uma propriedade `ROOT_BOT_APP_ID` com a ID do aplicativo do bot raiz simples como o valor dela.

   **v3-skill-bot/.env**

   [!code[.env file](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/.env)]

1. Crie o conector do chat para o bot. Este usa a configuração de autenticação padrão. Defina `enableSkills` como `true` para permitir que o bot seja usado como uma habilidade. `allowedCallers` é uma matriz das IDs do aplicativo dos bots que têm permissão para usar essa habilidade. Se o primeiro valor dessa matriz fosse '*', qualquer bot poderia usar essa habilidade.

   **v3-skill-bot/app.js**

   [!code-javascript[chat connector](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=24-30&highlight=5-6)]

1. Atualize o manipulador de mensagens para enviar uma atividade `endOfConversation` quando o usuário optar por encerrar a habilidade.

   **v3-skill-bot/app.js**

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=35-46&highlight=6)]

1. Como esse bot não mantém o estado da conversa e não cria nenhum recurso para a ela, o bot não precisa lidar com nenhuma atividade `endOfConversation` recebida do consumidor de habilidades.

1. Use este manifesto para o bot de eco. Defina a ID do aplicativo de ponto de extremidade como a ID do aplicativo do bot.

   **v3-skill-bot/manifest/v3-skill-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/manifest/v3-skill-bot-manifest.json?highlight=22)]

   Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="convert-the-booking-bot"></a>Converter o bot de reserva

Confira [Skills/v3-booking-bot-skill](https://aka.ms/v3-js-booking-skill) para ver um exemplo de um bot de reserva v3 que foi convertido em uma habilidade básica.
Antes da conversão, o bot era semelhante ao exemplo [core-MultiDialogs](https://aka.ms/v3-js-core-multidialogs) v3.

1. Importe os módulos necessários.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[require statements](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=2-7)]

1. Defina o bot para ser executado localmente na porta 3980.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[Setup server and port](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=9-13)]

1. No arquivo de configuração, adicione a ID e a senha do aplicativo do bot de reserva. Além disso, adicione uma propriedade `ROOT_BOT_APP_ID` com a ID do aplicativo do bot raiz simples como o valor dela.

   **v3-booking-bot-skill/.env**

   [!code[.env file](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/.env)]

1. Crie o conector do chat para o bot. Este usa a configuração de autenticação personalizada. Defina `enableSkills` como `true` para permitir que o bot seja usado como uma habilidade. `authConfiguration` contém o objeto de configuração de autenticação personalizado a ser usado para autenticação e validação de declarações.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[chat connector](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=18-24&highlight=5-6)]

   **v3-booking-bot-skill/allowedCallersClaimsValidator.js**

   Isso implementa a validação de declarações personalizadas e gerará um erro se a validação falhar.

   [!code-javascript[custom claims validation](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/allowedCallersClaimsValidator.js?range=4-47&highlight=22,25,39,41)]

1. Atualize o manipulador de mensagens para enviar uma atividade `endOfConversation` quando a habilidade for encerrada. Observe que `session.endConversation()` apaga o estado da conversa, além de enviar uma atividade `endOfConversation`.

   **v3-booking-bot-skill/app.js**

   Implemente uma função auxiliar para definir as propriedades `code` e `value` da atividade `endOfConversation` e apagar o estado da conversa.
   Se o bot tivesse gerenciado qualquer outro recurso para a conversa, você os liberaria aqui também.

   [!code-javascript[endConversation function](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=122-134&highlight=12)]

   Quando o usuário concluir o processo, use o método auxiliar para encerrar a habilidade e retornar os dados coletados do usuário.

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=39-40)]

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=72-77&highlight=4)]

   Se, em vez disso, o usuário finalizar o processo antecipadamente, o método auxiliar ainda será invocado.

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=86-101&highlight=9-10)]

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=107-108)]

1. Se a habilidade for cancelada pelo consumidor de habilidades, o consumidor enviará uma atividade `endOfConversation`. Manipule essa atividade e libere todos os recursos associados à conversa.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[endConversation function](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=110-115)]

1. Use esse manifesto para o bot de reserva. Defina a ID do aplicativo de ponto de extremidade como a ID do aplicativo do bot.

   **v3-booking-bot-skill/manifest/v3-booking-bot-skill-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/manifest/v3-booking-bot-skill-manifest.json?highlight=22)]

   Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="create-the-v4-root-bot"></a>Criar o bot raiz v4

O bot raiz simples consome as duas habilidades e permite que você verifique se as etapas de conversão funcionaram conforme planejado. Esse bot será executado localmente na porta 3978.

1. Para o arquivo de configuração, adicione a ID e a senha do aplicativo do bot raiz. Para cada uma das habilidades v3, adicione a ID do aplicativo da habilidade.

   **v4-root-bot/.env**

   [!code-json[configuration](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v4-root-bot/.env?highlight=2-3,6,10)]

## <a name="test-the-root-bot"></a>Testar o bot raiz

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente.

1. Inicie todos os três bots localmente em seu computador.
1. Use o Emulador para se conectar ao bot raiz.
1. Teste as habilidades e o consumidor de habilidades.

## <a name="additional-information"></a>Informações adicionais

### <a name="bot-to-bot-authentication"></a>Autenticação de bot para bot

A raiz e o skill se comunicam por HTTP. A estrutura usa tokens de portador e IDs de aplicativo de bot para verificar a identidade de cada bot. Ela usa um objeto de configuração de autenticação para validar o cabeçalho de autenticação em solicitações de entrada. Você pode adicionar um validador de declarações à configuração de autenticação. As declarações são avaliadas após o cabeçalho de autenticação. O código de validação deve gerar um erro ou uma exceção para rejeitar a solicitação.

Ao criar um conector de chat, inclua um `allowedCallers` ou uma propriedade `authConfiguration` no parâmetro de configurações para habilitar a autenticação de bot para bot.

O validador de declarações padrão do conector do chat usa a propriedade `allowedCallers`. O valor dele deve ser uma matriz de IDs do aplicativo dos bots que têm permissão para chamar a habilidade. Defina o primeiro elemento como '*' para permitir que todos os bots chamem a habilidade.

Para usar uma função de validação de declarações personalizadas, defina o campo `authConfiguration` como sua função de validação. Essa função deverá aceitar uma matriz de objetos de declaração e gerar um erro se a validação falhar. A etapa 4 da seção [converter o bot de reserva](#convert-the-booking-bot) tem um validador de declarações de exemplo.
