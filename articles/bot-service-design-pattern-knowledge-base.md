---
title: Criar bots de dados de conhecimento | Microsoft Docs
description: Saiba mais sobre as diferentes maneiras de criar um bot de dados de conhecimento que localiza e retorna as informações em resposta à consulta ou de entrada do usuário.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 12/13/2017
ms.openlocfilehash: e228209b4d239a05f9c76203e9fd2fb342c14d36
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999283"
---
# <a name="design-knowledge-bots"></a>Criar bots de dados de Conhecimento

Um bot de dados de conhecimento pode ser projetado para fornecer informações sobre a praticamente qualquer tópico. Por exemplo, um bot de dados de conhecimento pode responder perguntas sobre os eventos como "Quais eventos de bot há nesta conferência?", "Quando é o próxima show de Reggae?" ou "Quem é Tame Impala?" Outro pode responder a perguntas relacionados a TI, como "Como atualizar meu sistema operacional?" ou "Onde posso para redefinir a minha senha?" Outro ainda pode responder perguntas sobre contatos como "Quem é John Doe"? ou "Qual é o endereço de email de Jane Doe"? 

Independentemente do caso de uso para o qual um bot de dados de conhecimento é criado, seu objetivo básico é sempre o mesmo: localizar e retornar as informações que o usuário solicitou, aproveitando um corpo de dados, como dados relacionais em um SQL de banco de dados, dados JSON em um armazenamento não-relacional, ou PDFs em um armazenamento de documentos. 

## <a name="search"></a>Search

A funcionalidade de pesquisa pode ser uma ferramenta valiosa dentro de um bot. 

Primeiro, "pesquisa difusa" permite que um bot retorne informações que provavelmente serão relevantes para a pergunta do usuário, sem a necessidade de que o usuário forneça entrada precisa. Por exemplo, se o usuário solicita um bot de dados de conhecimento de música para obter informações sobre "impala" (em vez de "Tame Impala"), o bot pode responder com informações que são mais prováveis de serem relevante para essa entrada.

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/fuzzySearch2.png)

As pontuações de pesquisa indicam o nível de confiança para os resultados de uma pesquisa específica, permitindo um bot ordenar os resultados da mesma forma, ou até mesmo personalizar sua comunicação com base no nível de confiança. Por exemplo, se o nível de confiança é alto, o bot pode responder com "Aqui é o evento que melhor corresponde à sua pesquisa:".

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/searchScore2.png)

Se o nível de confiança é baixo, o bot pode responder com "Humm... você estava procurando algum desses eventos?"

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/searchScore1.png)

### <a name="using-search-to-guide-a-conversation"></a>Usando a pesquisa para orientar uma conversa

Se sua motivação para a criação de um bot é para habilitar a funcionalidade do mecanismo de pesquisa básica, talvez não seja realmente necessário um bot. O que uma interface de conversação oferece que os usuários não podem obter de um mecanismo de pesquisa típico em um navegador da web? 

Bots de dados de conhecimento são geralmente mais eficazes quando eles são criados para orientar a conversa. Uma conversa é composta de uma troca bidirecional entre o usuário e o bot, que apresenta o bot a oportunidades para fazer perguntas esclarecedoras, apresentar opções e validar os resultados de uma forma que uma pesquisa básica não é capaz de fazer. Por exemplo, o bot a seguir orienta um usuário por meio de uma conversa que cria faceta e filtra um conjunto de dados até localizar as informações que o usuário está procurando.

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/guidedConvo1.png)

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/guidedConvo2.png)

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/guidedConvo3.png)

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/guidedConvo4.png)

Ao processar a entrada do usuário em cada etapa e apresentar as opções relevantes, o bot orienta o usuário para as informações que ele está procurando. Depois que o bot fornece essas informações, ele ainda pode fornecer orientação sobre as maneiras mais eficientes para localizar informações semelhantes no futuro. 

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/Training.png)

### <a name="azure-search"></a>Azure Search

Ao usar o <a href="https://azure.microsoft.com/en-us/services/search/" target="_blank">Azure Search</a>, você pode criar um índice de pesquisa eficiente que um bot pode facilmente pesquisar, facetar e filtrar. Considere um índice de pesquisa que é criado usando o portal do Azure.

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/search3.PNG)

Você deseja ser capaz de acessar todas as propriedades do armazenamento de dados, para definir cada propriedade como "recuperável." Você deseja ser capaz de encontrar os músicos por nome, para definir a propriedade **nome** como "pesquisável". Por fim, você deseja ser capaz de filtrar por facetas as eras dos músicos, para marcar a propriedade **Eras** como "facetável" e "filtrável." 

O facetamento determina os valores que existem no armazenamento de dados para uma determinada propriedade, junto com a magnitude de cada valor. Por exemplo, esta captura de tela mostra que há 5 eras distintas no armazenamento de dados:

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/facet.png)

Por sua vez, a filtragem seleciona apenas as instâncias especificadas de uma determinada propriedade. Por exemplo, você pode filtrar o resultado definido acima para conter apenas os itens onde **Era** é igual a "Romântica". 

> [!NOTE]
> Consulte <a href="https://github.com/ryanvolum/AzureSearchBot" target="_blank">um bot de exemplo</a> para obter um exemplo completo de um bot de dados de conhecimento que é criado usando o Azure Document DB, o Azure Search e o Microsoft Bot Framework.
> 
> Para simplificar, o exemplo acima mostra um índice de pesquisa que é criado usando o portal do Azure. 
> Índices também podem ser criados de forma programática.

## <a name="qna-maker"></a>QnA Maker

Alguns bots de dados de conhecimento podem simplesmente pretender responder perguntas frequentes (FAQs). 
O <a href="https://www.microsoft.com/cognitive-services/en-us/qnamaker" target="_blank">QnA Maker</a> é uma ferramenta poderosa que foi desenvolvida especificamente para esse caso de uso. QnA Maker tem a capacidade interna de extrair perguntas e respostas de um site de perguntas frequentes existente, além de permitir que você configure manualmente sua própria lista personalizada de perguntas e respostas. O QnA Maker tem capacidades de processamento de linguagem natural, permitindo que ele forneça até mesmo respostas para perguntas que são um pouco diferente do que as esperadas. No entanto, ele não tem capacidades de reconhecimento de linguagem semântica. Ele não pode determinar que um filhote de cão é um tipo de cão, por exemplo. 

Usando a interface web do QnA Maker, você pode configurar uma base de conhecimento com três pares de perguntas e respostas: 

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/KnowledgeBaseConfig.png)

Em seguida, você pode testá-lo fazendo uma série de perguntas: 

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/exampleQnAConvo.png)

O bot responde corretamente às perguntas que são mapeadas diretamente para aquelas que foram configuradas na base de conhecimento. No entanto, ele responde incorretamente à pergunta "Posso trazer meu chá?", porque essa pergunta é mais semelhante em estrutura à pergunta "Posso trazer minha vodka?." O motivo pelo qual o QnA Maker fornece uma resposta incorreta é que ele não reconhece de forma inerente o significado das palavras. Ele não sabe que "chá" é um tipo de bebida não alcoólica. Portanto, ele responde "Álcool não é permitido."

> [!TIP]
> Crie seus pares de QnA e, em seguida, teste e treine novamente seu bot usando o botão "Inspecionar" na conversa para selecionar uma alternativa de resposta para cada resposta incorreta que receber. 

## <a name="luis"></a>LUIS

