---
title: Implantar um bot de mídia em tempo real do Skype no Azure | Microsoft Docs
description: Saiba como implantar um bot de áudio e vídeo em tempo real do Skype no Azure usando o recurso de publicação interno do Visual Studio.
author: MalarGit
ms.author: malarch
manager: ssulzer
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: add83b0534ff950e9e7dd5c97a970d251b9c8fea
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904112"
---
# <a name="deploy-a-real-time-media-bot-from-visual-studio-to-azure"></a>Implantar um bot de mídia em tempo real do Visual Studio no Azure

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Os bots de mídia em tempo real podem ser hospedados em uma Máquina virtual do Azure "IaaS" ou em um Serviço de nuvem do Azure "clássico". Este artigo mostra como implantar um bot, hospedado em uma função de trabalho do serviço de nuvem do Azure, do Visual Studio usando sua funcionalidade de publicação interna.

## <a name="prerequisites"></a>Pré-requisitos

É preciso ter uma assinatura do Microsoft Azure para poder implantar um bot no Azure. Se você ainda não tiver uma assinatura, poderá se inscrever para uma <a href="https://azure.microsoft.com/en-us/free/" target="_blank">conta gratuita</a>. Além disso, o processo descrito por este artigo exige o Visual Studio. Se você ainda não tiver o Visual Studio, baixe o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 Community</a> gratuitamente.

### <a name="certificate-from-a-valid-certificate-authority"></a>Certificado de uma Autoridade de Certificação válida
O bot precisa ser configurado com um certificado válido de uma autoridade de certificação (CA) confiável. O nome da entidade (SN) ou a última entrada do nome alternativo da entidade (SAN) do certificado deve ser o nome do serviço de nuvem. Não há suporte para certificados curinga no momento. Se um CNAME for usado para apontar para o serviço de nuvem, o CNAME deverá ser o SN ou a última entrada de SAN do certificado.

## <a name="configure-application-settings"></a>Definir as configurações do aplicativo
Para que o bot para funcione corretamente na nuvem, você deve garantir que as configurações de aplicativo estão corretas. Mais especificamente, defina os seguintes valores de chave no arquivo app.config da função de trabalho:
> <ul><li>MicrosoftAppId</li><li>MicrosoftAppPassword</li></ul>

> [!NOTE]
> Para encontrar a **AppID** e a **AppPassword** do bot, confira [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

## <a name="create-worker-role-in-the-azure-portal"></a>Criar função de trabalho no Portal do Azure
### <a name="step-1-create-cloud-serviceclassic"></a>Etapa 1: Criar serviço de nuvem (clássico)
Faça logon no <a href="https://portal.azure.com">Portal do Azure</a>. Clique em **+** no lado esquerdo da tela e escolha **Serviços de nuvem (clássico)**. Forneça as informações necessárias no formulário e clique em **Criar**.

![Criar o serviço de nuvem](../media/real-time-media-bot-portal-service-creation.png)

> [!NOTE]
> O nome DNS do bot deve ser fornecido na url para o registro do bot.

### <a name="step-2-upload-the-certificate-for-the-bot"></a>Etapa 2: Carregar o certificado para o bot
Depois que o bot for criado, carregue o certificado para o bot.

![Carregar um certificado](../media/real-time-media-bot-portal-certificates.png)

## <a name="modify-service-configuration-with-worker-role-details"></a>Modificar a configuração de serviço com os detalhes da função de trabalho
O nome de domínio totalmente qualificado (FQDN) do bot não está disponível por meio de APIs de RoleEnvironment do Azure. Portanto, o bot deve ser fornecido com seu FQDN. Ele também precisa saber sobre o certificado para HTTPS. Isso podem ser definido no arquivo de configuração (.cscfg) de serviço da função de trabalho.

> [!TIP]
> Se você estiver implantando uma amostra do repositório de git BotBuilder RealTimeMediaCalling,
> - Substitua o $DnsName$ pelo nome do serviço de nuvem ou o CNAME se um for usado na configuração do serviço.
>   ```xml
>      <Setting name="ServiceDnsName" value="$DnsName$" />
>   ```
> 
> - Substitua $CertThumbprint$ pela impressão digital do certificado carregado para o bot nas linhas a seguir da configuração.
>   ```xml
>      <Setting name="DefaultCertificate" value="$CertThumbprint$" />
>      <Certificate name="Default" thumbprint="$CertThumbprint$" thumbprintAlgorithm="sha1" />
>   ```

## <a name="publish-the-bot-from-visual-studio"></a>Publicar o bot do Visual Studio
### <a name="step-1-launch-the-microsoft-azure-publishing-wizard-in-visual-studio"></a>Etapa 1: Iniciar o Assistente de publicação do Microsoft Azure no Visual Studio

Abra o projeto no Visual Studio. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto do serviço de nuvem e selecione **Publicar**. Isso inicia o assistente de publicação do Microsoft Azure. Use suas credenciais para entrar na assinatura apropriada.

![Clique com o botão direito do mouse no projeto e escolha Publicar para iniciar o assistente de publicação do Microsoft Azure](../media/real-time-media-bot-publish-signin.png)

### <a name="step-2-publish-the-bot"></a>Etapa 2: Publicar o bot

Clique em **Próximo**. Isso abrirá a guia **Configurações**. Especifique o serviço de nuvem, o ambiente, a configuração da compilação e a configuração do serviço para implantar o bot.

![Clique em Avançar e vá até a guia Configurações](../media/real-time-media-bot-publish-settings.png)

É possível escolher opcionalmente **Configurações Avançadas** e especifique a conta de armazenamento para os logs de implantação (que você pode usar para depurar problemas).

![Clique na guia Configurações Avançadas](../media/real-time-media-bot-publish-advanced-settings.png)

Verifique se a configuração na guia **Resumo** e clique em **Publicar** para implantar o bot no Microsoft Azure.
