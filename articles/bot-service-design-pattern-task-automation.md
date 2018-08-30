---
title: Criar bots de automação de tarefas | Microsoft Docs
description: Saiba como criar bots que executam tarefas sem nenhuma intervenção humana.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 2/13/2018
ms.openlocfilehash: 60a3796e255611ff09d5418cdeaa6f363ad8da15
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904962"
---
# <a name="create-task-automation-bots"></a>Criar bots de automação de tarefas

[!INCLUDE [pre-release-label](./includes/pre-release-label-v3.md)]

Um bot de automação de tarefas permite que o usuário conclua uma tarefa específica ou um conjunto de tarefas sem nenhuma assistência de um humano. Muitas vezes, esse tipo de bot se assemelha a um aplicativo ou um site típico, comunicando-se com o usuário principalmente por meio de texto e controles de usuário avançados. Ele pode ter funcionalidades de reconhecimento vocal em idioma natural para enriquecer as conversas com os usuários. 

## <a name="example-use-case-password-reset"></a>Caso de uso de exemplo: redefinição de senha

Para entender melhor a natureza de um bot de tarefa, considere um caso de uso de exemplo: redefinição de senha. 

A empresa Contoso recebe várias chamadas de suporte técnico por dia de funcionários que precisam redefinir suas senhas. A Contoso deseja automatizar a tarefa simples e repetida de redefinição de senha de um funcionário, de modo que os agentes do suporte técnico possam dedicar seu tempo para resolver problemas mais complexos. 

Julio, um desenvolvedor experiente da Contoso, decide criar um bot para automatizar a tarefa de redefinição de senha. Ele começa escrevendo uma especificação de design para o bot, exatamente como faria se estivesse criando um aplicativo ou um site. 

::: moniker range="azure-bot-service-3.0"

### <a name="navigation-model"></a>Modelo de navegação

A especificação define o modelo de navegação:

![Estrutura de diálogos](~/media/bot-service-design-pattern-task-automation/simple-task1.png)

O usuário começa no `RootDialog`. Ao solicitar uma redefinição de senha, ele  
será direcionado para o `ResetPasswordDialog`. Com a `ResetPasswordDialog`, o bot solicitará ao usuário dois tipos de informação: número de telefone e data de nascimento. 

> [!IMPORTANT]
> O design de bot descrito neste artigo destina-se apenas a fins de exemplo. Em cenários do mundo real, um bot de redefinição de senha provavelmente implementará um processo de verificação de identidade mais robusto.

### <a name="dialogs"></a>Diálogos

Em seguida, a especificação descreve a aparência e a funcionalidade de cada diálogo. 

#### <a name="root-dialog"></a>Diálogo raiz

O diálogo raiz fornece ao usuário duas opções: 

1. **Alterar Senha** destina-se a cenários em que o usuário sabe sua senha atual e apenas deseja alterá-la.
2. **Redefinir Senha** destina-se a cenários em que o usuário esqueceu ou armazenou sua senha em um local inadequado e precisa gerar outra.

> [!NOTE]
> Para simplificar, este artigo descreve apenas o fluxo de **redefinição de senha**.

A especificação descreve o diálogo raiz, conforme mostrado na captura de tela a seguir.

![Estrutura de diálogos](~/media/bot-service-design-pattern-task-automation/simple-task2.png)

#### <a name="resetpassword-dialog"></a>Diálogo ResetPassword

Quando o usuário escolhe **Redefinir Senha** no diálogo raiz, o diálogo `ResetPassword` é invocado. 
Em seguida, o diálogo `ResetPassword` invoca dois outros diálogos. 
Primeiro, ele invoca o diálogo `PromptStringRegex` para coletar o número de telefone do usuário. 
Em seguida, ele invoca o diálogo `PromptDate` para coletar a data de nascimento do usuário. 

> [!NOTE]
> Neste exemplo, Julio optou por implementar a lógica para coletar o número de telefone e a data de nascimento do usuário usando dois diálogos separados. A abordagem não apenas simplifica o código necessário para cada diálogo, mas também aumenta a probabilidade de tornar esses diálogos utilizáveis por outros cenários no futuro. 

A especificação descreve o diálogo `ResetPassword`.

![Estrutura de diálogos](~/media/bot-service-design-pattern-task-automation/simple-task3.png)

#### <a name="promptstringregex-dialog"></a>Diálogo PromptStringRegex

O diálogo `PromptStringRegex` solicita ao usuário que ele insira seu número de telefone e verifica se o número de telefone fornecido pelo usuário corresponde ao formato esperado. 
Ele também é válido para o cenário em que o usuário fornece repetidamente uma entrada inválida. 
A especificação descreve o diálogo `PromptStringRegex`.

![Estrutura de diálogos](~/media/bot-service-design-pattern-task-automation/simple-task4.png)

### <a name="prototype"></a>Protótipo

Por fim, a especificação fornece um exemplo de um usuário que está se comunicando com o bot para concluir com êxito a tarefa de redefinição de senha.

![Estrutura de diálogos](~/media/bot-service-design-pattern-task-automation/simple-task5.png)

::: moniker-end 

## <a name="bot-app-or-website"></a>Bot, aplicativo ou site?

Você pode estar se perguntando: se um bot de automação de tarefas se parece muito com um aplicativo ou um site, por que não apenas criar um aplicativo ou um site? Dependendo do cenário específico, a criação de um aplicativo ou um site em vez de um bot pode ser uma opção inteiramente razoável. Você pode até mesmo optar por inserir o bot em um aplicativo, usando a [API de Linha Direta do Bot Framework][directLineAPI] ou o <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">controle de Webchat</a>. A implementação do bot no contexto de um aplicativo fornece o melhor dos dois mundos: uma experiência do aplicativo e uma experiência de conversa sofisticadas, tudo isso em um só lugar. 

Em muitos casos, no entanto, a criação de um aplicativo ou um site pode ser significativamente mais complexa e mais cara do que a criação de um bot. Com frequência, um aplicativo ou um site precisa dar suporte a vários clientes e plataformas, o empacotamento e a implantação podem ser processos cansativos e demorados e a experiência do usuário de precisar baixar e instalar um aplicativo não é necessariamente ideal. Por esses motivos, um bot pode, muitas vezes, fornecer uma maneira muito mais simples de resolver o problema em questão. 

Além disso, os bots fornecem a liberdade de expandir e estender com facilidade. Por exemplo, um desenvolvedor pode optar por adicionar funcionalidades de fala e de idioma natural ao bot de redefinição de senha, de modo que ele possa ser acessado por meio de uma chamada de voz, ou o desenvolvedor pode adicionar suporte para mensagens de texto. A empresa pode configurar quiosques em todo o edifício e inserir o bot de redefinição de senha nessa experiência.

::: moniker range="azure-bot-service-3.0"
## <a name="sample-code"></a>Exemplo de código

Para obter uma amostra completa que explica como implementar a automação de tarefas simples usando o SDK do Construtor de Bot para .NET, confira a <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/capability-SimpleTaskAutomation" target="_blank">amostra de Automação de Tarefas Simples</a> no GitHub.

Para obter uma amostra completa que explica como implementar a automação de tarefas simples usando o SDK do Construtor de Bot para Node.js, confira a <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/capability-SimpleTaskAutomation" target="_blank">amostra de Automação de Tarefas Simples</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- [Diálogos](~/dotnet/bot-builder-dotnet-dialogs.md)
- [Gerenciar o fluxo da conversa com diálogos (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Gerenciar o fluxo da conversa com diálogos (Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)

::: moniker-end

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle
