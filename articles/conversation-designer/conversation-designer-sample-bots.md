---
title: Criar um bot do Designer de Conversa a partir de um bot de Exemplo | Microsoft
description: Inicie um novo bot com um destes bots de exemplo.
author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ROBOTS: NoIndex, NoFollow
ms.openlocfilehash: 0ebf0d1b90b03789d8a77710631c83f0914099c5
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296982"
---
# <a name="create-a-conversation-designer-bot-from-a-sample-bots"></a>Criar um bot do Designer de Conversa a partir de bots de Exemplo

Designer de Conversa é uma estrutura avançada que fornece um construtor de bot visual, um modelo declarativo para definir bots e um tempo de execução que simplifica a criação de bots. Uma forma de simplificar a criação de bot é iniciar o bot a partir de um bot existente.

Você pode criar bots do Designer de Conversa a partir de um dos muitos bots de exemplo ou de um bot do Designer de Conversa exportado anteriormente. Cada bot de exemplo é um bot totalmente funcional. Isso lhe dá um ponto inicial sólido em que você pode começar a criar seu próprio bot ou personalizar o bot de exemplo para ajustar-se aos cenários.

## <a name="the-sample-bots"></a>Os bots de exemplo

O Designer de Conversa vem com um conjunto de **Bots de exemplo**. É possível começar a criar o bot com base em um desses **Bots de exemplo**. A maioria dos **Bots de exemplo** abrange um pequeno cenário de um bot totalmente funcional. Se você quiser ver todos esses bots de exemplo funcionando juntos, crie o **bot Completo da Cafeteria Contoso**. 

A tabela a seguir lista os tipos de bots que podem ser criados com uma breve descrição sobre cada bot e os cenários que ele abrange. 

