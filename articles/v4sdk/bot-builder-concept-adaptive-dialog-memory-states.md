---
title: Como gerenciar o estado em diálogos adaptáveis
description: Como gerenciar o estado em diálogos adaptáveis
keywords: bot, estado de gerenciamento, escopos de memória, escopo do usuário, escopo da conversa, escopo da caixa de diálogo, escopo das configurações, caixas de diálogo adaptáveis
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 05/08/2020
ms.openlocfilehash: 6516699ba8c7b955cde7f35ee25ae48defd9eecb
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416906"
---
<!--
I'd keep prerequisites, managing state, and memory short-hand notation sections in the concept article, and move the rest to a ref topic. Then, possibly add a little more about how memory scopes and paths are used in the abstract to the concept article, common patterns, etc. I'd also convert the list of scopes into a table with short descriptions.
-->
# <a name="managing-state-in-adaptive-dialogs"></a>Como gerenciar o estado em diálogos adaptáveis

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os termos _com estado_ e _sem estado_ são expressões que descrevem se um aplicativo foi projetado para lembrar um ou mais eventos anteriores em determinada sequência de interações com um usuário (ou qualquer outra atividade). Com estado significa que _o aplicativo mantém_ o controle do estado de suas interações, geralmente salvando valores na memória na forma de uma propriedade. Sem estado significa que o aplicativo _não_ acompanha o estado das interações, o que significa que não há memória de nenhuma interação anterior e todas as solicitações de entrada precisam conter todas as informações relevantes necessárias para executar a ação solicitada. Considere o _estado_ como o conjunto atual de valores ou do conteúdo do bot, como a ID da conversa ou o nome do usuário ativo.

O SDK do Bot Framework segue os mesmos paradigmas dos aplicativos Web modernos e não gerencia ativamente o estado. No entanto, o SDK do Bot Framework fornece algumas abstrações que facilitam muito a incorporação do gerenciamento de estado ao bot. Esse tópico é abordado detalhadamente no artigo [Como gerenciar o estado][managing-state] do SDK do Bot Framework. Recomendamos que você leia e entenda as informações abordadas nele antes de ler este artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma compreensão geral de [Como funcionam os bots][bot-builder-basics].
* Uma compreensão geral dos diálogos adaptáveis. Para obter mais informações, confira [Introdução aos diálogos adaptáveis][introduction] e [Bibliotecas de diálogos][concept-dialog].
* Confira o artigo [Como gerenciar o estado][managing-state] do SDK do Bot Framework para obter uma visão geral do gerenciamento de estado.

## <a name="managing-state"></a>Gerenciar estado

Um bot é inerentemente sem estado. Para alguns bots, o bot pode operar sem informações adicionais ou há a garantia de que as informações necessárias estão contidas na mensagem de entrada. Para outros bots, o estado (por exemplo, em que ponto da conversa estamos ou a resposta recebida do usuário) é necessário para que o bot tenha uma conversa útil.

O SDK do Bot Framework define escopos de memória para ajudar os desenvolvedores a armazenar e recuperar os valores na memória do bot, para uso durante o processamento de loops e branches, ao criar mensagens dinâmicas e outros comportamentos no bot.

Isso possibilita que os bots criados com o SDK do Bot Framework executem ações como:

* Passar informações entre diálogos.
* Armazenar perfis e preferências do usuário.
* Lembrar-se de informações entre as sessões como a última consulta de pesquisa ou a última seleção feita pelo usuário.

Os diálogos adaptáveis oferecem um modo de acesso e de gerenciamento de memória e todos os diálogos adaptáveis usam esse modelo, o que significa que todos os componentes que fazem leituras ou gravações na memória têm uma forma consistente de lidar com as informações dentro dos escopos apropriados.

> [!NOTE]
> Todas as propriedades da memória, em todos os escopos da memória, são recipientes de propriedades, o que significa que você pode adicionar propriedades a eles conforme necessário.

Aqui estão os escopos de memória diferentes disponíveis em caixas de diálogo adaptáveis, cada um com um link para informações adicionais contidas no artigo de referência de Estados de memória:

* O [escopo do usuário][user-scope] é um escopo de dados persistentes para a ID do usuário com o qual você está convertida.
* O [escopo da conversa][conversation-scope] é de dados persistentes com escopo para a ID da conversa que você está tendo.
* O [escopo da caixa de diálogo][dialog-scope] persiste dados durante a vida útil da caixa de diálogo associada, fornecendo espaço de memória para cada caixa de diálogo ter uma escrituração persistente interna.
* [Ativar o escopo][turn-scope] fornece um local para compartilhar dados durante o tempo de vida da ativação atual.
* O [escopo das configurações][settings-scope] representa as configurações disponibilizadas para o bot por meio do sistema de configuração de configurações específicas da plataforma.
* [Esse escopo][this-scope] persiste dados durante a vida útil da ação associada. Isso é útil para ações de entrada, pois o tipo de vida normalmente ultrapassa um só turno da conversa.
* [Escopo de classe][class-scope] contém as propriedades de instância da caixa de diálogo ativa.

## <a name="memory-short-hand-notations"></a>Notações abreviadas da memória

Há poucas notações abreviadas compatíveis para acessar os escopos da memória específicos.

| Símbolo | Uso           | Expansão                             | Observações                                                                                                                   |
|--------|-----------------|---------------------------------------|------------------------------------------------------------------------------------------------------------------------ |
| $      | `$userName`     | `dialog.userName`                     | Notação abreviada que representa o escopo do diálogo.                                                                   |
| #      | `#intentName`   | `turn.recognized.intents.intentName`  | Abreviação usada para indicar uma intenção nomeada retornada pelo reconhecedor.                                                    |
| @      | `@entityName`   | `turn.recognized.entities.entityName` | `@entityName` retorna o primeiro e o _único_ primeiro valor encontrado para a entidade, independentemente da cardinalidade do valor. |
| @@     | `@@entityName`  | `turn.recognized.entities.entityName` | O `@@entityName` retornará o valor real da entidade, preservando a cardinalidade do valor.                          |
| %      | `%propertyName` | `class.propertyName`                  | Usado para fazer referência a propriedades de instância (por exemplo, `MaxTurnCount`, `DefaultValue` etc.).                                         |

## <a name="additional-information"></a>Informações adicionais

* Para obter informações mais detalhadas sobre o gerenciamento de estado, incluindo exemplos, consulte o artigo [Gerenciamento de estado em caixas de diálogo adaptáveis – guia de referência][managing-state-ref] .

<!-- Links to other articles-->
[managing-state-ref]:../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md
[bot-builder-basics]:bot-builder-basics.md
[introduction]:bot-builder-adaptive-dialog-introduction.md
[managing-state]:bot-builder-concept-state.md
[recognizers]:bot-builder-concept-adaptive-dialog-recognizers.md
[botframework-activity]:https://github.com/microsoft/botbuilder/blob/master/specs/botframework-activity/botframework-activity.md
[foreach-action]:../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#foreach
[setproperties-action]:../adaptive-dialog/adaptive-dialog-prebuilt-actions.md#setproperties
[concept-dialog]:bot-builder-concept-dialog.md

<!-- Links to the Adaptive dialogs managing state reference article-->
[user-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#user-scope
[conversation-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#conversation-scope
[dialog-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#dialog-scope
[dialog-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#dialog-scope
[turn-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#turn-scope
[settings-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#settings-scope
[this-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#this-scope
[class-scope]: ../adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md#class-scope
