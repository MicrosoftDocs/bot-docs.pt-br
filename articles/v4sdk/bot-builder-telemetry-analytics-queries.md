---
title: Analisar os dados telemétricos do seu bot – Serviço de Bot
description: Saiba como analisar o comportamento de bot com consultas Kusto.
keywords: telemetria, appinsights, bot de monitor, Kusto, consultas
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/10/2020
ms.openlocfilehash: b6688c99226653bb95290d583028e0e5275c973f
ms.sourcegitcommit: 4ddee4f90a07813ce570fdd04c8c354b048e22f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77479324"
---
# <a name="analyze-your-bots-telemetry-data"></a>Analisar os dados telemétricos do seu bot 

## <a name="analyzing-bot-behavior"></a>Analisar o comportamento do bot

A coleção de consultas a seguir pode ser usada para analisar o comportamento do bot. Você pode usar a coleção para criar consultas personalizadas no [Azure Monitor Log Analytics](https://aka.ms/log-analytics-azure-monitor) e para criar dashboards de monitoramento e de visualização do [PowerBI](https://aka.ms/power-bi-overview).

## <a name="prerequisites"></a>Pré-requisitos
É útil ter uma compreensão básica dos seguintes conceitos:

* [Consultas Kusto](https://aka.ms/Kusto-query-overview)

* Como usar o [Log Analytics](https://aka.ms/azure-monitor-log-queries-get-started) no portal do Azure para escrever consultas de log do Azure Monitor

* Os conceitos básicos de [Consultas de log](https://aka.ms/azure-monitor-log-queries-get-started) no Azure Monitor

## <a name="dashboards"></a>Painéis
Os Painéis do Azure oferecem uma ótima maneira de exibir e compartilhar informações geradas de suas consultas.  Você pode criar painéis personalizados para ajudar a monitorar a atividade dos seus bots associando suas consultas aos blocos que você adiciona ao seu painel. Para obter informações sobre painéis e como associar suas consultas a eles, confira [Criar e compartilhar painéis de dados do Log Analytics](https://aka.ms/log-analytics-create-share-dashboards). O restante deste artigo mostra exemplos de algumas das consultas que você pode achar úteis no monitoramento do comportamento dos seus bots.  

## <a name="example-kusto-queries"></a>Consultas Kusto de exemplo

> [!NOTE]
> É recomendável dinamizar em dimensões diferentes, como período, canal e localidade para todas as consultas neste artigo.

### <a name="number-of-users-per-period"></a>Número de usuários por período
Este exemplo resulta em um gráfico de linha que mostra quantos usuários diferentes comunicaram-se com seu bot por dia nos últimos 14 dias.  O período pode ser facilmente alterado atribuindo diferentes valores às variáveis `queryStartDate`, `queryEndDate` e `interval`.

> [!IMPORTANT]
> Você só obterá uma contagem correta de usuários únicos nessa consulta se eles forem usuários autenticados, e os resultados também poderão depender das funcionalidades do canal.

```Kusto
// number of users per period
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| summarize uc=dcount(user_Id) by bin(timestamp, groupByInterval)
| render timechart
```

> [!TIP]
> O [operador summarize](https://aka.ms/kusto-summarize-operator) do Kusto é usado para produzir uma tabela que agrega o conteúdo da tabela de entrada.
>
> A função [Bin](https://docs.microsoft.com/azure/kusto/query/binfunction) é uma função escalar Kusto que, quando usada em conjunto com o `summarize operator`, agrupará os resultados da consulta no valor especificado. No exemplo acima, isso é agrupado por dia. O Kusto também aceitará h=horas, m=minutos, s=segundos, ms=milissegundos, microsecond=microssegundos.
>
> O [operador render](https://aka.ms/kusto-query-render-operator?pivots=Kusto) permite renderizar gráficos facilmente, como o _gráfico de tempo_, um gráfico de linhas em que o eixo x é um datetime e qualquer outra coluna numérica pode ser usada para o eixo y. Ele manterá automaticamente o espaço do eixo x bem uniforme mesmo se seus dados não tiverem todo horário especificado.  Se nenhuma instrução render for usada, o padrão será `table`.

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

<!-- 

OPEN ISSUE: Is it possible to define a more descriptive title in the legend when drawing the graph (vs. “Count”)?  Or overall title on top or underneath?  

Use the render PropertyName parameter:  title, xtitle, ytitle,, legend

-->

![Número de usuários por período](./media/userscount.png)

<!--  

OPEN ISSUE 1:

    "Days with little interaction may indicate service health issues"

    This is an interesting statement – I’m not sure if there’s a way to overlay service health metrics over these metrics.  It should all be in App Insights.

OPEN ISSUE 2:

    Gary Pretty: Overlap for activity per period - Looks like we have a large overlap between ‘Activity per period’ and ‘Activity per user per period’ – given that activity per user is only really useful in some scenarios, such as authenticated, I think it might simplify things to update the ‘Activity per period’ query / instructions to include a note as to how to filter down per user?   Not sure about this one myself, but thought it was worth bringing up.

    < I agree with you.  One approach might be to include both in the same section, with the ‘Activity per period’ as the primary with a note about what might be considered more of a ‘special case’ (I will need input on more details of where this is most applicable – as I’ve commented in a previous email). >


QUESTION: What changes are required?

-->

### <a name="activity-per-period"></a>Atividade por período
Este exemplo ilustra como medir o volume da atividade por dimensão desejada, independentemente se for uma contagem do número de conversas, caixas de diálogo ou mensagens por dia nos últimos 14 dias. O período pode ser facilmente alterado atribuindo diferentes valores às variáveis `querystartdate`, `queryEndDate` e `interval`. A dimensão desejada é definida pela cláusula `extend` no exemplo a seguir, `metric` pode ser definido como _InstanceId_, _DialogId_ ou _ActivityId_.

Atribua a *métrica* à dimensão que você deseja exibir:
  * *InstanceId* mede o número de [Conversas](https://aka.ms/bot-builder-conversations)
  * *DialogId* mede o número de [Caixas de diálogo](https://aka.ms/bot-builder-concept-dialog)
  * *ActivityId* mede o número de [Mensagens](https://aka.ms/bot-rest-create-messages)

```Kusto
// measures the number of activity's (conversations, dialogs, messages) per period 
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['<InstanceId>'])
| extend DialogId = tostring(customDimensions['<DialogId>'])
| extend ActivityId = tostring(customDimensions['<activityId>'])
| where DialogId != '' and  InstanceId != '' and user_Id != ''
| extend metric = InstanceId // DialogId or ActivityId
| summarize Count=dcount(metric) by  bin(timestamp, groupByInterval)
| order by Count desc nulls last 
| render timechart
```

> [!TIP]
> O [operador extend](https://aka.ms/kusto-extend-operator) do Kusto é usado para criar colunas calculadas e acrescentá-las ao conjunto de resultados.


#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![Atividade por período](./media/convscount.PNG)


### <a name="activity-per-user-per-period"></a>Atividade por usuário por período
Este exemplo demonstra como contar o número de atividades por usuário por período. Isso demonstra a busca detalhada na consulta de _atividade por período_ para se concentrar na atividade por usuário por período. As atividades incluem caixas de diálogo, conversas ou mensagens.  Isso ajuda a medir a interação do usuário com o bot e pode ajudar a determinar possíveis problemas, por exemplo: 

- Dias com muita atividade por um único usuário pode significar ataque ou teste
- Dias com pouca interação pode indicar problemas de integridade do serviço

> [!TIP]
> Você pode remover _by user_Id_ para obter o volume de atividade de bot geral que pode ser dinamizado em tempo e caixas de diálogo, mensagens ou conversas.

```Kusto
// number of users per period per dialogs
let queryStartDate = ago(14d);
let queryEndDate = now();
let interval = 6h;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['InstanceId'])
| extend DialogId = tostring(customDimensions['DialogId'])
| extend ActivityId = tostring(customDimensions['activityId'])
| where DialogId != '' and InstanceId != '' and user_Id != ''
| extend metric = ActivityId // InstanceId // DialogId // or InstanceId for conversation count
| summarize Count=dcount(metric) by user_Id, bin(timestamp, groupByInterval)
| order by Count desc nulls last 
```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo 

| **user_Id**   | **timestamp**        | **Count** |
| ------------- | -------------------- | :------:  |
| User-8107ffd2 | 2019-09-03T00:00:00Z |    14     |
| User-75f2cc8f | 2019-08-30T00:00:00Z |    13     |
| User-75f2cc8d | 2019-09-03T00:00:00Z |    13     |
| User-3060aada | 2019-09-03T00:00:00Z |    10     |


### <a name="dialog-completion"></a>Conclusão da caixa de diálogo
Depois de definir o cliente de telemetria para uma caixa de diálogo, a caixa de diálogo (e seus filhos) emitirá alguns dados telemétricos padrão, como _iniciado_ e _concluído_. Esse exemplo pode ser usado para medir as caixas de diálogo *concluídas* com relação às caixas de diálogo *iniciadas*.  Se o número de caixas de diálogo iniciadas for maior que o número de concluídas, alguns dos seus usuários não concluirão o fluxo de caixa de diálogo. Isso pode ser usado como um ponto de partida na identificação e na solução de problemas de qualquer possível lógica de caixa de diálogo.  Também pode ser usado para identificar as caixas de diálogo mais populares e as menos frequentadas.

```Kusto
// % Completed Waterfall Dialog: shows completes relative to starts 
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name=="WaterfallStart"
| extend DialogId = customDimensions['DialogId']
| extend InstanceId = tostring(customDimensions['InstanceId'])
| join kind=leftouter (
    customEvents 
    | where name=="WaterfallComplete" 
    | extend InstanceId = tostring(customDimensions['InstanceId'])
  ) on InstanceId    
| summarize started=countif(name=='WaterfallStart'), completed=countif(name1=='WaterfallComplete') by tostring(DialogId)
| where started > 100  // filter for sample
// Show starts vs. completes
| project tostring(DialogId), started, completed
| order by started desc, completed asc  nulls last 
| render barchart  with (kind=unstacked, xcolumn=DialogId, ycolumns=completed, started, ysplit=axes) 
```

> [!TIP]
> O [operador join](https://aka.ms/kusto-join-operator) do Kusto é usado para mesclar as linhas de duas tabelas para formar uma nova correspondendo valores das colunas especificadas de cada tabela.
>
> O [operador project](https://aka.ms/kusto-project-operator) é usado para selecionar os campos que você deseja exibir na saída. Semelhante ao `extend operator` que adiciona um novo campo, o `project operator` pode escolher dentre o conjunto de campos existente ou adicionar um novo campo.

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![Conclusão da caixa de diálogo](./media/dialogwfratio.PNG)


### <a name="dialog-incompletion"></a>Não conclusão da caixa de diálogo
Este exemplo pode ser usado para contar o número de fluxos de caixa de diálogo que foram iniciados, mas nunca concluídos devido ao cancelamento ou abandono durante o período especificado. Você pode usá-lo para examinar caixas de diálogo incompletas e examinar se elas foram ativamente canceladas devido a uma confusão do usuário ou simplesmente abandonadas devido à distração do usuário ou à perda de interesse dele.

<!--  

OPEN ISSUE:

“If number of started dialogs is much greater than number of completed, users do not complete the dialog flow. Troubleshoot dialog logic.”

you can use the funnel view to understand where step dropoff is.  If there is a doc describing how to do that in our docs, point to it... 

QUESTION: Who can I talk to about a a doc describing this? 

ALSO: I removed what was line 6 in the example because it was a duplicate where statement: | where timestamp > period    // change timespan accordingly

-->

```Kusto
// show incomplete dialogs
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents 
| where timestamp > queryStartDate 
| where timestamp < queryEndDate
| where name == "WaterfallStart" 
| extend DialogId = customDimensions['DialogId']
| extend instanceId = tostring(customDimensions['InstanceId'])
| join kind=leftanti (
  customEvents
  | where name == "WaterfallComplete" 
  | extend instanceId = tostring(customDimensions['InstanceId'])
  ) on instanceId
| summarize cnt=count() by  tostring(DialogId)
| order by cnt
| render barchart
```

> [!TIP]
> O [operador order](https://aka.ms/kusto-query-order-operator) do Kusto (o mesmo que `sort operator`) é usado para classificar as linhas da tabela de entrada em ordem por uma ou mais colunas.  Observação: se desejar excluir valores nulos dos resultados de qualquer consulta, você poderá filtrá-los em sua instrução where, por exemplo, você poderia adicionar "and isnotnull(Timestamp)" ou para retornar valores no início ou no fim, adicionar o `nulls first` ou `nulls first` ao fim da instrução order. 
>

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![](./media/cancelleddialogs.PNG)





### <a name="dialog-sequence-drill-down"></a>Busca detalhada de sequência da caixa de diálogo 

#### <a name="waterfall-startstepcomplete-for-dialog-in-conversation"></a>Início/etapa/conclusão da cascata para caixa de diálogo na conversa
Esse exemplo mostra a sequência de etapas de caixa de diálogo, agrupadas por conversa (instanceId). Isso pode ser útil para determinar quais etapas levam à interrupção da caixa de diálogo. 

Para executar esta consulta, insira o valor do `DialogId` desejado no lugar de \<SampleDialogId> 

```Kusto
// Drill down: Show waterfall start/step/complete for specific dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
let DialogActivity=(dlgid:string) {
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = customDimensions['DialogId']
| extend StepName = customDimensions['StepName']
| extend InstanceId = customDimensions['InstanceId']
| where DialogId == dlgid
| project timestamp, name, StepName, InstanceId 
| order by tostring(InstanceId), timestamp asc
};
// For example see SampleDialogId behavior
DialogActivity("<SampleDialogId>")
```

> [!TIP]
> Essa consulta foi escrita usando uma [função definida por consulta](https://aka.ms/kusto-user-functions), que é uma função definida pelo usuário definida e usada no escopo de uma única consulta e definida por meio de uma instrução let. Essa consulta escrita sem o uso do `query-defined function`:
>
> ```Kusto
> let queryStartDate = ago(14d);
> let queryEndDate = now();
> customEvents
> | where timestamp > queryStartDate
> | where timestamp < queryEndDate
> | extend DialogId = customDimensions['DialogId']
> | extend StepName = customDimensions['StepName']
> | extend InstanceId = customDimensions['InstanceId']
> | where DialogId == "<SampleDialogId>"
> | project timestamp, name, StepName, InstanceId 
> | order by tostring(InstanceId), timestamp asc
> ```

##### <a name="sample-query-results"></a>Resultados da consulta de exemplo

| **timestamp**       | **name**                   | **StepName**                    | **InstanceId**  |
| ------------------- | -------------------------- | ------------------------------- | --------------- |
| 2019-08-23T20:04... | WaterfallStart             | nulo                            | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | GetPointOfInterestLocations     | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | ProcessPointOfInterestSelection | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | GetRoutesToDestination          | ...79c0f03d8701 |
| 2019-08-23T20:05... | WaterfallStep              | ResponseToStartRoutePrompt      | ...79c0f03d8701 |
| 2019-08-23T20:05... | WaterfallComplete _<sup>1_ | nulo                            | ...79c0f03d8701 |
| 2019-08-28T23:35... | WaterfallStart             | nulo                            | ...6ac8b3211b99 |
| 2019-08-28T23:35... | WaterfallStep _<sup>2_     | GetPointOfInterestLocations     | ...6ac8b3211b99 |
| 2019-08-28T19:41... | WaterfallStart             | nulo                            | ...8137d76a5cbb |
| 2019-08-28T19:41... | WaterfallStep _<sup>2_     | GetPointOfInterestLocations     | ...8137d76a5cbb |
| 2019-08-28T19:41... | WaterfallStart             | nulo                            | ...8137d76a5cbb |

<sub>1</sup> _Concluída_ 

<sub>2</sup> _Abandonada_

_Interpretação: os usuários parecem abandonar a conversa na etapa GetPointOfInterestLocations._ 

> [!NOTE] 
> As caixas de diálogo de em cascata executam uma sequência (início, várias etapas, conclusão). Se uma sequência mostra o início sem conclusão, isso significa que a caixa de diálogo foi interrompida devido ao abandono do usuário ou ao cancelamento da caixa de diálogo. Nessa análise detalhada, pode-se ver esse comportamento (confira etapas concluídas versus abandonadas).

#### <a name="waterfall-startstepcompletecancel-steps-aggregate-totals"></a>Totais de agregação das etapas início/etapa/conclusão/cancelamento em cascata
Esse exemplo mostra os totais de agregação do número total de vezes que uma sequência de caixa de diálogo foi iniciada, o número total combinado de etapas em cascata, quantas foram concluídas com êxito, quantas foram canceladas e a diferença entre _WaterfallStart_ e o total combinado de _WaterfallComplete_, além de que _WaterfallCancel_ fornecerá o número total de abandonadas.

```Kusto
// Drill down: Aggregate view of waterfall start/step/complete/cancel steps totals for specific dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
let DialogSteps=(dlgid:string) {
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = customDimensions['DialogId']
| where DialogId == dlgid
| project name
| summarize count() by name
};
// For example see SampleDialogId behavior
DialogSteps("<SampleDialogId>")
```

##### <a name="sample-query-results"></a>Resultados da consulta de exemplo

| **name**          | **contagem** |
| ----------------- | --------: |
| WaterfallStart    | 21        |
| WaterfallStep     | 47        |
| WaterfallComplete | 11        |
| WaterfallCancel   | 1         |

_Interpretação: de 21 invocações de sequência de caixa de diálogo, apenas 11 foram concluídas, 9 foram abandonadas e uma foi cancelada pelo usuário_



### <a name="average-duration-in-dialog"></a>Duração média na caixa de diálogo
Esse exemplo mede a quantidade média de tempo que os usuários passam em uma determinada caixa de diálogo. Ficar muito tempo em uma caixa de diálogo pode sugerir oportunidades de simplificação.

 ```Kusto
// Average dialog duration
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name=="WaterfallStart"
| extend DialogId = customDimensions['DialogId']
| extend instanceId = tostring(customDimensions['InstanceId'])
| join kind=leftouter (customEvents | where name=="WaterfallCancel" | extend instanceId = tostring(customDimensions['InstanceId'])) on instanceId 
| join kind=leftouter (customEvents | where name=="WaterfallComplete" | extend instanceId = tostring(customDimensions['InstanceId'])) on instanceId 
| extend duration = case(not(isnull(timestamp1)), timestamp1 - timestamp, 
not(isnull(timestamp2)), timestamp2 - timestamp, 0s) // Abandoned are not counted. Alternate: now()-timestamp)
| extend seconds = round(duration / 1s)
| summarize AvgSeconds=avg(seconds) by tostring(DialogId)
| order by AvgSeconds desc nulls last 
| render barchart with (title="Duration in Dialog")
 ```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![dialogduration](./media/dialogduration.PNG)


### <a name="average-steps-in-dialog"></a>Etapas médias na caixa de diálogo
Esse exemplo mostra cada "duração" de caixas de diálogo executadas conforme calculado por desvio médio, mínimo, máximo e padrão. Isso pode ajudar a analisar a qualidade da caixa de diálogo. Por exemplo:

- caixas de diálogo com muitas etapas devem ser avaliadas para simplificação e oportunidades
- Caixas de diálogo com uma ampla lacuna entre mín/máx/média podem significar que os usuários ficam paralisados tentando concluir as tarefas. Talvez seja necessário avaliar a possibilidade de haver caminhos menores para concluir as tarefas ou maneiras de reduzir a complexidade da caixa de diálogo.
- Caixas de diálogo com um grande desvio padrão sugerem caminhos complexos ou uma experiência desfeita (abandonar/cancelar)
- Caixas de diálogo com muito poucas etapas também, pois elas nunca foram concluídas. Analisar as taxas de conclusão/abandono pode ajudar a fazer essa determinação.  

```Kusto
// min/max/std/avg steps per dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = tostring(customDimensions['DialogId'])
| extend StepName = tostring(customDimensions['StepName'])
| extend InstanceId = tostring(customDimensions['InstanceId'])
| where name == "WaterfallStart" or  name == "WaterfallStep" or  name == "WaterfallComplete" 
| order by InstanceId, timestamp asc
| project timestamp, DialogId, name, InstanceId, StepName 
| summarize cnt=count() by InstanceId, DialogId
| summarize avg=avg(cnt), minsteps=min(cnt),maxsteps=max(cnt), std=stdev(cnt) by DialogId
| extend avgsteps = round(avg, 1)
| extend avgshortbysteps=maxsteps-avgsteps
| extend avgshortbypercent=round((1.0 - avgsteps/maxsteps)*100.0, 1)
| project DialogId, avgsteps, minsteps, maxsteps, std, avgshortbysteps, avgshortbypercent
| order by std desc nulls last 
```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

| ID da caixa de diálogo               | etapas médias | etapas mín | etapas máx | std  | média curta por etapas | média curta por percentual |
| ----------------------- | --------: | :-------: | :-------: | ---: | :----------------: | -------------------: |
| FindArticlesDialog      | 6.2       | 2         | 7         | 2,04 | 0.8                | 11,4%                |
| CreateTicket            | 4.3       | 2         | 5         | 1.5  | 0,7                | 14%                  |
| CheckForCurrentLocation | 3.9       | 2         | 5         | 1,41 | 1,1                | 22%                  |
| BaseAuth                | 3.3       | 2         | 4         | 1,03 | 0,7                | 17,5%                |
| integração              | 2.7       | 2         | 4         | 0,94 | 1,3                | 32,5%                |

__Interpretação: por exemplo, FindArticlesDialog tem uma ampla extensão entre mín/máx e deve ser investigado e possivelmente recriado e otimizado.



### <a name="channel-activity-by-activity-metric"></a>Atividade do canal por métrica de atividade
Esse exemplo mede a quantidade de atividade que seu bot recebe por canal no período determinado. Ele faz isso contando qualquer uma das seguintes métricas: mensagens recebidas, usuários, conversas ou caixas de diálogo. Isso pode ser útil para a análise de integridade do serviço ou para medir a popularidade dos canais.


```Kusto
// number of metric: messages, users, conversations, dialogs by channel
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['InstanceId'])
| extend DialogId = tostring(customDimensions['DialogId'])
| extend ActivityId = tostring(customDimensions['activityId'])
| extend ChannelId = tostring(customDimensions['channelId'])
| where DialogId != '' and  InstanceId != '' and user_Id != ''
| extend metric = user_Id // InstanceId or ActivityId or user_Id
| summarize Count=count(metric) by  ChannelId, bin(timestamp, groupByInterval)
| order by Count desc nulls last 
| render barchart with (title="Users", kind=stacked) // or Incoming Messages or Conversations or Users
```

> [!TIP]
> Talvez convenha considerar experimentar estas variações:
> - Execute a consulta sem o bucketing de carimbo de data/hora: `bin(timestamp, groupByInterval)`
> - Você também pode usar `dcount` para usuários distintos versus `count` para todas as atividades de evento do usuário.  Isso também funciona para usuários repetidos.
> 

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![ChannelsUsage](./media/ChannelsUsage.PNG)

 _Interpretação: o teste de emulador é usado para ser o mais popular, mas depois que entramos em operação, DirectLineSpeech é o canal mais popular._

<!--  

Open Issue: More interesting than the “certainty” score would be linking intent to dialog completion %. That infers “certainty” by user’s actions. 

QUESTION: What changes are required?

-->
### <a name="total-intents-by-popularity"></a>Total de intenções por popularidade
Este exemplo aplica-se a bots habilitados para LUIS. Ele mostra um resumo de todas as [intenções](https://aka.ms/botbuilder-luis-concept#recognize-intent) por popularidade e a pontuação de certeza de detecção de intenção correspondente.

* Na prática, a exibição deve ser separada para cada métrica.
* Os caminhos de intenção populares devem ser otimizados para a experiência do usuário.
* Pontuações médias baixas indicam baixo reconhecimento e possível ausência de intenção do usuário real.

```Kusto
// show total intents
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name startswith "LuisResult" 
| extend intentName = tostring(customDimensions['intent'])
| extend intentScore = todouble(customDimensions['intentScore'])
| summarize ic=count(), ac=avg(intentScore)*100 by intentName
| project intentName, ic, ac
| order by ic desc nulls last 
| render barchart with (kind=unstacked, xcolumn=intentName, ycolumns=ic,ac, title="Intents Popularity")
```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo

![IntentPopularity](./media/Telemetry/IntentPopularity.PNG)

_Interpretação: por exemplo, a intenção mais popular, a confirmação é detectada apenas com 23% de confiança em média._


> [!TIP]
> Gráficos de barras são um de mais de uma dezena de opções disponíveis com consultas Kusto.  Algumas outras opções incluem: anomalychart, areachart, columnChart, linechart, scatterchart. para obter mais detalhes, confira o tópico [operador render](https://aka.ms/kusto-query-render-operator?pivots=Kusto).


## <a name="schema-of-bot-analytics-instrumentation"></a>Esquema da instrumentação de análise de bot
As tabelas a seguir mostram os campos mais comuns nos quais seu bot registrará dados telemétricos.

### <a name="general-envelope"></a>Envelope Geral

Campos de análise de logs comuns na instrumentação do Application Insights.

| **Campo**        | **Descrição**        | **Valores de Exemplo**                                            |
| ---------------- | ---------------------- | ------------------------------------------------------------ |
| name             | Tipo de mensagem           | BotMessageSend, BotMessageReceived, LuisResult, WaterfallStep, WaterfallStart, SkillWebSocketProcessRequestLatency, SkillWebSocketOpenCloseLatency, WaterfallComplete, QnaMessage, WaterfallCancel, SkillWebSocketTurnLatency, AuthPromptValidatorAsyncFailure |
| customDimensions | Análise de Bot do SDK      | activityId=\<id>,  activityType=message,  channelId=emulator,  fromId=\<id>,  fromName=User,  locale=en-us,  recipientId=\<id>,  recipientName=Bot,  text=find  a coffee shop |
| timestamp        | Horário do evento          | 2019-09-05T18:32:45.287082Z                                  |
| instance_Id      | ID da conversa        | f7b2c416-a680-4b2c-b4cc-79c0f03d8711                         |
| operation_Id     | ID do turno                | 084b2856947e3844a5a18a8476d99aaa                             |
| user_Id          | ID de usuário de canal exclusivo | emulator7c259c8e-2f47...                                     |
| client_IP        | Endereço IP do cliente      | 127.0.0.1 (pode estar ausente devido ao bloqueio de privacidade)               |
| client_City      | Cidade do cliente            | Redmond (se detectada, pode estar ausente)                         |

### <a name="custom-dimensions"></a>Dimensões Personalizadas

A maioria dos dados da atividade específicos do bot é armazenada no campo _customDimensions_.

| **Campo**     | **Descrição**      | **Valores de Exemplo**                                 |
| ------------- | -------------------- | ------------------------------------------------- |
| activityId    | ID da mensagem           | \<id>: 8da6d750-d00b-11e9-80e0-c14234b3bc2a       |
| activityType  | Tipo de mensagem      | message, conversationUpdate, event, invoke       |
| channelId     | Identificador de canal   | emulator, directline, msteams,  webchat           |
| fromId        | Do identificador      | \<id>                                             |
| fromName      | Nome de usuário do cliente | John Bonham, Keith Moon, Steve Smith,  Steve Gadd |
| localidade        | Localidade de origem do cliente | en-us, zh-cn, en-GB,  de-de, zh-CN                |
| recipientId   | Identificador do destinatário | \<id>                                             |
| recipientName | Nome do destinatário       | John Bonham, Keith Moon, Steve Smith,  Steve Gadd |
| text          | Texto na mensagem      | encontre uma cafeteria                                |

### <a name="custom-dimensions-luis"></a>Dimensões Personalizadas: LUIS

A instrumentação do LUIS armazena os dados nos campos Dimensões Personalizados a seguir.

| **Campo**      | **Descrição**         | **Valores de Exemplo**                                       |
| -------------- | ----------------------- | ------------------------------------------------------- |
| intenção         | Intenção detectada pelo LUIS    | pointOfInterestSkill                                    |
| intentScore    | Pontuação de reconhecimento do LUIS  | 0.98                                                    |
| Entidades       | Entidades detectadas pelo LUIS  | FoodOfGrocery =  [["coffee"]], KEYWORD= ["coffee shop"] |
| Pergunta       | Pergunta detectada pelo LUIS  | encontre uma cafeteria                                      |
| sentimentLabel | Sentimento detectado pelo LUIS | positivo                                                |

### <a name="custom-dimensions-qnamaker"></a>Dimensões Personalizadas: QnAMaker

a instrumentação do QnAMaker armazena os dados nos campos Dimensões Personalizadas a seguir.

| **Campo**       | **Descrição**            | **Valores de Exemplo**                                            |
| --------------- | -------------------------- | ------------------------------------------------------------ |
| pergunta        | Pergunta detectada pelo QnA      | o que você pode fazer?                                             |
| resposta          | Resposta do QnA                 | Você tem perguntas, eu posso ter respostas.                     |
| articleFound    | QnA                        | true                                                         |
| questionId      | ID da pergunta do QnA            | 488                                                          |
| knowledgeBaseId | ID da base de dados de conhecimento do QnA                  | 2a4936f3-b2c8-44ff-b21f-67bc413b9727                         |
| matchedQuestion | Matriz de perguntas correspondidas | ["Você pode me explicar qual é a sua função?","Você pode me falar um pouco sobre você mesmo?","Você pode me falar sobre você?","você poderia me ajudar?","hum e o que você pode fazer?","como você pode me ajudar","Como você pode me ajudar?","Como você pode ajudar?","então, como eu posso usar você em meus projetos?","Fale comigo sobre sua funcionalidade","O que você pode fazer?",… |

 

## <a name="see-also"></a>Consulte Também

* Para obter um tutorial sobre como escrever consultas de log, confira [Introdução às consultas de log no Azure Monitor](https://aka.ms/azure-monitor-log-queries-get-started)
* [Visualizar dados do Azure Monitor](https://aka.ms/azure-monitor-visualize-data)
* Saiba como [Adicionar telemetria ao seu bot](https://aka.ms/add-bot-telemetry)
* Saiba mais sobre [Consultas de log do Azure Monitor](https://aka.ms/azure-monitor-log-queries)
* [Criar e compartilhar dashboards de dados do Log Analytics](https://aka.ms/log-analytics-create-share-dashboards)
