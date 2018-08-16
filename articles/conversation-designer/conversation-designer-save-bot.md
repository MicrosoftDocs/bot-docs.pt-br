---
title: Salvar um bot de Designer de conversa | Microsoft Docs
description: Aprenda como salvar e treinar o modelo de compreensão de idioma e o reconhecimento de fala principal em um robô do Designer de conversas.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 3a911158c379f879c0be604fb5e8ba30ab22ee44
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297404"
---
# <a name="saving-your-conversation-designer-bot"></a>Salvando seu bot de Designer de conversa
> [!IMPORTANT]
> Conversa Designer ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de conversação serão lançados ainda este ano.

Enquanto trabalha no Designer de conversação, todo o seu trabalho é armazenado em cache na memória do navegador. Para confirmar suas alterações, clique no **salvar** botão localizado no canto superior esquerdo do menu de navegação à esquerda. Para evitar perder o trabalho, é recomendável que você salve seu trabalho frequentemente. Além de clicar no botão **Salvar**, você também pode salvar seu trabalho usando o atalho de teclado **CTRL + S**.

## <a name="trains-luis-and-primes-speech-recognition"></a>Treina o LUIS e prepara o reconhecimento de fala

Clicar no botão **Salvar** salvará as alterações no bot e executará algumas tarefas adicionais. Ao contrário do atalho de teclado, clicando na **salvar** botão também instruir o Designer de conversa para executar as seguintes tarefas:

- Treina quaisquer novas intenções e entidades LUIS para o bot e publica o modelo LUIS localmente no seu Serviço de Bot (se necessário). Essas tentativas podem ser adicionadas no Designer de conversa ou em correspondente do bot [LUIS](https://luis.ai) aplicativo.
- Atualiza o tempo de execução de conversação para usar o novo modelo LUIS.
- Desobstrução do reconhecimento de fala, preparando-se e enviando suas declarações de exemplo para serviços Cognitivos da Microsoft, que aprimora imensamente a precisão do reconhecimento de fala para este bot.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Bot de teste](conversation-designer-debug-bot.md)
