---
title: Converter um bot v3 do .NET em uma habilidade
description: Mostra como converter bots v3 do .NET em habilidades e consumi-los em um bot v4 do .NET.
keywords: .net, migração de bots, habilidades, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2cf0398986203e131cf456344440cef3c2b90955
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80117761"
---
# <a name="convert-a-net-v3-bot-to-a-skill"></a>Converter um bot v3 do .NET em uma habilidade

Este artigo descreve como converter três exemplos de bot v3 do .NET em habilidades e criar um consumidor de habilidades v4 que possa acessar essas habilidades.
Para converter um bot v3 do JavaScript em uma habilidade, confira como [Converter um bot v3 do JavaScript em uma habilidade](javascript-v3-as-skill.md).
Para migrar um bot do .NET de v3 para v4, confira como [Migrar um bot v3 do .NET para um bot v4 do .NET Framework](conversion-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2019.
- .NET Core 3.1.
- .NET Framework 4.6.1 ou versões posteriores.
- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Copia os bots v3 de exemplo do .NET para converter: um bot de eco, o [**PizzaBot**](https://aka.ms/v3-cs-pizza-bot) e o [**SimpleSandwichBot**](https://aka.ms/v3-cs-simple-sandwich-bot).
- Uma cópia do consumidor de habilidades de exemplo do .NET v4: [**SimpleRootBot**](https://aka.ms/cs-simple-root-bot).

## <a name="about-the-bots"></a>Sobre os bots

Neste artigo, cada bot v3 é atualizado para atuar como uma habilidade. Um consumidor de habilidades v4 está incluído para que você possa testar os bots convertidos como habilidades.

- O **EchoBot** ecoa de volta as mensagens recebidas. Como uma habilidade, ela _é concluída_ quando recebe uma mensagem "fim" ou "parar".
  O bot a ser convertido é baseado no modelo de projeto de Bot de Eco do Bot Builder v3.
- O **PizzaBot** explica a um usuário como pedir uma pizza. Como uma habilidade, ele enviará o pedido do usuário de volta para o pai quando terminar.
- O **SimpleSandwichBot** explica ao usuário como pedir um sanduíche. Como uma habilidade, ele enviará o pedido do usuário de volta para o pai quando terminar.

Além disso, um consumidor de habilidades v4, o **SimpleRootBot**, demonstra como consumir as habilidades e permite testá-las.

Para usar o consumidor de habilidades para testar as habilidades, todos os quatro bots precisam estar em execução ao mesmo tempo. Os bots podem ser testados localmente usando o Bot Framework Emulator, com cada bot usando uma porta local diferente.

## <a name="create-azure-resources-for-the-bots"></a>Criar recursos do Azure para os bots

A autenticação de bot para bot requer que cada bot participante tenha uma ID do aplicativo e uma senha válidas.

1. Crie um Registro de Canais de Bot para os bots conforme necessário.
1. Registre a ID e a senha do aplicativo para cada uma delas.

## <a name="conversion-process"></a>Processo de conversão

Para converter um bot existente em um bot de habilidade, são necessárias apenas algumas etapas, conforme descrito nas próximas seções. Para obter informações mais detalhadas, confira [sobre habilidades](../skills-conceptual.md).

- Atualize o arquivo `web.config` do bot para definir a ID do aplicativo e a senha do bot e para adicionar uma propriedade _allowed callers_.
- Adicionar validação de declarações. Isso restringirá o acesso à habilidade para que somente os usuários ou seu bot raiz possa acessar a habilidade. Confira a seção [informações adicionais](#additional-information) para obter mais informações sobre a validação de declarações padrão e personalizadas.
- Modifique o controlador de mensagens do bot para manipular as atividades `endOfConversation` do bot raiz.
- Modifique o código de bot para retornar uma atividade `endOfConversation` quando a habilidade for concluída.
- Sempre que a habilidade for concluída, se ela tiver o estado da conversa ou mantiver recursos, deverá apagar o estado da conversa e liberar os recursos.
- Ou adicione um arquivo de manifesto.
  Como um consumidor de skills não tem necessariamente acesso ao código do skill, use um manifesto de skills para descrever as atividades que o skill pode receber e gerar, seus parâmetros de entrada e saída e os pontos de extremidade do skill.
  O esquema no manifesto atual é [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="convert-the-echo-bot"></a>Converter o bot de eco

1. Crie um projeto com base no modelo de projeto do **Bot de Eco do Bot Builder** v3 e defina-o para usar a porta 3979.

   1. Crie o projeto.
   1. Abra as propriedades do projeto.
   1. Selecione a categoria **Web** e defina a **URL do Projeto** como `http://localhost:3979/`.
   1. Salve suas alterações e feche a guia de propriedades.

1. Para o arquivo de configuração, adicione a ID e a senha do aplicativo do bot de eco. Ainda nas configurações do aplicativo, adicione uma propriedade `EchoBotAllowedCallers` e a ID do aplicativo do bot raiz simples ao valor dele.

   **V3EchoBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Web.config?range=11-16)]

1. Adicione um validador de declarações personalizadas e uma classe de configuração de autenticação de suporte.

   **V3EchoBot\\Authentication\\CustomAllowedCallersClaimsValidator.cs**

   Implementará a validação de declarações personalizadas e gerará um `UnauthorizedAccessException` se a validação falhar.

   [!code-csharp[claims validator](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Authentication/CustomAllowedCallersClaimsValidator.cs?range=4-72&highlight=45,63,66)]

   **V3EchoBot\\Authentication\\CustomSkillAuthenticationConfiguration.cs**

   Carrega as informações de chamadores permitidos do arquivo de configuração e usa o `CustomAllowedCallersClaimsValidator` para validação de declarações.

   [!code-csharp[allowed callers](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Authentication/CustomSkillAuthenticationConfiguration.cs?range=4-20&highlight=12-14)]

1. Atualize a classe `MessagesController`.

   **V3EchoBot\\Controllers\\MessagesController.cs**

   Atualize as instruções de uso.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=4-15)]

   Atualize o atributo de classe de `BotAuthentication` para `SkillBotAuthentication`. Use o parâmetro `AuthenticationConfigurationProviderType` opcional para usar o provedor de validação de declarações personalizadas.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=19-21)]

   No método `HandleSystemMessage`, adicione uma condição para manipular uma mensagem `endOfConversation`. Isso permitirá que a habilidade apague o estado e libere os recursos quando a conversa for encerrada no consumidor de habilidades.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=45-65)]

