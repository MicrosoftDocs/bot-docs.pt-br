---
title: Visão geral do Modelo de Assistente Virtual | Microsoft Docs
description: Saiba mais sobre o Modelo de Assistente Virtual
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 503ec19444c51120bf46838e14edb891ec5c3bb5
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464659"
---
# <a name="virtual-assistant---template-outline"></a>Assistente Virtual - Descrição do Modelo

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

O Modelo de Assistente Virtual reúne várias da melhores práticas que identificamos durante a construção de experiências de conversação e automatiza a integração de componentes que consideramos altamente benéficos para desenvolvedores do Bot Framework. Esta seção cobre algumas informações básicas em relação a decisões importantes para ajudar a explicar por que o modelo funciona dessa forma.

Recurso      | DESCRIÇÃO |
------------ | -------------
Introdução | Mensagem de introdução com um [Cartão Adaptável]() no início da conversa
Indicadores de digitação  | Indicadores visuais de digitação automatizados durante as conversas e repetição em operações de execução prolongada
Modelo LUIS básico  | Oferece suporte a intenções comuns, como **Cancelar**, **Ajuda**, **Escalonar** etc.
Diálogos básicos | Fluxos de diálogo para captura de informações básicas do usuário, bem como lógica de interrupção das intenções de cancelar e de ajuda
Respostas de base  | Respostas de texto e fala para intenções de base e diálogos
Perguntas frequentes | Integração com [QnA Maker](https://www.qnamaker.ai) para responder a perguntas gerais de uma base de conhecimento 
Bate-papo | Um modelo de bate-papo profissional para fornecer respostas padrão às consultas comuns ([saiba mais](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base))
Dispatcher | Um modelo [Dispatch](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) integrado para identificar se um determinado enunciado deve ser processado por LUIS ou QnA Maker.
Suporte ao idioma | Disponível em inglês, francês, italiano, alemão, espanhol e chinês
Transcrições | Transcrições de todas as conversas guardadas no Armazenamento do Azure
Telemetria  | Integração do [Application Insights](https://azure.microsoft.com/services/application-insights/) para coletar telemetria de todas as conversas
Análise | Um exemplo de painel do Power BI para ajudar você a começar a usar insights em suas experiências de conversação.
Implantação automatizada | Implantação fácil de todos os serviços já mencionados utilizando modelos do Azure ARM.

## <a name="introduction-card"></a>Cartão de apresentação

Uma questão importante em várias experiências de conversação é os usuários finais não saberem como começar, o que pode levar a perguntas genéricas que o Bot pode não saber responder tão bem. A primeira impressão é o que conta! Um cartão de apresentação é uma oportunidade de apresentar recursos do Bot para o usuário final e sugerir algumas perguntas iniciais que o usuário pode usar para começar o processo. Também é uma ótima oportunidade para revelar a personalidade do Bot.

Um cartão de apresentação simples é fornecido como padrão e você pode adaptá-lo conforme necessário; um cartão de retorno de usuário será mostrado nas interações subsequentes quando o usuário terminar de preencher a caixa de diálogo de integração (acionada pelo botão Começar no cartão de Apresentação)

![Exemplo de Cartão de Apresentação](./media/enterprise-template/vabotintrocard.png)

## <a name="basic-language-understanding-luis-intents"></a>Intenções básicas do LUIS (Reconhecimento vocal)

Cada Bot deve lidar com um nível básico de reconhecimento vocal conversacional. Por exemplo, saudações são um item básico que todo Bot deve saber lidar com facilidade. Normalmente, os desenvolvedores precisam criar essas intenções de base e fornecer dados de treinamento iniciais. O modelo de Assistente Virtual fornece exemplos de arquivos de LU para você começar e evita a necessidade da criação desses arquivos a cada projeto novo, além de garantir logo de início um nível básico de capacidade.

Os arquivos de LU fornecem as intenções a seguir em inglês, chinês, francês, italiano, alemão, espanhol.

Intenção       | Exemplo de enunciados |
-------------|-------------|
Cancelar       |*cancel*, *nevermind*|
Escalonar     |*can I talk to a person?*|
FinishTask   |*done*, *all finished*|
GoBack       |*go back*|
Ajuda         |*can you help me?*|
Repetir       |*can you say that again?*|
SelectAny    |*any of these*|
SelectItem   |*the first one*|
SelectNone   |*none of these*|
ShowNext     |*show more*|
ShowPrevious |*show previous*|
StartOver    |*restart*|
Stop         |*stop*|

O formato [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) é semelhante ao Markdown, facilitando a modificação e o controle do código-fonte. A ferramenta [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) é usada para converter arquivos .LU em modelos de LUIS que podem ser publicados em sua assinatura do LUIS por meio do portal ou da ferramenta de CLI (interface de linha de comando) do [LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) associada.

## <a name="telemetry"></a>Telemetria

O fornecimento de insights sobre a participação do usuário no Bot se mostrou muito valioso. Esse insight pode ajudar você a entender os níveis de participação dos usuários, quais recursos do Bot eles estão usando (intenções), além de perguntas feitas que o Bot não é capaz de responder, o que revela lacunas no conhecimento do Bot que podem ser resolvidas com novos artigos QnA Maker, por exemplo.

A integração do Application Insights fornece insights técnicos/operacionais relevantes prontos para uso, mas ela também pode ser usada para capturar eventos específicos relacionados ao Bot: mensagens enviadas e recebidas, além de operações do LUIS e do QnA Maker.

A telemetria no nível do Bot está intrinsecamente vinculada à telemetria técnica e operacional, permitindo que você verifique como uma pergunta de um usuário foi respondida e vice-versa.

Um componente de middleware, combinado com uma classe wrapper em torno das classes de SDK QnA Maker e LuisRecognizer, fornece uma maneira elegante de coletar um conjunto consistente de eventos. Esses eventos consistentes podem ser usados pelas ferramentas do Application Insights juntamente com ferramentas como o Power BI.

Um exemplo de painel do PowerBI faz parte do repositório do github para Soluções do Bot Framework e funciona instantaneamente com todos os modelos de Assistente Virtual. Confira a seção [Análise](https://github.com/Microsoft/AI/blob/master/docs/readme.md#analytics) para obter mais informações.

![Exemplo de análise](./media/enterprise-template/powerbi-conversationanalytics-luisintents.png)

## <a name="dispatcher"></a>Dispatcher

Um padrão de design essencial usado com sucesso na primeira leva de experiências de conversação foi o aproveitamento do Reconhecimento vocal (LUIS) e do QnA Maker. O LUIS era treinado com tarefas que seu Bot poderia executar para um usuário final, e o QnA Maker era treinado com dados de conhecimento mais gerais.

Todas as declarações de entrada (perguntas) eram encaminhadas para o LUIS para análise. Se a intenção de determinada declaração não fosse identificada, era marcada como uma intenção None. Em seguida, o QnA Maker era usado para tentar encontrar uma resposta para o usuário final.

Embora esse padrão tenha funcionado bem, havia dois cenários principais que poderiam dar problemas.

- Se as declarações no modelo do LUIS e no QnA Maker se sobrepunham ligeiramente, isso podia resultar em comportamento estranho, em que o LUIS tentava processar uma pergunta quando ela deveria ser direcionada ao QnA Maker.
- Quando havia dois ou mais modelos de LUIS, um Bot tinha que invocar cada um deles e executar algum tipo de comparação de avaliação de intenção para identificar o destino de determinada declaração. Como não há nenhuma pontuação de linha de base comum, a comparação entre os modelos não funcionava com eficiência, resultando em uma experiência de usuário insatisfatória.

O [Dispatcher](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) oferece uma solução elegante para isso extraindo declarações de cada modelo do LUIS configurado e perguntas do QnA Maker e criando um modelo do LUIS como expedição central.

Isso permite que um Bot identifique rapidamente qual componente ou modelo do LUIS deve lidar com determinada expressão e faz com que os dados do QnA Maker sejam considerados no nível superior do processamento de intenções, não só na intenção None, como antes.

Esta ferramenta Dispatch também permite avaliações, o que destaca confusões e sobreposições entre modelos de LUIS e bases de conhecimento do QnA Maker antes da implantação.

O Dispatcher é usado no cerne de cada projeto criado usando o modelo. O modelo de expedição é usado na classe `MainDialog` para identificar se o destino é um modelo do LUIS ou QnA. No caso do LUIS, o modelo do LUIS secundário é invocado retornando a intenção e as entidades como de costume. O Dispatcher também é usado para detecção de interrupção.

![Exemplo de expedição](./media/enterprise-template/dispatchexample.png)

## <a name="qna-maker"></a>QnA Maker

O [QnA Maker](https://www.qnamaker.ai/) permite que não desenvolvedores coletem dados de conhecimento geral no formato de pares de pergunta e resposta. Esse conhecimento pode ser importado de fontes de dados de Perguntas frequentes, de manuais de produto e interativamente no portal do QnaMaker.

Exemplo de dois modelos do QnA Maker são fornecidos no formato de arquivo [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) dentro da pasta QnA de CognitiveModels, um para Perguntas Frequentes e outro para bate-papo. O [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) é usado como parte do script de implantação para criar um arquivo JSON do QnA Maker que a ferramenta CLI (interface de linha de comando) do [QnA Maker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) usa para publicar itens na base de conhecimento do QnA Maker.

![Exemplo do QnA ChitChat](./media/enterprise-template/qnachitchatexample.png)

## <a name="content-moderator"></a>Content Moderator

O Content Moderator é um componente opcional que permite a detecção de possíveis palavrões e ajuda a verificar se há informações de identificação pessoal (PII). Pode ser útil integrá-lo aos Bots, permitindo que um Bot reaja a palavrões ou ao compartilhamento de PII pelo usuário. Por exemplo, um Bot pode pedir desculpas e fazer a transição para um ser humano, ou não armazenar registros de telemetria se detectar PII.

Um componente de middleware é fornecido para fazer a triagem de texto e aparece por meio de um ```TextModeratorResult``` no objeto TurnState.

# <a name="next-steps"></a>Próximas etapas
Consulte [Introdução](https://github.com/Microsoft/AI/tree/master/docs#tutorials) para saber como criar e implantar seu Assistente Virtual. 

# <a name="additional-resources"></a>Recursos adicionais
O código-fonte completo do Modelo de Assistente Virtual pode ser encontrado no [GitHub](https://github.com/Microsoft/AI/).

