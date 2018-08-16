---
title: Conectar um bot à Cortana | Microsoft Docs
description: Saiba como configurar um bot para acesso por meio da interface da Cortana.
keywords: cortana, canal de bot, configurar cortana
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/30/2018
ms.openlocfilehash: 6e694ce8b54ebd2405d7496d333c2bb27eb344f1
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296805"
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
| **Descrição** | Uma descrição da sua habilidade da Cortana. Isso será usado quando as habilidades forem detectáveis (como a Microsoft Store). |
| **Descrição breve** | Uma breve descrição da funcionalidade da sua habilidade, usada para descrever a habilidade no Caderno da Cortana. |

## <a name="general-bot-information"></a>Informações gerais sobre o bot

Em **Gerenciar a identidade do usuário por meio da seção de serviços conectados** pressione a opção para habilitá-lo. Preencha o formulário.

Todos os campos marcados com um asterisco (*) são obrigatórios. Os bots devem ser publicados no Bot Framework antes de poderem ser conectados à Cortana.

![Fornecer informações gerais](~/media/channels/cortana-details.png)

### <a name="sign-in-at-invocation"></a>Entrar na invocação

Selecione essa opção se você quiser que a Cortana conecte o usuário no momento em que ele invocar uma habilidade.

### <a name="sign-in-when-required"></a>Entrar quando necessário

Selecione essa opção se você usar o cartão de Entrada do Bot Framework para conectar o usuário. Normalmente, você usa essa opção se quiser conectar o usuário somente se ele usar um recurso que exige autenticação. Quando sua habilidade envia uma mensagem que inclui o cartão de Entrada como anexo, a Cortana ignora o cartão de Entrada e executa o fluxo de autorização usando as configurações da Conta de Conexão.

### <a name="connected-service-icon"></a>Ícone de serviço conectado

O ícone que você quer exibir quando o usuário entrar em sua habilidade.

### <a name="account-name"></a>Nome da conta

O nome da sua habilidade que você quer exibir quando o usuário entrar em sua habilidade.

### <a name="client-id-for-third-party-services"></a>ID de cliente para serviços de terceiros

ID do aplicativo do seu bot. Você recebeu a ID quando registrou seu bot.

### <a name="space-separated-list-of-scopes"></a>Lista de escopos separados por espaços

Especifique os escopos exigidos pelo serviço (confira a documentação do serviço).

### <a name="authorization-url"></a>URL de autorização

Defina como `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`.

### <a name="grant-type"></a>Tipo de concessão

Selecione o código de Autorização para usar o fluxo de concessão de código. Selecione Implícito para usar o fluxo implícito.

### <a name="token-url"></a>URL de token

Se você selecionar o código de Autorização, defina como `https://login.microsoftonline.com/common/oauth2/v2.0/token`.

### <a name="client-secretpassword-for-third-party-services"></a>Senha/segredo do cliente para serviços de terceiros

A senha do bot. Você recebeu a senha quando registrou seu bot.

### <a name="client-authentication-scheme"></a>Esquema de autenticação de cliente

Selecione HTTP Básico.

### <a name="token-options"></a>Opções de token

Defina como POST.

### <a name="request-user-profile-data-optional"></a>Solicitar dados de perfil do usuário (opcional)

A Cortana fornece acesso a vários tipos diferentes de informações de perfil do usuário, as quais você pode usar para personalizar o bot para o usuário. Por exemplo, se uma habilidade tiver acesso ao nome e ao local do usuário, a habilidade poderá ter uma resposta personalizada, por exemplo, "Olá Kamran, espero que tenha um dia agradável em Bellevue, Washington".

Clique no link **Adicionar uma solicitação de perfil do usuário** e selecione as informações de perfil de usuário que você deseja na lista suspensa. Adicione um nome amigável ao acesso dessas informações no código do seu bot.

### <a name="save-skill"></a>Salvar habilidade

Quando você terminar de preencher o formulário de registro da sua habilidade da Cortana, clique em Salvar para concluir a conexão. Isso levará você de volta à folha Canais do seu bot, e você verá que ele está conectado à Cortana.

Neste ponto, seu bot já foi automaticamente implantado como uma habilidade da Cortana à sua conta.

## <a name="next-steps"></a>Próximas etapas

* [Kit de habilidades da Cortana](https://aka.ms/CortanaSkillsDocs)
* [Habilitar depuração](bot-service-debug-cortana-skill.md)
* [Publicar uma habilidade da Cortana][publish]

[invocation]: https://docs.microsoft.com/en-us/cortana/skills/cortana-invocation-guidelines
[publish]: https://docs.microsoft.com/en-us/cortana/skills/publish-skill
[connected]: https://aka.ms/CortanaSkillsBotConnectedAccount
[CortanaEntity]: https://aka.ms/lgvcto
