---
title: Criar um Registro de Canais de Bot com o Serviço de Bot – Serviço de Bot
description: Saiba como registrar bots com o serviço bot. Consulte Como conectar bots aos canais do serviço de bot do Azure para que eles possam interagir com os usuários.
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 07/30/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: eaf93be0cb6970f11ac8531f132170f4ccf39050
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89360959"
---
# <a name="bot-channels-registration"></a>Registro de canais do bot

Este artigo mostra como registrar um bot com o **serviço de bot do Azure**. Se o bot estiver hospedado em outro lugar, você também poderá disponibilizá-lo no Azure e conectá-lo aos canais com suporte. Há duas maneiras de registrar um bot com o Azure:

1. Seguindo as etapas no artigo [criar um bot com o serviço de bot do Azure](~/v4sdk/abs-quickstart.md), o bot é criado e registrado com o Azure e um aplicativo Web é criado para hospedar o bot. Você usará essa abordagem se desenvolver e hospedar um bot no Azure.
1. Para criar e desenvolver seu bot localmente, siga as etapas neste artigo. Ao registrar o bot, você fornece o endereço da Web onde o bot está hospedado. Você ainda pode hospedá-lo no Azure.

> [!IMPORTANT]
> Você só precisa registrar um bot se ele não estiver hospedado no Azure.
Os bots criados usando o portal do Azure ou a CLI (interface de linha de comando) já estão registrados com o serviço de bot do Azure. Para obter mais informações, consulte [implantar seu bot](~/bot-builder-deploy-az-cli.md) e [criar um bot com o serviço de bot do Azure](~/v4sdk/abs-quickstart.md).

## <a name="create-a-registration-application"></a>Criar um aplicativo de registro

