---
title: Sobre os consumidores de habilidades | Microsoft Docs
description: Descreve como a lógica de conversação em um bot de habilidade pode ser usada por um consumidor de habilidades usando o SDK do bot Framework.
keywords: bot skill, host bot, skill consumer.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/12/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: dddca86b72f6f6efd6e98a27f71b89f4a1929701
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95452020"
---
# <a name="about-skill-consumers"></a>Sobre os consumidores de habilidades

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Um _consumidor de habilidades_ é um bot que pode chamar uma ou mais habilidades.
Em relação aos skills, um _bot raiz_ é um bot voltado para o usuário que também é um consumidor de skills.

Da perspectiva do usuário, o bot raiz é o bot com o qual ele está interagindo.
Da perspectiva do skill, o consumidor de skills é o canal pelo qual ele se comunica com o usuário.
(Para obter mais informações, confira a [Visão geral de habilidades](skills-conceptual.md) e [Sobre os bots de habilidades](skills-about-skill-bots.md).)

Como um consumidor de skills, um bot raiz inclui algumas lógicas adicionais para gerenciar o tráfego entre ele e um skill:

- Informações de configuração para cada skill que a raiz usa.
- Um _alocador de ID de conversa_ que permite que a alterne entre a conversa que está tendo com o usuário e a que está tendo com um skill.
- Um _cliente de skills_ que pode empacotar e encaminhar atividades para um bot de skills.
- Um _manipulador de skills_ que pode receber solicitações e desempacotar atividades de um bot skill.

## <a name="managing-skills"></a>Gerenciando skills

O início e a permissão de execução de uma só habilidade até a conclusão são gerenciados com algumas adições ao consumidor de habilidades. Cenários mais complicados são possíveis, com várias habilidades ou threads de conversa.

Um consumidor de habilidades implementa pelo menos dois pontos de extremidade HTTP:

- Um _ponto de extremidade de mensagens_ recebe atividades do usuário ou do canal. Esse é o ponto de extremidade de mensagens usual que todos os bots implementam.
- Um _ponto de extremidade do host de habilidades_ para receber atividades de uma habilidade. Isso atua como uma URL de retorno de chamada, a URL de serviço para a qual a habilidade responde. (O consumidor de habilidades precisa emparelhar o código que recebe a solicitação do método HTTP da habilidade com um manipulador de habilidades.)

### <a name="skill-descriptions"></a>Descrições de skills

Para cada skill, adicione um objeto de _skill do Bot Framework_ ao arquivo de configuração do consumidor de skills. Cada um terá uma ID, uma ID do aplicativo e um ponto de extremidade para a habilidade.

| Propriedade | Descrição
| :--- | :--- |
| _ID_ | A ID ou a chave do skill, específica ao consumidor de skills. |
| _ID do Aplicativo_ | O `appId` atribuído ao recurso bot quando o skill foi registrado no Azure. |
| _Ponto de extremidade de skill_ | O ponto de extremidade de mensagens para o skill. Essa é a URL que o consumidor usará para se comunicar com o skill. |

### <a name="skill-client-and-skill-handler"></a>Cliente de skills e manipulador de skills

<!-- Is this still accurate? -->
O consumidor de skills usa um cliente de skills para enviar atividades a um skill. O cliente:

- Seleciona uma atividade a ser enviada para a habilidade, seja de um usuário ou gerada pelo consumidor.
- Define a URL do serviço na atividade enviada para a habilidade para o ponto de extremidade do host de habilidades do consumidor.
- Substitui a referência de conversa original por uma para a conversa consumidora de skills.
- Adiciona um token de autenticação de bot para bot.
- Envia a atividade atualizada para o skill.

O consumidor de skills usa um manipulador de skills para receber atividades de um skill. O manipulador:

- Processa os métodos da API REST de Serviço de canal.
- Impõe a validação de autenticação e declarações.
- Recupera a referência de conversa original.
- Gera uma atividade para o adaptador do consumidor. Essa atividade sinalizará que o skill foi concluído ou que será uma atividade a ser encaminhada ao usuário.

## <a name="manage-a-skill-directly"></a>Gerenciar uma habilidade diretamente

