---
title: Modelo de resposta | Microsoft Docs
description: Saiba como configurar o modelo de resposta para os bots de Designer de Conversa.
author: vkannan
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: b09b7fa5f5672c121711deb2edcdc9718a79983f
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39297420"
---
# <a name="response-template-for-conversation-designer-bots"></a>Modelo de resposta para bots de Designer de Conversa
> [!IMPORTANT]
> O Designer de Conversa ainda não está disponível para todos os clientes. Mais detalhes sobre a disponibilidade do Designer de Conversa serão lançados ainda este ano.

A geração de linguagem permite que o bot comunique-se com o usuário de uma maneira natural e avançada, usando mensagens de resposta variável. Essas mensagens são gerenciadas por meio de modelos de resposta no Designer de Conversa.

Os modelos de resposta permitem a reutilização da resposta e ajudam a manter uma linguagem e sonoridade consistentes nas respostas do bot. 

## <a name="create-response-templates"></a>Criar modelos de resposta

No Designer de Conversa é possível criar modelos de resposta que podem ser reutilizados em qualquer situação em que seja necessário enviar uma resposta a um usuário. 

Modelos de resposta simples definem uma coleção única de possíveis enunciados de exibição e fala. É possível usar esses modelos nas respostas do bot, nos estados de solicitação ou em cartões adaptáveis para reconstruir uma cadeia de caracteres totalmente resolvida.

Para adicionar um modelo de resposta simples, faça o seguinte:
1. No painel esquerdo, clique em **Adicionar**. Um menu de contexto será exibido.
2. Clique em **Resposta simples**. Uma janela de edição será exibida no painel de conteúdo principal.
3. No campo **Nome da resposta simples**, insira um nome para essa resposta simples.
4. No campo **Resposta do bot ao usuário**, insira a resposta uma frase de cada vez.
5. No canto superior direito, clique em **Salvar** ao concluir a configuração da resposta simples. 

Por exemplo, é possível criar um modelo de frase de confirmação (chame-o de "AckPhrase") com os itens a seguir:

- OK
- Claro
- Pode apostar
- Eu ficaria feliz em ajudar
- Prazer em ajudá-lo

Usando esse modelo, o tempo de execução de conversa é resolvido aleatoriamente para uma dessas cadeias de caracteres, de modo que as respostas do bot pareçam mais naturais do que maçantes e monótonas.

## <a name="conditional-response-templates"></a>Modelos de resposta condicional

Os modelos de resposta condicional especificam várias respostas com condições. A função de retorno de chamada que você escreve ajuda o mecanismo de geração de linguagem a decidir qual cadeia de caracteres de resposta usar com base na condição especificada. 

Por exemplo, um modelo de hora do dia deve resolver para "bom dia" ou "boa noite" com base na hora real do dia. 

Para adicionar um modelo de resposta condicional, faça o seguinte:
1. No painel esquerdo, clique em **Adicionar**. Um menu de contexto será exibido.
2. Clique em **Resposta condicional**. Uma janela de edição será exibida no painel de conteúdo principal.
3. No campo **Nome de resposta condicional**, insira um nome para esse modelo.
4. No campo **Resposta condicional**, insira as frases uma de cada vez. Por exemplo, para o "timeOfDayTemplate", insira "Bom dia" e "Boa noite" como duas respostas possíveis no modelo.
5. Para a resposta "Bom dia", especifique o **Nome da condição** como "manhã". Da mesma forma, para a resposta "Boa noite", especifique o **Nome da condição** como "noite". O script personalizado que você irá gravar retornará uma dessas condições.
6. No campo **Código para executar na execução** insira um nome de função de retorno de chamada (por exemplo, `fnResolveTimeOfDayTemplate`). Em seguida, clique em **Exibir código** para carregar o editor de *Scripts**. A partir daí, será possível definir a implementação dessa função de retorno de chamada.
7. No canto superior direito, clique em **Salvar** para salvar as alterações e criar esse modelo.

Exemplo de código para a função de retorno de chamada *fnResolveTimeOfDayTemplate*. Essa função retornará a cadeia de caracteres que corresponderá ao **Nome da condição** especificado pela resposta, "manhã" ou "noite".

```javascript
module.exports.fnTimeOfDayTemplate = function(context) {
    var currentTime = new Date().getHours();
    if(currentTime >= 12) {
        return "evening!";
    } else {
        return "morning!";
    }
}
```

## <a name="send-a-response-to-user"></a>Enviar uma resposta ao usuário

Para enviar uma resposta ao usuário usando modelos de resposta, coloque os nomes dos modelos entre colchetes. Por exemplo, para usar o modelo de resposta simples "AckPhrase" em um estado de comentários, insira a **Resposta do Bot à frase do usuário** como `[AckPhrase], I will get that done for you right away`

Se você quiser usar colchetes nas respostas, utilize "\" como o caractere de escape para instruir o tempo de execução de conversa a ignorar a resolução para colchetes.

Se você tiver entidades definidas, também poderá usá-las na resposta ao usuário. É possível referir-se às entidades na geração de linguagem, colocando o nome da entidade entre chaves. Por exemplo, se o bot tiver uma entidade `location` será possível consultá-lo nas respostas, conforme a seguir: `[AckPhrase], I can help you find a table at {location}.`

## <a name="nesting-response-templates"></a>Aninhar modelos de resposta

Modelos de resposta podem ser "aninhados", ou seja, um modelo de resposta pode ter referências a outro modelo de resposta. Por exemplo, é possível usar o modelo de resposta simples `AckPhrase` e o modelo de resposta condicional `timeOfDayTemplate` para construir um novo modelo de resposta `timeBasedGreeting` que use os dois modelos para resolver a resposta final. 

> [!TIP]
> Embora o aninhamento de modelo seja um recurso poderoso, verifique se os modelos aninhados não causam um loop infinito. Isto é, evite situações em que tenha `AckPhrase` chamando `timeOfDayTemplate` e `timeOfDayTemplate` chamando de volta para `AckPhrase`.

Por exemplo, crie um nome de **Resposta simples** novo `timeBasedGreeting` e insira o texto a seguir como uma possível **Resposta do Bot ao usuário**  para este modelo: `[timeOfDayTemplate] [AckPhrase], ... `

## <a name="define-user-utterance-as-help-tips"></a>Definir enunciado do usuário como dicas de ajuda

Ao definir o **Enunciado** do usuário, você também define um enunciado como uma **Dica de Ajuda**. Para definir um enunciado como uma **Dica de Ajuda**, clique em `...`, à direita do enunciado e selecione **Usar como dica de ajuda**. 

A dica de ajuda é usada no modelo interno de geração de linguagem `[builtin-tasktips]` em qualquer lugar em que você define um campo **Resposta do bot ao usuário**. Por exemplo, você poderia redigir uma resposta que diz: `Sorry I did not understand that. Here are some things you can try - [builtin.tasktips]`

Se uma tarefa não tiver **Dica de Ajuda** definida, então `[builtin-tasktips]` será resolvido para uma cadeia de caracteres vazia. Se uma tarefa tiver várias **Dicas de Ajuda** definidas, então `[builtin-tasktips]` selecionará uma aleatoriamente sempre que uma resposta for dada.

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Cartões adaptáveis](conversation-designer-adaptive-cards.md)
