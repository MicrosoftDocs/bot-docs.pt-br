---
title: Gerenciar Estado | Microsoft Docs
description: Descreve como o estado funciona dentro do SDK do Bot Framework.
keywords: estado, estado do bot, estado da conversa, estado do usuário
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 037331b10719a47fba42485b19d5ecc9dab08cbe
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215478"
---
# <a name="managing-state"></a>Gerenciar estado

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

O estado dentro de um bot segue os mesmos paradigmas dos aplicativos Web modernos e o SDK do Bot Framework oferece algumas abstrações para facilitar o gerenciamento do estado.

Assim como acontece com os aplicativos Web, um bot é inerentemente sem estado; uma instância diferente do seu bot pode lidar com qualquer turno da conversa. Para alguns bots, essa simplicidade é preferível; o bot também pode operar sem informações adicionais ou as informações necessárias são garantidas dentro da mensagem de entrada. Para outros, o estado (por exemplo, onde estamos na conversa ou os dados sobre o usuário recebidos anteriormente) é necessário para o bot ter uma conversa útil.

**Por que eu preciso de um estado?**

Manter o estado permite que seu bot tenha conversas mais significativas, lembrando-se de algumas coisas sobre o usuário ou a conversa. Por exemplo, se você já falou antes com um usuário, pode salvar as informações anteriores sobre ele para não precisar pedi-las novamente. O estado também mantém os dados por mais tempo que o turno atual, assim seu bot mantém as informações ao longo de uma conversa com vários turnos.

Em relação aos bots, há algumas camadas que usam o estado, que abordaremos aqui: camada de armazenamento, gerenciamento do estado (contida no estado do bot no diagrama abaixo) e acessadores de propriedades do estado. Este diagrama ilustra as partes da sequência de interação entre essas camadas, com as setas sólidas representando uma chamada de método e as setas tracejadas representando a resposta (com ou sem um valor retornado).

![estado do bot](media/bot-builder-state.png)

O fluxo desse diagrama é explicado nas seções a seguir com os detalhes dessas camadas.

## <a name="storage-layer"></a>Camada de armazenamento

Começando no back-end, onde as informações do estado são, de fato, armazenadas, está a nossa *camada de armazenamento*. Ela pode ser considerada nosso armazenamento físico, como na memória, o Azure ou um servidor de terceiros.

O SDK do Bot Framework inclui algumas implementações para a camada de armazenamento:

- **Armazenamento da memória** implementa o armazenamento na memória para fins de teste. O armazenamento de dados na memória serve apenas para teste local, pois esse armazenamento é volátil e temporário. Os dados serão limpos sempre que o bot for reiniciado.
- O **Armazenamento de Blobs do Azure** se conecta a um banco de dados de objeto do Armazenamento de Blobs do Azure.
- O **Armazenamento do Azure Cosmos DB** se conecta a um banco de dados NoSQL do Cosmos DB.

Para obter instruções sobre como se conectar a outras opções de armazenamento, confira [Gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md).

## <a name="state-management"></a>Gerenciamento de estado

O *Gerenciamento do estado* automatiza a leitura e a gravação do estado do bot na camada de armazenamento subjacente. O estado é armazenado como as *propriedades do estado*, que são pares de chave-valor que seu bot pode ler e gravar por meio do objeto de gerenciamento de estados, sem se preocupar com a implementação subjacente específica. Essas propriedades do estado definem como essas informações são armazenadas. Por exemplo, ao recuperar uma propriedade definida como uma classe ou objeto específico, você sabe como os dados serão estruturados.

Essas propriedades do estado são agrupadas em "buckets" com escopo, que são apenas coleções para ajudar a organizá-las. O SDK inclui três "buckets":

- Estado do usuário
- Estado da conversa
- Estado da conversa privada

Todos esses buckets são subclasses da classe do *estado do bot*, que podem ser derivadas para definir outros tipos de buckets com escopos diferentes.

Esses buckets predefinidos estão no escopo para determinada visibilidade, dependendo do bucket:

- O estado do usuário está disponível em qualquer turno no qual o bot esteja conversando com tal usuário naquele canal, independentemente da conversa
- O estado da conversa está disponível em qualquer turno em uma conversa específica, independentemente do usuário (ou seja, conversas em grupo)
- O estado da conversa privada tem escopo na conversa específica e naquele usuário específico

> [!TIP]
> O escopo do estado de usuário e da conversa é definido pelo canal.
> A mesma pessoa que usa canais diferentes para acessar seu bot aparece como usuários diferentes, um para cada canal, e cada um com um estado de usuário distinto.

As chaves usadas para cada um desses buckets predefinidos são específicas para o usuário e a conversa, ou ambos. Ao definir o valor da propriedade do estado, a chave é definida para você internamente com informações contidas no contexto do turno para garantir que cada usuário ou conversa seja colocado no bucket e na propriedade corretos. Especificamente, as chaves são definidas da seguinte maneira:

- O estado do usuário cria uma chave usando a *ID do canal* e a *ID de*. Por exemplo, _{Activity.ChannelId}/users/{Activity.From.Id}#SeuNomePropriedade_
- O estado da conversa cria uma chave usando a *ID do canal* e a *ID da conversa*. Por exemplo, _{Activity.ChannelId}/conversations/{Activity.Conversation.Id}#SeuNomePropriedade_
- O estado da conversa privada cria uma chave usando a *ID do canal*, a *ID de* e a *ID da conversa*. Por exemplo, _{Activity.ChannelId}/conversations/{Activity.Conversation.Id}/users/{Activity.From.Id}#SeuNomePropriedade_

### <a name="when-to-use-each-type-of-state"></a>Quando usar cada tipo de estado

