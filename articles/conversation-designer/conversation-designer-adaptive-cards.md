---
title: Configurar cartões adaptáveis – Serviço de Bot
description: Saiba como configurar cartões adaptáveis.
author: vkannan
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 85693eb188b68dca5829c54af3160967ce2acaf6
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416447"
---
# <a name="configure-adaptive-cards"></a>Configurar cartões adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de Conversa serão lançados ainda este ano.

<a href="http://adaptivecards.io" target="_blank">Cartões Adaptáveis</a> é um novo esquema que define cartões de interface do usuário avançados para uso em vários pontos de extremidade diferentes, incluindo canais do Microsoft Bot Framework. 

O Designer de Conversa fornece um ambiente de criação amplamente integrado para criar, visualizar e usar os cartões adaptáveis em seus bots. 

Os cartões adaptáveis podem ser definidos em vários lugares importantes diferentes.

- Uma resposta simples para executar uma ação para uma tarefa.
- Em estado de comentários em um diálogo.
- Em estados de solicitação em um diálogo. Observe que os prompts podem ter cartões separados: um para a resposta e outro para nova solicitação.

Para definir um cartão adaptável, navegue até o editor relevante. Procure e escolha um dos modelos de Cartão Adaptável existentes ou crie o seu próprio no editor de código JSON. 

À medida que cria um cartão, uma prévia avançada do cartão é renderizada no portal de criação.

> [!NOTE]
> Os recursos dos cartões adaptáveis estão em desenvolvimento contínuo. Nem todos os canais dão suporte a todos os recursos de cartão adaptável no momento. Para ver os recursos com suporte em cada canal, confira a seção Status do canal.

## <a name="input-form"></a>Formato de entrada

Os cartões adaptáveis podem conter formulários de entrada. No Designer de Conversa, os formulários são integrados a entidades de tarefa. Por exemplo, se um campo tiver um `id` de **myName**, e a ação `Submit` de formulário for executada, uma `taskEntity` com o nome **myName** será criado e conterá o valor do campo. 

O snippet de código abaixo mostra como a entidade **myName** é definida no código:

```javascript
{
   "type": "Input.Text",
   "id": "myName",
   "placeholder": "Last, First"
}
```

Além disso, se um campo tiver uma ID de `@task`, o valor do campo será usado como um nome de tarefa. Quando esse campo for disparado (por exemplo, com um clique de botão), a tarefa nomeada será executada. 

Veja este snippet de código, por exemplo:

```javascript
{
  'type': 'Action.Submit',
  'title': 'Search',
  'speak': '<s>Search</s>',
  'data': {
    '@task': 'Hotel Search'
  }
}
```

Ao clicarem nesse botão, uma ação de envio é disparada, e `context.sticky` é definido como `Hotel Search`. Isso resultará na execução da tarefa **Pesquisa de Hotel**. Para usar esse recurso, verifique se `@task` corresponde a um nome de tarefa que você definiu no Designer de Conversa.

## <a name="use-entities-and-language-generation-templates"></a>Usar entidades e modelos de geração de linguagem
Os cartões adaptáveis oferecem suporte a resolução total de geração de linguagem.

* `entityName` usa entidades dentro do cartão.
* `responseTemplateName` usa modelos de resposta simples ou condicional dentro do cartão.

Você pode aprender mais sobre os cartões adaptáveis aqui  TODO: Inserir link para a documentação do esquema de cartões adaptáveis -->

## <a name="sample-adaptive-card-payload"></a>Exemplo de conteúdo do cartão adaptável

O JSON a seguir mostra o conteúdo de um cartão adaptável.

```json
{
    "$schema": "https://microsoft.github.io/AdaptiveCards/schemas/adaptive-card.json",
    "type": "AdaptiveCard",
    "version": "1.0",
    "body": [
        {
            "speak": "<s>Serious Pie is a Pizza restaurant which is rated 9.3 by customers.</s>",
            "type": "ColumnSet",
            "columns": [
                {
                    "type": "Column",
                    "size": "2",
                    "items": [
                        {
                            "type": "TextBlock",
                            "text": "[Greeting], [TimeOfDayTemplate], You can eat in {location}",
                            "weight": "bolder",
                            "size": "extraLarge"
                        },
                        {
                            "type": "TextBlock",
                            "text": "9.3 · $$ · Pizza",
                            "isSubtle": true
                        },
                        {
                            "type": "TextBlock",
                            "text": "[builtin.feedback.display]",
                            "wrap": true
                        }
                    ]
                },
                {
                    "type": "Column",
                    "size": "1",
                    "items": [
                        {
                            "type": "Image",
                            "url": "http://res.cloudinary.com/sagacity/image/upload/c_crop,h_670,w_635,x_0,y_0/c_scale,w_640/v1397425743/Untitled-4_lviznp.jpg",
                            "size":"auto"
                        }
                    ]
                }
            ]
        }
    ],
    "actions": [
        {
            "type": "Action.Http",
            "method": "POST",
            "title": "More Info",
            "url": "http://foo.com"
        },
        {
            "type": "Action.Http",
            "method": "POST",
            "title": "View on Foursquare",
            "url": "http://foo.com"
        }
    ]
}
```

