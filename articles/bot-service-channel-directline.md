---
title: Sobre o canal Direct Line
titleSuffix: Bot Service
description: Saiba mais sobre os três canais da linha de bot Framework Direct. Selecione o canal a ser usado para integrar os bots em aplicativos móveis, páginas da Web e outros aplicativos.
services: bot-service
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: eed7965bdd467b5df21e4e74c4838699f481d4de
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97758824"
---
# <a name="about-direct-line"></a>Sobre o Direct Line
[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]
A estrutura de bot oferece vários canais com a marca de linha direta. É importante que você selecione a versão mais adequada ao A.I. de conversação experiência que você está projetando.


- **Linha direta**. Essa é a oferta de canal padrão de linha direta. Ele funciona por padrão com modelos de bot por meio do [portal do Azure](https://ms.portal.azure.com/), bots dos [exemplos de bot Builder](https://github.com/Microsoft/BotBuilder-Samples/blob/main/README.md)e bots criados com o [CLI do Azure](https://docs.microsoft.com/cli/azure/what-is-azure-cli). Essa é a linha direta mais adequada na maioria dos casos. Consulte [conectar um bot à linha direta](bot-service-channel-connect-directline.md).
- **Direct line Speech**. Ele fornece texto para fala e fala para serviços de texto no canal. Ele permite que um cliente transmita áudio diretamente para o canal que será convertido em texto e enviado para o bot. A Direct line Speech também é capaz de converter mensagens de texto do bot em mensagens de áudio, conforme falado por uma variedade de A.I. vozes alimentadas. Combinado, isso faz com que a fala de linha direta seja capaz de ter áudio apenas conversas com clientes. Consulte [conectar um bot para direcionar a linha de fala](bot-service-channel-connect-directlinespeech.md).
- **Extensão de serviço de aplicativo de linha direta**. Ele é executado dentro da mesma assinatura, serviço de aplicativo e rede do Azure que o bot. Se você tiver requisitos de isolamento de rede, essa versão da linha direta pode ser ideal. Bots e clientes exigem modificações especiais para trabalhar com a extensão de serviço de aplicativo de linha direta para garantir que o tráfego nunca saia da rede isolada. Consulte [extensão do serviço de aplicativo de linha direta](bot-service-channel-directline-extension.md).

Você pode escolher qual oferta do Direct Line é melhor avaliando que recursos cada uma oferece e as necessidades de sua solução.
Essas ofertas serão simplificadas ao longo do tempo.

| Recurso                    | Linha Direta | Direct Line App Service Extension | Direct Line Speech |
|----------------------------|-------------|-----------------------------------|--------------------|
| Disponibilidade e licenciamento    | GA | GA  | GA |
| Desempenho de texto-fala e reconhecimento de fala | Standard | Standard | Alto desempenho |
| Dá suporte a navegadores da web herdados | Sim | Sim | Sim |
| Suporte ao SDK do Bot Framework | Todos os v3, v4 | v 4.63+ obrigatório | v 4.63+ obrigatório |
| Suporte de SDK de cliente    | JS, C# | JS, C# | C++, C#, Unity, JS|
| Funciona com o Bate-Papo da Web  | Sim | Sim | Sim |
| VNET | Não | Sim | Não |

## <a name="additional-resources"></a>Recursos adicionais

- [Conectar um bot à Linha Direta](bot-service-channel-connect-directline.md)
- [Conectar um bot ao Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
- [Extensão do Serviço de Aplicativo do Direct Line](bot-service-channel-directline-extension.md)
- [Usar o Web Chat com a Direct line Speech](https://github.com/microsoft/BotFramework-WebChat/blob/master/docs/DIRECT_LINE_SPEECH.md#using-direct-line-speech)