1. Em seu navegador, navegue até o [portal do Azure](https://ms.portal.azure.com). Se você não tiver uma assinatura, poderá se inscrever para obter uma <a href="https://azure.microsoft.com/free/" target="_blank">conta gratuita</a>.

1. No painel esquerdo, clique em **criar um recurso**.
1. Na caixa de seleção do painel direito, digite *bot*. Na lista suspensa, selecione o **registro de canais de bot**.
1. Selecione o botão **Criar**.
1. No formulário de **registro de canais de bot** , forneça as informações solicitadas sobre o bot conforme especificado na tabela a seguir.

    > [!div class="mx-tdBreakAll"]
    >   |Configuração |Valor sugerido|Descrição|
    >   |---|---|--|
    >   |**Nome do bot** <img width="200px"/>|\<Your bot display name><img width="200px"/>|Atribua o nome de exibição para o bot que aparece em canais e diretórios. Você pode alterar esse nome a qualquer momento.|
    >   |**Assinatura**|\<Your subscription>|Selecione a assinatura do Azure que deseja usar.|
    >   |**Grupo de recursos**|\<Your resource group name>|Crie um novo [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou selecione um existente.|
    >   |**Localidade**|Oeste dos EUA|Escolha a localização geográfica do seu grupo de recursos. Geralmente, é melhor escolher um local perto de você. O local não pode ser alterado depois que o grupo de recursos é criado.|
    >   |**Tipo de preços**|F0|Selecione um tipo de preço. Você pode atualizar o tipo de preço a qualquer momento. Para obter mais informações, confira [Preços do Serviço de Bot](https://azure.microsoft.com/pricing/details/bot-service/).|
    >   |**Ponto de extremidade de mensagens**|https:// \<your bot name> . azurewebsites.NET/API/messages|Insira a URL do ponto de extremidade de mensagens do seu bot. Você pode deixar esse campo vazio, pois você inserirá a URL necessária após a implantação do bot.|
    >   |**Application Insights**|Por| Decida se deseja deixar o [Application Insights](bot-service-manage-analytics.md) **Ativado** ou **Desativado**. Se você selecionar **Ativar**, será preciso especificar também um local regional. |
    >   |**ID do aplicativo e senha**| Criar ID de Aplicativo e senha |Use essa opção se precisar inserir manualmente uma ID do Aplicativo e uma senha da Microsoft. Confira a próxima seção [Registro manual do aplicativo](#manual-app-registration). Caso contrário, uma nova ID do aplicativo da Microsoft será criada no processo de registro. |

1. Deixe a caixa *ponto de extremidade do sistema de mensagens* vazia por enquanto. Você inserirá a URL necessária após a implantação do bot.
1. Clique em **ID e senha do aplicativo Microsoft**, realçado na captura de tela abaixo.  As próximas etapas permitem que você crie a senha do aplicativo de registro e a ID do aplicativo a ser usada em seus arquivos de configuração do bot `appsettings.json` (.net), `.env` (JavaScript) e `config.py` (Python).

    ![configurações de registro de bot](media/azure-bot-quickstarts/bot-channels-registration-settings.png)

1. Clique em **Criar Novo**.
1. Clique no link **criar ID do aplicativo no portal de registro de aplicativo** .
1. Na janela de *registro do aplicativo* exibido, clique na guia **novo registro** no canto superior esquerdo.
1. Insira o nome do aplicativo de bot que você está registrando.
1. Para os tipos de conta com suporte, selecione o tipo com base em seus requisitos. Consulte também [usar credenciais de bot](bot-service-debug-emulator.md#use-bot-credentials).
1. Clique no botão **registrar** . Depois de concluído, o Azure exibe a página Visão geral do aplicativo.
1. Copie o valor da **ID do aplicativo (cliente)** e salve-o em um arquivo.
1. No painel esquerdo, clique em **certificado e segredos**.
1. Em *Segredos de cliente*, clique em **Novo segredo do cliente**.
1. Adicione uma descrição para identificar esse segredo em relação a outros que talvez você precise criar para esse aplicativo.
1. Definir **expira** para sua escolha.
1. Clique em **Adicionar**.
1. Copie o segredo do cliente e salve-o em um arquivo. Certifique-se de manter o arquivo em um local seguro.
1. Volte para a janela Registro do Canal do Bot e copie a ID do Aplicativo e o Segredo do cliente nas caixas ID do Aplicativo da Microsoft e Senha, respectivamente.
1. Clique em **OK**.
1. Clique no botão **criar** e aguarde até que o recurso seja criado. Ele será exibido em sua lista de recursos.

> [!NOTE]
> O aplicativo de registro mostrará a região global, embora você tenha selecionado um diferente. Isso é esperado.

## <a name="manual-app-registration"></a>Registro manual do aplicativo

Um registro manual é necessário para situações como:

- Você não consegue fazer os registros em sua organização e precisa de terceiros para criar a ID do aplicativo para o bot que você está criando.
- Você precisa criar manualmente sua própria ID de aplicativo e senha.

> [!IMPORTANT]
> Na seção *dá suporte a tipos de conta*, escolha um dos dois tipos de vários locatários, *contas em qualquer diretório organizacional (qualquer multilocatário do Azure AD)* ou *contas em qualquer diretório organizacional (qualquer Azure ad-multilocatário) e contas pessoais da Microsoft (por exemplo, Xbox, Outlook.com)*, ao criar o aplicativo. Não definir esse valor corretamente resultará em um bot não funcional. Para obter mais informações, consulte [registrar um novo aplicativo usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) e [registrar um aplicativo com a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal).

## <a name="update-the-bot"></a>Atualizar o bot

1. SDK do bot Framework para .NET. Defina os seguintes valores de chave no `appsettings.json` arquivo:
    - `MicrosoftAppId = <appId>`
    - `MicrosoftAppPassword = <appSecret>`

1. SDK do bot Framework para Node.js. Defina as seguintes variáveis de ambiente no `.env` arquivo:
    - `MICROSOFT_APP_ID = <appId>`
    - `MICROSOFT_APP_PASSWORD = <appSecret>`

1. SDK do bot Framework para Python. Defina as seguintes variáveis de ambiente no `config.py` arquivo:
    - `APP_ID = os.environ.get("MicrosoftAppId", <appId>)`
    - `APP_PASSWORD = os.environ.get("MicrosoftAppPassword", <appSecret>)`

## <a name="test-the-bot"></a>Testar o bot

Agora que o serviço de bot foi criado, [teste-o no Webchat](v4sdk/abs-quickstart.md#test-the-bot).

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu como registrar o seu bot hospedado no Serviço de Bot. A próxima etapa é saber como gerenciar seu Serviço de Bot.

> [!div class="nextstepaction"]
> [Gerenciar um bot](bot-service-manage-overview.md)
