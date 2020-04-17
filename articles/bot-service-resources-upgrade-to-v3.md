---
title: Fazer upgrade do bot para a API v3 do Bot Framework – Serviço de Bot
description: Saiba como fazer upgrade do bot para a API v3 do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 6f08da5acbf8ac050d5dbf6a1a0290c3e6288013
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75790988"
---
# <a name="upgrade-your-bot-to-bot-framework-api-v3"></a>Fazer upgrade do bot para a API v3 do Bot Framework

Na Build 2016 a Microsoft anunciou o Microsoft Bot Framework e a iteração inicial da API do Bot Connector, juntamente com os SDKs do Bot Builder e do Bot Connector. Desde então, estivemos coletando comentários e trabalhando ativamente para melhorar a API REST e os SDKs.

Em julho de 2016, a API v3 do Bot Framework foi lançada e a API v1 do Bot Framework foi preterida. Os bots que usam a API v1 pararam de funcionar no Skype em dezembro de 2016 e em todos os canais restantes no dia 23 de fevereiro de 2017. Se você criou um bot usando a API v1 e quer torná-lo funcional novamente, é necessário fazer o upgrade dele para a API v3, seguindo as instruções neste artigo. Para certificar-se de entender o processo de atualização de ponta a ponta, leia este artigo por completo antes de começar. 

## <a name="step-1-get-your-app-id-and-password-from-the-bot-framework-portal"></a>Etapa 1: obter sua ID de aplicativo e a senha no portal do Bot Framework

