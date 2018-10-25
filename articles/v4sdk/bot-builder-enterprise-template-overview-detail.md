---
title: Visão geral detalhada do Modelo de Bot do Enterprise | Microsoft Docs
description: Saiba mais sobre como ocorrem as decisões de design por trás do Modelo de Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6f295794ca7d3cc17688337e70df2a52cdb665ed
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708491"
---
# <a name="enterprise-template---detailed-overview"></a>Modelo do Enterprise: visão geral detalhada

> [!NOTE]
> Este tópico aplica-se à versão v4 do SDK. 

O Modelo de Bot do Enterprise reúne várias melhores práticas que identificamos durante a criação de experiências de conversação e automatiza a integração de componentes que consideramos altamente benéficos para os desenvolvedores do Serviço de Bot do Azure. Esta seção cobre algumas informações básicas em relação a decisões importantes para ajudar a explicar por que o modelo funciona dessa forma.

## <a name="introduction-card"></a>Cartão de apresentação

Uma questão importante em várias experiências de conversação é os usuários finais não saberem como começar, o que pode levar a perguntas genéricas que o Bot pode não saber responder tão bem. A primeira impressão é o que conta! Um cartão de apresentação é uma oportunidade de apresentar recursos do Bot para o usuário final e sugerir algumas perguntas iniciais que o usuário pode usar para começar o processo. Também é uma ótima oportunidade para revelar a personalidade do Bot.

O cartão de apresentação é fornecido de forma padrão simples, e você pode adaptá-lo conforme a necessidade.

## <a name="basic-language-understanding-luis-intents"></a>Intenções básicas do LUIS (Reconhecimento vocal)

Cada Bot deve lidar com um nível básico de reconhecimento vocal conversacional. Por exemplo, saudações são um item básico que todo Bot deve saber lidar com facilidade. Normalmente, os desenvolvedores precisam criar essas intenções de base e fornecer dados de treinamento iniciais. O Modelo de Bot do Enterprise fornece exemplos de arquivos de LU para ajudar você a começar, e isso também evita a necessidade de criar esses arquivos a cada projeto novo, além de garantir já de início um nível básico de capacidade.

Os arquivos de LU fornecem as intenções a seguir em inglês, francês, italiano, alemão e espanhol.

> Greeting, Help, Cancel, Restart, Escalate, ConfirmYes, ConfirmNo, ConfirmMore, Next, Goodbye

O formato [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) é semelhante ao Markdown, facilitando a modificação e o controle do código-fonte. A ferramenta [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) é usada para converter arquivos .LU em modelos de LUIS que podem ser publicados em sua assinatura do LUIS por meio do portal ou da ferramenta de CLI (interface de linha de comando) do [LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) associada.

## <a name="content-moderator"></a>Content Moderator

O Content Moderator permite a detecção de possíveis palavrões e ajuda a verificar se há PII (informação de identificação do usuário). Pode ser útil integrá-lo aos Bots, permitindo que um Bot reaja a palavrões ou ao compartilhamento de PII pelo usuário. Por exemplo, um Bot pode pedir desculpas e fazer a transição para um ser humano, ou não armazenar registros de telemetria se detectar PII.

Um componente de middleware é fornecido para fazer a triagem de texto e aparece por meio de um ```TextModeratorResult``` no objeto TurnState.

## <a name="telemetry"></a>Telemetria

O fornecimento de insights sobre a participação do usuário no Bot se mostrou muito valioso. Esse insight pode ajudar você a entender os níveis de participação dos usuários, quais recursos do Bot eles estão usando (intenções), além de perguntas feitas que o Bot não é capaz de responder, o que revela lacunas no conhecimento do Bot que podem ser resolvidas com novos artigos QnAMaker, por exemplo.

A integração do Application Insights fornece insights técnicos/operacionais relevantes prontos para uso, mas isso também pode ser usado para capturar eventos específicos relacionados ao Bot: mensagens enviadas e recebidas, além das operações do LUIS e do QnAMaker.

A telemetria no nível do Bot está intrinsecamente vinculada à telemetria técnica e operacional, permitindo que você verifique como uma pergunta de um usuário foi respondida e vice-versa.

Um componente de middleware combinado com uma classe wrapper em torno das classes de SDK QnAMaker e LuisRecognizer fornecem uma maneira elegante de coletar um conjunto consistente de eventos. Esses eventos consistentes podem ser usados por ferramentas do Application Insights juntamente com ferramentas como o Power BI.

Um painel do Power BI de exemplo é fornecido com cada projeto criado usando o Modelo de Bot do Enterprise. Consulte a seção [PowerBI](bot-builder-enterprise-template-powerbi.md) para obter mais informações.

## <a name="dispatcher"></a>Dispatcher

Um padrão de design essencial usado com sucesso na primeira leva de experiências de conversação era o aproveitamento do LUIS (Reconhecimento vocal) e do QnAMaker. O LUIS era treinado com tarefas que seu Bot poderia executar para um usuário final, e o QnAMaker era treinado com dados de conhecimento mais gerais.

Todas as declarações de entrada (perguntas) eram encaminhadas para o LUIS para análise. Se a intenção de determinada declaração não fosse identificada, era marcada como uma intenção None. Em seguida, o QnAMaker era usado para tentar encontrar uma resposta para o usuário final.

Embora esse padrão tenha funcionado bem, havia dois cenários principais que poderiam dar problemas.

- Se as declarações no modelo do LUIS e no QnAMaker se sobrepunham ligeiramente, isso podia resultar em comportamento estranho, em que o LUIS tentava processar uma pergunta quando ela deveria ser direcionada ao QnaMaker.
- Quando havia dois ou mais modelos de LUIS, um Bot tinha que invocar cada um deles e executar algum tipo de comparação de avaliação de intenção para identificar o destino de determinada declaração. Como não há nenhuma pontuação de linha de base comum, a comparação entre os modelos não funcionava com eficiência, resultando em uma experiência de usuário insatisfatória.

O [Dispatcher](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) oferece uma solução elegante para isso extraindo declarações de cada modelo do LUIS configurado e perguntas do QnAMaker e criando um modelo do LUIS como expedição central.

Isso permite que um Bot identifique rapidamente qual componente ou modelo do LUIS deve lidar com determinada expressão e faz com que os dados do QnAMaker sejam considerados no nível superior do processamento de intenções, não só na intenção None, como antes.

Essa ferramenta de expedição também permite avaliações, o que destaca confusões e sobreposições entre modelos de LUIS e bases de conhecimento do QnAMaker antes da implantação.

O Dispatcher é usado no cerne de cada projeto criado usando o Modelo de Bot do Enterprise. O modelo de expedição é usado na classe `MainDialog` para identificar se o destino é um modelo do LUIS ou QnA. No caso do LUIS, o modelo do LUIS secundário é invocado retornando a intenção e as entidades como de costume.

## <a name="qnamaker"></a>QnAMaker

O [QnAMaker](https://www.qnamaker.ai/) permite que não desenvolvedores coletem dados de conhecimento gerais no formato de pares de pergunta e resposta. Esse conhecimento pode ser importado de fontes de dados de perguntas frequentes, de manuais de produto e interativamente no portal do QnaMaker.

Um exemplo de conjunto de entradas do QnA é fornecido no formato de arquivo [LU](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) dentro da pasta QnA de CogSvcModels. [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) é usado como parte do script de implantação para criar um arquivo JSON do QnAMaker que a ferramenta CLI (interface de linha de comando) do [QnAMaker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) usa para publicar itens na base de conhecimento do QnAMaker.