1. Modifique o código do bot para permitir que a habilidade sinalize que a conversa foi concluída quando receber uma mensagem "fim" ou "parar" do usuário. A habilidade também deverá apagar o estado e liberar recursos quando encerrar a conversa.

   **V3EchoBot\\Dialogs\\RootDialog.cs**

   [!code-csharp[message received](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Dialogs/RootDialog.cs?range=21-41&highlight=5-13)]

1. Use este manifesto para o bot de eco. Defina a ID do aplicativo de ponto de extremidade como a ID do aplicativo do bot.

   **V3EchoBot\\wwwroot\\echo-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/wwwroot/echo-bot-manifest.json?highlight=22)]

   Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="convert-the-pizza-bot"></a>Converter o bot de pizza

1. Abra sua cópia do projeto PizzaBot e defina-a para usar a porta 3980.

   1. Abra as propriedades do projeto.
   1. Selecione a categoria **Web** e defina a **URL do Projeto** como `http://localhost:3980/`.
   1. Salve suas alterações e feche a guia de propriedades.

1. Para o arquivo de configuração, adicione a ID e a senha do aplicativo do bot de pizza. Ainda nas configurações do aplicativo, adicione uma propriedade `AllowedCallers` e a ID do aplicativo do bot raiz simples ao valor dele.

   **V3PizzaBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Web.config?range=11-16)]

1. Adicionar uma classe `ConversationHelper` que tem métodos auxiliares
   - Envie a atividade `endOfConversation` quando a habilidade for encerrada. Isso pode retornar as informações do pedido na propriedade `Value` da atividade e definir a propriedade `Code` para refletir por que a conversa foi encerrada.
   - Apague o estado da conversa e libere os recursos associados.

   **V3PizzaBot\\ConversationHelper.cs**

   [!code-csharp[conversation helper](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/ConversationHelper.cs?range=4-79)]

1. Atualize a classe `MessagesController`.

   **V3PizzaBot\\Controllers\\MessagesController.cs**

   Atualize as instruções de uso.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=4-16)]

   Atualize o atributo de classe de `BotAuthentication` para `SkillBotAuthentication`. Esse bot usa o validador de declarações padrão.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=20-21)]

   No método `Post`, modifique a condição de atividade `message` para permitir que o usuário cancele o processo de pedido de dentro da habilidade. Além disso, adicione uma condição de atividade `endOfConversation` para permitir que a habilidade apague o estado e libere recursos quando a conversa for encerrada no consumidor de habilidades.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=69-87)]

1. Modifique o código do bot.

   **V3PizzaBot\\PizzaOrderDialog.cs**

   Atualize as instruções de uso.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=4-14)]

   Adicione uma mensagem de boas-vindas. Isso ajudará o usuário a saber o que está acontecendo quando o bot raiz invocar o bot de pizza como uma habilidade.

   [!code-csharp[start form](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=29-35)]

   Modifique o código do bot para permitir que a habilidade sinalize que a conversa está concluída quando o usuário cancelar ou concluir o pedido dele. A habilidade também deverá apagar o estado e liberar recursos quando encerrar a conversa.

   [!code-csharp[form complete](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=76-104)]

