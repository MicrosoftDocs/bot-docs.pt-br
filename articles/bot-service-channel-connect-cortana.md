---
title: Conectar um bot à Cortana | Microsoft Docs
description: Saiba como configurar um bot para acesso por meio da interface da Cortana.
keywords: cortana, canal de bot, configurar cortana
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 04/30/2018
ms.openlocfilehash: 9e3f2f19c480a9d2fe6df0baea74d449bb584b4f
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999075"
---
# <a name="connect-a-bot-to-cortana"></a>Conectar um bot à Cortana

Cortana é um canal habilitado para fala que pode enviar e receber mensagens de voz e texto. Para que um bot possa se conectar à Cortana, ele deve ser projetado para fala e também texto. Uma *habilidade* da Cortana é um bot que pode ser invocado usando um cliente da Cortana. A publicação de um bot o adiciona à lista de habilidades disponíveis.

Para adicionar o canal da Cortana, abra o bot no [Portal do Azure](https://portal.azure.com/), clique na folha **Canais** e clique em **Cortana**.

![Adicione o canal da Cortana](~/media/channels/cortana-addchannel.png)

## <a name="configure-cortana"></a>Configurar a Cortana

Ao conectar seu bot ao canal da Cortana, algumas informações básicas sobre seu bot serão preenchidas previamente no formulário de registro. Examine essas informações com atenção. Esse formulário contém os campos a seguir.

| Campo | DESCRIÇÃO |
|------|------|
| **Ícone de Habilidade** | Um ícone exibido na tela da Cortana quando sua habilidade for invocada. Isso também será usado quando as habilidades forem detectáveis (como a Microsoft Store). (Apenas PNG com no máximo 32KB).|
| **Nome de exibição** | O nome da sua habilidade da Cortana é exibido ao usuário na parte superior da interface do usuário visual. (Limite de 30 caracteres) |
| **Nome da invocação** | Esse é o nome falado pelos usuários ao invocar uma habilidade. Ele não deve ter mais do que três palavras e deve ser fácil de pronunciar. Confira as [Diretrizes de nome de invocação][invocation] para saber mais sobre como escolher esse nome.|

![Configurações padrão](~/media/channels/cortana-defaultsettings.png)

## <a name="general-bot-information"></a>Informações gerais sobre o bot

Em **Gerenciar a identidade do usuário por meio da seção de serviços conectados** pressione a opção para habilitá-lo. Preencha o formulário.

Todos os campos marcados com um asterisco (*) são obrigatórios. Um Bot deve ser publicado no Azure antes de poder ser conectado à Cortana.

![Gerenciar identidade de usuário, parte 1](~/media/channels/cortana-manageidentity-1.png)
![Gerenciar identidade de usuário, parte 2](~/media/channels/cortana-manageidentity-2.png)

### <a name="when-should-cortana-prompt-for-a-user-to-sign-in"></a>Quando a Cortana deve solicitar a entrada de um usuário

Selecione **Entrar na invocação** se você quiser que a Cortana conecte o usuário no momento em que ele invocar uma habilidade.

Selecione **Entrar quando for necessário** se você usar um cartão de entrada do Serviço de Bot para a entrada do usuário. Normalmente, você usa essa opção se quiser conectar o usuário somente se ele usar um recurso que exige autenticação. Quando sua habilidade envia uma mensagem que inclui o cartão de entrada como anexo, a Cortana ignora o cartão de entrada e executa o fluxo de autorização usando as configurações da Conta de Conexão.

### <a name="account-name"></a>Nome da conta

O nome da sua habilidade que você quer exibir quando o usuário entrar em sua habilidade.

### <a name="client-id-for-third-party-services"></a>ID de cliente para serviços de terceiros

ID do aplicativo do seu bot. Você recebeu a ID quando registrou seu bot.

### <a name="space-separated-list-of-scopes"></a>Lista de escopos separados por espaços

Especifique os escopos exigidos pelo serviço (confira a documentação do serviço).

### <a name="authorization-url"></a>URL de autorização

Defina como `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`.

### <a name="token-options"></a>Opções de token

Selecione **POST**.

### <a name="grant-type"></a>Tipo de concessão

Selecione **Código de autorização** para usar o fluxo de concessão de código, ou selecione **Implícito** para usar o fluxo implícito.

### <a name="token-url"></a>URL de token

Para o tipo de concessão **Código de autorização** defina como `https://login.microsoftonline.com/common/oauth2/v2.0/token`.

### <a name="client-secretpassword-for-third-party-services"></a>Senha/segredo do cliente para serviços de terceiros

A senha do bot. Você recebeu a senha quando registrou seu bot.

### <a name="client-authentication-scheme"></a>Esquema de autenticação de cliente

Selecione **HTTP Básico**.

### <a name="internet-access-required-to-authenticate-users"></a>O acesso à Internet é necessário para autenticar usuários

Deixe desmarcada.

### <a name="request-user-profile-data-optional"></a>Solicitar dados de perfil do usuário (opcional)

A Cortana fornece acesso a vários tipos diferentes de informações de perfil do usuário, as quais você pode usar para personalizar o bot para o usuário. Por exemplo, se uma habilidade tiver acesso ao nome e ao local do usuário, a habilidade poderá ter uma resposta personalizada, por exemplo, "Olá Kamran, espero que tenha um dia agradável em Bellevue, Washington".

Clique em **Adicionar uma solicitação de perfil do usuário** e selecione as informações de perfil de usuário que você deseja na lista suspensa. Adicione um nome amigável ao acesso dessas informações no código do seu bot.

### <a name="deploy-on-cortana"></a>Implantar na Cortana

Quando você terminar de preencher o formulário de registro da sua habilidade da Cortana, clique em **Implantar na Cortana** para concluir a conexão. Isso levará você de volta à folha Canais do seu bot, e você verá que ele está conectado à Cortana.

Neste ponto, seu bot já foi implantado como uma habilidade da Cortana à sua conta.

## <a name="next-steps"></a>Próximas etapas

* [Kit de habilidades da Cortana](https://aka.ms/CortanaSkillsDocs)
* [Habilitar depuração](bot-service-debug-cortana-skill.md)
* [Publicar uma habilidade da Cortana][publish]

[invocation]: https://docs.microsoft.com/en-us/cortana/skills/cortana-invocation-guidelines
[publish]: https://docs.microsoft.com/en-us/cortana/skills/publish-skill
[connected]: https://aka.ms/CortanaSkillsBotConnectedAccount
[CortanaEntity]: https://aka.ms/lgvcto
