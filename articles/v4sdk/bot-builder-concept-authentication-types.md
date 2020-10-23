---
title: Tipos de autenticação do bot Framework no serviço de bot do Azure-serviço bot
description: Saiba mais sobre os tipos de autenticação de bot no serviço de bot do Azure.
keywords: serviço de bot do azure, autenticação, serviço de token do bot framework
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/14/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 158882314663d954095d84c5c6175159a2b76f77
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92414395"
---
# <a name="authentication-types"></a>Tipos de autenticação

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

No bot Framework, existem duas categorias de autenticação amplas: **autenticação de bot** e autenticação de **usuário**. Cada um tem um **token** associado para permitir o acesso a recursos protegidos. A figura a seguir mostra os elementos envolvidos no bot e na autenticação do usuário.

![contexto de autenticação do bot Framework](media/concept-bot-authentication/bot-framework-auth-context.png)

Nesta figura:

- A **plataforma de host** é a plataforma de Hospedagem de bot. Ele pode ser o Azure ou qualquer plataforma de host que você escolher.
- O **serviço de conector de bot** facilita a comunicação entre um bot e um canal. Ele converte as mensagens recebidas de canais em objetos de atividade e as envia para o ponto de extremidade de mensagens do bot. Da mesma forma, ele converte os objetos de atividade recebidos do bot em mensagens compreendidas pelo canal e os envia para o canal.
- **Adaptador de bot**. Esse é o adaptador padrão do bot Framework. Ele executa estas tarefas:
  - Converte a carga JSON em um objeto. Neste ponto, ele já é um objeto de atividade, graças ao serviço de conector de bot.
  - Cria um contexto de ativação e adiciona o objeto de atividade a ele.
  - Executa o middleware, se houver.
  - Encaminha o contexto de ativação para o bot.

