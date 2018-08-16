---
title: Implementar manipuladores de mensagens globais | Microsoft Docs
description: Saiba como habilitar seu bot ouvir e manipular a entrada do usuário que contém determinadas palavras-chave usando o SDK do construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 796e36fdfd5ef2ee20ba1f5e000f92ef3e4ec1ae
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574652"
---
# <a name="implement-global-message-handlers"></a>Implementar manipuladores de mensagens globais

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to global message handlers](../includes/snippet-global-handlers-intro.md)]

## <a name="listen-for-keywords-in-user-input"></a>Ouvir as palavras-chave na entrada do usuário

O seguinte passo mostra como implementar manipuladores de mensagem global usando o Bot Builder SDK for .NET.

Primeiro, `Global.asax.cs`registra`GlobalMessageHandlersBotModule`, que é implementado como mostrado aqui. Neste exemplo, o módulo registra dois scorables: um para gerenciar uma solicitação para alterar configurações (`SettingsScorable`) e outro para gerenciar uma solicitação para cancelar (`CancelScoreable`).

```cs
public class GlobalMessageHandlersBotModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        base.Load(builder);

        builder
            .Register(c => new SettingsScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();

        builder
            .Register(c => new CancelScorable(c.Resolve<IDialogTask>()))
            .As<IScorable<IActivity, double>>()
            .InstancePerLifetimeScope();
    }
}
```

O `CancelScorable` contém um `PrepareAsync` método que define o acionador: se o texto da mensagem for "cancel", essa pontuação será acionada.

```cs
protected override async Task<string> PrepareAsync(IActivity activity, CancellationToken token)
{
    var message = activity as IMessageActivity;
    if (message != null && !string.IsNullOrWhiteSpace(message.Text))
    {
        if (message.Text.Equals("cancel", StringComparison.InvariantCultureIgnoreCase))
        {
            return message.Text;
        }
    }
    return null;
}
```

Quando uma solicitação de "cancelamento" é recebida, o método `PostAsync` em `CancelScoreable` redefine a pilha de caixas de diálogo. 

```cs
protected override async Task PostAsync(IActivity item, string state, CancellationToken token)
{
    this.task.Reset();
}
```

Quando uma requisição "change settings" é recebida, o método `PostAsync` dentro de `SettingsScorable` invoca o `SettingsDialog` (passando a requisição para aquele diálogo), adicionando assim o `SettingsDialog` ao topo da pilha de diálogos e colocando-o controle da conversa.

```cs
protected override async Task PostAsync(IActivity item, string state, CancellationToken token)
{
    var message = item as IMessageActivity;
    if (message != null)
    {
        var settingsDialog = new SettingsDialog();
        var interruption = settingsDialog.Void<object, IMessageActivity>();
        this.task.Call(interruption, null);
        await this.task.PollAsync(token);
    }
}
```

## <a name="sample-code"></a>Exemplo de código

Para um exemplo completo que mostra como implementar manipuladores de mensagens globais usando o SDK do Bot Builder para .NET, consulte o <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers" target="_blank">exemplo de manipuladores de mensagens globais</a> no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

- [Design e controle de fluxo da conversa](../bot-service-design-conversation-flow.md)
- <a href="/dotnet/api/?view=botbuilder-3.12.2.4" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>
- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-GlobalMessageHandlers" target="_blank">Exemplo de manipuladores de mensagens global (GitHub)</a>