1. Use este manifesto para o bot de pizza. Defina a ID do aplicativo de ponto de extremidade como a ID do aplicativo do bot.

   **V3PizzaBot\\wwwroot\\pizza-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/wwwroot/pizza-bot-manifest.json?highlight=22)]

   Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="convert-the-sandwich-bot"></a>Converter o bot de sanduíche

1. Abra sua cópia do projeto SimpleSandwichBot e defina-a para usar a porta 3981.

   1. Abra as propriedades do projeto.
   1. Selecione a categoria **Web** e defina a **URL do Projeto** como `http://localhost:3981/`.
   1. Salve suas alterações e feche a guia de propriedades.

1. Para o arquivo de configuração, adicione a ID e a senha do aplicativo do bot de pizza. Ainda nas configurações do aplicativo, adicione uma propriedade `AllowedCallers` e a ID do aplicativo do bot raiz simples ao valor dele.

   **V3SimpleSandwichBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Web.config?range=11-16)]

1. Adicionar uma classe `ConversationHelper` que tem métodos auxiliares
   - Envie a atividade `endOfConversation` quando a habilidade for encerrada. Isso pode retornar as informações do pedido na propriedade `Value` da atividade e definir a propriedade `Code` para refletir por que a conversa foi encerrada.
   - Apague o estado da conversa e libere os recursos associados.

   **V3SimpleSandwichBot\\ConversationHelper.cs**

   [!code-csharp[conversation helper](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/ConversationHelper.cs?range=4-79)]

1. Atualize a classe `MessagesController`.

   **V3SimpleSandwichBot\\Controllers\\MessagesController.cs**

   Atualize as instruções de uso.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=4-17)]

   Atualize o atributo de classe de `BotAuthentication` para `SkillBotAuthentication`. Esse bot usa o validador de declarações padrão.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=21-22)]

   No método `Post`, modifique a condição de atividade `message` para permitir que o usuário cancele o processo de pedido de dentro da habilidade. Além disso, adicione uma condição de atividade `endOfConversation` para permitir que a habilidade apague o estado e libere recursos quando a conversa for encerrada no consumidor de habilidades.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=42-60)]

1. Modifique o formulário do sanduíche.

   **V3SimpleSandwichBot\\Sandwich.cs**

   Atualize as instruções de uso.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Sandwich.cs?range=4-8)]

   Modifique o método `BuildForm` do formulário para permitir que a habilidade sinalize que a conversa está concluída.

   [!code-csharp[form complete](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Sandwich.cs?range=47-54&highlight=6)]

1. Use esse manifesto para o bot de sanduíche. Defina a ID do aplicativo de ponto de extremidade como a ID do aplicativo do bot.

   **V3SimpleSandwichBot\\wwwroot\\sandwich-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/wwwroot/sandwich-bot-manifest.json?highlight=22)]

   Para o esquema do manifesto de skill, consulte [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## <a name="create-the-v4-root-bot"></a>Criar o bot raiz v4

O bot raiz simples consome as três habilidades e permite que você verifique se as etapas de conversão funcionaram conforme planejado. Esse bot será executado localmente na porta 3978.

1. Para o arquivo de configuração, adicione a ID e a senha do aplicativo do bot raiz. Para cada uma das habilidades v3, adicione a ID do aplicativo da habilidade.

   **V4SimpleRootBot\\appsettings.json**

   [!code-json[configuration](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V4SimpleRootBot/appsettings.json?highlight=2-3,8,13,18)]

## <a name="test-the-root-bot"></a>Testar o bot raiz

Baixe e instale o [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) mais recente.

1. Crie e execute todos os quatro bots localmente em seu computador.
1. Use o Emulador para se conectar ao bot raiz.
1. Teste as habilidades e o consumidor de habilidades.

## <a name="additional-information"></a>Informações adicionais

### <a name="bot-to-bot-authentication"></a>Autenticação de bot para bot

A raiz e o skill se comunicam por HTTP. A estrutura usa tokens de portador e IDs de aplicativo de bot para verificar a identidade de cada bot. Ela usa um objeto de configuração de autenticação para validar o cabeçalho de autenticação em solicitações de entrada. Você pode adicionar um validador de declarações à configuração de autenticação. As declarações são avaliadas após o cabeçalho de autenticação. O código de validação deve gerar um erro ou uma exceção para rejeitar a solicitação.

O validador de declarações personalizadas lê a configuração de aplicativo `AllowedCallers` do arquivo de configuração do bot. Essa configuração deve conter uma lista separada por vírgulas das IDs de aplicativo dos bots que têm permissão para chamar a habilidade ou "*" para permitir que todos os bots chamem a habilidade.

Para implementar um validador de declarações personalizadas, implemente classes derivadas de `AuthenticationConfiguration` e `ClaimsValidator`; em seguida, referencie a configuração de autenticação derivada no atributo `SkillBotAuthentication`. As etapas 3 e 4 da seção [converter o bot de eco](#convert-the-echo-bot) têm classes de validação de declarações de exemplo.
