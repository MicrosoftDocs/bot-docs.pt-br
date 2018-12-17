---
title: Conectar um bot ao Skype | Microsoft Docs
description: Saiba como configurar um bot para acesso por meio da interface do Skype.
keywords: skype, canais de bot, configurar skype, publicar, conectar-se aos canais
author: v-ducvo
ms.author: RobStand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 10/11/2018
ms.openlocfilehash: f52c8fd668299486eccda3920181df971f475a90
ms.sourcegitcommit: 75f32b3325dd0fc4d8128dee6c22ebf91e5785b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2018
ms.locfileid: "53120653"
---
# <a name="connect-a-bot-to-skype"></a>Conectar um bot ao Skype

O Skype mantém você conectado com os usuários por meio de mensagens instantâneas, telefone e videochamadas. Amplie essa funcionalidade criando bots que os usuários podem descobrir e interagir por meio da interface do Skype.

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
