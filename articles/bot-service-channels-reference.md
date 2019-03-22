---
title: Referência de canais
description: Referência de canais do Bot Framework
keywords: referência de canais, canais do bot builder, canais do bot framework
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: tools
ms.date: 03/01/2019
ms.openlocfilehash: 28c4ece705c001d5f80fdc877773c03ac400204c
ms.sourcegitcommit: 4139ef7ebd8bb0648b8af2406f348b147817d4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58073842"
---
# <a name="categorized-activities-by-channel"></a>Atividades categorizadas por canal

As tabelas a seguir mostram quais eventos (atividades durante a transmissão) têm origem em quais canais.

Esta é a legenda das tabelas:

Símbolo              | Significado
:------------------:|:------------------------------------------------
:white_check_mark:  |O Bot deve esperar receber essa Atividade
:x:                 |O Bot não deve **nunca** esperar receber essa Atividade
:white_large_square:|Atualmente não determinado se o Bot pode receber isto

As atividades podem ser divididas em categorias diferentes de maneira significativa. Para cada categoria, temos uma tabela de Atividades possíveis.

<a name="conversational"></a>Conversação
--------------

 \                      | Cortana            | Linha Direta        | Direct Line (Webchat) | Email              | Facebook           | GroupMe            | Kik                | Teams              | Margem de atraso              | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :----------------: | :--------------------: |:----:              | :------:           | :-----:            | :-----:            | :---:              | :---:              | :---:   | :------------: | :------: | :----:  
Mensagem                 | :white_check_mark: | :white_check_mark: | :white_check_mark:     | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark:        | :white_check_mark:  | :white_check_mark: 
MessageReaction         | :x:                | :x:                | :x:                    | :x:                | :x:                | :x:                | :x:                | :white_check_mark: | :x:                | :x:      | :x:             | :x:       | :x:      

- Todos os canais enviam atividades de mensagens.
- Ao usar um Diálogo, geralmente as Atividades de Mensagens devem sempre ser passadas para o Diálogo.
- Provavelmente isso não acontece com MessageReaction, embora a maioria faça parte da conversa.
- Logicamente, há dois tipos de MessageReaction: Adicionada e Removida


> [!TIP]
> As “Reações da Mensagem” normalmente são como um “sinal de joia” em um comentário anterior. Elas podem ocorrer fora de ordem, por isso, podem ser encaradas como semelhantes aos botões. Atualmente, esta Atividade é enviada pelo Canal do Teams.  


<a name="welcome"></a>Bem-Vindo
-------

 \                         | Cortana            | Linha Direta        | Direct Line (Webchat) | Email   | Facebook             | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:----------------------    | :-----:            | :---------:        | :--------------------: |:----:   | :------:             | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
ConversationUpdate         | :white_check_mark: | :white_check_mark: | :white_check_mark:     | :x:     | :white_large_square: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:      | :x:             | :white_check_mark:  | :x:     
ContactRelationUpdate      | :x:                | :x:                | :x:                    | :x:     | :x:                  | :x:      | :x:      | :x:      | :x:      | :white_check_mark: | :white_check_mark:        | :x:       | :x:     

- É comum que os canais enviem Atividades ConversationUpdate.
- Logicamente, há dois tipos de MessageReaction: Adicionada e Removida
- É tentador supor que o comportamento de “Boas-vindas” do bot possa ser simplesmente implementado conectando ConversationUpdate.Added, e isso às vezes funciona.
- Porém, essa é uma simplificação. Para produzir um comportamento de “boas-vindas” confiável, a implementação do bot talvez precise usar estado também.


<a name="application-extensibility"></a>Extensibilidade de aplicativo
-------------------------

 \                      | Cortana            | Linha Direta        | Direct Line (Webchat) | Email   | Facebook | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Event.*                    | :white_large_square: | :white_check_mark: | :white_check_mark:    | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.CreateConversation   | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.ContinueConversation | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  

- As Atividades de Evento são um mecanismo de extensibilidade no Direct Line (_também conhecido como Chat na Web_).
- Um aplicativo que possua cliente e servidor pode optar por encapsular os próprios eventos através do serviço usando a Atividade de Evento.


<a name="microsoft-teams"></a>Equipes da Microsoft
------------------

 \                      | Cortana            | Linha Direta        | Direct Line (Webchat) | Email   | Facebook | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Invoke.TeamsVerification   | :x:      | :x:          | :x: | :x:   | :x:       | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
Invoke.ComposeResponse     | :x:      | :x:          | :x: | :x:   | :x:       | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     

- Além de outras atividades do tipo, o Microsoft Teams define algumas Atividades de Invocação específicas do Teams.
- As Atividades de Invocação são específicas de um aplicativo e não algo que um cliente define.
- Não há nenhuma noção geral dos subtipos específicos de Invocação da atividade.
- Atualmente, a invocação é a única Atividade que aciona um comportamento de solicitação-resposta no bot.

Isto é muito importante: ao usar Diálogos, para o OAuth Prompt funcionar a Atividade Invoke.TeamsVerification deve ser encaminhada para o Diálogo.


<a name="message-update"></a>Atualização de mensagem
--------------

 \                      | Cortana            | Linha Direta        | Direct Line (Webchat) | Email   | Facebook | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
MessageUpdate | :x:      | :x:          | :x:    | :x: | :x:      | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
MessageDelete | :x:      | :x:          | :x:    | :x: | :x:      | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     

- No momento, o Teams oferece suporte à Atualização de Mensagens.


<a name="oauth"></a>OAuth
-------

 \                      | Cortana            | Linha Direta        | Direct Line (Webchat) | Email   | Facebook | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Event.TokenResponse| :white_large_square:  | :white_check_mark:   | :white_check_mark:    | :x:    | :white_large_square: | :white_large_square: | :white_large_square: | :x:    | :white_large_square: | :white_large_square: | :white_large_square:       | :white_large_square: | :white_large_square: 

