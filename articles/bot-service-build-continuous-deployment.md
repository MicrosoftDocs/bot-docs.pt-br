---
redirect_url: /bot-framework/bot-builder-deploy-az-cli
ms.openlocfilehash: 8149471553658df6778e2983bae114e80c846c9b
ms.sourcegitcommit: 8183bcb34cecbc17b356eadc425e9d3212547e27
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55971417"
---
<a name="--"></a><!--
---
Título: Configurar a implantação contínua para o Serviço de Bot | descrição do Microsoft Docs: Saiba como configurar a implantação contínua do controle do código-fonte para um Serviço de Bot. palavras-chave: implantação contínua, publicar, implantar, portal do azure autor: ivorb ms.author: v-ivorb manager: kamrani ms.topic: article ms.service: bot-service ms.date: 06/12/2018
---

# <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua
Se seu código for verificado no **GitHub** ou no **Azure DevOps (anteriormente conhecido como Visual Studio Team Services)**, use a implantação contínua para implantar automaticamente as alterações de código do seu repositório de origem para o Azure. Neste tópico, abordaremos como configurar a implantação contínua para **GitHub** e **Azure DevOps**.

> [!NOTE]
> O cenário abordado neste artigo pressupõe que você implantou seu bot no Azure, e agora você deseja habilitar a implantação contínua para o bot. Depois que a implantação contínua for configurada, o editor de códigos online no portal do Azure se torna somente leitura.

## <a name="continuous-deployment-using-github"></a>Implantação contínua usando o GitHub

Para configurar a implantação contínua usando o repositório GitHub que contém o código-fonte que você deseja implantar no Azure, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), vá até a folha **Todas as configurações do Serviço de Aplicativo** do seu bot e clique em **Opções de implantação (Clássica)**. 

1. Clique em **Escolher fonte** e selecione **GitHub**.

   ![Escolher o GitHub](~/media/azure-bot-build/continuous-deployment-setup-github.png)

1. Clique em **Autorização** e, em seguida, clique no botão **Autorizar** e siga os prompts para fornecer autorização ao Azure para acessar sua conta do GitHub.

1. Clique em **Escolher projeto** e selecione um projeto.

1. Clique em **Escolher ramificação** e selecione uma ramificação.

1. Clique em **OK** para concluir o processo de instalação.

Agora a sua implantação contínua com a instalação do GitHub foi concluída. Sempre que você confirmar no repositório do código-fonte, suas alterações serão automaticamente implantadas no Serviço de Bot do Azure.

## <a name="continuous-deployment-using-azure-devops"></a>Implantação contínua usando o Azure DevOps

1. No [portal do Azure](https://portal.azure.com), vá até a folha **Todas as configurações do Serviço de Aplicativo** do seu bot e clique em **Opções de implantação (Clássica)**. 
2. Clique em **Escolher fonte** e selecione **Visual Studio Team Services**. Lembre-se que o Visual Studio Team Services agora se chama Azure DevOps Services.

   ![Escolher o Visual Studio Team Services](~/media/azure-bot-build/continuous-deployment-setup-vs.png)

3. Clique em **Escolha sua conta** e selecione uma conta.

> [!NOTE]
> Se você não vir a sua conta listada, você precisará [vincular sua conta à sua assinatura do Azure](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=vsts&tabs=new-nav). Lembre-se que há suporte apenas para projetos Git do VSTS.

4. Clique em **Escolher projeto** e selecione um projeto.
5. Clique em **Escolher ramificação** e selecione uma ramificação.
6. Clique em **OK** para concluir o processo de instalação.

   ![Configuração do Visual Studio](~/media/azure-bot-build/continuous-deployment-setup-vs-configuration.png)

Agora a sua implantação contínua com a instalação do Azure DevOps foi concluída. Sempre que você fizer uma confirmação, as alterações serão implantadas automaticamente no Azure.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Enquanto o bot é configurado para a implantação contínua, você não pode usar o editor de códigos online para fazer alterações no bot. Caso deseje usar o editor de códigos online, desabilite temporariamente a implantação contínua.

Para desabilitar a implantação contínua, faça o seguinte:
1. No [portal do Azure](https://portal.azure.com), vá até a folha **Todas as configurações do Serviço de Aplicativo** do seu bot e clique em **Opções de implantação (Clássica)**. 
2. Clique em **Desconectar** para desabilitar a implantação contínua. Para habilitar novamente a implantação contínua, repita as etapas das seções apropriadas acima.

## <a name="additional-information"></a>Informações adicionais
- O Visual Studio Team Services agora se chama [Azure DevOps Services](https://docs.microsoft.com/en-us/azure/devops/?view=vsts)


-->
