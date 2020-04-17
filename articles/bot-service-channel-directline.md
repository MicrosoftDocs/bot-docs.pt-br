---
title: Sobre o canal Direct Line
titleSuffix: Bot Service
description: Recursos do canal Direct Line
services: bot-service
author: ivorb
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: kamrani
ms.openlocfilehash: b5a6296e65ab05cd8a5af24872d31e5ef356dcf7
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77441503"
---
# <a name="about-direct-line"></a>Sobre o Direct Line

O canal Direct Line do Bot Framework é uma maneira fácil de integrar o bot ao seu aplicativo móvel, página da Web ou outro aplicativo.
O Direct Line está disponível em três formas:
- Serviço Direct Line: um serviço global e robusto para a maioria dos desenvolvedores
- Extensões de Serviço de Aplicativo do Direct Line – funcionalidade do Direct Line dedicada para segurança e desempenho (disponível na versão prévia pública)
- Direct Line Speech – otimizado para fala de alto desempenho (GA)

Você pode escolher qual oferta do Direct Line é melhor avaliando que recursos cada uma oferece e as necessidades de sua solução. Essas ofertas serão simplificadas ao longo do tempo.

|                            | Linha Direta | Direct Line App Service Extension | Direct Line Speech |
|----------------------------|-------------|-----------------------------------|--------------------|
| Disponibilidade e licenciamento    | Disponibilidade Geral | Versão prévia pública, sem SLA  | GA |
| Desempenho de texto-fala e reconhecimento de fala | Standard | Standard | Alto desempenho |
| Dá suporte a navegadores da web herdados | Sim | Na GA | Sim |
| Suporte ao SDK do Bot Framework | Todos os v3, v4 | v 4.63+ obrigatório | v 4.63+ obrigatório |
| Suporte de SDK de cliente    | JS, C# | JS, C# | C++, C#, Unity, JS|
| Funciona com o Bate-Papo da Web  | Sim | Sim | Não|
| VNET | Não | Visualização | Não |


## <a name="additional-resources"></a>Recursos adicionais
- [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md)
- [Conectar um bot ao Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
