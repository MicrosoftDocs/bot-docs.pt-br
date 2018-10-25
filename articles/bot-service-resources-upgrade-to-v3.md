---
title: Fazer upgrade do bot para a API v3 do Bot Framework | Microsoft Docs
description: Saiba como fazer upgrade do bot para a API v3 do Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 6ee7120536d42257dde2ed1411df32d807268e33
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "50000009"
---
# <a name="upgrade-your-bot-to-bot-framework-api-v3"></a>Fazer upgrade do bot para a API v3 do Bot Framework

Na Build 2016 a Microsoft anunciou o Microsoft Bot Framework e a iteração inicial da API do Bot Connector, juntamente com os SDKs do Bot Builder e do Bot Connector. Desde então, estivemos coletando comentários e trabalhando ativamente para melhorar a API REST e os SDKs.

Em julho de 2016, a API v3 do Bot Framework foi lançada e a API v1 do Bot Framework foi preterida. Os bots que usam a API v1 pararam de funcionar no Skype em dezembro de 2016 e em todos os canais restantes no dia 23 de fevereiro de 2017. Se você criou um bot usando a API v1 e quer torná-lo funcional novamente, é necessário fazer o upgrade dele para a API v3, seguindo as instruções neste artigo. Para certificar-se de entender o processo de atualização de ponta a ponta, leia este artigo por completo antes de começar. 

## <a name="step-1-get-your-app-id-and-password-from-the-bot-framework-portal"></a>Etapa 1: Obter sua ID de aplicativo e a senha no portal do Bot Framework

Entre no [Portal do Bot Framework](https://dev.botframework.com/), clique em **Meus bots** e, em seguida, selecione o bot para abrir o painel. Em seguida, clique no link **SETTINGS** localizado próximo ao canto superior direito da página. 

Na seção **Configuração** da página de configurações, examine o conteúdo do campo **ID de Aplicativo** e prossiga com as outras etapas, dependendo se o campo **ID de Aplicativo** já está preenchido ou não.

### <a name="case-1-app-id-field-is-already-populated"></a>Caso 1: O campo ID de Aplicativo já está preenchido

Se o campo **ID de Aplicativo** já estiver preenchido, conclua estas etapas:

1. Clique em **Gerenciar ID de Aplicativo da Microsoft e senha**.  
![Configuração](~/media/upgrade/manage-app-id.png)

2. Clique em **Gerar Nova Senha**.  
![Gerar nova senha](~/media/upgrade/generate-new-password.png)

3. Copie e salve a nova senha juntamente com a ID de Aplicativo da MSA; você precisará desses valores futuramente.  
![Nova senha](~/media/upgrade/new-password-generated.png)

### <a name="case-2-app-id-field-is-empty"></a>Caso 2: O campo ID de Aplicativo está vazio

Se o campo **ID de Aplicativo** estiver vazio, conclua estas etapas:

1. Clique em **Criar ID de Aplicativo da Microsoft e senha**.  
   ![Criar ID de Aplicativo e senha](~/media/upgrade/generate-appid-and-password.png)
   > [!IMPORTANT]
   > Não selecione ainda o botão de opção **Versão 3.0**. Você fará isso mais tarde, após ter [atualizado o código de bot](#update-code).</div>

2. Clique em **Gerar uma senha para continuar**.  
   ![Gerar senha do aplicativo](~/media/upgrade/generate-a-password-to-continue.png)

3. Copie e salve a nova senha juntamente com a Id de Aplicativo da MSA; você precisará desses valores futuramente.  
   ![Nova senha](~/media/upgrade/new-password-generated.png)

4. Clique em **Concluir e voltar ao Bot Framework**.  
   ![Concluir e voltar ao portal](~/media/upgrade/finish-and-go-back-to-bot-framework.png)

5. De volta à página de configurações de bot no portal do Bot Framework, role até a parte inferior da página e clique em **Salvar alterações**.  
   ![Salvar alterações](~/media/upgrade/save-changes.png)

## <a id="update-code"></a> Etapa 2: Atualizar o código do bot para a versão 3.0

Para atualizar o código do bot para a versão 3.0, conclua estas etapas:

1. Atualize para a última versão do [SDK do Bot Builder](https://github.com/Microsoft/BotBuilder) para o idioma do bot.
2. Atualize o código para aplicar as alterações necessárias, de acordo com as orientações abaixo.
3. Use o [Bot Framework Emulator](~/bot-service-debug-emulator.md) para testar o bot localmente e depois na nuvem.

As seções a seguir descrevem as principais diferenças entre a API v1 e a API v3. Depois de atualizar o código para a API v3, é possível concluir o processo de upgrade [atualizando as configurações do bot](#step-3) no portal do Bot Framework.

### <a name="botbuilder-and-connector-are-now-one-sdk"></a>BotBuilder e Connector agora são um SDK

Em vez de precisar instalar SDKs separados para o Builder e o Connector usando vários pacotes NuGet (ou módulos NPM), agora é possível obter ambas as bibliotecas em um único SDK do Bot Builder:

- SDK do Bot Builder SDK para .NET: `Microsoft.Bot.Builder` pacote NuGet
- SDK do Bot Builder SDK para Node.js: `botbuilder` módulo NPM

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
| objeto `From` | objeto `From` |
| objeto `To` | objeto `Recipient` |
| Propriedade `ChannelConversationID` | objeto `Conversation`|
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
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção e pode ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para saber mais, consulte o tópico **Gerenciar dados de estado** para implementação de [.NET](~/dotnet/bot-builder-dotnet-state.md) ou [Node](~/nodejs/bot-builder-nodejs-state.md).

### <a name="webconfig-changes"></a>Alterações do Web.config

A API v1 do Bot Framework armazenava as propriedades de autenticação com estas chaves no **Web.config**:

- `AppID`
- `AppSecret`

A API v3 do Bot Framework armazena as propriedades de autenticação com estas chaves no **Web.config**:

- `MicrosoftAppID`
- `MicrosoftAppPassword`

## <a id="step-3"></a> Etapa 3: Atualizar as configurações do bot no portal do Bot Framework

Depois de fazer upgrade do código do bot para a API v3 e implantá-lo na nuvem, conclua o processo de upgrade seguindo estas etapas: 

1. Entre no [Portal do Bot Framework](https://dev.botframework.com/).

2. Clique em **Meus bots** e selecione o bot para abrir o painel dele. 

3. Clique no link **SETTINGS** localizado próximo ao canto superior direito da página. 

4. Na **versão 3.0** na seção **Configuração**, cole o ponto de extremidade do bot no campo **Ponto de extremidade de mensagens**.  
![Configuração da versão 3](~/media/upgrade/paste-new-v3-enpoint-url.png)

5. Selecione o botão de opção **Versão 3.0**.  
![Selecionar versão 3.0](~/media/upgrade/switch-to-v3-endpoint.png)

6. Role até a parte inferior da página e clique em **Salvar alterações**.  
![Salvar alterações](~/media/upgrade/save-changes.png)