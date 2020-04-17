---
title: Implementar um armazenamento personalizado para seu bot – Serviço de Bot
description: Como criar um armazenamento personalizado no SDK do Bot Framework v4.0
keywords: personalizado, armazenamento, estado, diálogo
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3ab03b552afa780845c8be84df62d97c1f0decd1
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75798606"
---
# <a name="implement-custom-storage-for-your-bot"></a>Implementar um armazenamento personalizado para seu bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

Interações do bot se enquadram em três áreas: primeiro, o intercâmbio de atividades com o Serviço de Bot do Azure. Segundo, o carregamento e salvamento do diálogo de estado com um Store e, por fim, qualquer outro serviço de back-end que o bot precise para trabalhar e concluir seu trabalho.

![diagrama de expansão](../media/scale-out/scale-out-interaction.png)


## <a name="prerequisites"></a>Pré-requisitos
- O código de exemplo completo usado neste artigo pode ser encontrado aqui: [Exemplo de C#](https://aka.ms/scale-out).

Neste artigo, exploraremos a semântica em torno das interações do bot com o Serviço de Bot do Azure e o Store.

O Bot Framework inclui uma implementação padrão. Essa implementação provavelmente atenderá às necessidades de muitos aplicativos e, para fazer uso dela, basta juntar as peças com algumas linhas de código de inicialização. Muitos dos exemplos ilustram exatamente isso.

No entanto, o objetivo aqui é descrever o que você pode fazer quando a semântica da implementação padrão não funciona tão bem quanto o desejado em seu aplicativo. O ponto básico é que essa é uma estrutura e não é um aplicativo pronto com um comportamento fixo, ou seja, a implementação de muitos dos mecanismos na estrutura é apenas a implementação padrão e não somente a implementação.

Especificamente, a estrutura não dita a relação entre o intercâmbio de atividades com o Serviço de Bot do Azure e o carregamento e salvamento de qualquer estado de Bot; ele simplesmente fornece um padrão. Para ilustrar melhor esse ponto, desenvolveremos uma implementação alternativa que tem semânticas diferentes. Essa solução alternativa fica igualmente bem na estrutura e pode até mesmo ser mais apropriada para o aplicativo que está sendo desenvolvido. Em geral, depende do cenário.

## <a name="behavior-of-the-default-botframeworkadapter-and-storage-providers"></a>Comportamento dos provedores BotFrameworkAdapter e Storage padrão

Em primeiro lugar, vamos examinar a implementação padrão que é fornecida como parte dos pacotes de estrutura, conforme mostrado pelo diagrama de sequência a seguir:

![diagrama de expansão](../media/scale-out/scale-out-default.png)

Ao receber uma Atividade, o bot carrega o estado correspondente para esta conversa. Em seguida, ele executa a lógica de diálogo com esse estado e a atividade que acabou de chegar. No processo de executar o diálogo, uma ou mais atividades de saída são criadas e enviadas imediatamente. Quando o processamento do diálogo estiver concluído, o bot salva o estado atualizado, substituindo o estado antigo por novos.

Vale a pena considerar algumas coisas que podem dar errado com esse comportamento.

Em primeiro lugar, se a operação Salvar falhasse por alguma razão, o estado sairia de sincronia implicitamente com o que é visto no canal, pois o usuário que viu as respostas teria a impressão de que o estado havia avançado, o que não é verdade. Isso é geralmente pior do que no caso de o estado e a mensagem de resposta terem sido bem-sucedidos. Isso pode ter implicações para o design da conversa: por exemplo, o diálogo pode incluir as trocas de confirmação adicionais, porém redundantes, com o usuário. 

Em segundo lugar, se a implementação é implantada de forma expandida por vários nós, o estado pode ter sido substituído acidentalmente, o que pode ser especialmente confuso porque o diálogo terá provavelmente enviado atividades para o canal que transporta as mensagens de confirmação. Considere o exemplo de um bot de pedido de pizza: se quando o usuário escolher os ingredientes, adicionar cogumelo e logo em seguida adicionar queijo, em um cenário expandido com várias instâncias executando atividades subsequentes, será possível enviar essas informações ao mesmo tempo para máquinas diferentes executando o bot. Quando isso acontece, ocorre o que se chama de “condição de corrida”, em que um computador pode substituir o estado gravado por outro. No entanto, em nosso cenário, como as respostas já foram enviadas, o usuário recebeu uma confirmação informando que os cogumelos e o queijo foram adicionados. Infelizmente, quando chega a pizza, ela conterá somente cogumelos ou queijo, não ambos.

## <a name="optimistic-locking"></a>Bloqueio otimista

A solução é apresentar algum bloqueio em torno do estado. O estilo específico de bloqueio que usaremos aqui é chamado de bloqueio otimista, porque permitiremos que cada elemento seja executado como se fosse a única coisa em execução e, em seguida, detectaremos todas as violações de simultaneidade depois que o processamento for concluído. Isso pode parecer complicado, mas é muito fácil compilar usando tecnologias de armazenamento em nuvem e os pontos de extensão certos na estrutura do bot.

Vamos usar um mecanismo HTTP padrão com base no cabeçalho de marca da entidade, (ETag). Entender esse mecanismo é crucial para entender o código a seguir. O diagrama a seguir ilustra a sequência.

![diagrama de expansão](../media/scale-out/scale-out-precondition-failed.png)

O diagrama ilustra o caso de dois clientes que estão executando uma atualização para algum recurso. Quando um cliente emite uma solicitação GET e um recurso é retornado do servidor, ela é acompanhada por um cabeçalho ETag. O cabeçalho ETag é um valor opaco que representa o estado do recurso. Se um recurso for alterado, a ETag será atualizada. Quando o cliente tiver feito sua atualização para o estado, ele lança-a (POST) novamente para o servidor. Fazendo esta solicitação, o cliente anexa o valor de ETag que tinha recebido anteriormente em um cabeçalho de pré-condição If-Match. Se esta ETag não corresponde ao valor, a verificação do servidor retornado pela última vez (em qualquer resposta, para qualquer cliente) falha com uma Falha de Pré-condição 412. Esta falha é um indicador para o cliente que fez a solicitação POST de que o recurso foi atualizado. Ao ver esta falha, o comportamento típico de um cliente será obter (GET) o recurso novamente, aplicar a atualização desejada e lançar (POST) novamente o recurso. O segundo POST será bem-sucedido, pressupondo, é claro, que nenhum outro cliente atualizou o recurso e, caso isso tenha acontecido, o cliente simplesmente terá que tentar novamente.

Esse processo é chamado de "otimista" porque o cliente, ao ter em mãos um recurso realiza o processamento, sendo que o recurso em si não está “bloqueado” no sentido de que outros clientes podem acessá-lo sem qualquer restrição. Qualquer contenção entre os clientes sobre qual deve ser o estado do recurso deve ser não é determinada até que o processamento seja concluído. Como regra, em um sistema distribuído essa estratégia é melhor do que a abordagem “pessimista” oposta.

O mecanismo de bloqueio otimista que abordamos pressupõe que a lógica do programa pode ser repetida com segurança, sendo desnecessário dizer que a coisa importante a considerar aqui é o que acontece com as chamadas de serviço externo. A solução ideal aqui é se esses serviços podem ser tornados idempotentes. Em ciência da computação, uma operação idempotente é aquela que não tem nenhum efeito adicional se for chamada mais de uma vez com os mesmos parâmetros de entrada. Serviços REST HTTP puros que implementam GET, PUT e DELETE se encaixam nessa descrição. O raciocínio aqui é intuitivo: podemos estar repetindo o processamento e, por isso, fazer qualquer chamada que ele precise não terá nenhum efeito adicional, pois as chamadas são executadas novamente como parte dessa repetição, o que é bom. Para esta discussão, vamos pressupor que vivemos em um mundo ideal e os serviços de back-end mostrados à direita da imagem do sistema no início deste artigo são todos serviços REST HTTP idempotentes, assim sendo, a partir daqui abordaremos somente a troca de atividades.

## <a name="buffering-outbound-activities"></a>Atividades de saída de buffer

O envio de uma atividade não é uma operação idempotente, nem sequer é evidente que faria muito sentido no cenário de ponta a ponta. Afinal de contas, a atividade normalmente se resume em transportar uma mensagem anexada a uma exibição ou talvez uma mensagem falada por um agente de conversão de texto em fala.

O mais importante que queremos evitar com o envio de atividades é enviá-las várias vezes. O problema que temos é que o mecanismo de bloqueio otimista exige que executemos novamente nossa lógica, possivelmente, várias vezes. A solução é simples: devemos armazenar em buffer as atividades do diálogo até termos certeza de que não vamos executar a lógica novamente. Ou seja, até termos uma operação Salvar bem-sucedida. Estamos procurando um fluxo que se parece com o seguinte:

![diagrama de expansão](../media/scale-out/scale-out-buffer.png)

Supondo que podemos criar um loop de repetição em torno da execução do diálogo, obtemos o seguinte comportamento quando há uma falha na pré-condição na operação Salvar:

![diagrama de expansão](../media/scale-out/scale-out-save.png)

Aplicando esse mecanismo e revisitando nosso exemplo anterior, nunca deveríamos ver uma confirmação positiva errônea de um recheio de pizza sendo adicionado a um pedido. Na verdade, embora tenhamos expandido nossa implantação por vários computadores, serializamos de forma eficaz nossas atualizações de estado com o esquema de bloqueio otimista. Em nosso pedido de pizza, a confirmação de um item adicional agora pode até mesmo ser gravado para refletir o estado completo com precisão. Por exemplo, se o usuário digita imediatamente “queijo” e, em seguida, antes que o bot tenha tido a chance de responder “cogumelo”, as duas respostas agora podem ser “pizza com queijo” e então “pizza com queijo e cogumelo”.

Examinando o diagrama de sequência podemos ver que as respostas poderiam ser perdidas após uma operação Salvar bem-sucedida. No entanto, elas poderiam ser perdidas em qualquer ponto da comunicação de ponta a ponta. A questão é que isso não é um problema que a infraestrutura de gerenciamento de estado pode corrigir. Isso exigirá um protocolo de nível superior e, possivelmente, um que envolve o usuário do canal. Por exemplo, se o bot parecer não ter respondido, é razoável esperar que o usuário acabe tentando novamente ou repita tal comportamento. Portanto, embora seja razoável para um cenário ter interrupções transitórias ocasionais como essas, é muito menos razoável esperar que um usuário seja capaz de filtrar as confirmações positivas incorretas ou outras mensagens indesejadas. 

Juntando tudo isso, em nossa nova solução de armazenamento personalizado, vamos fazer três coisas que a implementação padrão na estrutura não faz. Em primeiro lugar, vamos usar ETags para detectar a contenção, em segundo lugar, vamos repetir o processamento quando a falha de ETag é detectada e, em terceiro lugar, vamos armazenar em buffer todas as atividades de saída até termos um salvamento bem-sucedido. O restante deste artigo descreve a implementação dessas três partes.

## <a name="implementing-etag-support"></a>Implementação do suporte de ETag

Para dar suporte a testes de unidade começamos definindo uma interface para o nosso novo repositório com o suporte de ETag. Ter a interface significa que podemos escrever duas versões, uma para os testes de unidade que são executados na memória, sem a necessidade de usar a rede, e outra para a produção. A interface tornará muito fácil aproveitar os mecanismos de injeção de dependência que temos no ASP.NET.

A interface consiste nos métodos Load e Save. Ambos usam a chave que usaremos para o estado. Load retornará os dados e a ETag associada. E Save os utilizará. Além disso, Save retornará o bool. Esse bool indicará se a ETag é correspondente e se o Save foi bem-sucedido. O objetivo não é ser um indicador de erro geral, mas um indicador específico de falha de pré-condição, que podemos modelar como um código de retorno em vez de uma exceção, porque escreveremos a lógica de fluxo de controle em torno dele na forma do nosso loop de repetição.

Como gostaríamos que essa parte do armazenamento de nível mais baixo fosse conectável, nós nos certificaremos de evitar colocar quaisquer requisitos de serialização nela. No entanto, gostaríamos de especificar que o conteúdo salvo deve ser JSON, para que uma implementação de repositório possa definir o tipo de conteúdo. A maneira mais fácil e mais natural de fazer isso no .NET é por meio de tipos de argumento. Especificamente, digitaremos o argumento de conteúdo como um JObject. Em JavaScript ou TypeScript isso será um objeto nativo regular.  

Essa é a saída resultante:

**IStore.cs**  
[!code-csharp[IStore](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/IStore.cs?range=14-19)]

Implementar isso no Armazenamento de Blobs do Azure é muito simples.

**BlobStore.cs**  
[!code-csharp[BlobStore](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/BlobStore.cs?range=18-101)]

Como você pode ver, o Armazenamento de Blobs do Azure está fazendo o trabalho real aqui. Observe a captura de exceções específicas e como isso é convertido para atender às expectativas do código de chamada. Ou seja, na carga queremos que uma exceção “Não Encontrado” retorne nula e a exceção “Falha na Precondição” em Save queremos que retorne bool.

Todo esse código-fonte estará disponível em um [exemplo](https://aka.ms/scale-out) correspondente e esse exemplo incluirá uma implementação de armazenamento de memória.

## <a name="implementing-the-retry-loop"></a>Implementando o loop de repetição
A forma básica do loop deriva diretamente do comportamento mostrado nos diagramas de sequência.

Ao receber uma atividade, podemos criar uma chave para o estado correspondente dessa conversa. Não estamos alterando a relação entre a atividade e o estado de conversa, portanto, criaremos a chave exatamente da mesma maneira como na implementação de estado padrão.

Depois de termos criado a chave apropriada, vamos tentar carregar o estado correspondente. Em seguida, execute os diálogos do bot e tente salvar. Se esse salvamento for bem-sucedido, enviaremos as atividades de saída que resultaram da execução do diálogo e nada mais. Caso contrário, voltaremos e repetiremos todo o processo anterior ao carregamento. Carregar novamente criará uma ETag e, assim, esperamos que o salvamento seja realizado com êxito da próxima vez.

A implementação OnTurn resultante tem esta aparência:

**ScaleoutBot.cs**  
[!code-csharp[OnMessageActivity](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/Bots/ScaleOutBot.cs?range=43-72)]

Observe que modelamos a execução do diálogo como uma chamada de função. Talvez uma implementação mais sofisticada seria ter definido uma interface e tornado essa dependência injetável, mas para os nossos fins, ter o diálogo todo por trás de uma função estática enfatiza a natureza funcional da nossa abordagem. Como uma instrução geral, organizar nossa implementação, de modo que as partes essenciais se tornem funcionais, nos coloca em um lugar confortável quando se trata de fazê-la funcionar com êxito em redes.


## <a name="implementing-outbound-activity-buffering"></a>Implementando armazenamento em buffer da atividade de saída 

O próximo requisito é armazenarmos em buffer as atividades de saída até a realização de um salvamento bem-sucedido. Isso exigirá uma implementação personalizada do BotAdapter. Neste código, implementaremos a função SendActivity abstrata para adicionar a atividade a uma lista em vez de enviá-la. O diálogo que estamos hospedando não será conhecido.
Nesse cenário específico, as operações UpdateActivity e DeleteActivity não têm suporte e, por isso, apenas lançarão “Não Implementado” desses métodos. Também não nos importamos com o valor retornado do SendActivity. Isso é usado por alguns canais em cenários em que é necessário enviar atualizações para as atividades, por exemplo, para desabilitar botões em cartões exibidos no canal. Essas trocas de mensagens podem ficar complicadas, especialmente quando o estado é obrigatório, o que está fora do escopo deste artigo. A implementação completa do BotAdapter personalizado tem esta aparência:

**DialogHostAdapter.cs**  
[!code-csharp[DialogHostAdapter](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/DialogHostAdapter.cs?range=19-46)]

## <a name="integration"></a>Integração

Só falta integrar essas novas partes e conectá-las às partes existentes do framework. O loop de repetição principal apenas fica na função IBot OnTurn. Ele contém nossa implementação IStore personalizada na qual, para fins de teste, deixamos a dependência injetável. Colocamos todo o código de hospedagem do diálogo em uma classe chamada DialogHost, que expõe uma única função estática pública. Essa função é definida para usar a atividade de entrada e o antigo estado e, em seguida, retornar as atividades resultantes e o novo estado.

A primeira coisa a fazer nessa função é criar o BotAdapter personalizado que apresentamos anteriormente. Em seguida, vamos executar apenas o diálogo exatamente como fazemos normalmente, criando um DialogSet e DialogContext e fazendo o habitual fluxo Continuar ou Iniciar. A única informação que não abordamos é a necessidade de um acessador personalizado. Isso acaba sendo uma correção muito simples, que facilita a passagem do estado do diálogo para o sistema do diálogo. O acessador usa semântica de referência ao trabalhar com o sistema de diálogo e, portanto, só é necessário passar o identificador por ele. Para tornar as coisas ainda mais claras, restringimos o modelo de classe que estamos usando para a semântica de referência.

Estamos sendo muito cuidadosos com a disposição em camadas, estamos colocando o JsonSerialization embutido em nosso código de hospedagem porque não o queríamos dentro da camada de armazenamento conectável quando diferentes implementações podem serializar de forma diferente.

Aqui está o código do driver:

**DialogHost.cs**  
[!code-csharp[DialogHost](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/DialogHost.cs?range=22-72)]

E, por fim, no acessador personalizado, só precisamos implementar Get porque o estado é por referência:

**RefAccessor.cs**  
[!code-csharp[RefAccessor](~/../botbuilder-samples/samples/csharp_dotnetcore/42.scaleout/RefAccessor.cs?range=22-60)]

## <a name="additional-information"></a>Informações adicionais
O código de [exemplo C#](https://aka.ms/scale-out) usado neste artigo está disponível no GitHub.