> [!NOTE]
> Quando um adaptador de canal personalizado é usado, o próprio adaptador executa as tarefas que o serviço de conector de bot e o adaptador de bot padrão fazem. Além disso, ele fornece o mecanismo de autenticação para a API de gancho da Web relacionada. Para obter um exemplo, consulte [conectar um bot à margem de atraso usando o adaptador de margem de atraso](~/bot-service-channel-connect-slack.md?tabs=adapter#connect-a-bot-to-slack-using-the-slack-adapter).

## <a name="bot-authentication"></a>Autenticação do bot

Um bot é identificado por seu **MicrosoftAppID** e **MicrosoftAppPassword**, que são mantidos dentro dos arquivos de configuração do bot ( `appsettings.json` (.net), `.env` (JavaScript), `config.py` (Python)) ou em **Azure Key Vault**.
Para obter mais informações, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

Quando você registra um bot no portal do Azure, por exemplo, por meio do **registro de canais de bot**, o Azure cria um aplicativo de registro de Active Directory (AD). Se você usar a CLI do bot Framework, deverá executar especificamente uma etapa para criar o registro do AD. Esse registro tem uma ID do aplicativo ( `MicrosoftAppID` ) e o segredo do cliente ( `MicrosoftAppPassword` ). O Azure usa esses valores para gerar um **token** com o qual o bot pode acessar recursos seguros.

Quando um canal envia uma solicitação para um bot, por meio do serviço de conector de bot, ele especifica um **token** no **cabeçalho de autorização** da solicitação. O bot autentica chamadas do serviço de conector de bot verificando a autenticidade do token.


Quando o bot envia uma solicitação para um canal por meio do **serviço de conector de bot**, ele deve especificar o **token** no **cabeçalho Authorization** da solicitação.
Todas as solicitações devem incluir o token de acesso que é verificado pelo serviço do conector de bot para autorizar a solicitação.

As operações descritas são executadas automaticamente pelo SDK do bot Framework.

Para obter mais detalhes, consulte a documentação da API REST sobre como [autenticar solicitações do serviço de conector de bot para o bot](~/rest-api/bot-framework-rest-connector-authentication.md#connector-to-bot) e [autenticar solicitações de seu bot para o serviço de conector de bot](~/rest-api/bot-framework-rest-connector-authentication.md#bot-to-connector).
### <a name="channels"></a>Canais

Normalmente, os canais se comunicam com um bot por meio do **serviço de conector de bot** , isso significa que os princípios de autenticação anteriores geralmente se aplicam. Talvez você queira observar as características de canais específicos.

#### <a name="direct-line"></a>Linha Direta

Além dos canais padrão com suporte, um aplicativo cliente pode se comunicar com um bot usando o canal de linha direta.

O aplicativo cliente autentica as solicitações para a linha direta (versão 3,0) usando um **segredo** obtido na página de [configuração do canal de linha direta](~/bot-service-channel-connect-directline.md) no portal do Azure ou, melhor, usando um **token** obtido no tempo de execução. O segredo ou o token são especificados no cabeçalho de autorização de cada solicitação.

> [!IMPORTANT]
> Quando você usa a autenticação do Serviço de Bot do Azure com o Webchat, há algumas considerações importantes sobre segurança que você precisa ter em mente. Para obter mais informações, confira a seção [considerações sobre segurança](~/bot-service-channel-connect-webchat.md#keep-your-secret-hidden-exchange-your-secret-for-a-token-and-generate-the-embed) no artigo de autenticação REST.


Para obter mais informações, consulte [manter seu segredo oculto, trocar seu segredo por um token e gerar o embed](~/bot-service-channel-connect-webchat.md#keep-your-secret-hidden-exchange-your-secret-for-a-token-and-generate-the-embed).


#### <a name="web-chat"></a>Chat na Web

O chat da Web tem duas implementações: o **canal** e o **controle**.

- Quando você registra um bot com o Azure, o canal de bate-papo da Web é configurado automaticamente para permitir o teste do bot.

    ![teste de chat Web de bot](media/concept-bot-authentication/bot-webchat-testing.PNG).

    Para obter mais informações, consulte [conectar um bot ao bate-papo na Web](~/bot-service-channel-connect-webchat.md).

- Você pode usar um controle de chat da Web com o canal de linha direto para fornecer acesso a um bot em um aplicativo cliente. Para obter mais informações sobre o controle, consulte [bate-papo Web do bot Framework](https://github.com/microsoft/BotFramework-WebChat).

### <a name="skills"></a>Habilidades

Uma habilidade e um consumidor de habilidades são dois bots distintos, cada um com sua própria ID de aplicativo e senha.

- O consumidor pode encaminhar as atividades do usuário para uma habilidade e encaminhar as respostas da habilidade para o usuário.
- Para a habilidade, o consumidor de habilidades atua como um canal. O consumidor tem um ponto de extremidade de host de habilidades que atua como a URL de serviço para a qual a habilidade envia atividades.
- Para obter mais informações sobre habilidades, consulte [visão geral das habilidades](skills-conceptual.md).

A autenticação de nível de serviço é gerenciada pelo serviço do Bot Connector. A estrutura usa tokens de portador e IDs de aplicativo de bot para verificar a identidade de cada bot.

> [!IMPORTANT]
> Isso exige que todos os bots (o consumidor de habilidades e quaisquer habilidades consumidas) tenham credenciais de aplicativo válidas.

#### <a name="claims-validation"></a>Validação de declarações

Além desse nível básico de autenticação, você deve adicionar um _validador de declarações_ à configuração de autenticação da habilidade e do consumidor de habilidades. As declarações são avaliadas após o cabeçalho de autenticação. Isso permite que cada bot restrinja a quais outros bots eles aceitarão atividades.

Para a validação de declarações de exemplo, consulte como [implementar uma habilidade](skill-implement-skill.md) e [implementar um consumidor de habilidades](skill-implement-consumer.md).

### <a name="bot-framework-emulator"></a>Bot Framework Emulator

O emulador do bot Framework tem seu próprio fluxo de autenticação e seus próprios tokens. O emulador tem seu próprio canal e um servidor interno.

## <a name="user-authentication"></a>Autenticação de usuário

Às vezes, um bot deve acessar recursos online protegidos em nome do usuário. Para fazer isso, o bot deve ser autorizado. Isso ocorre porque, para executar determinadas operações, como verificar o email, verificar o status do vôo ou fazer um pedido, o bot precisa chamar um serviço externo, como Microsoft Graph, GitHub ou o serviço REST de uma empresa. O OAuth é usado para autenticar o usuário e autorizar o bot.

> [!NOTE]
> Duas etapas de macro são envolvidas para um bot acessar os recursos de um usuário.
>
> 1. **Autenticação**. O processo de verificar a identidade do usuário.
> 1. **Autorização**. O processo de verificar se o bot pode acessar os recursos do usuário.
>
> Se a primeira etapa for bem-sucedida, um token baseado nas credenciais do usuário será emitido. Na segunda etapa, o bot usa o token para acessar os recursos do usuário.

Para obter mais informações, consulte [autenticação do usuário](bot-builder-concept-authentication.md).

### <a name="identity-providers"></a>Provedores de identidade

Um provedor de identidade autentica as identidades do usuário ou do cliente e emite tokens de segurança consumíveis. Ele fornece autenticação de usuário como um serviço. Os aplicativos cliente, como aplicativos Web, delegam a autenticação a um provedor de identidade confiável.

Um bot pode usar um provedor de identidade confiável para:

- Habilite recursos de SSO (logon único), permitindo que ele acesse vários recursos protegidos.
- Conecte-se aos recursos de computação em nuvem em nome de um usuário, diminuindo a necessidade dos usuários de autenticar novamente.

> [!NOTE]
> O token emitido durante a **autenticação de bot** não é o mesmo token emitido durante a autenticação do **usuário**. A primeira é usada para estabelecer uma comunicação segura entre um bot, canais e, por fim, aplicativos cliente. O segundo é usado para autorizar o bot a acessar recursos protegidos em nome do usuário.

Observe que os canais fornecem sua própria autenticação de usuário separada para permitir que um usuário entre no canal.

Consulte [provedores de identidade](bot-builder-concept-identity-providers.md) para saber mais sobre como os bots podem usar provedores de identidade para acessar recursos em nome de um usuário.
