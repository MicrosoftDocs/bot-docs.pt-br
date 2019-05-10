---
ms.openlocfilehash: 4e2ab87f509c29c33a27810668445975e31f6ac4
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563847"
---
## <a name="application-settings-and-messaging-endpoint"></a>Configurações de aplicativo e ponto de extremidade de mensagens

### <a name="verify-application-settings"></a>Verificar configurações de aplicativo

Para que o bot para funcione corretamente na nuvem, você deve garantir que as configurações de aplicativo estão corretas. Se você tiver um **appID** e **senha**, atualize os valores `Microsoft AppId` e `Microsoft App Password` nas definições de configuração do aplicativo como parte do processo de implantação. Para encontrar a **AppID** e a **AppPassword** do bot, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

> [!TIP]
> [!INCLUDE [Application configuration settings](~/includes/snippet-tip-bot-config-settings.md)]

Se você ainda não registrou o bot com o Bot Framework (e, portanto, ainda não tem uma **appID** e **senha**), é possível implantar o bot com os valores de espaço reservado temporário para essas configurações.
Em seguida, após [registrar o bot](~/bot-service-quickstart-registration.md), atualize as configurações do aplicativo implantado com a **appID** e a **senha** que foram geradas para o bot durante o registro.

### <a id="messagingEndpoint"></a> Verificar o ponto de extremidade de mensagens

O bot implantado deve ter um **Ponto de extremidade de mensagens** que possa receber mensagens do Serviço do Bot Framework Connector.

> [!NOTE]
> Ao implantar o bot no Azure, o SSL será configurado automaticamente para o aplicativo, habilitando assim o **Ponto de extremidade de mensagens** que o Bot Framework exige.
> Se você implantar em outro serviço de nuvem, certifique-se de verificar se o aplicativo está configurado para SSL para que o bot tenha um **Ponto de extremidade de mensagens**.
