---
title: Guia para as IDs no Bot Framework | Microsoft Docs
description: Este guia descreve as características dos campos de ID presentes no protocolo v3 do Bot Framework.
keywords: id, bots, protocolo
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 3c4f8549f40740961feea24f73aa2e4b9b7bc82f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998883"
---
# <a name="id-fields-in-the-bot-framework"></a>Campos de ID no Bot Framework

Este guia descreve as características dos campos de ID presentes no protocolo v3 do Bot Framework.

## <a name="channel-id"></a>ID de canal

Cada canal do Bot Framework é identificado por uma ID exclusiva.

Exemplo: `"channelId": "skype"`

IDs de canal servem como namespaces para outras IDs. Chamadas de tempo de execução no protocolo do Bot Framework devem ocorrer dentro do contexto de um cana; o canal fornece um significado para a conversa e as IDs da conta usada na comunicação.

Por convenção, todas as IDs de canal são em minúsculas. Os canais garantem que as IDs de canal que eles emitem tenham uso consistente de maiúsculas e minúsculas e, portanto, os bots pode usar comparações ordinais para estabelecer a equivalência.

### <a name="rules-for-channel-ids"></a>Regras para as IDs de canal

- As IDs de canal diferenciam maiúsculas e minúsculas.

## <a name="bot-handle"></a>Identificador de Bot

Cada bot que foi registrado com o Bot Framework tem um identificador de bot.

Exemplo: `FooBot`

Um identificador de bot representa o registro de um bot com o Bot Framework on-line. Esse registro é associado a um ponto de extremidade de webhook HTTP e aos registros com canais.

O portal do desenvolvedor do Bot Framework garante a exclusividade dos identificadores de bot. O portal executa uma verificação de exclusividade que não diferencia maiúsculas de minúsculas (o que significa que as variações de maiúsculas e minúsculas do identificador de bot são tratadas como um identificador único) embora essa seja uma característica do portal do desenvolvedor, e não necessariamente do próprio identificador de bot.

### <a name="rules-for-bot-handles"></a>Regras para identificadores de bot

* Os identificadores de bot são exclusivos (não diferenciam maiúsculas de minúsculas) dentro do Bot Framework.

## <a name="app-id"></a>ID do Aplicativo

Cada bot que foi registrado com o Bot Framework tem uma ID do Aplicativo.

> [!NOTE]
> Anteriormente, aplicativos eram normalmente conhecidos como "Aplicativos MSA" ou "Aplicativos MSA/AAD". Agora, os aplicativos são mais conhecidos simplesmente como "apps", mas alguns elementos de protocolo podem se referir aos aplicativos como "Aplicativos MSA" perpetuamente.

Exemplo: `"msaAppId": "353826a6-4557-45f8-8d88-6aa0526b8f77"`

Um aplicativo representa um registro no portal de Aplicativos da equipe de Identidade e serve como o mecanismo de identidade de serviço a serviço dentro do protocolo de tempo de execução do Bot Framework. Os aplicativos podem ter outras associações que não sejam de bot, como sites e aplicativos móveis/área de trabalho.

Cada bot registrado tem exatamente um aplicativo. Embora não seja possível que um proprietário de bot, independentemente, altere qual aplicativo está associado ao bot, a equipe do Bot Framework pode fazer isso em um pequeno número de casos excepcionais.

Bots e canais podem usar as IDs do aplicativo para identificar exclusivamente um bot registrado.

IDs do aplicativo são garantidamente GUIDs. IDs do aplicativo devem ser comparadas sem diferenciação de maiúsculas e minúsculas.

### <a name="rules-for-app-ids"></a>Regras para IDs do aplicativo

* IDs do aplicativo são exclusivas (GUID de comparação) dentro da plataforma Microsoft App.
* Cada bot tem exatamente um aplicativo correspondente.
* Alterar qual aplicativo está associado a um bot requer o auxílio da equipe do Bot Framework.

## <a name="channel-account"></a>Conta de Canal

Todo o bot e o usuário tem uma conta dentro de cada canal. A conta contém um identificador (`id`) e outros dados de bot informativos não estruturais, como um nome opcional.

Exemplo: `"from": { "id": "john.doe@contoso.com", "name": "John Doe" }`

Essa conta descreve o endereço dentro do canal em que mensagens podem ser enviadas e recebidas. Em alguns casos, esses registros existem em um único serviço (por exemplo, Skype, Facebook). Em outros, eles são registrados em vários sistemas (endereços de email, números de telefone). Em canais mais anônimos (por exemplo, Webchat), o registro pode ser efêmero.

Contas de canal são aninhadas em canais. Uma conta do Facebook, por exemplo, é simplesmente um número. Esse número pode ter um significado diferente em outros canais, não tendo significado fora de todos os canais.

A relação entre as contas de canal e os usuários (pessoas reais) depende das convenções associadas a cada canal. Por exemplo, um número SMS geralmente se refere a uma pessoa por um período de tempo, após o qual o número pode ser transferido para outra pessoa. Por outro lado, uma conta do Facebook geralmente se refere a uma pessoa perpetuamente, embora não seja incomum que duas pessoas compartilhem uma conta do Facebook.

Na maioria dos canais, é apropriado imaginar uma conta de canal como um tipo de caixa de correio onde mensagens podem ser entregues. É comum para a maioria dos canais permitir que vários endereços sejam mapeados em uma única caixa de correio; por exemplo, "jdoe@contoso.com" e "john.doe@service.contoso.com" pode resolver a mesma caixa de entrada. Alguns canais vão mais além e alteram o endereço da conta com base em qual bot está acessado-a; por exemplo, o Skype e o Facebook alteram as IDs de usuário para que cada bot tenha um endereço diferente para enviar e receber mensagens.

Embora em alguns casos seja possível estabelecer equivalência entre endereços, estabelecer equivalência entre caixas de correio e equivalência entre pessoas requer conhecimento das convenções dentro do canal, o que em muitos casos não é possível.

Um bot é informado sobre o endereço da conta de canal por meio do campo `recipient` nas atividades enviadas para o bot.

### <a name="rules-for-channel-accounts"></a>Regras para contas de canal

* Contas de canal têm significado somente dentro do canal associado a elas.
* Mais de uma ID pode resolver a mesma conta.
* A comparação ordinal pode ser usada para estabelecer que duas IDs são as mesmas.
* Em geral, não há nenhuma comparação que possa ser usada para identificar se duas IDs diferentes resolvem a mesma conta, bot ou pessoa.
* A estabilidade das associações entre IDs, contas, caixas de correio e pessoas depende do canal.

## <a name="conversation-id"></a>Identificação da conversa

Mensagens são enviadas e recebidas no contexto de uma conversa, o que pode ser identificado pela ID.

Exemplo: `"conversation": { "id": "1234" }`

Uma conversa contém uma troca de mensagens e outras atividades. Cada conversa tem zero ou mais atividades, sendo que cada atividade aparece em exatamente uma conversa. Conversas podem ser perpétuas, ou podem ter inícios e fins distintos. O processo de criar, modificar ou finalizar uma conversa ocorre dentro do canal (ou seja, uma conversa existe quando o canal está ciente dela) e as características desses processos são estabelecidas pelo canal.

As atividades dentro de uma conversa são enviadas por usuários e bots. A definição de quais usuários "participam" de uma conversa varia de acordo com o canal e, teoricamente, pode incluir os usuários presentes, usuários que nunca receberam uma mensagem, ou usuários que enviaram uma mensagem.

Vários canais (por exemplo, SMS, Skype e possivelmente outros) têm a peculiaridade de que a Identificação da conversa é atribuída a uma conversa de 1:1 é a ID da conta de canal remoto. Essa peculiaridade tem dois efeitos colaterais:
1. A Identificação da conversa é subjetiva com base em quem está visualizando ela. Se os participantes A e B estão falando, o participante A vê a Identificação da conversa como "B" e o participante B vê a Identificação da conversa como "A".
2. Se o bot tiver várias contas de canal dentro desse canal (por exemplo, se o bot tiver dois números SMS), a Identificação da conversa não é suficiente para identificar exclusivamente a conversa dentro do campo de visão do bot.

Portanto, uma Identificação da conversa não identifica necessariamente uma única conversa de forma exclusiva dentro de um canal mesmo para um único bot.

### <a name="rules-for-conversation-ids"></a>Regras para Identificações da conversa

* Conversas têm significado somente dentro do canal associado a elas.
* Mais de uma Identificação pode resolver a mesma conversa.
* A igualdade ordinal não necessariamente estabelece que essas duas Identificações da conversa sejam a mesma conversa, embora isso seja feito na maioria dos casos.

## <a name="activity-id"></a>ID da atividade

As atividades são enviadas e recebidas dentro do protocolo do Bot Framework, sendo que às vezes elas são identificáveis.

Exemplo: `"id": "5678"`

IDs da atividade são opcionais e são utilizadas por canais para proporcionar ao bot uma maneira de referenciar a ID em chamadas à API subsequentes, se estiverem disponíveis:
* Responder a uma atividade específica
* Consultar a lista de participantes no nível de atividade

Como não há mais casos de uso que tenham sido estabelecidos, não há nenhuma regra adicional para o tratamento de IDs da atividade.