Você precisa adicionar lógica ao consumidor de skills para acompanhar os skills ativos.
Cabe ao consumidor saber ele gerencia os skills em geral, se ele pode manter vários skills ativos paralelamente ou não, e assim por diante.
Cenários específicos a serem considerados incluem:

- Inicio de uma nova conversa consumidora de skills. (Isso será associado a uma conversa específica de consumidor usuário).
  - Para passar parâmetros para um skill, defina a propriedade _valor_ na atividade inicial para o skill.
- Continuação de uma conversa consumidora de skills existente.
- Reconhecimento de uma atividade `endOfConversation` do skill como sinalizadora do fim de uma conversa consumidora de skills.
  - Para recuperar qualquer valor retornado de um skill, verifique a propriedade _valor_ da atividade.
  - Para verificar por que o skill está terminando, verifique o parâmetro _código_ da atividade, que pode indicar que o skill encontrou um erro.
- Cancelamento de um skill do consumidor enviando uma atividade `endOfConversation` para o skill.

Confira como [Implementar um consumidor de habilidades](skill-implement-consumer.md) para obter um consumidor que gerencia uma habilidade diretamente.

## <a name="manage-a-skill-using-a-skill-dialog"></a>Gerenciar uma habilidade usando um diálogo de habilidade

Se estiver usando a [biblioteca de caixas de diálogo](bot-builder-concept-dialog.md), você poderá usar uma _caixa de diálogo de habilidade_ para gerenciar uma habilidade. Embora a caixa de diálogo de habilidade seja a caixa de diálogo ativa, ela encaminhará atividades para a habilidade associada.

- Ao criar a caixa de diálogo de habilidade, use o parâmetro _opções de caixa de diálogo_ para fornecer todas as informações de que a caixa de diálogo precisa para gerenciar a habilidade, como a ID do aplicativo e a URL de retorno de chamada do consumidor, a fábrica da ID de conversa a ser usada, as propriedades da habilidade e assim por diante.
  - Se desejar gerenciar mais de uma habilidade como uma caixa de diálogo, será necessário criar uma caixa de diálogo de habilidade separada para cada habilidade.
  - Geralmente, você adicionará a caixa de diálogo de habilidade a uma caixa de diálogo de componente.
- Para iniciar a caixa de diálogo de habilidade, use o método _begin_ do contexto da caixa de diálogo e forneça a ID da caixa de diálogo de habilidade. Use o parâmetro _opções_ para fornecer a atividade que o consumidor enviará como a primeira atividade para a habilidade.
- Você pode cancelar ou interromper a caixa de diálogo de habilidade como faria com qualquer outra caixa de diálogo. Confira como [manipular interrupções do usuário](bot-builder-howto-handle-user-interrupt.md) para ver um exemplo.

Confira como [Usar um diálogo para consumir uma habilidade](skill-use-skilldialog.md) para obter um consumidor que usa um diálogo para gerenciar uma habilidade.

## <a name="using-a-delivery-mode-of-expect-replies"></a>Usando um modo de entrega de respostas esperadas

Os bots e as habilidades usam REST padrão do setor e JSON por HTTPS para comunicação. O fluxo de processamento de atividades normais começa quando o bot raiz recebe uma postagem de um canal no _ponto de extremidade de mensagens_. Em seguida, o bot raiz envia a atividade para a habilidade de processamento. As respostas da habilidade são enviadas de volta para o ponto de _extremidade do host de habilidades_ do bot raiz, não para o ponto de extremidade do sistema de mensagens. Por fim, as respostas são processadas mais detalhadamente ou enviadas de volta ao canal pelo bot raiz. Esse fluxo normal pode ser alterado alterando o _modo de entrega_ da atividade enviada para a habilidade. Se o _modo de entrega_ for definido como "ExpectReplies", a habilidade não será reposta para o ponto de extremidade do host de habilidade.  Em vez disso, todas as atividades de resposta são serializadas no corpo da resposta.  Em seguida, o bot raiz itera essas atividades, processando-as de forma semelhante a como elas seriam processadas pelo ponto de extremidade do host de habilidades.

Para obter informações, consulte o [modo de entrega](https://github.com/microsoft/botframework-sdk/blob/main/specs/botframework-activity/botframework-activity.md#delivery-mode) na especificação da atividade.
