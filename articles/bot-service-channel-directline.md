---
title: Sobre o canal Direct Line
titleSuffix: Bot Service
description: Recursos do canal Direct Line
services: bot-service
author: ivorb
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: kamrani
ms.openlocfilehash: 3a1bf9ef71ec3121cc845a2a93fd191eacc28f8f
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298190"
---
## <a name="about-direct-line"></a>Sobre o Direct Line

O canal Direct Line do Bot Framework é uma maneira fácil de integrar o bot ao seu aplicativo móvel, página da Web ou outro aplicativo.
O Direct Line está disponível em três formas:
- Serviço Direct Line: um serviço global e robusto para a maioria dos desenvolvedores
- Direct Line App Service Extensions: funcionalidade dedicada do Direct Line para segurança e desempenho (disponível em versão prévia privada a partir de maio de 2019)
- Direct Line Speech: otimizado para fala de alto desempenho (disponível em versão prévia privada a partir de maio de 2019)

Você pode escolher qual oferta do Direct Line é melhor avaliando que recursos cada uma oferece e as necessidades de sua solução. Essas ofertas serão simplificadas ao longo do tempo.

|                            | Linha Direta | Direct Line App Service Extension | Direct Line Speech |
|----------------------------|-------------|-----------------------------------|--------------------|
| Disponibilidade e licenciamento    | Disponibilidade Geral | Versão prévia privada, sem SLA  | Versão prévia privada, sem SLA |
| Desempenho de texto-fala e reconhecimento de fala | Standard | Standard | Alto desempenho |
| OAuth integrado           | Sim | sim | Não |
| Telemetria integrada       | Sim | sim | Não |
| Dá suporte a navegadores da web herdados | Sim | Não | Não |
| Suporte ao SDK do Bot Framework | Todos os v3, v4 | v4.5+ necessário | v4.5+ necessário |
| Suporte de SDK de cliente    | JS, C# | JS, C# | C++, C#, Unity |
| Funciona com o Bate-Papo da Web  | Sim | sim | Não|
| API de histórico de conversas | Sim | sim| Não|
| VNET | Não | Versão prévia* | Não |

_* O Direct Line App Service Extensions pode ser usado em VNETs mas ainda não permite a restrição de chamadas de saída._

## <a name="addtional-resources"></a>Recursos adicionais
- [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md)
- [Conectar um bot ao Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