O estado da conversa é bom para acompanhar o contexto da conversa, por exemplo:

- Se o bot perguntou algo ao usuário, e qual foi a pergunta
- Qual é o tópico atual da conversa, ou qual foi o último

O estado do usuário é bom para acompanhar informações sobre o usuário, por exemplo:

- Informações não críticas sobre o usuário, como o nome e as preferências, uma configuração de alarme ou uma preferência de alerta
- Informações sobre a última conversa que ele teve com o bot
  - Por exemplo, um bot de suporte ao produto pode controlar sobre quais produtos o usuário perguntou.

O estado da conversa privada é bom para os canais que dão suporte a conversas em grupo, mas nos quais você deseja acompanhar informações específicas sobre o usuário e a conversa. Por exemplo, se você tivesse um bot para clique em sala de aula:

- O bot poderia agregar e exibir as respostas dos alunos para uma determinada pergunta.
- O bot poderia agregar o desempenho de cada aluno e retransmitir isso para eles ao final da sessão.

Para obter detalhes sobre como usar esses buckets predefinidos, confira o [artigo de instruções do estado](bot-builder-howto-v4-state.md).

### <a name="connecting-to-multiple-databases"></a>Conectar-se a vários bancos de dados

Se o seu bot precisar se conectar a vários bancos de dados, crie uma camada de armazenamento para cada banco de dados.
Para cada camada de armazenamento, crie os objetos de gerenciamento de estado que você precisa para dar suporte a suas propriedades de estado.

## <a name="state-property-accessors"></a>Acessadores de propriedades do estado

Os *acessadores de propriedades do estado* são usados para realmente ler ou gravar uma de suas propriedades do estado e fornecer os métodos *get*, *set* e *delete* para acessar as propriedades do estado de dentro de um turno. Para criar um acessador, você deve fornecer o nome da propriedade, que geralmente acontece quando está inicializando seu bot. Em seguida, é possível usar esse acessador para obter e manipular essa propriedade do estado do bot.

Os acessadores permitem que o SDK obtenha o estado no armazenamento subjacente e atualizam o *cache do estado* do bot para você. O cache do estado é um cache local mantido pelo bot que armazena o objeto de estado para você, permitindo operações leitura e gravação sem acessar o armazenamento subjacente. Se ainda não estiver no cache, chamar o método *get* do acessador irá recuperar o estado e também colocá-lo no cache. Depois de recuperada, a propriedade do estado pode ser manipulada como uma variável local.

O método *delete* do acessador remove a propriedade do cache e também a exclui do armazenamento subjacente.

> [!IMPORTANT]
> Para a primeira chamada do método *get* do acessador, você deve fornecer um método de fábrica para criar o objeto, caso ele ainda não exista em seu estado. Se não for fornecido nenhum método de fábrica, você receberá uma exceção. Detalhes sobre como usar um método de fábrica podem ser encontrados no [artigo de instruções do estado](bot-builder-howto-v4-state.md).

Para persistir as alterações feitas na propriedade do estado que você obtém do acessador, a propriedade no cache de estado deve ser atualizada. Você pode fazer isso chamando o método *set* do acessador, que define o valor da propriedade no cache e estará disponível se precisar ser lido ou atualizado posteriormente nesse turno. Para realmente persistir esses dados no armazenamento subjacente (e, portanto, disponibilizá-los após o turno atual), você deve [salvar seu estado](#saving-state).

### <a name="how-the-state-property-accessor-methods-work"></a>Como funcionam os métodos do acessador de propriedades do estado

Os métodos do acessador são a principal maneira do bot interagir com o estado. Como cada um funciona e como as camadas subjacentes interagem:

- O método *get* do acessador:
  - O acessador solicita a propriedade do cache do estado.
  - Se a propriedade estiver no cache, retorne-a. Caso contrário, obtenha-a no objeto de gerenciamento do estado.
    (Se ainda não estiver no estado, use o método de fábrica fornecido na chamada *get* dos acessadores.)
- O método *set* do acessador:
  - Atualize o cache do estado com o novo valor da propriedade.
- O método *save changes* do objeto de gerenciamento do estado:
  - Verifique as alterações da propriedade no cache de estado.
  - Grave essa propriedade no armazenamento.

## <a name="saving-state"></a>Salvando o estado

Quando você chama o método set do acessador para registrar o estado atualizado, a propriedade do estado ainda não foi salva em seu armazenamento persistente e somente está salva no cache do estado do bot. Para salvar as alterações no cache de estado para o estado persistente, você deve chamar o método *save changes* do objeto de gerenciamento do estado, que está disponível na implementação da classe de estado do bot mencionada acima (como os estados do usuário ou da conversa).

Chamar o método save changes de um objeto de gerenciamento do estado (ou seja, os buckets mencionados acima) salva todas as propriedades no cache de estado que você configurou naquele ponto do bucket, mas não qualquer bucket que possa haver no estado do bot.

> [!TIP]
> O estado do bot implementa um comportamento de "última gravação prevalece", no qual a última gravação será marcada acima do estado gravado anteriormente. Isso pode funcionar para muitos aplicativos, mas tem implicações, especialmente nos cenários de expansão em que pode haver algum nível de simultaneidade ou latência.

Se você tiver um middleware personalizado que possa atualizar o estado após a conclusão do manipulador de turnos, considere [manipular o estado no middleware](bot-builder-concept-middleware.md#handling-state-in-middleware).

## <a name="additional-resources"></a>Recursos adicionais

- [Estado do diálogo](bot-builder-concept-dialog.md#dialog-state)
- [Gravar diretamente no armazenamento](bot-builder-howto-v4-storage.md)
- [Salvar dados da conversa e do usuário](bot-builder-howto-v4-state.md)
