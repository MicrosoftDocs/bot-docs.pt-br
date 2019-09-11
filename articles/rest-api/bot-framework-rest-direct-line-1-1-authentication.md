---
title: Autenticação | Microsoft Docs
description: Saiba como autenticar solicitações de API na API da Linha Direta v1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 555cb3298114c3eb8ba8a4e1c41b5515e929fd91
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299640"
---
# <a name="authentication"></a>Authentication

> [!IMPORTANT]
> Este artigo descreve a autenticação na API da Linha Direta v1.1. Se você estiver criando uma nova conexão entre seu aplicativo cliente e o bot, use [API da Linha Direta 3.0](bot-framework-rest-direct-line-3-0-authentication.md) em vez disso.

Um cliente pode autenticar solicitações para a API da Linha Direta 1.1 usando um **segredo** que você [obtém da página de configuração de canal da Linha Direta](../bot-service-channel-connect-directline.md) no Portal do Bot Framework ou usando um **token** obtido em tempo de execução.

O segredo ou o token deve ser especificado no cabeçalho `Authorization` de cada solicitação, usando o esquema "Bearer" ou "BotConnector". 

**Esquema Bearer**:
```http
Authorization: Bearer SECRET_OR_TOKEN
```

**Esquema de BotConnector**:
```http
Authorization: BotConnector SECRET_OR_TOKEN
```

## <a name="secrets-and-tokens"></a>Segredos e tokens

Um **segredo** de Linha Direta é uma chave mestra que pode ser usada para acessar qualquer conversa que pertença ao bot associado. Um **segredo** também pode ser usado para obter um **token**. Os segredos não expiram. 

Um **token** de Linha Direta é uma chave que pode ser usada para acessar uma única conversa. Um token expira, mas pode ser atualizado. 

Se você estiver criando um aplicativo de serviço a serviço, a abordagem mais simples poderá ser especificar o **segredo** no cabeçalho `Authorization` de solicitações da API da Linha Direta. Se estiver gravando um aplicativo em que o cliente é executado em um navegador da Web ou em um aplicativo móvel, talvez convenha trocar seu segredo por um token (que funciona somente para uma única conversa e irá expirar se não for atualizado) e especificar o **token** no cabeçalho `Authorization` de solicitações de API da Linha Direta. Escolha o modelo de segurança que funcione melhor para você.

> [!NOTE]
> Suas credenciais de cliente de Linha Direta são diferentes das credenciais do seu bot. Isso permite revisar as chaves de forma independente e compartilhar os tokens de cliente sem revelar a senha do seu bot. 

## <a name="get-a-direct-line-secret"></a>Obter um segredo de Linha Direta

Você pode [obter um segredo de Linha Direta](../bot-service-channel-connect-directline.md) através da página de configuração do canal de Linha Direta para o bot no <a href="https://dev.botframework.com/" target="_blank">Portal do Bot Framework</a>:

![Configuração da Linha Direta](../media/direct-line-configure.png)

## <a id="generate-token"></a> Gerar um token de Linha Direta

Para gerar um token da Linha Direta que possa ser usado para acessar uma única conversa, primeiro obtenha o segredo da Linha Direta na página de configuração do canal da Linha Direta no <a href="https://dev.botframework.com/" target="_blank">Portal do Bot Framework</a>. Em seguida, envie essa solicitação para trocar seu segredo da Linha Direta para um token da Linha Direta:

```http
POST https://directline.botframework.com/api/tokens/conversation
Authorization: Bearer SECRET
```

No cabeçalho `Authorization` dessa solicitação, substitua **SECRET** pelo valor do seu segredo de Linha Direta.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Gerar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/tokens/conversation
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá um token válido para uma conversa. Esse token expirará em 30 minutos. Para continuar a usar o token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]

"RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn"
```

### <a name="generate-token-versus-start-conversation"></a>Gerar Token versus Iniciar Conversa

A operação Gerar Token (`POST /api/tokens/conversation`) é semelhante à operação [Iniciar Conversa](bot-framework-rest-direct-line-1-1-start-conversation.md) (`POST /api/conversations`), já que ambas retornam um `token`, que pode ser usado para acessar uma única conversa. No entanto, ao contrário da operação Iniciar Conversa, a operação Gerar Token não inicia a conversa ou entra em contato com o bot. 

Se você planeja distribuir o token para clientes e deseja que eles iniciem a conversa, use a operação Gerar Token. Se você pretende iniciar a conversa imediatamente, use a operação [Iniciar Conversa](bot-framework-rest-direct-line-1-1-start-conversation.md).

## <a id="refresh-token"></a> Atualizar um token de Linha Direta

Um token de Linha Direta é válido por 30 minutos a partir do momento em que é gerado e pode ser atualizado uma quantidade ilimitada de vezes, desde que não tenha expirado. Um token expirado não poderá ser atualizado. Para atualizar um token de Linha Direta, emita esta solicitação:

```http
POST https://directline.botframework.com/api/tokens/{conversationId}/renew
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

No URI dessa solicitação, substitua **{conversationId}** pela ID da conversa para a qual o token é válido e, no cabeçalho `Authorization` dessa solicitação, substitua **TOKEN_TO_BE_REFRESHED** pelo token da Linha Direta que você deseja atualizar.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Atualizar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/api/tokens/abc123/renew
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá um novo token válido para a mesma conversa como o token anterior. O novo token expirará em 30 minutos. Para continuar a usar o novo token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]

"RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0"
```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-1-1-concepts.md)
- [Conectar um bot à Linha Direta](../bot-service-channel-connect-directline.md)