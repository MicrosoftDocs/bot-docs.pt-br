---
title: Definir um reconhecedor de LUIS como gatilho de tarefa | Microsoft Docs
description: Saiba como configurar o reconhecedor de reconhecimento vocal como gatilho de tarefa usando LUIS.ai
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 39fe222fb1d54346b33617c425b1fdf2d56daa0d
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297417"
---
# <a name="define-a-luis-recognizer-as-task-trigger"></a>Definir um reconhecedor de LUIS como disparador de tarefa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de Conversa serão lançados ainda este ano.

Na maioria das vezes, o usuário expressa a intenção de executar uma tarefa em linguagem natural. Com o Designer de Conversa, é possível configurar facilmente um modelo de reconhecimento de linguagem natural para o bot acionado pelo <a href="https://luis.ai" target="_blank">LUIS.ai</a>.

Para fazer isso, selecione o tipo de gatilho **Usuário diz ou digita algo**. Isso fornecerá a opção de especificar o nome da **intenção**. 

É possível pesquisar intenções existentes ou criar uma nova no campo **Qual a intenção da linguagem?**.

## <a name="create-a-new-intent"></a>Criar uma nova intenção

Para criar uma nova intenção, digite o nome da intenção e clique em **Criar nova intenção**. Insira enunciados de exemplo para os possíveis assuntos que você espera que os usuários digam e disparem essa tarefa específica.

Por exemplo, um bot de cafeteria deve ser capaz de realizar a tarefa de localizar cafeterias próximas ao usuário. Para lidar com esse cenário, selecione **Usuário diz ou digita algo** e defina o **Nome da intenção** como "LocationNearMe". Em seguida, forneça enunciados de exemplo para essa intenção. Por exemplo:  
- *encontrar locais perto de mim*
- *encontrar cafeterias perto de mim*
- *a unidade da Redmond está aberta agora?*
- *há uma unidade em Seattle?*
- *quais cafeterias estão abertas agora?*
- *onde posso comer alguma coisa?*
- *eu gostaria de comer alguma coisa*
- *estou com fome*

Insira quantos enunciados puder imaginar e que ajudem a expressar a intenção do usuário para disparar essa tarefa específica.

## <a name="default-intents-provisioned-for-your-bot"></a>Intenções padrão provisionadas para o bot

Por padrão, o bot é provisionado com 4 intenções. 
- **Nenhum**: essa é a intenção de fallback (padrão) para o bot. Use essa intenção para ajudar a capturar assuntos que o bot ainda não sabe como responder.
- **Ajuda**: configure com enunciados de exemplo que ajudam a determinar se o usuário precisa de ajuda. *Ajuda, eu preciso de ajuda, o que posso dizer?, Estou parado no trânsito*, e assim por diante.
- **Saudação**: configure com enunciados de exemplo que ajudam a combinar a intenção de saudação, como *Oi, Olá, Bom dia, Como você está bot?*, e assim por diante.
- **Cancelar**: configure com enunciados de exemplo para intenção de cancelamento. *Pare, Cancele isso, Não faça isso, Reverter*, e assim por diante.

## <a name="create-and-label-entities"></a>Criar e rotular entidades

Além de ajudar a determinar a intenção do usuário, o reconhecimento vocal também pode ajudar a determinar entidades específicas de interesse relevantes para a tarefa. Por exemplo, quando o usuário disser *encontrar cafeterias próximas à Redmond*, será possível extrair*Redmond* como um possível valor para a ***localização***. 

Para configurar entidades da tarefa, na cadeia de caracteres do **Enunciado**, selecione a parte do enunciado que deve ser um exemplo para um valor de entidade. Atribua isso a uma entidade existente ou crie uma nova. Para criar uma nova entidade, digite o nome da entidade no campo **Pesquisar ou criar** e clique em **Criar nova entidade**. 

# <a name="supported-entity-types"></a>Tipos de entidade com suporte

O reconhecimento vocal possibilita que você crie diferentes tipos de entidades. Ao criar uma nova entidade, será necessário especificar um `type`. 

Os tipos disponíveis são:

- **Simples**: esse é o tipo *padrão*.
- **Lista**: use esse tipo se a entidade tiver um conjunto finito de valores possíveis. Exemplo: *Cor*, *Cidade*.
- **Hierárquico**: use esse tipo para criar entidades com relacionamento pai-filho. Exemplo: *fromCity* e *toCity* têm a entidade *City* como pai
- **Composição**: use esse tipo para criar grupos de valores que formam uma unidade significativa. Exemplo: *Cidade* e *Estado* juntos formam a entidade *Local*.

<!-- # pre-built entity types TBD -->

# <a name="entities-in-use"></a>Entidades em uso

Na medida em que você criar e adicionar entidades à seção de reconhecimento vocal, a tabela **Entidades em uso** é atualizada com a lista de entidades que essa tarefa específica usa. É possível adicionar manualmente à lista outras entidades usadas nessa tarefa. 

As opções disponíveis são:

- **Código**: essa é uma entidade criada no seu script personalizado. É possível especificá-lo aqui para ajudar com recursos de criação como o IntelliSense.

<!-- # Use as help tip TBD  -->

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Reconhecedor de código](conversation-designer-code-recognizer.md)
