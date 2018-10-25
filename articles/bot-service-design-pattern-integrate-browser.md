---
title: Integrar seu bot a um navegador da Web | Microsoft Docs
description: Saiba como projetar uma transição suave de usuário do bot para o navegador da Web e vice-versa.
author: matvelloso
ms.author: mateusv
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: e3833595003edda46a6ffd1d508237262aad94e1
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999223"
---
# <a name="integrate-your-bot-with-a-web-browser"></a>Integrar seu bot a um navegador da Web

Alguns cenários exigem mais do que apenas um bot para atender a um requisito. Um bot talvez seja necessário para enviar o usuário para um navegador da Web para concluir uma tarefa e, em seguida, continuar a conversa com o usuário depois que a tarefa tiver sido concluída. 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Se um bot quiser a capacidade de ler o calendário do usuário no Office 365, ou talvez até mesmo criar compromissos em nome do usuário, o usuário deverá primeiro se autenticar com o Microsoft Azure Active Directory e autorizar o bot para acessar os dados de calendário do usuário. O bot redirecionará o usuário para um navegador da Web para concluir as tarefas de autenticação e autorização e, em seguida, continuará a conversa com o usuário. 

## <a name="security-and-compliance"></a>Segurança e conformidade
Os requisitos de segurança e conformidade muitas vezes restringem o tipo de informação que um bot pode trocar com um usuário. Em alguns casos, pode ser necessário para o usuário enviar/receber dados fora da conversa atual. Por exemplo, se um usuário quiser executar um pagamento usando um provedor de pagamento de terceiros, um número de cartão de crédito não deverá ser especificado dentro do contexto da conversa. Em vez disso, o bot redirecionará o usuário para um navegador da Web para concluir o processo de pagamento e, em seguida, continuará a conversa com o usuário.

Este artigo explora o processo de facilitar a transição de um usuário do bot para o navegador da Web e de volta. 

> [!NOTE]
> A transição de bate-papo do navegador da Web e de volta não é ideal, porque alternar entre aplicativos pode confundir o usuário. Para fornecer uma melhor experiência, vários canais oferecem janelas HTML internas que um bot pode usar para apresentar aplicativos que de outra forma seriam exibidos em um navegador da Web. Essa técnica permite que o usuário permaneça dentro da conversa enquanto ainda acessa recursos externos. Essa abordagem é conceitualmente semelhante a aplicativos móveis gerenciando os fluxos de autorização usando OAuth em modos de exibição da Web incorporados.

## <a name="bot-to-web-browser-and-back-again"></a>Bot para o navegador da Web e de volta

Este diagrama mostra o fluxo de alto nível para a integração entre o navegador da Web e o bot. 

![Bot para interação da Web](~/media/bot-service-design-pattern-integrate-browser/bot-to-web1.png)

Considere cada etapa do fluxo:

1. <a id="generate-hyperlink"></a>O bot gera e exibe um hiperlink que redirecionará o usuário para um site da Web. 
   O hiperlink normalmente inclui dados por meio de parâmetros de querystring na URL de destino que especificam informações sobre o contexto da conversa atual, como ID da conversa, a ID do canal e a ID do usuário no canal. 

2. O usuário clica no hiperlink e é redirecionado para a URL de destino dentro de um navegador da Web. 