Isto é muito importante: ao usar Diálogos, para o OAuth Prompt funcionar a Atividade Event.TokenResponse deve ser encaminhada para o Diálogo.


<a name="uncategorized"></a>Não categorizado 
-------------

 \                      | Cortana  | Linha Direta        | Direct Line (Webchat) | Email | Facebook | GroupMe | Kik     | Teams | Margem de atraso | Skype | Skype Business | Telegram | Twilio  
:---------------------- | :-----:  | :---------:        | :--------------------: |:----: | :------: | :-----: | :-----: | :---: | :---: | :---: | :------------: | :------: | :----:  
EndOfConversation       | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
InstallationUpdate      | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Digitação                  | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Entrega                 | :x:      | :x:                | :x:                    | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     


<a name="out-of-use-includes-payment-specific-invoke"></a>Fora de uso (inclui Invocação específica de Pagamento)
---------------------------------------------
- DeleteUserData 
- Invoke.PaymentRequest  
- Invoke.Address
- Ping

---

## <a name="summary-of-activities-supported-per-channel"></a>Resumo das Atividades com suporte por Canal

<a name="cortana"></a>Cortana
-------
- Mensagem
- ConversationUpdate
- _Event.TokenResponse_
- _EndOfConversation (quando a janela fecha?)_

<a name="direct-line"></a>Linha Direta
--------
- Mensagem
- ConversationUpdate
- Event.TokenResponse
- Event.*
- _Event.CreateConversation_
- _Event.ContinueConversation_

<a name="email"></a>Email
-----
- Mensagem

<a name="facebook"></a>Facebook
--------
- Mensagem
- _Event.TokenResponse_

<a name="groupme"></a>GroupMe
-------
- Mensagem
- ConversationUpdate
- _Event.TokenResponse_

<a name="kik"></a>Kik
---
- Mensagem
- ConversationUpdate
- _Event.TokenResponse_

<a name="teams"></a>Teams
-----
- Mensagem
- ConversationUpdate
- MessageReaction
- MessageUpdate
- MessageDelete
- Invoke.TeamsVerification
- Invoke.ComposeResponse


<a name="slack"></a>Margem de atraso
-----
- Mensagem
- ConversationUpdate
- _Event.TokenResponse_


<a name="skype"></a>Skype
-----
- Mensagem
- ContactRelationUpdate
- _Event.TokenResponse_


<a name="skype-business"></a>Skype Business
--------------
- Mensagem
- ContactRelationUpdate 
- _Event.TokenResponse_


<a name="telegram"></a>Telegram
--------
- Mensagem
- ConversationUpdate
- _Event.TokenResponse_


<a name="twilio"></a>Twilio
------
- Mensagem

## <a name="summary-table-all-activities-to-all-channels"></a>Tabela de resumo de todas as atividades para todos os canais

 \                         | Cortana              | Linha Direta          | Direct Line (Webchat) | Email                | Facebook             | GroupMe | Kik     | Teams   | Margem de atraso   | Skype   | Skype Business | Telegram | Twilio  
:----------------------    | :-----:              | :---------:          | :--------------------: |:----:                | :------:             | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Mensagem                    | :white_check_mark:   | :white_check_mark:   | :white_check_mark:     | :white_check_mark:   | :white_check_mark:   | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark:        | :white_check_mark:  | :white_check_mark: 
MessageReaction            | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:      | :x:      | :x:             | :x:       | :x:      
ConversationUpdate         | :white_check_mark:   | :white_check_mark:   | :white_check_mark:     | :x:                  | :white_large_square: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:      | :x:             | :white_check_mark:  | :x:     
ContactRelationUpdate      | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :x:      | :x:      | :white_check_mark: | :white_check_mark:        | :x:       | :x:     
Event.*                    | :white_large_square: | :white_check_mark:   | :white_check_mark:     | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.CreateConversation   | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.ContinueConversation | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Invoke.TeamsVerification   | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
Invoke.ComposeResponse     | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
MessageUpdate              | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
MessageDelete              | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
Event.TokenResponse        | :white_large_square: | :white_check_mark:   | :white_check_mark:     | :x:                  | :white_large_square: | :white_large_square: | :white_large_square: | :x:    | :white_large_square: | :white_large_square: | :white_large_square:       | :white_large_square: | :white_large_square: 
EndOfConversation          | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
InstallationUpdate         | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Digitação                     | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Entrega                    | :x:                  | :x:                  | :x:                    | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     

## <a name="web-chat"></a>Chat na Web 
O Webchat enviará:
- "message": com "text" e/ou "attachments"
- "event": com "name" e "value" (como JSON/cadeia de caracteres)
- "typing": se o usuário definir uma opção, ou seja, "sendTypingIndicator", o Webchat não enviará "contactRelationUpdate". Além disso, o Webchat não oferece suporte a "messageReaction" e ninguém nos pede explicitamente para oferecer suporte a este recurso.

Por padrão, o Webchat renderiza:
- “message”: renderiza como carrossel ou empilhada, dependendo da opção na atividade
- “typing”: renderiza durante 5 segundos e o oculta ou até a próxima atividade entrar
- "conversationUpdate": oculta
- "event": oculta
- Outros: mostra uma caixa de aviso (nunca vimos na produção). Você pode modificar esse pipeline de renderização para adicionar, remover ou substituir qualquer renderização personalizada.

Você pode usar o Webchat para enviar qualquer tipo de atividade e conteúdo. Nós não documentamos nem recomendamos este recurso. Você deve usar a atividade “event” no lugar.
