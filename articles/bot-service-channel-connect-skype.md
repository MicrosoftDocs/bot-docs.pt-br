---
title: Conectar um bot ao Skype – Serviço de Bot
description: Saiba como configurar um bot para acesso por meio da interface do Skype.
keywords: skype, canais de bot, configurar skype, publicar, conectar-se aos canais
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/11/2018
ms.openlocfilehash: 5ec52eabc0ed1a84101a97f793510c6374f3c0f3
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80647594"
---
# <a name="connect-a-bot-to-skype"></a>Conectar um bot ao Skype

O Skype mantém você conectado com os usuários por meio de mensagens instantâneas, telefone e videochamadas. Amplie essa funcionalidade criando bots que os usuários podem descobrir e interagir por meio da interface do Skype.

>[!NOTE]
> Desde 31 de outubro de 2019, o canal do Skype não aceita mais novas solicitações de publicação de bot. Isso significa que você pode continuar a desenvolver bots usando o canal do Skype, mas o bot será limitado a 100 usuários. Você não poderá publicar o bot para um público-alvo maior. Os bots atuais do Skype continuarão a ser executados sem interrupção. Leia mais sobre [por que alguns recursos não estão mais disponíveis no Skype](https://support.skype.com/faq/fa12091/why-are-some-features-not-available-in-skype-anymore).

Para adicionar o canal do Skype, abra o bot no [Portal do Azure](https://portal.azure.com/), clique na folha **Canais** e clique em **Skype**.

![Adicionar o canal do Skype](~/media/channels/skype-addchannel.png)

Isso levará você para até a página de configurações **Configurar o Skype**.

![Configurar o canal do Skype](~/media/channels/skype_configure.png)

Você precisa definir as configurações no **Controle da Web**, **Mensagem**, **Chamando**, **Grupos** e **Publicar**. Vamos falar sobre cada um, individualmente.

## <a name="web-control"></a>Controle da Web

Para inserir o bot em seu site, clique no botão **Obter código de inserção** na seção **Controle da Web**. Isso direcionará você para a página do Skype para Desenvolvedores. Siga as instruções para obter o código de inserção.

## <a name="messaging"></a>Mensagens

Esta seção configura como o seu bot envia e recebe mensagens no Skype.

## <a name="calling"></a>Chamando

Esta seção configura o recurso de chamada do Skype em seu bot. Especifique se **Chamando** está habilitado para o seu bot e, se estiver habilitado, se a funcionalidade de IVR ou de Real Time Media deve ser usada.

## <a name="groups"></a>Grupos

Esta seção configura se o seu bot pode ser adicionado a um grupo, e como ele se comporta em um grupo de mensagens, além de também ser usada para habilitar as Videochamadas de Grupo para bots Chamando.

## <a name="publish"></a>Publicar

Esta seção define as configurações de publicação do seu bot. Todos os campos com um * são campos obrigatórios.

Os Bots na **Versão prévia** estão limitados a 100 contatos. Se você precisar de mais de 100 contatos, envie seu bot para revisão. **Enviar para Revisão** torna seu bot automaticamente pesquisável no Skype, se for aceito. Se a sua solicitação não puder ser aprovada, você receberá uma notificação sobre o que você precisa alterar para que ela seja aprovada.

> [!TIP]
> Se você deseja enviar seu bot para revisão, tenha em mente que ele deve atender à [lista de verificação de certificação Skype](https://github.com/Microsoft/skype-dev-bots/blob/master/certification/CHECKLIST.md) antes de ser aceito.

Após terminar a configuração, clique em **Salvar** e aceite os **Termos de serviço**. O canal do Skype foi adicionado ao seu bot.

## <a name="next-steps"></a>Próximas etapas

* [Skype for Business](bot-service-channel-connect-skypeforbusiness.md)