| Nome do bot | Cenários | Recursos introduzidos |
| ---- | ---- | ---- |
| **Bot Básico** | Um bot que tem alguns recursos básicos integrados. Por exemplo, esse bot exibe uma mensagem de boas-vindas, quando um novo usuário é adicionado a uma conversa, responde às mensagens de saudação e executa um comportamento de fallback quando o usuário pede algo que o bot não reconhece. | - [Tarefas](conversation-designer-tasks.md) <br/>- Gatilhos e ações de tarefas <br/>- [Gatilho de Reconhecimento Vocal](conversation-designer-luis.md)<br/>- [Gatilho de script](conversation-designer-code-recognizer.md)<br/>- [Ação de resposta](conversation-designer-reply.md) |
| **Bot de Eco** | Um **Bot básico** que ecoa uma mensagem do usuário.  Tudo o que você enviar para o bot, ele irá ecoar a mensagem dizendo: "Aqui está o que você disse - ...mensagem...". | - [Gatilho de script](conversation-designer-code-recognizer.md)<br/>- Interpretar o objeto de contexto em código<br/>- Criar entidades em código<br>- Usar entidades na resposta do Bot |
| **Bot de QnA** | Um bot que pode realizar uma experiência de pergunta e resposta de turno único [QnAMaker.ai](http://qnamaker.ai). Para que esse exemplo funcione, você deve ter uma conta do [QnA Maker](http://qnamaker.ai) configurada e ter treinado com algumas perguntas e respostas. Em seguida, abra esse **Bot do QnA**, acesse a página **Scripts** e substitua esses dois espaços reservados com as informações do QnA Maker: `<INSERT YOUR KB ID>` e `<INSERT YOUR SUBSCRIPTION KEY>`. Depois inserir esses dois tipos de informação, [salve](conversation-designer-save-bot.md) o bot e, em seguida, você pode [testar](conversation-designer-debug-bot.md) o bot. | - [Gatilho de script](conversation-designer-code-recognizer.md)<br/>- Realizar solicitações HTTP a partir do código<br/>- Ligação ao serviço de NLP/LU personalizado (QnAMaker.ai no exemplo) |
| **Bot de Conversa** | Um bot que pode participar de uma conversa simples e lembra-se do contexto, como o nome do usuário. | - [Ação de diálogo](conversation-designer-dialogues.md)<br/>- [Estados de diálogo](conversation-designer-dialogues.md#dialogue-states) básicos e propriedades<br/>- Introdução ao [Diálogo Solicitado](conversation-designer-dialogues.md#prompt-state) (definir um comportamento de solicitação e nova solicitação)<br/>- [Criar intenções](conversation-designer-luis.md#create-a-new-intent) com entidades rotuladas em diálogo solicitado<br/>- Usar entidades geradas por Reconhecimento Vocal na resposta do bot<br/>- Usar[cartões](conversation-designer-adaptive-cards.md) para melhorar a experiência do usuário final<br/>- Vincular entidades de bot para [formulários de entrada](conversation-designer-adaptive-cards.md#input-form)<br/>- Criar e usar o ativo do bot [Modelo de resposta](conversation-designer-response-templates.md) |
| **Bot de Memória** | Um bot que pode solicitar as preferências do usuário e posteriormente recuperar as informações em uma conversa. Quando você diz algo como: "definir preferência de comunicação". Esse bot perguntará se você quer receber comunicação por meio de "Chamada" ou "Texto". Também é possível pedir a esse bot para obter informações de localização da Cafeteria Contoso dizendo algo assim: "encontrar endereço da cafeteria" ou "obter o trajeto até a Cafeteria Contoso em Redmond, WA". Uma vez que o bot localizar as informações, ele lhe chamará ou enviará texto a você fornecendo as informações encontradas por ele. <br/><br/>Se uma preferência de comunicação já estiver definida, o bot apenas usará essa preferência, caso contrário, ele solicitará isso ao usuário. | - [Ação de diálogo](conversation-designer-dialogues.md)<br/>- Salvar dados na memória do bot usando [`context.global`](conversation-designer-context-object.md#context-properties)<br/>- Recuperar informações contextuais da memória a serem usadas em um diálogo |
| **Bot de Reserva de Mesa** | Um bot que pode participar de uma conversa multiturno com um usuário para ajudá-lo a reservar uma mesa na Cafeteria Contoso. <br/><br/>Para reservar uma mesa, esse bot precisa saber de você três tipos de informações: (1) *quantidade de pessoas*, (2) *data/hora* e (3) *localização*. <br/><br/>Você pode fornecer todas as três informações em uma mensagem dizendo algo como: "eu gostaria de reservar uma mesa para 4 pessoas no local em Redmond na terça-feira às 14h." Se você ignorar qualquer uma das três informações, o bot solicitará que você a forneça. | - [Ação de diálogo](conversation-designer-dialogues.md)<br/>- Ação de Diálogo complexa com referências a outro Diálogo<br/>- Compor diálogos conjuntos<br/>- Configurar o Diálogo do bot usando todo o poder do LUIS para gerenciar ações como a experiência de preenchimento de opções e correções<br/>- Usar[cartões](conversation-designer-adaptive-cards.md) para melhorar a experiência do usuário final<br/>- Vincular entidades de bot para [formulários de entrada](conversation-designer-adaptive-cards.md#input-form)<br/>- Criar e usar o ativo do bot [modelo de resposta condicional](conversation-designer-response-templates.md#conditional-response-templates) |
| **Bot de Refinamento de Pesquisa** | Um bot que pode ajudá-lo a refinar sua pesquisa usando as informações contextuais transferidas de conversas anteriores. Você pode dizer coisas como: "a loja de Seattle abre este fim de semana?" seguido por "E quanto à loja Renton" - o bot lembrará que ainda estamos falando sobre este fim de semana para a segunda pergunta. Você também pode tentar coisas como: "quais lojas estão abertas nesta sexta-feira às 20h" seguido por "e às 22h"? etc | - [Ação de diálogo](conversation-designer-dialogues.md)<br/>- Salvar dados na memória do bot usando [`context.global`](conversation-designer-context-object.md#context-properties)<br/>- Usar a memória do bot para ter uma conversa do tipo transferência de contexto com o usuário<br/>- Recuperar informações contextuais da memória a serem usadas em um Diálogo |
| **Bot de Pedido de Sanduíche** | Um bot que demonstra diferentes maneiras de solicitar ao entradas ao usuário ao fazer um pedido de sanduíche. Por exemplo, este bot ajudará você a fazer um pedido de sanduíche. <br/><br/>Para pedir um sanduíche, o bot exige quatro tipos de informações: (1) *tamanho do sanduíche*, (2) *tipo de pão*, (3) *opção de proteína*e (4) *molhos do sanduíche*. <br/><br/>Semelhante ao **Bot de Reserva de Mesa**, você pode fornecer todas as quatro informações necessárias em uma mensagem, ou fornecer uma de cada vez, dizendo: "eu gostaria de pedir um sanduíche." ou "posso obter um sanduíche grande?" e o bot solicitará o restante das informações necessárias para processar seu pedido. | - [Ação de diálogo](conversation-designer-dialogues.md)<br/>- Configurar [solicitações](conversation-designer-response-templates.md) para coletar informações de diferentes maneiras |
| **Bot da Cafeteria Contoso** | Um bot totalmente funcional criado para a Cafeteria Contoso. Esse bot pode fazer tudo o que outros bots de exemplo fazem. Ele fornece uma mensagem de boas-vindas com um cartão avançado com o qual é possível interagir. Ele dá suporte a mensagens de Ajuda, Saudação e Fallback. Ajuda a reservar uma mesa, encontrar a localização da cafeteria ou pedir um sanduíche. | - Todos os recursos do Designer de Conversa<br/>- Salvar dados na memória do bot usando [`context.global`](conversation-designer-context-object.md#context-properties)<br/>- Usar [cartões](conversation-designer-adaptive-cards.md) para disparar tarefas específicas de bot com base na entrada do usuário (veja o cartão com botões na mensagem de boas-vindas)<br/>- Criação de um bot complexo que pode executar várias [tarefas](conversation-designer-tasks.md) diferentes. |

## <a name="explore-sample-bots"></a>Explorar bots de exemplo

Se o bot de exemplo atual que você está usando não for exatamente o que você queria, é possível alternar para outro bot de exemplo a qualquer instante. Você pode encontrar uma lista de todos os bots a exemplo na página **Explorar bots de exemplo**.

> [!WARNING] 
> Importar um bot de exemplo substituirá o bot atual pelo bot de exemplo. Se você não quiser perder sua personalização no bot atual, [exporte](conversation-designer-export-import-bot.md#export-a-conversation-designer-bot) ele para um arquivo .zip.

Para alternar para um bot de exemplo diferente, faça o seguinte:
1. A partir do bot atual, clique em **Explorar bots de exemplo**. Essa opção pode ser localizada na parte inferior do painel de navegação esquerdo. 
2. Escolha um bot em uma lista de **Bots de exemplo** e clique em **Importar**.
3. Se você quer salvar seu trabalho no bot atual, escolha a opção **Fazer backup e importar**. Essa opção salvará o bot atual no computador local e, em seguida, importará o novo **Bot de exemplo**. Caso contrário, escolha **Importar sem fazer backup**.

Agora, o bot será substituído por um novo bot de exemplo que você acabou de selecionar. 

## <a name="next-step"></a>Próxima etapa
> [!div class="nextstepaction"]
> [Salvar o bot](conversation-designer-save-bot.md)
