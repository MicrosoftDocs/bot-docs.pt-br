---
title: Autenticação | Microsoft Docs
description: Saiba como autenticar solicitações de API na API de Linha Direta versão 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 96f2963604d12c9c9e235288ad4df25924f45af4
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998003"
---
# <a name="authentication"></a>Autenticação

Um cliente pode autenticar solicitações para a API de Linha Direta 3.0 usando um **segredo** que você [obtém da página de configuração de canal de Linha Direta](../bot-service-channel-connect-directline.md) no Portal Bot Framework ou usando um  **token** obtido em tempo de execução. O segredo ou o token deve ser especificado no cabeçalho `Authorization` de cada solicitação, usando este formato: 

```http
Authorization: Bearer SECRET_OR_TOKEN
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
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer SECRET
```

No cabeçalho `Authorization` dessa solicitação, substitua **SECRET** pelo valor do seu segredo de Linha Direta.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Gerar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá um `token`, válido para uma conversa, e um valor `expires_in`, que indica o número de segundos até o token expirar. Para continuar a usar o token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

### <a name="generate-token-versus-start-conversation"></a>Gerar Token versus Iniciar Conversa

A operação Gerar Token (`POST /v3/directline/tokens/generate`) é semelhante à operação [Iniciar Conversa](bot-framework-rest-direct-line-3-0-start-conversation.md) (`POST /v3/directline/conversations`), já que ambas retornam um `token`, que pode ser usado para acessar uma única conversa. No entanto, ao contrário da operação Iniciar Conversa, a operação Gerar Token não inicia a conversa, não entra em contato com o bot e não cria uma URL WebSocket de streaming. 

Se você planeja distribuir o token para os clientes e deseja que eles iniciem a conversa, use a operação Gerar Token. Se você pretende iniciar a conversa imediatamente, use a operação [Iniciar Conversa](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a id="refresh-token"></a> Atualizar um token de Linha Direta

Um token de Linha Direta pode ser atualizado uma quantidade ilimitada de vezes, desde que não tenha expirado. Um token expirado não pode ser atualizado. Para atualizar um token de Linha Direta, emita esta solicitação: 

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

No cabeçalho `Authorization` dessa solicitação, substitua **TOKEN_TO_BE_REFRESHED** pelo token de Linha Direta que você deseja atualizar.

Os snippets a seguir fornecem um exemplo da solicitação e da resposta de Atualizar Token.

### <a name="request"></a>Solicitação

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>Response

Se a solicitação for bem-sucedida, a resposta conterá um novo `token`, válido para a mesma conversa como o token anterior, e um valor `expires_in` que indica o número de segundos até que o novo token expire. Para continuar a usar o novo token, você deve [atualizar o token](#refresh-token) antes que ele expire.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0",
  "expires_in": 1800
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Principais conceitos](bot-framework-rest-direct-line-3-0-concepts.md)
- [Conectar um bot à Linha Direta](../bot-service-channel-connect-directline.md)