3. O bot entra em um estado aguardando a comunicação do site da Web para indicar que o fluxo do site foi concluído.  
   > [!TIP]
   > Crie este fluxo para que o bot não fique permanentemente no estado 'aguardando' se o usuário nunca concluir o fluxo do site. Em outras palavras, se o usuário abandonar o navegador da Web e começar a se comunicar com o bot novamente, o bot deverá confirmar, e não [ignorar](~/bot-service-design-navigation.md#the-mysterious-bot) essa entrada.

4. O usuário conclui as tarefas necessárias por meio do navegador da Web. 
   Isso pode ser um fluxo de OAuth ou qualquer sequência de eventos necessários pelo cenário em questão. 

5. <a id="generate-magic-number"></a>Quando o usuário concluir o fluxo do site, o site gerará um '[número mágico](#verify-identity)' e instruirá o usuário para copiara o valor e colá-lo de volta na conversa com o bot. 

6. <a id="signal-to-bot"></a>O site [sinaliza para o bot](#website-signal-to-bot) que o usuário concluiu o fluxo do site. 
   Ele comunica o 'número mágico' para o bot e fornece todos os outros dados relevantes.
   Por exemplo, no caso de um fluxo de OAuth, o site forneceria um token de acesso para o bot.

7. O usuário retorna ao bot e cola o 'número mágico' no bate-papo. 
   O bot valida se o 'número mágico' fornecido pelo usuário corresponde ao valor esperado, verificando se o usuário atual é o mesmo usuário que anteriormente clicou no hiperlink para iniciar o fluxo de site. 

### <a id="verify-identity"></a> Verificar a identidade do usuário usando o 'número mágico'

A geração de um 'número mágico' durante o fluxo do bot para o site ([etapa 5](#generate-magic-number) acima) permite que o bot, subsequentemente verifique se que o usuário que iniciou o fluxo de site é realmente o usuário para o qual ele foi destinado. Por exemplo, se um bot está realizando um chat de grupo com vários usuários, qualquer um deles poderia ter clicado no hiperlink para iniciar o fluxo de site. Sem o processo de validação do 'número mágico', o bot não tem nenhuma maneira de saber qual usuário concluiu o fluxo. Um usuário pode autenticar e injetar os tokens de acesso na sessão de outro usuário. 

> [!WARNING] 
> Isso não é apenas um risco em bate-papos de grupo. Sem o processo de validação do 'número mágico', qualquer pessoa que obtiver o hiperlink para iniciar o fluxo de site poderá falsificar a identidade do usuário. 

O número mágico deve ser um número aleatório gerado usando uma biblioteca de criptografia forte. Para obter um exemplo do processo de geração em C#, veja <a href="https://github.com/MicrosoftDX/botauth/tree/master/CSharp" target="_blank">esse código</a> dentro da biblioteca <a href="https://www.nuget.org/packages/BotAuth" target="_blank">BotAuth</a>. O BotAuth permite que os bots que são criados no Microsoft Bot Framework implementem o fluxo de bot para site para autenticar um usuário em um site e, em seguida, usar o token de acesso que foi gerado pelo processo de autenticação. Como o BotAuth não faz nenhuma suposição sobre os recursos do canal, esses fluxos devem funcionar bem com a maioria dos canais. 

> [!NOTE]
> A necessidade para o processo de validação do 'número mágico' deve ser preterida porque os canais criam seus próprios modos de exibição da Web incorporados.

### <a id="website-signal-to-bot"></a> Como o site 'sinaliza' o bot?

Quando o bot [gera o hiperlink](#generate-hyperlink) em que o usuário clicará para iniciar o fluxo do site, isso inclui informações por meio de parâmetros de querystring na URL de destino sobre o contexto da conversa atual, como ID da conversa, a ID do canal e a ID do usuário no canal. O site, posteriormente, pode usar essas informações para ler e gravar as variáveis de estado para esse usuário ou conversa com o SDK do Construtor de Bot ou APIs REST. Veja a [etapa 6](#signal-to-bot) acima para obter um exemplo de como o site 'sinaliza' para o bot que o fluxo do site foi concluído.

## <a name="sample-code"></a>Exemplo de código

Conforme descrito neste artigo, a biblioteca <a href="https://github.com/MicrosoftDX/botauth" target="_blank">BotAuth</a> permite que os fluxos de OAuth sejam associados aos bots que são criados usando o .NET e o Node no Microsoft Bot Framework.

## <a name="additional-resources"></a>Recursos adicionais

- [Diálogos](~/dotnet/bot-builder-dotnet-dialogs.md)
- [Gerenciar o fluxo da conversa com diálogos (.NET)](~/dotnet/bot-builder-dotnet-manage-conversation-flow.md)
- [Gerenciar o fluxo da conversa com diálogos (Node.js)](~/nodejs/bot-builder-nodejs-manage-conversation-flow.md)
