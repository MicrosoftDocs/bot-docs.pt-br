---
title: Visão geral das Habilidades do Bot Framework | Microsoft Docs
description: Saiba mais sobre as Habilidades do Bot Framework
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7ce7556b58c047e20f1b32597c896baa802968aa
ms.sourcegitcommit: b94c4286f6f64955fd51ccf4a68109c43db0e47d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65083763"
---
# <a name="virtual-assistant---skills-overview"></a>Assistente Virtual - Visão Geral das Habilidades

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

## <a name="overview"></a>Visão geral

Os desenvolvedores podem compor experiências de conversação combinando recursos de conversa reutilizáveis, conhecidos como Habilidades.

Dentro de uma empresa poderíamos ter a criação de um único bot pai reunindo vários sub-bots pertencentes a diferentes equipes ou o aproveitamento mais amplo dos recursos comuns fornecidos por outros desenvolvedores. Com essa versão prévia das Habilidades, os desenvolvedores podem criar um novo bot (normalmente por meio do modelo de Assistente Virtual) e adicionar/remover Habilidades com uma única operação de linha de comando incorporando todas as alterações na Expedição e Configuração.     

As próprias Habilidades são Bots, chamados remotamente, e um modelo de desenvolvedor (.NET, TS) está disponível para facilitar a criação de novas Habilidades.

Um objetivo-chave de design das Habilidades era manter o protocolo de Atividade consistente e garantir que a experiência de desenvolvimento fosse a mais próxima possível de qualquer bot comum do SDK V4. 

![Cenários de Habilidades](./media/enterprise-template/skills-scenarios.png)

## <a name="bot-framework-skills"></a>Habilidades do Bot Framework

Atualmente fornecemos as seguintes Habilidades do Bot Framework, suportadas pelo Microsoft Graph e disponível em vários idiomas.

![Cenários de Habilidades](./media/enterprise-template/skills-at-build.png)

| NOME | DESCRIÇÃO |
| ---- | ----------- |
|[Habilidade de Calendário](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-calendar.md)|Adicione recursos de calendário ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de Email](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-email.md)|Adicione recursos de email ao seu assistente. Da plataforma Microsoft Graph e Google.|
|[Habilidade de tarefas](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-todo.md)|Adicione recursos de gerenciamento de tarefas ao seu assistente. Da plataforma Microsoft Graph.|
|[Habilidade de Ponto de Interesse](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-pointofinterest.md)|Localize pontos de interesse e direções. Da plataforma Azure Mapas e FourSquare.|
|[Habilidade Automotiva](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/automotive.md)|Habilidade de demonstração para segmento vertical da indústria que permite o controle de recursos do carro.|
|[Habilidades Experimentais](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/experimental.md)|Notícias, Reservas em Restaurantes e Previsão do Tempo.|

## <a name="getting-started"></a>Introdução

Consulte a [Introdução](https://github.com/Microsoft/AI/tree/master/docs#tutorials) para aprender como se beneficiar das Habilidades existentes e você mesmo construir as suas.
