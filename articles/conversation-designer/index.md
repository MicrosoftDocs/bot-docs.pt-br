---
title: Designer de Conversa| Microsoft Docs
description: Visão geral do Designer de Conversa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 42e9e8137619fff2ca86f82fea1fe81aa348449b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39111550"
---
# <a name="conversation-designer"></a>Designer de Conversa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Surgirão mais detalhes sobre a disponibilidade do Designer de Conversa posteriormente este ano.

O Designer de Conversa é uma estrutura avançada que fornece construtor de bot visual, um modelo declarativo para definir bots e um tempo de execução que simplifica a criação de bots.

Criar bots de conversa úteis requer uma abordagem unificada para a integração de controles conversacionais, reconhecimento vocal, diálogos e geração de linguagem, ao mesmo tempo que mantém o foco na experiência do usuário e na integração com uma lógica de negócios existente. O Designer de Conversa permite que você crie bots com um caminho integrado que abrange criação, depuração, LUIS, análise, reconhecimento de fala e compatibilidade com os bots existentes do SDK do Bot Framework.

Usando o Designer de Conversa, você pode criar bots que se adaptam facilmente às modalidades de entrada/saída disponíveis e tiram proveito dos recursos a seguir: 

- um tempo de execução eficiente que minimiza a sobrecarga de gerenciamento de estado
- estados conversacionais internos que permitem uma modelagem fácil e visual de uma conversa
- separação do código e da interface do usuário do bot
- suporte interno para estruturas de IA, como <a href="https://luis.ai" target="_blank">LUIS</a> e <a href="https://www.microsoft.com/cognitive-services/en-us/speech-api" target="_blank">reconhecimento de fala</a>
- suporte interno para geração de idioma usando modelos de resposta simples e condicionais
- suporte para [Cartões adaptáveis](conversation-designer-adaptive-cards.md)

## <a name="prerequisites"></a>Pré-requisitos

- O Designer de Conversa exige uma assinatura do Azure. Você pode começar a usar <a href="https://azure.microsoft.com/en-us/" target="_blank">aqui</a>
- Se você ainda não fez isso, verifique se você entrou no [portal do LUIS](https://luis.ai) pelo menos uma vez depois de te criado uma conta com eles.
- Familiaridade com programação JavaScript. Funções de script personalizadas são escritas em JavaScript.
- Microsoft Edge ou Google Chrome

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Criar um bot](conversation-designer-create-bot.md)

## <a name="additional-resources"></a>Recursos adicionais
Saiba mais sobre como criar bots usando o Designer de Conversa:
- [Reconhecimento Vocal](conversation-designer-luis.md): configurar o Serviço Inteligente de Reconhecimento Vocal para seu bot
- [Diálogo](conversation-designer-dialogues.md): configurar diálogos de conversa para seu bot
- [Modelos de resposta](conversation-designer-response-templates.md): dicas rápidas sobre como configurar modelos de resposta para seu bot
- [Cartões adaptáveis](conversation-designer-adaptive-cards.md): configurar interações visuais avançadas para seu bot usando cartões adaptáveis