Alguns bots de dados de conhecimento exigem funcionalidades de processamento de linguagem natural (NLP) para que eles possam analisar as mensagens de um usuário para determinar a intenção do usuário. 
O [Reconhecimento Vocal (LUIS)](https://www.luis.ai) fornece um meio rápido e eficiente de adicionar funcionalidades de NLP aos bots. LUIS permite que você use modelos existentes e pré-criados do Bing e Cortana sempre que atenderem às suas necessidades, bem como permitindo que você crie modelos especializados de sua preferência. 

Ao trabalhar com grandes conjuntos de dados, não é necessariamente viável treinar um modelo NLP com cada variação de uma entidade. Em um bot de reprodução da música, por exemplo, um usuário pode utilizar a mensagem "Reproduzir Reggae", "Reproduzir Bob Marley" ou "Reproduzir One Love". Embora um bot possa mapear cada uma dessas mensagens à intenção "playMusic", sem que estar treinado com cada nome do artista, gênero e música, um modelo NLP não seria capaz de identificar se a entidade é um gênero, artista ou música. Usando um modelo NLP para identificar a entidade genérica do tipo "music", o bot poderia procurar seu repositório de dados para essa entidade e prosseguir desse ponto. 

## <a name="combining-search-qna-maker-andor-luis"></a>Combinar Pesquisa, QnA Maker e/ou LUIS

Pesquisa, QnA Maker e LUIS são ferramentas poderosas, mas também podem ser combinadas para criar bots de dados de conhecimento que possuam mais de um desses recursos.

### <a name="luis-and-search"></a>LUIS e Pesquisa

No exemplo de bot festival de música [abordado anteriormente](#search), o bot orienta a conversa, mostrando os botões que representam a antecipação da linha. No entanto, este bot também pode incorporar o reconhecimento de linguagem natural usando LUIS para determinar a intenção e entidades em perguntas como "que tipo de música Romit Girdhar toca?". O bot pode então pesquisar em relação a um índice de Azure Search usando o nome músico. 
 
Não seria viável treinar o modelo com cada nome músico possível, pois há muitos valores possíveis, mas você poderia fornecer exemplos suficientemente representativos para LUIS identificar corretamente a entidade em questão.  Por exemplo, considere que você treina seu modelo, fornecendo exemplos de músicos: 

![Estrutura da caixa de diálogo](~/media/bot-service-design-pattern-knowledge-base/answerGenre.png)
![Estrutura da caixa de diálogo](~/media/bot-service-design-pattern-knowledge-base/answerGenreOneWord.png)

Ao testar esse modelo com declarações novas, como, "que tipo de música os beatles tocam?", LUIS determina com êxito a intenção "answerGenre" e identifica a entidade "beatles". No entanto, se você envia uma pergunta mais longa, como "que tipo de música the devil makes three play?", LUIS identifica "the devil" como a entidade.

![Estrutura de diálogos](~/media/bot-service-design-pattern-knowledge-base/devilMakesThreeScore.png)

Ao treinar o modelo com entidades de exemplo que são representativas de conjunto de dados subjacente, você pode aumentar a precisão do reconhecimento vocal do seu bot. 

> [!TIP]
> Em geral, ele é melhor para o modelo errar ao identificar palavras em excesso no seu reconhecimento de entidade, por exemplo, identificar "John Smith por favor" da expressão "ligar para John Smith por favor", em vez de identificar muito poucas palavras, por exemplo, identificar "John" da declaração "ligar para John Smith por favor". O índice de pesquisa irá ignorar palavras irrelevantes como "por favor" na locução "John Smith por favor". 

### <a name="luis-and-qna-maker"></a>LUIS e QnA Maker

Alguns bots de dados de conhecimento podem usar o QnA Maker para responder perguntas básicas em combinação com o LUIS para determinar as intenções, extrair entidades e invocar caixas de diálogo mais elaboradas. Por exemplo, considere um bot simples do suporte técnico de TI. Este bot pode usar o QnA Maker para responder a perguntas básicas sobre o Windows ou o Outlook, mas ele também pode precisar facilitar cenários, como a redefinição de senha, que exige o reconhecimento de intenção e a comunicação bidirecional entre o usuário e o bot. Há várias maneiras que um bot pode implementar uma mistura de LUIS e QnA Maker:

1. Chamar o QnA Maker e LUIS ao mesmo tempo e responder ao usuário usando informações da primeira que retorna uma pontuação igual a um limite específico. 
2. Chamar LUIS primeiro e, se nenhuma intenção atender uma pontuação de limite específico, ou seja, a intenção "None" for disparada, chamar o QnA Maker. Como alternativa, criar uma intenção LUIS para QnA Maker, alimentando o modelo LUIS com perguntas de QnA de exemplo que são mapeadas para "QnAIntent". 
3. Chamar o QnA Maker primeiro e, se nenhuma resposta atender uma pontuação de limite específico, chamar o LUIS. 

O SDK do Construtor de Bot fornece suporte interno para LUIS e QnA Maker. Isso permite que você dispare caixas de diálogo ou responda automaticamente perguntas usando o LUIS e/ou o QnA Maker sem a necessidade de implementar chamadas personalizadas para qualquer uma das ferramentas. Confira o [Tutorial da ferramenta Dispatch do Bot Builder](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0) para saber mais.

> [!TIP]
> Ao implementar uma combinação de LUIS, QnA Maker e/ou Azure Search, teste as entradas com cada uma das ferramentas para determinar a pontuação de limite para cada um dos seus modelos. O LUIS, o QnA Maker e o Azure Search geram pontuações usando um critério de pontuação diferente, portanto, as pontuações geradas entre essas ferramentas não são comparáveis diretamente. Além disso, o LUIS e QnA Maker normalizam as pontuações. Uma pontuação de determinado pode ser considerada 'boa' em um modelo de LUIS, mas não em outro modelo. 

## <a name="sample-code"></a>Exemplo de código

- Para obter um exemplo que mostra como criar um bot de dados de conhecimento usando o SDK do Construtor de Bot para .NET, consulte o <a href="https://aka.ms/qna-with-appinsights" target="_blank">exemplo de bot de dados de conhecimento</a> no GitHub. 
<!-- TODO: Do not have a current bot sample to work with this
- For a sample that shows how to create more complex knowledge bots using the Bot Builder SDK for .NET, see the <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/demo-Search" target="_blank">Search-powered Bots sample</a> in GitHub.
-->

[qnamakerTemplate]: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle
