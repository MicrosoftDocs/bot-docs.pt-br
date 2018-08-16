---
title: Especificações do Bot Framework | Microsoft Docs
description: Especificações do Bot Framework
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/07/2018
ms.openlocfilehash: 0406d489f7d1e27131b4b01411e86850ca4a17b8
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296553"
---
# <a name="bot-framework----activity"></a>Bot Framework -- Atividade

## <a name="abstract"></a>Resumo

O esquema de Atividade do Bot Framework é uma representação no nível do aplicativo de ações de conversa entre humanos e um software automatizado. O esquema inclui provisões para comunicação de texto, multimídia e ações sem conteúdo, como interações sociais e indicadores de digitação.

Esse esquema é usado dentro do protocolo do Bot Framework e é implementado por sistemas de chat da Microsoft, bots interoperáveis e clientes de várias fontes.

## <a name="table-of-contents"></a>Sumário

1. [Introdução](#introduction)
2. [Estrutura básica de atividades](#basic-activity-structure)
3. [Atividade de mensagem](#message-activity)
4. [Atividade de atualização de relação de contato](#contact-relation-update-activity)
5. [Atividade de atualização de conversa](#conversation-update-activity)
6. [Atividade de término da conversa](#end-of-conversation-activity)
7. [Atividade de evento](#event-activity)
8. [Atividade de invocação](#invoke-activity)
9. [Atividade de atualização de instalação](#installation-update-activity)
10. [Atividade de exclusão de mensagem](#message-delete-activity)
11. [Atividade de atualização de mensagem](#message-update-activity)
12. [Atividade de reação de mensagem](#message-reaction-activity)
13. [Atividade de digitação](#typing-activity)
14. [Tipos complexos](#complex-types)
15. [Referências](#references)
16. [Apêndice I - Alterações](#appendix-i---changes)
17. [Apêndice II - Tipos de entidade não IRI](#appendix-ii---non-iri-entity-types)
18. [Apêndice III - Protocolos que usam a atividade de invocação](#appendix-iii---protocols-using-the-invoke-activity)

## <a name="introduction"></a>Introdução

### <a name="overview"></a>Visão geral

O esquema de Atividade do Bot Framework representa comportamentos de conversa apresentados por humanos e softwares automatizados dentro de aplicativos de chat, email e outros programas de interação via texto. Cada objeto de atividade inclui um campo de tipo e representa uma única ação: o mais comum é o envio de conteúdo de texto, mas também inclui anexos multimídia e comportamentos sem conteúdo, como um botão "curtir" ou um indicador de digitação.

Este documento explica cada tipo de atividade e descreve os campos obrigatórios e opcionais que podem ser incluídos. Ele também define as funções do cliente e do servidor e fornece orientação sobre quais campos são controlados por cada participante e quais podem ser ignorados.

Há três funções de consequência nesta especificação: clientes, que enviam e recebem atividades em nome dos usuários; bots, que enviam e recebem atividades e normalmente são automatizados; e o canal, que armazena e encaminha as atividades entre clientes e bots.

Embora essa especificação exija a transmissão das atividades entre as funções, a natureza exata dessa transmissão não está descrita aqui.

Para manter a compactação, os cartões interativos visuais não são definidos nesta especificação. Confira a especificação [Cartões Adaptáveis](https://adaptivecards.io) [[11](#references)]. Esse cartão, e outros tipos de cartão indefinidos, pode ser incluído como anexo em atividades do Bot Framework.

### <a name="requirements"></a>Requisitos

As palavras-chave "DEVE", "NÃO DEVE", "OBRIGATÓRIO", "PODE", "NÃO PODE", "DEVERIA", "NÃO DEVERIA", "RECOMENDADO" e "OPCIONAL" neste documento devem ser interpretadas conforme descritas na [RFC 2119](https://tools.ietf.org/html/rfc2119) [[1](#references)].

Uma implementação não estará em conformidade se não atender a um ou mais requisitos de nível DEVE ou OBRIGATÓRIO para os protocolos que ela implementa. Uma implementação que atenda a todos os requisitos de nível DEVE ou OBRIGATÓRIO e a todos os requisitos de nível DEVERIA em seus protocolos é conhecida como "em conformidade incondicional"; uma que atenda a todos os requisitos de nível DEVE, mas nem a todos os requisitos de nível DEVERIA em seus protocolos é conhecida como "em conformidade condicional".

### <a name="numbered-requirements"></a>Requisitos numerados

As linhas que começam com marcadores com o formato `RXXXX` são requisitos específicos projetados para referência por um número na discussão fora deste documento. Elas não têm um peso maior ou menor do que as instruções normativas feitas fora das linhas `RXXXX`.

`R1000`: os editores dessa especificação PODEM adicionar novos requisitos `RXXXX`. Eles DEVERIAM encontrar valores `RXXXX` numéricos que preservassem o fluxo do documento.

`R1001`: editores NÃO DEVEM renumerar requisitos `RXXXX` existentes.

`R1002`: os editores PODEM excluir ou revisar requisitos de `RXXXX`. Em caso de revisão, os editores DEVERIAM reter o valor `RXXXX` existente se o tópico do requisito permanecesse essencialmente intacto.

`R1003`: os editores NÃO DEVERIAM reutilizar valores `RXXXX` desativados. Uma lista de valores excluídos PODE ser mantida no final deste documento.

### <a name="terminology"></a>Terminologia

activity
> Uma ação expressada por um bot, um canal ou um cliente e que está em conformidade com o esquema de Atividade.

canal
> Software que envia e recebe atividades e as transforma entre comportamentos de chat ou aplicativo. Os canais são o repositório oficial para dados de atividade.

bot
> Software que envia e recebe as atividades e gera respostas automatizadas, semiautomatizadas ou totalmente manuais. Os bots têm pontos de extremidade registrados nos canais.

cliente
> Software que envia e recebe as atividades, normalmente em nome de usuários humanos. Os clientes não têm pontos de extremidade.

remetente
> Software que transmite uma atividade.

receptor
> Software que aceita uma atividade.

endpoint
> Um local endereçável por meio de programação no qual um bot ou canal pode receber atividades.

endereço
> Um identificador ou endereço no qual um usuário ou bot pode ser contatado.

field
> Um valor nomeado dentro de uma atividade ou de um objeto aninhado.

### <a name="overall-organization"></a>Organização geral

O objeto de atividade é uma lista plana de pares de nome/valor, alguns são objetos primitivos e outros são complexos (aninhados). O objeto de atividade normalmente é expresso no formato JSON, mas também pode ser projetado em estruturas de dados na memória em .Net ou JavaScript.

O campo `type` da atividade controla o significado da atividade e os campos contidos nele. Dependendo da função desempenhada por um participante (cliente, bot ou canal), cada campo é obrigatório, opcional ou ignorado. Por exemplo, o campo `id` é controlado pelo canal e é obrigatório em algumas circunstâncias, mas ignorado se for enviado por um bot.

Campos que descrevem a identidade da atividade e todos os participantes, como os campos `type` e `from`, são compartilhados entre todas as atividades. Em muitas linguagens de programação, é conveniente organizar esses campos em um tipo base principal a partir do qual outros tipos de atividades, mais específicos, derivam.

Ao armazenar ou transmitir atividades, alguns campos podem ser duplicados dentro do mecanismo de transporte. Por exemplo, se uma atividade for transmitida via HTTP POST para uma URL que inclui a ID da conversa, o receptor poderá inferir seu valor sem exigir que ele esteja presente no corpo da atividade. Este documento simplesmente descreve os requisitos abstratos para esses campos, e depende do protocolo de controle estabelecer se os valores devem ser declarados explicitamente ou se há permissão para valores implícitos ou inferidos.

Quando um bot ou cliente envia uma atividade para um canal, normalmente é uma solicitação para a atividade a ser gravada. Quando um canal envia uma atividade para um bot ou cliente, normalmente é uma notificação de que a atividade já foi gravada.

## <a name="basic-activity-structure"></a>Estrutura básica de atividades

Esta seção define os requisitos para a estrutura básica do objeto de atividade.

Os objetos de atividade incluem uma lista plana de pares de nome/valor, chamadas de campos. Os campos podem ser tipos primitivos. JSON é usado como o formato de intercâmbio comum e, embora nem sempre todas as atividades devam ser serializadas em JSON, elas permitir isso. Isso permite que as implementações dependam de um conjunto simples de convenções para lidar com campos de atividades conhecidos e desconhecidos.

`R2001`: as atividades DEVEM ser serializáveis para o formato JSON, incluindo a observância de, por exemplo, restrições de exclusividade de campo.

`R2002`: os receptores PODEM permitir nomes de campo capitalizados incorretamente, embora isso não seja obrigatório. Os destinatários PODEM rejeitar atividades que não incluem campos com capitalização apropriada.

`R2003`: os receptores PODEM rejeitar atividades que contenham valores de campo cujos tipos não correspondam aos tipos de valor descritos nesta especificação.

`R2004`: a menos que indicado de outra forma, os remetentes NÃO DEVERIAM incluir valores de cadeia de caracteres vazios em campos de cadeia de caracteres.

`R2005`: a menos que indicado de outra forma, os remetentes PODEM incluir campos adicionais dentro da atividade ou de quaisquer objetos complexos aninhados. Os receptores DEVEM aceitar os campos que não entendem.

`R2006`: os receptores DEVERIAM aceitar eventos de tipos os quais não entendem.

### <a name="type"></a>Tipo

O campo `type` controla o significado de cada atividade e é preenchido, por convenção, por cadeias de caracteres curtas (por exemplo, "`message`"). Os remetentes podem definir seus próprios tipos de camada de aplicativo, embora sejam incentivados a escolher valores que provavelmente não entrarão em conflito com valores futuros bem-definidos. Se os remetentes usassem URIs como valores de tipo, eles NÃO DEVERIAM implementar as comparações de escada de URI para estabelecer a equivalência.

`R2010`: as atividades DEVEM incluir um campo `type` com tipo de valor de cadeia de caracteres.

`R2011`: dois `type` valores só serão equivalentes se forem ordinalmente idênticos.

`R2012`: um remetente PODE gerar valores `type` de atividade não definidos neste documento.

`R2013`: um canal DEVERIA rejeitar atividades de um tipo o qual não entende.

`R2014`: um bot ou cliente DEVERIA ignorar atividades de um tipo que não entende.

### <a name="channel-id"></a>ID do Canal

O campo `channelId` estabelece o canal e o repositório oficial para a atividade. O valor do campo `channelId` é do tipo cadeia de caracteres.

`R2020`: as atividades DEVEM incluir um campo `channelId` com tipo de valor de cadeia de caracteres.

`R2021`: dois `channelId` valores só serão equivalentes se forem ordinalmente idênticos.

`R2022`: um canal PODE ignorar ou rejeitar qualquer atividade que receba sem um valor `channelId` esperado.

### <a name="id"></a>ID

O campo `id` estabelece a identidade da atividade após ela ter sido registrada no canal. As atividades em andamento que ainda não foram registradas não têm identidades. Nem todas as atividades recebem uma identidade (por exemplo, uma [atividade de digitação](#typing-activity) nunca pode receber uma `id`). O valor do campo `id` é do tipo cadeia de caracteres.

`R2030`: os canais DEVERIAM incluir um campo `id` se ele estivesse disponível para essa atividade.

`R2031`: clientes e bots NÃO DEVERIAM incluir um campo `id` em atividades que eles mesmos geram.

Para facilitar a implementação, deve-se assumir que os outros participantes não têm conhecimento sofisticado das IDs da atividade, e que usarão apenas a comparação ordinal para estabelecer a equivalência.

Por exemplo, um canal pode usar GUIDs com codificação hexadecimal para a ID de cada atividade. Apesar de os GUIDs codificados em letras maiúsculas serem logicamente equivalentes aos GUIDs codificados em letras minúsculas, quando possível, os remetentes NÃO DEVERIAM usar essas codificações alternativas. A versão normalizada de cada ID é estabelecida pelo repositório oficial, o canal.

`R2032`: ao gerar valores de `id`, os remetentes DEVERIAM escolher valores cuja equivalência pudesse ser estabelecida por comparação ordinal. No entanto, os remetentes e receptores PODEM permitir a equivalência lógica de dois valores que não são equivalentes ordinais se eles tiverem conhecimento especial das circunstâncias.

O campo `id` foi projetado para permitir a eliminação de duplicação, mas isso é proibitivo na maioria dos aplicativos.

`R2033`: receptores PODEM eliminar a duplicação de atividades por ID, no entanto, os remetentes NÃO DEVERIAM confiar em receptores que executam essa eliminação de duplicação.

### <a name="timestamp"></a>Timestamp

O campo `timestamp` registra a hora UTC exata da atividade. Devido à natureza distribuída dos sistemas de computação, o momento importante é quando o canal (o repositório oficial) grava a atividade. A hora em que um cliente ou bot iniciou uma atividade pode ser transmitida separadamente no campo `localTimestamp`. O valor do campo `timestamp` é um datetime [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) [[2](#references)] codificado dentro de uma cadeia de caracteres.

`R2040`: os canais DEVERIAM incluir um campo `timestamp` se ele estivesse disponível para essa atividade.

`R2041`: clientes e bots NÃO DEVERIAM incluir um campo `timestamp` em atividades que eles mesmos geram.

`R2042`: clientes e bots NÃO DEVERIAM usar `timestamp` para rejeitar as atividades porque elas podem aparecer fora de ordem. No entanto, eles PODEM usar `timestamp` para organizar atividades dentro de uma interface do usuário ou para processamento downstream.

`R2043`: os remetentes DEVERIAM sempre codificar o valor de campos `timestamp` como UTC e DEVERIAM sempre incluir Z como uma marca UTC explícita dentro do valor.

### <a name="local-timestamp"></a>Carimbo de data/hora local

O campo `localTimestamp` expressa o deslocamento entre o datetime e o fuso horário no qual a atividade foi gerada. Isso pode ser diferente do UTC `timestamp` no qual a atividade foi gravada. O valor do campo `localTimestamp` é um datetime codificado ISO 8601 [[3](#references)] dentro de uma cadeia de caracteres.

`R2050`: os clientes e bots PODEM incluir o campo `localTimestamp` em suas atividades. Eles DEVERIAM listar explicitamente o deslocamento de fuso horário dentro do valor codificado.

`R2051`: os canais DEVERIAM preservar `localTimestamp` ao encaminhar as atividades de um remetente aos destinatários.

### <a name="from"></a>Da

O campo `from` descreve qual cliente, bot ou canal gerou uma atividade. O valor do campo `from` é um objeto complexo do tipo [Conta de canal](#channel-account).

O campo `from.id` identifica quem gerou uma atividade. Normalmente, isso é outro usuário ou bot dentro do sistema. Em alguns casos, o campo `from` identifica o próprio canal.

`R2060`: os canais DEVEM incluir os campos `from` e `from.id` ao gerar uma atividade.

`R2061`: os bots e clientes DEVERIAM incluir os campos `from` e `from.id` ao gerar uma atividade. Um canal PODE rejeitar uma atividade devido à ausência dos campos `from` e `from.id`.

O campo `from.name` é opcional e representa o nome de exibição da conta dentro do canal. Os canais DEVERIAM incluir esse valor para que os clientes e bots pudessem preencher as interfaces de usuário e os sistemas de back-end. Os bots e clientes NÃO DEVERIAM enviar esse valor para os canais que tivessem um registro central desse repositório, mas PODEM enviar esse valor para os canais que preenchem o valor em todas as atividades (por exemplo, um canal de email).

`R2062`: os canais DEVERIAM incluir o campo `from.name`, se o campo `from` estivesse presente e `from.name` estivesse disponível.

`R2063`: bots e clientes NÃO DEVERIAM incluir o campo `from.name`, a menos que ele tenha valor semântico dentro do canal.

### <a name="recipient"></a>Destinatário

O campo `recipient` descreve qual cliente ou bot está recebendo essa atividade. Este campo só tem valor quando uma atividade for transmitida exatamente para um destinatário; não tem valor quando ela é transmitida para vários destinatários (como ocorre quando uma atividade é enviada para um canal). A finalidade do campo é permitir que o destinatário se identifique. Isso é útil quando um cliente ou bot tiver mais de uma identidade dentro do canal. O valor do campo `recipient` é um objeto complexo do tipo [Conta de canal](#channel-account).

`R2070`: os canais DEVEM incluir os campos `recipient` e `recipient.id` durante a transmissão de uma atividade para um único destinatário.

`R2071`: os bots e clientes NÃO DEVERIAM incluir o campo `recipient` ao gerar uma atividade.

O campo `recipient.name` é opcional e representa o nome de exibição da conta dentro do canal. Os canais DEVERIAM incluir esse valor para que os clientes e bots pudessem preencher as interfaces de usuário e os sistemas de back-end.

`R2072`: os canais DEVERIAM incluir o campo `recipient.name`, se o campo `recipient` estivesse presente e `recipient.name` estivesse disponível.

### <a name="conversation"></a>Conversação

O campo `conversation` descreve a conversa na qual existe a atividade. O valor do campo `conversation` é um objeto complexo do tipo [Conta de conversa](#conversation-account).

`R2080`: os canais, bots e clientes DEVEM incluir os campos `conversation` e `conversation.id` ao gerar uma atividade.

O campo `conversation.name` é opcional e representa o nome de exibição da conversa, se houver um e estiver disponível.

`R2081`: os canais DEVERIAM incluir os campos `conversation.name` e `conversation.isGroup` se eles estivessem disponíveis.

`R2082`: bots e clientes NÃO DEVERIAM incluir o campo `conversation.name`, a menos que ele tenha valor semântico dentro do canal.

`R2083`: bots e clientes NÃO DEVERIAM incluir o campo `conversation.isGroup` em atividades que eles mesmos geram.

### <a name="reply-to-id"></a>Responder à ID

O campo `replyToId` identifica a atividade anterior para a qual a atividade atual é uma resposta. Este campo permite que a conversa encadeada e o aninhamento de comentário sejam comunicados entre os participantes. `replyToId` só é válido dentro da conversa atual. (Confira [relatesTo](#relates-to) para saber sobre referências a outras conversas). O valor do campo `replyToId` é uma cadeia de caracteres.

`R2090`: remetentes DEVERIAM incluir `replyToId` em uma atividade, quando ela fosse uma resposta a outra atividade.

`R2091`: um canal PODE rejeitar uma atividade se seu `replyToId` não fizer referência a uma atividade válida dentro da conversa.

`R2092`: os bots e os clientes PODEM omitir `replyToId` se ele souberem que o canal não uso o campo, mesmo se a atividade enviada é uma resposta a outra atividade.

### <a name="entities"></a>Entidades

O campo `entities` contém uma lista plana de objetos de metadados que pertencem a essa atividade. Ao contrário dos anexos (confira o campo [anexos](#attachments)), as entidades não necessariamente se manifestam como elementos de conteúdo para interação do usuário, e devem ser ignoradas se não forem compreendidas. Os remetentes podem incluir entidades que eles consideram úteis a um receptor, mesmo se não tiverem certeza de que o receptor pode aceitá-las. O valor de cada elemento da lista `entities` é um objeto complexo do tipo [Entity](#entity).

`R2100`: os remetentes DEVERIAM omitir o campo `entities` se ele não contivesse elementos.

`R2101`: os remetentes PODEM enviar várias entidades do mesmo tipo, desde que as entidades tenham um significado distinto.

`R2102`: remetentes NÃO DEVEM incluir duas ou mais entidades com conteúdo e tipos idênticos.

`R2103`: os remetentes e receptores NÃO DEVERIAM confiar na ordem específica das entidades incluídas em uma atividade.

### <a name="channel-data"></a>Dados do canal

Os dados de extensibilidade no esquema de atividade são organizados principalmente dentro do campo `channelData`. Isso simplifica a canalização em SDKs que implementam o protocolo. O formato do objeto `channelData` é definido pelo canal que está enviando ou recebendo a atividade.

`R2200`: os canais NÃO DEVERIAM definir formatos `channelData` que são JSON primitivos (por exemplo, cadeias de caracteres, inteiros). Em vez disso, DEVERIAM definir `channelData` como um tipo complexo, ou deixá-lo indefinido.

`R2201`: se o formato `channelData` fosse indefinido para o canal atual, os receptores DEVERIAM ignorar o conteúdo de `channelData`.

### <a name="service-url"></a>URL do Serviço

Com frequência, as atividades são enviadas de forma assíncrona, com conexões de transporte separadas para enviar e receber tráfego. O campo `serviceUrl` é usado pelos canais para indicar a URL para onde as respostas da atividade atual podem ser enviadas. O valor do campo `serviceUrl` é do tipo cadeia de caracteres.

`R2300`: os canais DEVEM incluir o campo `serviceUrl` em todas as atividades que eles enviam aos bots.

`R2301`: os canais NÃO DEVERIAM incluir o campo `serviceUrl` para os clientes que demonstram já conhecer o ponto de extremidade do canal.

`R2302`: os bots e clientes NÃO DEVERIAM preencher o campo `serviceUrl` em atividades que eles mesmos geram.

`R2302`: os canais DEVEM ignorar o valor de `serviceUrl` em atividades enviadas por bots e clientes.

`R2304`: os canais DEVERIAM usar valores estáveis para o campo `serviceUrl` porque os bots pode persisti-los por períodos longos.

## <a name="message-activity"></a>Atividade de mensagem

As atividades de mensagem representam o conteúdo que deve ser mostrado em uma interface de conversa. Atividades de mensagem podem conter texto, fala, cartões interativos e anexos binários ou desconhecidos; normalmente, os canais exigem no máximo uma destas opções para a atividade de mensagem ser bem-formada.

Atividades de mensagem são identificadas por um valor `type` de `message`.

### <a name="text"></a>Texto

O campo `text` contém o conteúdo de texto no formato Markdown, XML ou como texto sem formatação. O formato é controlado pelo campo [`textFormat`](#text-format) como simples, se não for especificado ou for ambíguo. O valor do campo `text` é do tipo cadeia de caracteres.

`R3000`: o campo `text` PODE conter uma cadeia de caracteres vazia para indicar o envio de texto sem conteúdo.

`R3001`: os canais DEVERIAM tratar textos formatados em `markdown` de uma maneira que se degradasse harmoniosamente para esse canal.

### <a name="text-format"></a>Formato de texto

O campo `textFormat` indica se o campo [`text`](#text) deve ser interpretado como [Markdown](https://daringfireball.net/projects/markdown/) [[4](#references)], XML ou texto sem formatação. O valor do campo `textFormat` é do tipo cadeia de caracteres, com valores definidos de `markdown`, `plain` e `xml`. O valor padrão é `plain`. Esse campo não é projetado para ser estendido com valores arbitrários.

O campo `textFormat` controla campos adicionais dentro de anexos etc. Essa relação é descrita dentro desses campos, em outro lugar neste documento.

`R3010`: se um remetente incluísse o campo `textFormat`, ele DEVERIA apenas enviar os valores definidos.

`R3011`: os remetentes DEVERIAM omitir `textFormat` se o valor fosse `plain`.

`R3012`: os receptores DEVERIAM interpretar valores indefinidos como `plain`.

`R3013`: os bots e os clientes NÃO DEVERIAM enviar o valor `xml` a menos que tivessem conhecimento prévio de que o canal dá suporte a ele e às características do dialeto XML com suporte.

`R3014`: os canais NÃO DEVERIAM enviar conteúdo em `markdown` ou `xml` aos bots.

`R3015`: os canais DEVERIAM aceitar valores de `textformat` pelo menos em `plain` e `markdown`.

`R3016`: canais PODEM rejeitar `textformat` do valor `xml`.

### <a name="locale"></a>Local

O campo `locale` comunica o código do idioma do campo [`text`](#text). O valor do campo `locale` é um código [ISO 639](https://www.iso.org/iso-639-language-codes.html) [[5](#references)] dentro de uma cadeia de caracteres.

`R3020`: os receptores DEVERIA tratar valores ausentes e desconhecidos do campo `locale` como desconhecidos.

`R3021`: os receptores NÃO DEVERIAM rejeitar atividades com localidade desconhecida.

### <a name="speak"></a>Speak

O campo `speak` indica como a atividade deve ser falada por meio de um sistema de conversão de texto em fala. O campo só é usado para personalizar a renderização da fala quando o padrão é considerado inadequado. Ele substitui a síntese de fala para qualquer conteúdo dentro da atividade, incluindo texto, anexos e resumos. O valor do campo `speak` é codificado em [SSML](https://www.w3.org/TR/speech-synthesis/) [[6](#references)] dentro de uma cadeia de caracteres. O texto sem quebra de texto tem permissão e é atualizado automaticamente para dar suporte a SSML.

`R3030`: o campo `speak` PODE conter uma cadeia de caracteres vazia para indicar a não necessidade de geração de fala.

`R3031`: os receptores que não conseguissem gerar fala DEVERIAM ignorar o campo `speak`.

`R3032`: se nenhum elemento SSML raiz for encontrado, os receptores DEVEM considerar o texto incluído como o conteúdo interno de uma marca `<speak>` SSML, precedida por um [prólogo XML](https://www.w3.org/TR/xml/#sec-prolog-dtd) [[7](#references)] válido e atualizado de outra forma para ser um documento SSML válido.

`R3033`: os receptores NÃO DEVERIAM usar XML DTD ou resolução de esquema para incluir recursos remotos de fora do fragmento XML comunicado.

`R3034`: os canais NÃO DEVERIAM enviar o campo `speak` aos bots.

### <a name="input-hint"></a>Dica de entrada

O campo `inputHint` indica se o gerador da atividade está antecipando uma resposta ou não. Este campo é usado principalmente em canais que possuem interfaces de usuário modal, e normalmente não é usado em canais com feeds de chat contínuos. O valor do campo `inputHint` é do tipo cadeia de caracteres, com valores definidos de `accepting`, `expecting` e `ignoring`. O valor padrão é `accepting`.

`R3040`: se um remetente incluísse o campo `inputHint`, ele DEVERIA apenas enviar os valores definidos.

`R3041`: ao enviar uma atividade para um canal no qual `inputHint` fosse usado, os bots DEVERIAM incluir o campo, mesmo quando o valor fosse `accepting`.

`R3042`: os receptores DEVERIAM interpretar valores indefinidos como `accepting`.

### <a name="attachments"></a>Anexos

O campo `attachments` contém uma lista plana de objetos a serem exibidos como parte dessa atividade. O valor de cada elemento da lista `attachments` é um objeto complexo do tipo [Attachment](#attachment).

`R3050`: os remetentes DEVERIAM omitir o campo `attachments` se ele não contivesse elementos.

`R3051`: os remetentes PODEM enviar várias entidades do mesmo tipo.

`R3052`: os receptores PODEM tratar anexos de tipos desconhecidos como documentos para download.

`R3053`: os receptores DEVERIAM preservar a ordem dos anexos ao processar o conteúdo, exceto quando as limitações de renderização forçassem alterações, por exemplo, agrupamento de documentos após as imagens.

### <a name="attachment-layout"></a>Layout do anexo

O campo `attachmentLayout` instrui os renderizadores de interface do usuário como apresentar o conteúdo incluído no campo [`attachments`](#attachments). O valor do campo `attachmentLayout` é do tipo cadeia de caracteres, com valores definidos de `list` e `carousel`. O valor padrão é `list`.

`R3060`: se um remetente incluísse o campo `attachmentLayout`, ele DEVERIA apenas enviar os valores definidos.

`R3061`: os receptores DEVERIAM interpretar valores indefinidos como `list`.

### <a name="summary"></a>Resumo

O campo `summary` contém o texto usado para substituir [`attachments`](#attachments) em canais que não dão suporte a isso. O valor do campo `summary` é do tipo cadeia de caracteres.

`R3070`: os receptores DEVERIAM considerar o campo `summary` como um acompanhamento lógico do campo `text`.

`R3071`: os canais NÃO DEVERIAM enviar o campo `summary` aos bots.

`R3072`: os canais capazes de processar todos os anexos em uma atividade DEVERIAM ignorar o campo `summary`.

### <a name="suggested-actions"></a>Ações sugeridas

O campo `suggestedActions` apresenta um conteúdo de ações interativas que podem ser exibidas ao usuário. O suporte para `suggestedActions` e sua manifestação depende muito do canal. O valor do campo `suggestedActions` é um objeto complexo do tipo [Ações sugeridas](#suggested-actions-2).

### <a name="value"></a>Valor

O campo `value` apresenta um conteúdo programático específico à atividade que está sendo enviada. Seu significado e o formato são definidos em outras seções deste documento que descrevem seu uso.

`R3080`: os remetentes NÃO DEVERIAM incluir campos `value` de tipos primitivos (por exemplo, cadeia de caracteres, int). Os campos `value` DEVERIAM ser tipos complexos ou omitidos.

### <a name="expiration"></a>Expiração

O campo `expiration` contém uma hora na qual a atividade deve ser considerada "expirada" e não deve ser apresentada ao destinatário. O valor do campo `expiration` é um datetime [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) [[2](#references)] codificado dentro de uma cadeia de caracteres.

`R3090`: os remetentes DEVERIAM sempre codificar o valor de campos `expiration` como UTC e DEVERIAM sempre incluir Z como uma marca UTC explícita dentro do valor.

### <a name="importance"></a>Importância

O campo `importance` contém um conjunto enumerado de valores para sinalizar ao destinatário a importância relativa da atividade.  Cabe ao receptor mapear essas dicas de importância para a experiência do usuário. O valor do campo `importance` é do tipo cadeia de caracteres, com valores definidos de `low`, `normal` e `high`. O valor padrão é `normal`.

`R3100`: se um remetente incluísse o campo `importance`, ele DEVERIA apenas enviar os valores definidos.

`R3101`: os receptores DEVERIAM interpretar valores indefinidos como `normal`.

### <a name="deliverymode"></a>DeliveryMode

O campo `deliveryMode` contém um de um conjunto enumerado de valores para sinalizar ao destinatário os caminhos de entrega alternativos da atividade.  O valor do campo `DeliveryMode` é do tipo cadeia de caracteres, com valores definidos de `normal` e `notification`. O valor padrão é `normal`.

`R3110`: se um remetente incluísse o campo `deliveryMode`, ele DEVERIA apenas enviar os valores definidos.

`R3111`: os receptores DEVERIAM interpretar valores indefinidos como `normal`.

## <a name="contact-relation-update-activity"></a>Atividade de atualização de relação de contato

As atividades de atualização de relação de contato sinalizam uma alteração na relação entre o destinatário e um usuário dentro do canal. As atividades de atualização de relação de contato geralmente não apresentam conteúdo gerado pelo usuário. A atualização da relação descrita por uma atividade de atualização de relação de contato existente entre o usuário no campo `from` (muitas vezes, mas não sempre, o usuário que inicia a atualização) e o usuário ou bot no campo `recipient`.

As atividades de atualização de relação d contato são identificadas por um valor `type` de `contactRelationUpdate`.

### <a name="action"></a>Ação

O campo `action` descreve o significado da atividade de atualização de relação de contato. O valor do campo `action` é uma cadeia de caracteres. Somente os valores de `add` e `remove` são definidos, o que indica uma relação entre os usuários/bots nos campos `from` e `recipient`.

## <a name="conversation-update-activity"></a>Atividade de atualização de conversa

Atividades de atualização de conversa descrevem uma alteração de membros, descrição, existência ou outro de uma conversa. As atividades de atualização de conversa geralmente não apresentam conteúdo gerado pelo usuário. A conversa que está sendo atualizada está descrita no campo `conversation`.

As atividades de atualização de conversas são identificadas por um valor `type` de `conversationUpdate`.

`R4100`: os remetentes PODEM incluir zero ou mais campos `membersAdded`, `membersRemoved`, `topicName` e `historyDisclosed` em uma atividade de atualização de conversa.

`R4101`: cada `channelAccount` (identificado pelo campo `id`) DEVERIA aparecer no máximo uma vez dentro dos campos `membersAdded` e `membersRemoved`. Uma ID NÃO DEVERIA aparecer nos dois campos. Uma ID NÃO DEVE ser duplicada em cada campo.

`R4102`: os canais NÃO DEVERIAM usar as atividades de atualização de conversa para indicar as alterações nos campos de uma conta de canal (por exemplo, `name`) se a conta de canal não tivesse sido adicionada ou removida da conversa.

`R4103`: os canais NÃO DEVERIAM enviar os campos `topicName` ou `historyDisclosed` se a atividade não estivesse sinalizando uma alteração no valor para qualquer um dos campos.

### <a name="members-added"></a>Membros adicionados

O campo `membersAdded` contém uma lista de participantes do canal (bots ou usuários) adicionados à conversa. O valor do campo `membersAdded` é uma matriz do tipo [`channelAccount`](#channel-account).

### <a name="members-removed"></a>Membros removidos

O campo `membersRemoved` contém uma lista de participantes do canal (bots ou usuários) removidos da conversa. O valor do campo `membersRemoved` é uma matriz do tipo [`channelAccount`](#channel-account).

### <a name="topic-name"></a>Nome do tópico

O campo `topicName` contém o tópico ou a descrição do texto para a conversa. O valor do campo `topicName` é do tipo cadeia de caracteres.

### <a name="history-disclosed"></a>Histórico divulgado

O campo `historyDisclosed` foi preterido.

`R4110`: os remetentes NÃO DEVERIAM incluir o campo `historyDisclosed`.

## <a name="end-of-conversation-activity"></a>Atividade de término da conversa

Atividades de termino da conversa sinalizam o fim de uma conversa do ponto de vista do destinatário. Talvez isso ocorra porque a conversa foi encerrada completamente, ou porque o destinatário foi removido da conversa de uma maneira indistinguível do término. A conversa que está sendo encerrada está descrita no campo `conversation`.

As atividades de término de conversa são identificadas por um valor `type` de `endOfConversation`.

Os campos `code` e `text` são opcionais.

### <a name="code"></a>Código

O campo `code` contém um valor programático que descreve por que e como a conversa foi encerrada. O valor do campo `code` é do tipo cadeia de caracteres e seu significado é definido pelo canal que está enviando a atividade.

### <a name="text"></a>Texto

O campo `text` apresenta o conteúdo de texto opcional a ser comunicado a um usuário. O valor do campo `text` é do tipo cadeia de caracteres, e seu formato é texto sem formatação.

## <a name="event-activity"></a>Atividade de evento

As atividades de evento comunicam informações programáticas de um cliente ou canal para um bot. O significado de uma atividade de evento é definido pelo campo `name`, que é significativo dentro do escopo de um canal. As atividades de evento são projetadas para transportar informações interativas (como cliques de botão) e informações não interativas (como uma notificação de um cliente que está atualizando automaticamente um modelo de fala inserido).

As atividades de evento são o equivalente assíncrono das [atividades de invocação](#invoke-activity). Ao contrário da invocação, o evento é projetado para ser ampliado por extensões de aplicativo do cliente.

As atividades de evento são identificadas por um valor `type` de `event` e por valores específicos do campo `name`.

`R5000`: os canais PODEM permitir que mensagens de evento definidas pelo aplicativo entre clientes e bots, se os clientes permitirem a personalização do aplicativo.

### <a name="name"></a>NOME

O campo `name` controla o significado do evento e o esquema do campo `value`. O valor do campo `name` é do tipo cadeia de caracteres.

`R5001`: as atividades de evento DEVEM conter um campo `name`.

`R5002`: os receptores DEVEM ignorar as atividades de evento com campos `name` que não eles entendem.

### <a name="value"></a>Valor

O campo `value` contém parâmetros específicos a esse evento, conforme definido pelo nome do evento. O valor do campo `value` é do tipo complexo.

`R5100`: O campo `value` PODERÁ estar ausente ou vazio, se for definido pelo nome do evento.

`R5101`: as extensões para a atividade do evento NÃO DEVERIAM exigir que os receptores usem qualquer informação que não fosse dos campos `type` e `name` da atividade para compreender o esquema do campo `value`.

### <a name="relates-to"></a>Se relaciona com

O campo `relatesTo` faz referência a outra conversa e, opcionalmente, a uma atividade específica dentro dessa atividade. O valor do campo `relatesTo` é um objeto complexo do tipo de referência Conversa.

`R5200`: `relatesTo` NÃO DEVERIA fazer referência a uma atividade dentro de conversa identificada pelo campo `conversation`.

## <a name="invoke-activity"></a>Atividade de invocação

A invocação de atividades comunica informações programáticas de um cliente ou um canal para um bot e tem um conteúdo de retorno correspondente para uso dentro do canal. O significado de uma atividade de invocação é definido pelo campo `name`, que é significativo dentro do escopo de um canal. 

Atividades de invocação são o equivalente assíncrono das [atividades de evento](#event-activity). Atividades de evento são projetadas permitir a ampliação. As atividades de invocação diferem apenas em sua capacidade de retornar conteúdo de resposta para o canal, pois o canal deve decidir onde e como processar esse conteúdo de resposta. A invocação é útil apenas em casos nos quais o suporte explícito para cada nome de invocação tiver sido adicionado ao canal. Assim, Invoke não foi projetado para ser um mecanismo de extensibilidade de aplicativo genérico.

Atividades de invocação são identificadas por um valor `type` de `invoke` e por valores específicos do campo `name`.

A lista de atividades de invocação definidas está incluída no [Apêndice III](#appendix-iii---protocols-using-the-invoke-activity).

`R5301`: os canais NÃO DEVERIAM permitir mensagens de invocação definidas pelo aplicativo entre clientes e bots.

### <a name="name"></a>NOME

O campo `name` controla o significado da invocação e o esquema do campo `value`. O valor do campo `name` é do tipo cadeia de caracteres.

`R5401`: as atividades de invocação DEVEM conter um campo `name`.

`R5402`: os receptores DEVEM ignorar as atividades de evento com campos `name` que não eles entendem.

### <a name="value"></a>Valor

O campo `value` contém parâmetros específicos a esse evento, conforme definido pelo nome do evento. O valor do campo `value` é do tipo complexo.

`R5500`: O campo `value` PODERÁ estar ausente ou vazio, se for definido pelo nome do evento.

`R5501`: as extensões para a atividade do evento NÃO DEVERIAM exigir que os receptores usem qualquer informação que não fosse dos campos `type` e `name` da atividade para compreender o esquema do campo `value`.

### <a name="relates-to"></a>Se relaciona com

O campo `relatesTo` faz referência a outra conversa e, opcionalmente, a uma atividade específica dentro dessa atividade. O valor do campo `relatesTo` é um objeto complexo do tipo de [referência Conversa](#conversation-reference).

`R5600`: `relatesTo` NÃO DEVERIA fazer referência a uma atividade dentro de conversa identificada pelo campo `conversation`.

## <a name="installation-update-activity"></a>Atividade de atualização de instalação

Atividades de atualização de instalação representam uma instalação ou desinstalação de um bot dentro de uma unidade organizacional (por exemplo, um locatário do cliente ou uma "equipe") de um canal. Atividades de atualização de instalação geralmente não representam a adição ou remoção de um canal.

As atividades de atualização de instalação são identificadas por um valor `type` de `installationUpdate`.

`R5700`: os canais PODEM enviar as atividades de instalação quando um bot é adicionado ou removido de um locatário, equipe ou a outra unidade de organização dentro do canal.

`R5701`: os canais NÃO DEVERIAM enviar as atividades de instalação quando o bot fosse instalado ou removido de um canal.

### <a name="action"></a>Ação

O campo `action` descreve o significado da atividade de atualização da instalação. O valor do campo `action` é uma cadeia de caracteres. Somente os valores de `add` e `remove` são definidos.

## <a name="message-delete-activity"></a>Atividade de exclusão de mensagem

As atividades de exclusão da mensagem representam a exclusão de uma atividade de mensagem existente em uma conversa. A atividade de exclusão é referenciada pelos campos `id` e `conversation` dentro da atividade.

As atividades de exclusão de mensagem são identificadas por um valor `type` de `messageDelete`.

`R5800`: os canais PODEM optar por enviar atividades de exclusão de mensagem para todas as exclusões em uma conversa, um subconjunto das exclusões dentro de uma conversa (por exemplo, somente exclusões por determinados usuários) ou nenhuma atividade dentro da conversa.

`R5801`: os canais NÃO DEVERIAM enviar as atividades de exclusão de mensagem para conversas ou atividades não observadas pelo bot.

`R5802`: se um bot disparasse uma exclusão, o canal NÃO DEVERIA enviar uma atividade de exclusão de mensagem de volta para o bot.

`R5803`: os canais NÃO DEVERIAM enviar atividades de exclusão de mensagem correspondentes às atividades cujo tipo não fosse `message`.

## <a name="message-update-activity"></a>Atividade de atualização de mensagem

As atividades de atualização da mensagem representam uma atualização de uma atividade de mensagem existente em uma conversa. A atividade atualizada é referenciada pelos campos `id` e `conversation` dentro da atividade, e a atividade de atualização da mensagem contém todos os campos da atividade de mensagem revisada.

As atividades de atualização de mensagem são identificadas por um valor `type` de `messageUpdate`.

`R5900`: os canais PODEM optar por enviar atividades de messageUpdate para todas as atualizações em uma conversa, um subconjunto das atualizações dentro de uma conversa (por exemplo, somente atualizações de determinados usuários) ou nenhuma atividade dentro da conversa.

`R5901`: se um bot disparasse uma atualização, o canal NÃO DEVERIA enviar uma atividade de atualização de mensagem de volta para o bot.

`R5902`: os canais NÃO DEVERIAM enviar atividades de atualização de mensagem correspondentes às atividades cujo tipo não fosse `message`.

## <a name="message-reaction-activity"></a>Atividade de reação de mensagem

As atividades de reação da mensagem representam uma interação social em uma atividade de mensagem existente em uma conversa. A atividade original é referenciada pelos campos `id` e `conversation` dentro da atividade. O campo `from` representa a origem de reação (ou seja, o usuário que reagiu à mensagem).

As atividades de reação de mensagem são identificadas por um valor `type` de `messageReaction`.

### <a name="reactions-added"></a>Reações adicionadas

O campo `reactionsAdded` contém uma lista de reações adicionadas a essa atividade. O valor do campo `reactionsAdded` é uma matriz do tipo [`messageReaction`](#message-reaction).

### <a name="reactions-removed"></a>Reações removidas

O campo `reactionsRemoved` contém uma lista de reações removidas dessa atividade. O valor do campo `reactionsRemoved` é uma matriz do tipo [`messageReaction`](#message-reaction).

## <a name="typing-activity"></a>Atividade de digitação

Atividades de digitação representam a entrada em andamento de um usuário ou bot. Geralmente, essa atividade é enviada quando um usuário pressiona teclas, embora também seja usada por bots para indicar que estão "pensando", e também pode ser usada para indicar, por exemplo, a coleta de áudio de usuários.

Atividades de digitação devem persistir nas interfaces do usuário durante três segundos.

Atividades de digitação são identificadas por um valor `type` de `typing`.

`R6000`: se fosse possível, os clientes DEVERIAM exibir indicadores de digitação para três segundos após o recebimento de uma atividade de digitação.

`R6001`: a menos que o canal os conheça, os remetentes NÃO DEVERIAM enviar atividades de digitação com mais frequência do que uma cada três segundos. Os remetentes PODEM enviar atividades de digitação a cada dois segundos para impedir que as lacunas apareçam.

`R6002`: se um canal atribui um [`id`](#id) a uma atividade de digitação, ele PODE permitir que bots e clientes excluam a atividade de digitação antes de sua expiração.

`R6003`: se fosse possível, os canais DEVERIAM enviar atividades de digitação aos bots.

## <a name="complex-types"></a>Tipos complexos

Esta seção define tipos complexos usados dentro do esquema de atividade, conforme descrito acima.

### <a name="attachment"></a>Anexo

Os anexos são um conteúdo incluído dentro de uma [atividade de mensagem](#message-activity): cartões, documentos binários e outro conteúdo interativo. Eles devem ser exibidos em conjunto com o conteúdo de texto. O conteúdo pode ser enviado via URI de dados de URL dentro do campo `contentUrl` ou inserido no campo `content`.

`R7100`: os remetentes NÃO DEVERIAM incluir os campos `content` e `contentUrl` em um único anexo.

#### <a name="content-type"></a>Tipo de conteúdo

O campo `contentType` descreve o [tipo de mídia MIME](https://www.iana.org/assignments/media-types/media-types.xhtml) [[8](#references)] do conteúdo do anexo. O valor do campo `contentType` é do tipo cadeia de caracteres.

#### <a name="content"></a>Conteúdo

Quando estiver presente, o campo `content` contém um anexo de objeto JSON estruturado. O valor do campo `content` é do tipo complexo definido pelo campo `contentType`.

`R7110`: os remetentes NÃO DEVERIAM incluir os primitivos JSON no campo `content` de um anexo.

#### <a name="content-url"></a>URL do conteúdo

Quando estiver presente, o campo `contentUrl` contém uma URL para o conteúdo do anexo. URIs de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] normalmente têm suporte dos canais. O valor do campo `contentUrl` é do tipo cadeia de caracteres.

`R7120`: os receptores DEVERIAM aceitar URLs HTTPS.

`R7121`: os receptores PODEM aceitar URLs HTTP.

`R7122`: os canais DEVERIAM aceitar URIs de dados.

`R7123`: os canais NÃO DEVERIAM enviar URIs de dados para os clientes ou bots.

#### <a name="name"></a>NOME

O campo `name` contém um nome opcional ou um nome de arquivo para o anexo. O valor do campo `name` é do tipo cadeia de caracteres.

#### <a name="thumbnail-url"></a>URL da miniatura

Alguns clientes têm a capacidade de exibir as miniaturas personalizadas para anexos não interativos ou como espaços reservados para anexos interativos. O campo `thumbnailUrl` identifica a origem dessa miniatura. URIs de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] normalmente têm permissão.

`R7140`: os receptores DEVERIAM aceitar URLs HTTPS.

`R7141`: os receptores PODEM aceitar URLs HTTP.

`R7142`: os canais DEVERIAM aceitar URIs de dados.

`R7143`: os canais NÃO DEVERIAM enviar campos `thumbnailUrl` aos bots.

### <a name="card-action"></a>Ação de cartão

Uma ação de cartão representa um botão interativo ou para clicar para uso dentro de cartões ou como [ações sugeridas](#suggested-actions). Eles são usados para solicitar a entrada de usuários. Apesar do nome, as ações de cartão não estão limitadas ao uso exclusivo em cartões.

As ações de cartão têm sentido apenas quando são enviadas para os canais.

Os canais decidem como cada ação se manifesta em sua experiência de usuário. Na maioria dos casos, os cartões são clicáveis. Em outros, eles podem ser selecionados por entrada de fala. Em casos nos quais o canal não oferece uma experiência de ativação interativa (por exemplo, ao interagir por SMS), talvez o canal não ofereça suporte a qualquer tipo de ativação. A decisão sobre como renderizar ações é controlada pelo requisitos normativos em outro lugar neste documento (por exemplo, dentro do formato de cartão ou na definição de [ações sugeridas](#suggested-actions)).

#### <a name="type"></a>Tipo

O campo `type` descreve o significado do botão e o comportamento quando o botão é ativado. Os valores do campo `type` são, por convenção, cadeias de caracteres curtas (por exemplo, "`openUrl`"). Confira as seções subsequentes para requisitos específicos para cada tipo de ação.

* Uma ação do tipo `messageBack` representa uma resposta de texto a ser enviado por meio do sistema de chat.
* Uma ação do tipo `imBack` representa uma resposta de texto adicionada ao feed de chat.
* Uma ação do tipo `postBack` representa uma resposta de texto não adicionada ao feed de chat.
* Uma ação do tipo `openUrl` representa um hiperlink a ser manipulados pelo cliente.
* Uma ação do tipo `downloadFile` representa um hiperlink a ser baixado.
* Uma ação do tipo `showImage` representa uma imagem a ser exibida.
* Uma ação do tipo `signin` representa um hiperlink a ser manipulados pelo sistema de entrada do cliente.
* Uma ação do tipo `playAudio` representa uma mídia de áudio a ser reproduzida.
* Uma ação do tipo `playVideo` representa uma mídia de vídeo a ser reproduzida.
* Uma ação do tipo `call` representa um número de telefone que pode ser chamado.
* Uma ação do tipo `payment` representa uma solicitação para fornecer o pagamento.

#### <a name="title"></a>Title

O campo `title` inclui o texto a ser exibido na face do botão. O valor do campo `title` é do tipo cadeia de caracteres, e não contém marcação.

Este campo se aplica a ações de todos os tipos.

`R7210`: os canais não DEVERIAM processar a marcação dentro do campo `title` (por exemplo, Markdown).

#### <a name="image"></a>Imagem

O campo `image` contém uma URL que faz referência a uma imagem a ser exibida na face do botão. URIs de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)] normalmente têm suporte dos canais. O valor do campo `image` é do tipo cadeia de caracteres.

Este campo se aplica a ações de todos os tipos.

`R7220`: os canais DEVERIAM aceitar URLs HTTPS.

`R7221`: os canais PODEM aceitar URLs HTTP.

`R7222`: os canais DEVERIAM aceitar URIs de dados.

#### <a name="text"></a>Texto

O campo `text` contém conteúdo de texto a ser enviado para um bot e incluído no feed do chat quando o botão for clicado. O conteúdo do campo `text` pode ou não ser exibido, dependendo do tipo de botão. O campo `text` pode conter marcação, controlado pelo campo [`textFormat`](#text-format) na raiz da atividade. O valor do campo `text` é do tipo cadeia de caracteres.

Esse campo é usado somente em ações de tipos selecionados. Os detalhes sobre cada tipo de ação são incluídos posteriormente neste documento.

`R7230`: o campo `text` PODE conter uma cadeia de caracteres vazia para indicar o envio de texto sem conteúdo.

`R7231`: os canais DEVERIAM processar o conteúdo do campo `text` de acordo com o campo [`textFormat`](#text-format) na raiz da atividade.

#### <a name="display-text"></a>Exibir texto

O campo `displayText` contém conteúdo de texto a ser incluído no feed do chat quando o botão for clicado. O conteúdo do campo `displayText` DEVERIA sempre ser exibido, quando fosse possível tecnicamente dentro do canal. O campo `displayText` pode conter marcação, controlado pelo campo [`textFormat`](#text-format) na raiz da atividade. O valor do campo `displayText` é do tipo cadeia de caracteres.

Esse campo é usado somente em ações de tipos selecionados. Os detalhes sobre cada tipo de ação são incluídos posteriormente neste documento.

`R7240`: o campo `displayText` PODE conter uma cadeia de caracteres vazia para indicar o envio de texto sem conteúdo.

`R7241`: os canais DEVERIAM processar o conteúdo do campo `displayText` de acordo com o campo [`textFormat`](#text-format) na raiz da atividade.

#### <a name="value"></a>Valor

O campo `value` contém conteúdo programático a ser enviado para um bot quando o botão for clicado. O conteúdo do campo `value` pode ser de qualquer tipo primitivo ou complexo, embora determinados tipos de atividade restrinjam esse campo.

Esse campo é usado somente em ações de tipos selecionados. Os detalhes sobre cada tipo de ação são incluídos posteriormente neste documento.

#### <a name="message-back"></a>Message Back

Uma ação `messageBack` representa uma resposta de texto a ser enviado por meio do sistema de chat. Message Back usa estes campos:
* `type` ("`messageBack`")
* `title`
* `image`
* `text`
* `displayText`
* `value` (de qualquer tipo)

`R7350`: os remetentes NÃO DEVERIAM incluir campos `value` de tipos primitivos (por exemplo, cadeia de caracteres, int). Os campos `value` DEVERIAM ser tipos complexos ou omitidos.

`R7351`: os canais PODEM rejeitar ou descartar campos `value` diferentes do tipo complexo.

`R7352`: quando ativados, os canais DEVEM enviar uma atividade do tipo `message` a todos os destinatários relevantes.

`R7353`: se o canal der suporte ao armazenamento e transmissão de texto, o conteúdo do campo `text` da ação DEVE ser preservado e transmitido no campo `text` da atividade de mensagem gerada.

`R7352`: se o canal der suporte ao armazenamento e transmissão de outros valores programáticos, o conteúdo do campo `value` DEVE ser preservado e transmitido no campo `value` da atividade de mensagem gerada.

`R7353`: se o canal der suporte à preservação de um valor diferente no feed do chat do que é enviado aos bots, ele DEVERÁ incluir o campo `displayText` no histórico de chat.

`R7354`: se o canal não der suporte a `R7353`, mas der suporte à gravação de texto dentro do feed do chat, ele DEVERÁ incluir o campo `text` no histórico de chat.

#### <a name="im-back"></a>IM Back

Uma ação `imBack` representa uma resposta de texto adicionada ao feed de chat. Message Back usa estes campos:
* `type` ("`imBack`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7360`: quando ativados, os canais DEVEM enviar uma atividade do tipo `message` a todos os destinatários relevantes.

`R7361`: se o canal der suporte ao armazenamento e transmissão de texto, o conteúdo do campo `title` DEVERÁ ser preservado e transmitido no campo `text` da atividade de mensagem gerada.

`R7362`: Se o campo `title` em uma ação não estiver presente e o campo `value` for do tipo cadeia de caracteres, o canal PODERÁ transmitir o conteúdo do campo `value` no campo `text` da atividade de mensagem gerada.

`R7363`: se o canal der suporte à gravação de texto dentro do feed do chat, ele DEVERÁ incluir o conteúdo do campo `title` no histórico de chat.

#### <a name="post-back"></a>Post Back

Uma ação `postBack` representa uma resposta de texto não adicionada ao feed de chat. O postback usa estes campos:
* `type` ("`postBack`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7370`: quando ativados, os canais DEVEM enviar uma atividade do tipo `message` a todos os destinatários relevantes.

`R7371`: os canais NÃO DEVERIAM incluir o texto do histórico de chat quando uma ação de postback fosse ativada.

`R7372`: os canais DEVEM rejeitar ou descartar campos `value` com tipo diferente de cadeia de caracteres.

`R7373`: se o canal der suporte ao armazenamento e transmissão de texto, o conteúdo do campo `value` DEVERÁ ser preservado e transmitido no campo `text` da atividade de mensagem gerada.

`R7374`: se o canal não conseguisse dar suporte à transmissão para o bot sem incluir o histórico no feed do chat, ele DEVERIA usar o campo `title` como texto de exibição.

#### <a name="open-url-actions"></a>Ações de Open URL

Uma ação `openUrl` representa um hiperlink a ser manipulados pelo cliente. A URL aberta usa estes campos:
* `type` ("`openUrl`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7380`: remetentes DEVEM incluir uma URL no campo `value` de uma ação `openUrl`.

`R7381`: receptores PODEM rejeitar a ação `openUrl` cujo campo `value` não esteja presente ou não uma cadeia de caracteres.

`R7382`: os receptores DEVERIAM rejeitar ou descartar ações `openUrl` cujo campo `value` contivesse um URI de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)].

`R7383`: os receptores NÃO DEVERIAM rejeitar ações `openUrl` cujo URI `value` fosse um esquema ou valor de URI inesperado.

`R7384`: os clientes com conhecimento dos esquemas de URI específicos (por exemplo, HTTP) PODEM manipular ações `openUrl` em um renderizador inserido (por exemplo, um controle de navegador).

`R7385`: mediante disponibilidade, os clientes DEVERIAM delegar a manipulação de ações `openUrl` não manipuladas por `R7354` ao manipulador de URI no nível do sistema operacional ou do shell.

#### <a name="download-file-actions"></a>Ações de Download File

Uma ação `downloadFile` representa um hiperlink a ser baixado. Download File usa estes campos:
* `type` ("`downloadFile`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7390`: remetentes DEVEM incluir uma URL no campo `value` de uma ação `downloadFile`.

`R7391`: receptores PODEM rejeitar a ação `downloadFile` cujo campo `value` não esteja presente ou não uma cadeia de caracteres.

`R7392`: os receptores DEVERIAM rejeitar ou descartar ações `downloadFile` cujo campo `value` contivesse um URI de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)].

#### <a name="show-image-file-actions"></a>Ações Show Image File

Uma ação `showImage` representa uma imagem a ser exibida. Show Image usa estes campos:
* `type` ("`showImage`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7400`: remetentes DEVEM incluir uma URL no campo `value` de uma ação `showImage`.

`R7401`: receptores PODEM rejeitar a ação `showImage` cujo campo `value` não esteja presente ou não uma cadeia de caracteres.

`R7402`: os receptores PODEM rejeitar ações `showImage` cujo campo `value` é um URI de Dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)].

#### <a name="signin"></a>Signin

Uma ação `signin` representa um hiperlink a ser manipulados pelo sistema de entrada do cliente. A entrada usa os seguintes campos:
* `type` ("`signin`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7410`: remetentes DEVEM incluir uma URL no campo `value` de uma ação `signin`.

`R7411`: receptores PODEM rejeitar a ação `signin` cujo campo `value` não esteja presente ou não uma cadeia de caracteres.

`R7412`: Receptores DEVEM rejeitar ou descartar ações `signin` cujo campo `value` contém um URI de dados, conforme definido na [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)].

#### <a name="play-audio"></a>Reproduzir Áudio

Uma ação `playAudio` representa uma mídia de áudio a ser reproduzida. Reproduzir Áudio usa estes campos:
* `type` ("`playAudio`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7420`: quando forem ativados, os canais PODEM enviar eventos de mídia.

`R7421`: os canais DEVEM rejeitar ou descartar campos `value` com tipo diferente de cadeia de caracteres.

`R7422`: os remetentes NÃO DEVERIAM enviar URIs de dados, conforme definido em [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)], sem o conhecimento prévio de que o canal dá suporte a eles.

#### <a name="play-video"></a>Reproduzir vídeo

Uma ação `playAudio` representa uma mídia de vídeo a ser reproduzida. Reproduzir Vídeo usa estes campos:
* `type` ("`playVideo`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7430`: quando forem ativados, os canais PODEM enviar eventos de mídia.

`R7431`: os canais DEVEM rejeitar ou descartar campos `value` com tipo diferente de cadeia de caracteres.

`R7432`: os remetentes NÃO DEVERIAM enviar URIs de dados, conforme definido em [RFC 2397](https://tools.ietf.org/html/rfc2397) [[9](#references)], sem o conhecimento prévio de que o canal dá suporte a eles.

#### <a name="call"></a>Chamada

Uma ação `call` representa um número de telefone que pode ser chamado. A chamada estes campos:
* `type` ("`call`")
* `title`
* `image`
* `value` (do tipo cadeia de caracteres)

`R7440`: remetentes DEVEM incluir uma URL do esquema `tel` no campo `value` de uma ação `signin`.

`R7441`: receptores DEVEM rejeitar a ação `signin` cujo campo `value` não esteja preenchido ou não seja um URI de cadeia de caracteres do esquema `tel`.

#### <a name="payment"></a>Pagamento

Uma ação `payment` representa uma solicitação para fornecer o pagamento. O pagamento usa estes campos:
* `type` ("`payment`")
* `title`
* `image`
* `value` (do tipo complexo [Solicitar Pagamento](#payment-request))

`R7450`: remetentes DEVEM incluir um objeto complexo do tipo [Solicitação de Pagamento](#payment-request) no campo `value` de uma ação `payment`.

`R7451`: canais DEVEM rejeitar a ação `payment` cujo campo `value` não esteja preenchido ou não seja um objeto complexo do tipo [Solicitação de Pagamento](#payment-request).

### <a name="channel-account"></a>Conta de canal

As contas de canal representam identidades dentro de um canal. A conta do canal inclui uma ID que pode ser usada para identificar e contatar a conta dentro desse canal. Às vezes, essas IDs existem dentro de um único namespace (por exemplo, o IDs do Skype); às vezes, elas são agrupadas em vários servidores (por exemplo, endereços de email). Além da ID, as contas de canal incluem nomes para exibição e IDs de objeto do Azure Active Directory (AAD).

#### <a name="channel-account-id"></a>ID da conta de canal

O campo `id` é o identificador e o endereço dentro do canal. O valor do campo `id` é uma cadeia de caracteres. Um exemplo `id` dentro de um canal que usa os endereços é "name@example.com"

`R7510`: os canais DEVERIAM usar os mesmos valores e convenções para as IDs da conta independentemente da posição deles dentro do esquema (`from.id`, `recipient.id`, `membersAdded` etc.). Isso permite que os bots e os clientes usem comparações de cadeia de caracteres ordinais para saber quando, por exemplo, são descritos no campo `membersAdded` de uma atividade `conversationUpdate`.

#### <a name="channel-account-name"></a>Nome da conta de canal

O campo `name` é um nome amigável opcional no canal. O valor do campo `name` é uma cadeia de caracteres. Um exemplo `name` dentro de um canal é "John Doe"

#### <a name="channel-account-aad-object-id"></a>ID de objeto do AAD da conta do canal

O campo `aadObjectId` é uma ID opcional correspondente à ID de objeto da conta no Azure AAD (Active Directory). O valor do campo `aadObjectId` é uma cadeia de caracteres.

### <a name="conversation-account"></a>Conta de conversa

As contas de conversa representam a identidade de conversas dentro de um canal. Em canais que dão suporte a apenas uma única conversa entre duas contas (por exemplo, SMS), a conta de conversa é persistente e não tem um início ou fim predeterminado. Em canais que dão suporte a várias conversas paralelas (por exemplo, email), cada conversa provavelmente terá uma ID exclusiva.

#### <a name="conversation-account-id"></a>ID da conta de conversa

O campo `id` é o identificador dentro do canal. O formato dessa ID é definido pelo canal e é usado como uma cadeia de caracteres opaca em todo o protocolo.

Os canais DEVERIAM escolher valores `id` que são estáveis para todos os participantes dentro de uma conversa. (Por exemplo, um exemplo ruim para o campo `id` para uma conversa individual é usar a ID de outro participante como o valor `id`. Isso resultaria em um `id` diferente da perspectiva de cada participante. Uma opção melhor é classificar as IDs de ambos os participantes e concatená-las, o que seria igual para ambas as partes).

#### <a name="conversation-account-name"></a>Nome da conta de conversa

O campo `name` é um nome amigável opcional para a conversa no canal. O valor do campo `name` é uma cadeia de caracteres.

#### <a name="conversation-account-aad-object-id"></a>ID de objeto do AAD da conta de conversa

O campo `aadObjectId` é uma ID opcional correspondente à ID de objeto de conversa no Azure AAD (Active Directory). O valor do campo `aadObjectId` é uma cadeia de caracteres.

#### <a name="conversation-account-is-group"></a>Conta de conversa Is Group

O campo `isGroup` indica se a conversa contém mais de dois participantes no momento de geração da atividade. O valor de campo `isGroup` é um valor booliano; se for omitido, o valor padrão será `false`. Normalmente, este campo controla o comportamento mediante menção dos participantes no canal e DEVERIA ser definido como `true` se, e somente se, mais de dois participantes tivessem a capacidade de enviar e receber atividades dentro da conversa.

### <a name="entity"></a>Entidade

Entidades carregam metadados sobre uma atividade ou conversa. O significado e a forma de cada entidade são definidos pelo campo `type` e outros campos de tipo específico ficam como pares para o campo `type`.

`R7600`: receptores DEVEM ignorar entidades cujos tipos eles não entendem.

`R7601`: os receptores DEVERIAM ignorar entidades cujo tipo eles compreendam, mas não possam processar devido a erros de sintaxe, por exemplo.

As partes que projetam um tipo de entidade existente para o formato de entidade de atividade são aconselhadas a resolver conflitos com o nome do campo `type` e incompatibilidades com o requisito de serialização `R2001` como parte da associação entre o IRI e o esquema de entidade.

#### <a name="type"></a>Tipo

O campo `type` é obrigatório e define o significado e a forma da entidade. `type` deve conter [IRIs](https://tools.ietf.org/html/rfc3987) [[3](#references)], apesar de haver um pequeno número de tipos de entidade não IRI definidos no [Apêndice I](#appendix-ii---non-iri-entity-types).

`R7610`: os remetentes DEVERIAM usar nomes de tipo não IRI apenas para tipos descritos no [Apêndice II](#appendix-ii---non-iri-entity-types).

`R7611`: os remetentes PODEM enviar os tipos IRI para tipos descritos no [Apêndice II](#appendix-ii---non-iri-entity-types) se eles souberem que o receptor os entenda.

`R7612`: os remetentes DEVERIAM usar ou estabelecer IRIs para tipos de entidades não definidas no [Apêndice II](#appendix-ii---non-iri-entity-types).

### <a name="suggested-actions"></a>Ações sugeridas

As ações sugeridas podem ser enviadas dentro do conteúdo da mensagem para criar elementos de ação interativa em um interface de usuário do cliente.

`R7700`: os clientes que não dão suporte a uma interface do usuário capaz de renderizar ações sugeridas DEVERIAM ignorar o campo `suggestedActions`.

`R7701`: os remetentes DEVERIAM omitir o campo `suggestedActions` se o campo `actions` estivesse vazio.

#### <a name="to"></a>Para

O campo `to` contém IDs de conta de canal ao qual as ações sugeridas devem ser exibidas. Este campo pode ser usado para filtrar as ações para um subconjunto de participantes na conversa.

`R7710`: se o campo `to` estivesse ausente ou vazio, o cliente DEVERIA exibir as ações sugeridas para todos os participantes da conversa.

`R7711`: se o campo `to` contivesse IDs inválidas, esses valores DEVERIAM ser ignorados.

#### <a name="actions"></a>Ações

O campo `actions` uma lista plana de ações a serem exibidas. O valor de cada elemento da lista `actions` é um objeto complexo do tipo `cardAction`.

### <a name="message-reaction"></a>Reação de mensagem

Reações de mensagem representam uma interação social ("curtir", "+1" etc.). Atualmente, as reações de mensagem contêm apenas um campo: o campo `type`.

#### <a name="type"></a>Tipo

O campo `type` descreve o tipo de interação social. O valor do campo `type` é uma cadeia de caracteres e seu significado é definido pelo canal no qual a interação ocorre. Alguns valores comuns, como `like` e `+1` embora sejam uniformes por convenção e não por regra.

## <a name="references"></a>Referências

1. [RFC 2119](https://tools.ietf.org/html/rfc2119)
2. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)
3. [RFC 3987](https://tools.ietf.org/html/rfc3987)
4. [Markdown](https://daringfireball.net/projects/markdown/)
5. [ISO 639](https://www.iso.org/iso-639-language-codes.html)
6. [SSML](https://www.w3.org/TR/speech-synthesis/)
7. [XML](https://www.w3.org/TR/xml/)
8. [Tipos de mídia MIME](https://www.iana.org/assignments/media-types/media-types.xhtml)
9. [RFC 2397](https://tools.ietf.org/html/rfc2397)
10. [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html)
11. [Cartões Adaptáveis](https://adaptivecards.io)

# <a name="appendix-i---changes"></a>Apêndice I - Alterações

## <a name="2018-02-07"></a>2018-02-07

* Rascunho inicial

# <a name="appendix-ii---non-iri-entity-types"></a>Apêndice II - Tipos de entidade não IRI

As [entidades](#entity) de atividade comunicam metadados adicionais sobre a atividade, como a localização de um usuário ou a versão do aplicativo de mensagens usado. Os tipos de atividades devem ser IRIs, mas uma pequena lista de nomes não IRI é usada normalmente. Este apêndice é uma lista completa dos tipos de entidade não IRI com suporte.

| Tipo           | Equivalente do URI                          | DESCRIÇÃO               |
| -------------- | --------------------------------------- | ------------------------- |
| GeoCoordinates | https://schema.org/GeoCoordinates       | Schema.org GeoCoordinates |
| Mention        | https://botframework.com/schema/mention | @-mention                 |
| Posicionar          | https://schema.org/Place                | Schema.org place          |
| Thing          | https://schema.org/Thing                | Schema.org thing          |
| clientInfo     | N/D                                     | Informações de cliente do Skype         |

### <a name="clientinfo"></a>clientInfo

A entidade clientInfo contém informações estendidas sobre o software cliente usado para enviar a mensagem do usuário. Ele contém três propriedades, todas opcionais.

`R9201`: os bots não DEVERIAM enviar a entidade `clientInfo`.

`R9202`: os remetentes DEVERIAM incluir a entidade `clientInfo` apenas quando um ou mais campos estivessem preenchidos.

#### <a name="locale-deprecated"></a>Locale (Preterido)

O campo `locale` contém a localidade do usuário. Este campo duplica o campo [`locale`](#locale) na raiz da Atividade. O valor do campo `locale` é um código [ISO 639](https://www.iso.org/iso-639-language-codes.html) [[5](#references)] dentro de uma cadeia de caracteres.

O campo `locale` dentro de `clientInfo` foi preterido.

`R9211`: os receptores NÃO DEVERIAM usar o campo `locale` dentro do objeto `clientInfo`.

`R9212`: os remetentes PODEM preencher o campo `locale` dentro de `clientInfo` por razões de compatibilidade. Se a compatibilidade com receptores mais antigos não fosse necessária, os remetentes NÃO DEVERIAM enviar a propriedade `locale`.

#### <a name="country"></a>País

O campo `country` contém a localidade detectada do usuário. Esse valor pode ser diferente de quaisquer dados [`locale`](#locale), pois `country` é detectado, enquanto `locale` normalmente é uma configuração do usuário ou do aplicativo. O valor do campo `country` é um código de país [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) [[10](#references)] de 2 ou 3 letras.

`R9220`: os canais NÃO DEVERIAM permitir que os clientes especifiquem valores arbitrários para o campo `country`. Os canais DEVERIAM usar um mecanismo, como GPS, API de localização ou detecção de endereço IP para estabelecer o país gerador da solicitação.

#### <a name="platform"></a>Plataforma

O campo `platform` descreve a plataforma cliente de mensagens usada para gerar a atividade. O valor do campo `platform` é uma cadeia de caracteres, e a lista de valores possíveis e seu significado é definido pelo canal que as envia.

Em canais com um feed de chat persistente, `platform` normalmente é útil apenas ao decidir que conteúdo incluir, não o formato desse conteúdo. Por exemplo, se um usuário em um dispositivo móvel solicitar ajuda do suporte ao produto, um bot poderia gerar a ajuda específica ao dispositivo móvel. No entanto, o usuário pode reabrir o feed do chat em seus PCs para que possam lê-lo nessa tela ao fazer alterações em seu dispositivo móvel. Nessa situação, o campo `platform` destina-se a informar o conteúdo, mas o conteúdo deve ser visível em outros dispositivos.

`R9230`: os bots NÃO DEVERIAM usar o campo `platform` para controlar como os dados de resposta são formatados, a menos que tivessem um conhecimento específico de que o conteúdo que estão enviando pudesse ser visto apenas no dispositivo em questão.

# <a name="appendix-iii---protocols-using-the-invoke-activity"></a>Apêndice III - Protocolos que usam a atividade de invocação

A [atividade de invocação](#invoke-activity) foi projetada para uso somente dentro de protocolos com suporte dos canais do Bot Framework (ou seja, não é um mecanismo de extensibilidade genérico). Este apêndice contém uma lista de todos os protocolos de Bot Framework que usam essa atividade.

## <a name="payments"></a>Pagamentos

O protocolo de pagamentos do Bot Framework usa Invoke para calcular as taxas de envio e impostos e para comunicar uma confirmação dos pagamentos concluídos.

O protocolo de pagamentos define três operações (definidas no campo `name` de uma atividade de invocação):
* `payment/shippingAddressChange`
* `payment/shppingOptionsChange`
* `payment/paymentResponse`

É possível encontrar mais detalhes na página [Solicitar pagamento](https://docs.microsoft.com/en-us/bot-framework/dotnet/bot-builder-dotnet-request-payment).

## <a name="teams-compose-extension"></a>Extensão de composição de Equipes

O canal do Microsoft Teams usa Invoke para [compor extensões](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/messaging-extensions). Esse uso de Invoke é específico do Microsoft Teams.



