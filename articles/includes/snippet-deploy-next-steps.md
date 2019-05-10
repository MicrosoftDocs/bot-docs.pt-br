---
ms.openlocfilehash: 8e5677fe59dd9edad6ac1da9d029e5f7c08bf179
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563964"
---
## <a name="next-steps"></a>Próximas etapas
Depois que você tiver implantado seu bot na nuvem e verificado que a implantação foi bem-sucedida testando o bot usando o emulador do Bot Framework, a próxima etapa no processo de publicação de bot dependerá de você já ter registrado seu bot no Bot Framework.

### <a name="if-you-have-already-registered-your-bot-with-the-bot-framework"></a>Se você já tiver registrado seu bot no Bot Framework:

1. Volte para o <a href="https://dev.botframework.com" target="_blank">Portal do Bot Framework</a> e [atualize os dados de Configurações do seu bot](~/bot-service-manage-settings.md) para especificar o **Ponto de extremidade de mensagens** para o bot.

2. [Configure o bot para execução em um ou mais canais](~/bot-service-manage-channels.md).

### <a name="if-you-have-not-yet-registered-your-bot-with-the-bot-framework"></a>Se você ainda não tiver registrado seu bot no Bot Framework:

1. [Registre um bot no Bot Framework](~/bot-service-quickstart-registration.md).

2. Atualize os valores de Id do Aplicativo Microsoft e Senha do Aplicativo Microsoft nas definições de configuração do seu aplicativo implantado para especificar os valores **appID** e **password** gerados para seu bot durante o processo de registro. Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

3. [Configure o bot para execução em um ou mais canais](~/bot-service-manage-channels.md).