Entre no [Portal do Bot Framework](https://dev.botframework.com/), clique em **Meus bots** e, em seguida, selecione o bot para abrir o painel. Em seguida, clique no link **CONFIGURAÇÕES**, localizado na lateral esquerda da página em **Gerenciamento de Bot**. 

Na seção **Configuração** da página de configurações, examine o conteúdo do campo **ID de Aplicativo da Microsoft** e prossiga com as outras etapas.

<!-- TODO: Remove this 
### Case 1: App ID field is already populated

If the **App ID** field is already populated, complete these steps:
-->

1. Clique em **Gerenciar ID de Aplicativo da Microsoft e senha**.  
![Configuration](./media/upgrade/manage-app-id.png)

2. Clique em **Gerar Nova Senha**.  
![Gerar nova senha](./media/upgrade/generate-new-password.png)

3. Copie e salve a nova senha juntamente com a ID de Aplicativo da MSA; você precisará desses valores futuramente.  
![Nova senha](./media/upgrade/new-password-generated.png)

Há outro método para recuperar a **ID e Senha do Aplicativo da Microsoft**. Para isso, siga estas [instruções](https://blog.botframework.com/2018/07/03/find-your-azure-bots-appid-and-appsecret/).

<!-- TODO: These steps are no longer valid. AppID will always be generated, confirmed with Support Engineers
### Case 2: App ID field is empty

If the **App ID** field is empty, complete these steps:

1. Click **Create Microsoft App ID and password**.  
   ![Create App ID and password](~/media/upgrade/generate-appid-and-password.png)
   > [!IMPORTANT]
   > Do not select the **Version 3.0** radio button yet. You will do this later, after you have [updated your bot code](#update-code).</div>

2. Click **Generate a password to continue**.  
   ![Generate app password](~/media/upgrade/generate-a-password-to-continue.png)

3. Copy and save the new password along with the MSA App Id; you will need these values in the future.  
   ![New password](~/media/upgrade/new-password-generated.png)

4. Click **Finish and go back to Bot Framework**.  
   ![Finish and go back to Portal](~/media/upgrade/finish-and-go-back-to-bot-framework.png)

5. Back on the bot settings page in the Bot Framework Portal, scroll to the bottom of the page and click **Save changes**.  
   ![Save changes](~/media/upgrade/save-changes.png)
-->

## <a name="step-2-update-your-bot-code-to-version-40"></a><a id="update-code"></a> Etapa 2: atualizar o código do bot para a versão 4.0

Os bots V1 já não são compatíveis. Para atualizar seu bot, você precisará criar um novo bot em V3. Se quiser preservar códigos antigos, você terá que migrar seu código manualmente.

A solução mais fácil é recriar seu bot com o novo [SDK](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) e implantá-lo. 

Se você quiser preservar seu código antigo, siga as etapas abaixo:

1. Crie um novo aplicativo de Bot.
2. Copie seu código antigo para o novo aplicativo de Bot.
3. Atualize o SDK para a versão mais recente por meio do gerenciador de pacotes Nuget.
4. Corrija os erros que surgirem, confira o novo [SDK](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0).
5. Implantar seu bot no Azure seguindo estas [instruções](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-deploy-azure?view=azure-bot-service-4.0)

<!-- TODO: Remove outdated code 
To update your bot code to version 3.0, complete these steps:

1. Update to the latest version of the [Bot Framework SDK](https://github.com/Microsoft/BotBuilder) for your bot's language.
2. Update your code to apply the necessary changes, according the guidance below.
3. Use the [Bot Framework Emulator](~/bot-service-debug-emulator.md) to test your bot locally and then in the cloud.

The following sections describe the key differences between API v1 and API v3. After you have updated your code to API v3, you can finish the upgrade process by [updating your bot settings](#step-3) in the Bot Framework Portal.
-->

### <a name="botbuilder-and-connector-are-now-one-sdk"></a>BotBuilder e Connector agora são um SDK

Em vez de precisar instalar SDKs separados para o Builder e o Connector usando vários pacotes NuGet (ou módulos NPM), agora é possível obter ambas as bibliotecas em um único SDK do Bot Framework:

- SDK do Bot Framework para .NET: Pacote NuGet `Microsoft.Bot.Builder`
- SDK do Bot Framework para Node.js: Módulo NPM `botbuilder`

O SDK `Microsoft.Bot.Connector` autônomo agora é obsoleto e não está mais sendo mantido.

### <a name="message-is-now-activity"></a>Mensagem é agora Atividade

O objeto `Message` foi substituído pelo objeto `Activity` na API v3. O tipo de atividade mais comum é **mensagem**, mas há outros tipos de atividade que podem ser usados para comunicar diversos tipos de informações a um bot ou canal. Para obter mais informações sobre mensagens, consulte [Criar mensagens](~/dotnet/bot-builder-dotnet-create-messages.md) e [Enviar e receber atividades](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="activity-types--events"></a>Tipos de atividades e eventos

Alguns eventos foram renomeados e/ou refatorados na API v3. Além disso, uma nova enumeração `ActivityTypes` foi adicionada ao Connector para eliminar a necessidade de lembrar tipos específicos de atividades.

- O tipo de Atividade `conversationUpdate` substitui Bot/Usuário Adicionou/Removeu Para/Da Conversa por um único método.
- O novo tipo de Atividade `typing` permite ao bot indicar que ele está compilando uma resposta e saber quando o usuário está digitando uma resposta.
- O novo tipo de Atividade `contactRelationUpdate` permite ao bot saber se ele foi adicionado ao ou removido da lista de contatos do usuário.

Quando o bot recebe uma atividade `conversationUpdate`, a propriedade `MembersRemoved` e a propriedade `MembersAdded` indicarão quem foi adicionado à ou removido da conversa. Quando o bot recebe uma atividade `contactRelationUpdate`, a propriedade `Action` indicará se o usuário adicionou ou removeu o bot da lista de contatos dele. Para obter mais informações sobre tipos de atividades, consulte [Visão geral das atividades](~/dotnet/bot-builder-dotnet-activities.md).

### <a name="addressing-messages"></a>Endereçamento de mensagens

Onde as informações de remetente, destinatário e canal que são especificadas dentro de uma mensagem foram suavemente alteradas na API v3:

|Campo da API v1 | Campo da API v3|
|--------|--------|
| Objeto `From` | Objeto `From` |
| Objeto `To` | Objeto `Recipient` |
| Propriedade `ChannelConversationID` | Objeto `Conversation`|
| Propriedade `ChannelId` | Propriedade `ChannelId` |

Para obter mais informações sobre o endereçamento de mensagens, consulte [Enviar e receber atividades](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="sending-replies"></a>Enviar respostas

Na API v3 do Bot Framework, todas as respostas ao usuário serão enviadas assincronamente em uma solicitação HTTP iniciada separadamente em vez de embutidas com o HTTP POST para a mensagem de entrada ao bot. Como nenhuma mensagem será retornada embutida ao usuário por meio do Conector, o tipo de retorno do método de postagem do bot será `HttpResponseMessage`. Isso significa que o bot não "retorna"sincronicamente a cadeia de caracteres que você quer enviar ao usuário, mas em vez disso, envia uma mensagem de resposta em qualquer ponto no código em vez de ter que responder novamente como uma resposta para o POST de entrada. Estes dois métodos enviarão uma mensagem para uma conversa:

- `SendToConversation`
- `ReplyToConversation`

O método `SendToConversation` acrescentará a mensagem especificada ao final da conversa, enquanto o método `ReplyToConversation` (para conversas que dão suporte a ele) adicionará a mensagem especificada como uma resposta direta a uma mensagem anterior na conversa. Para obter mais informações sobre esses métodos, consulte [Enviar e receber atividades](~/dotnet/bot-builder-dotnet-connector.md).

### <a name="starting-conversations"></a>Iniciar conversas

Na v3 da API do Bot Framework, você pode iniciar uma conversa usando o novo método `CreateDirectConversation` para iniciar uma conversa privada com um único usuário ou usando o novo método `CreateConversation` para iniciar uma conversa em grupo com vários usuários. Para obter mais informações sobre como iniciar conversas, consulte [Enviar e receber atividades](~/dotnet/bot-builder-dotnet-connector.md#start-a-conversation).

### <a name="attachments-and-options"></a>Anexos e opções

A API v3 do Bot Framework apresenta uma implementação mais robusta de anexos e cartões. O tipo `Options` não é mais suportado na API v3 e, em vez disso, foi substituído por cartões. Para obter mais informações sobre como adicionar anexos de mensagens usando o .NET, consulte [Adicionar anexos de mídia às mensagens](~/dotnet/bot-builder-dotnet-add-media-attachments.md) e [Adicionar anexos de cartão avançado às mensagens](~/dotnet/bot-builder-dotnet-add-rich-card-attachments.md).

### <a name="bot-data-storage-bot-state"></a>Armazenamento de dados do bot (estado do bot)

Na API v1 do Bot Framework, a API para gerenciar os dados de estado do bot foi integrada à API de mensagens. Na API v3 do Bot Framework, essas APIs são separadas. Agora, você deve usar o serviço de Estado do Bot para obter dados de estado (em vez de assumir que serão incluídos dentro do objeto `Message`) e para armazenar dados de estado (em vez de passá-los como parte do objeto `Message`). Para obter informações sobre como gerenciar dados de estado do bot usando o serviço de Estado do Bot, consulte [Gerenciar dados de estado](~/dotnet/bot-builder-dotnet-state.md).

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e poderá ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para obter mais informações, confira o tópico **Gerenciar dados de estado** para implementação do [.NET](~/dotnet/bot-builder-dotnet-state.md) ou do [Node](~/nodejs/bot-builder-nodejs-state.md).

### <a name="webconfig-changes"></a>Alterações do Web.config

A API v1 do Bot Framework armazenava as propriedades de autenticação com estas chaves no **Web.config**:

- `AppID`
- `AppSecret`

A API v3 do Bot Framework armazena as propriedades de autenticação com estas chaves no **Web.config**:

- `MicrosoftAppID`
- `MicrosoftAppPassword`

## <a name="step-3-deploy-your-update-bot-to-azure"></a><a id="step-3"></a> Etapa 3: implantar seu Bot de Atualização no Azure.

Depois de atualizar seu código do bot na API v3, basta implantar o bot no Azure seguindo estas [instruções](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-deploy-azure?view=azure-bot-service-4.0). Como já não há suporte para V1, todos os bots automaticamente usarão a API V3 quando forem implantados nos serviços do Azure.

<!-- TODO: Documentation set for removal 
1. Sign in to the [Bot Framework Portal](https://dev.botframework.com/).

2. Click **My bots** and select your bot to open its dashboard. 

3. Click the **SETTINGS** link that is located near the top-right corner of the page. 

4. Under **Version 3.0** within the **Configuration** section, paste your bot's endpoint into the **Messaging endpoint** field.  
![Version 3 configuration](~/media/upgrade/paste-new-v3-enpoint-url.png)

5. Select the **Version 3.0** radio button.  
![Select version 3.0](~/media/upgrade/switch-to-v3-endpoint.png)

6. Scroll to the bottom of the page and click **Save changes**.  
![Save changes](~/media/upgrade/save-changes.png)
-->