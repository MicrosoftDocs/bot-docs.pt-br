---
title: Bot do Enterprise criando um novo projeto | Microsoft Docs
description: Saiba como criar um novo Bot com base no modelo do Bot do Enterprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4b85b0e2d3c1ae8c30ea9d5d9fa62783c2968744
ms.sourcegitcommit: 8b7bdbcbb01054f6aeb80d4a65b29177b30e1c20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51645476"
---
# <a name="enterprise-bot-template---creating-a-new-project"></a>Modelo do Bot do Enterprise - Criar um novo projeto

> [!NOTE]
> Este tópico se aplica à versão 4 do SDK. 

O modelo do Bot do Enterprise reúne todas as práticas recomendadas e componentes de suporte que identificamos através da criação de experiências de conversação. O modelo está disponível nas seguintes plataformas de SDK do Botbuilder:

- .NET
- Node.js (em breve)

## <a name="net"></a>.NET

O modelo do Bot do Enterprise está disponível para .NET, voltado para versões **V4** do SDK. Ele está disponível como um pacote [VSIX](https://docs.microsoft.com/en-us/visualstudio/extensibility/anatomy-of-a-vsix-package). Para baixar, clique no link a seguir:

- [Modelo do Bot do Enterprise do SDK V4 do BotBuilder](https://aka.ms/GetEnterpriseBotTemplate)

#### <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 ou superior](https://www.visualstudio.com/downloads/)
- [Conta do Azure](https://azure.microsoft.com/en-us/free/)
- [PowerShell do Azure](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-6.8.1)

### <a name="install-the-template"></a>Instalar o modelo

No diretório salvo, basta abrir o pacote VSIX e o modelo do Bot do Enterprise será instalado no Visual Studio e disponibilizado na próxima vez que você abri-lo.

Para criar um novo projeto de bot usando o modelo, basta abrir o Visual Studio e selecionar **Arquivo** > **novo** > **Projeto** e, no Visual C#, selecionar **Bot Framework** > Modelo do Bot do Enterprise. Isso criará um novo projeto de bot localmente que você poderá editar como desejar. 

![Arquivar novo modelo do projeto](media/enterprise-template/EnterpriseBot-NewProject.png)

## <a name="deploy-your-bot"></a>Implantar seu Bot

Agora que o projeto foi criado a próxima etapa é criar a infraestrutura de suporte do Azure e executar configuração/implantação habilitando o Bot para ficar pronto para uso. Continue com [Implantar o Bot](bot-builder-enterprise-template-deployment.md).

> Você deve executar essa etapa, caso contrário a inicialização do Bot (AppInsights) e as dependências de LUIS não estarão disponíveis.
## <a name="customize-your-bot"></a>Personalizar seu Bot

Após verificar que você implantou com sucesso o Bot diretamente, personalize-o para atender seu cenário e necessidades. Prossiga com [Personalizar seu Bot](bot-builder-enterprise-template-customize.md).
