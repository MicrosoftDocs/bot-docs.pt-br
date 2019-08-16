---
title: Usar a extensão de serviço de aplicativo do Direct Line com uma VNET
titleSuffix: Bot Service
description: Usar a extensão de serviço de aplicativo do Direct Line com uma VNET
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 7c565d77879641d92a3e331852ff38ea21fdaf9e
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68866444"
---
# <a name="use-direct-line-app-service-extension-within-a-vnet"></a>Usar a extensão de serviço de aplicativo do Direct Line com uma VNET

Este artigo descreve como usar a Extensão de Serviço de Aplicativo do Direct Line com uma VNET (Rede Virtual do Azure).

## <a name="create-an-app-service-environment-and-other-azure-resources"></a>Criar um Ambiente do Serviço de Aplicativo e outros recursos do Azure

1. A extensão do Serviço de Aplicativo do Direct Line está disponível em todos os **Serviços de Aplicativos do Azure**, incluindo aqueles hospedados em um **Ambiente do Serviço de Aplicativo do Azure**. Um Ambiente do Serviço de Aplicativo do Azure oferece isolamento e é ideal para trabalhar em uma VNET.
    - As instruções para criar um Ambiente do Serviço de Aplicativo externo podem ser encontradas no artigo [Criar um Ambiente do Serviço de Aplicativo externo](https://docs.microsoft.com/en-us/azure/app-service/environment/create-external-ase).
    - As instruções para criar um Ambiente do Serviço de Aplicativo interno podem ser encontradas no artigo [Criar e usar um Ambiente do Serviço de Aplicativo de Balanceador de Carga Interno](https://docs.microsoft.com/en-us/azure/app-service/environment/create-ilb-ase).
1. Depois de criar seu Ambiente do Serviço de Aplicativo, você precisará adicionar um Plano do Serviço de Aplicativo a ele, em que você possa implantar seus bots (e, assim, executar a Extensão do Serviço de Aplicativo do Direct Line). Para fazer isso:
    - Acesse https://portal.azure.com/
    - Crie um novo recurso “Plano do Serviço de Aplicativo”.
    - Em Região, selecione seu Ambiente do Serviço de Aplicativo
    - Conclua a criação do Plano do Serviço de Aplicativo

## <a name="configure-the-vnet-network-security-groups-nsg"></a>Configure os NSGs (Grupos de Segurança de Rede) da VNET

1. A Extensão do Serviço de Aplicativo do Direct Line requer uma conexão de saída para que possa emitir solicitações HTTP. Isso pode ser configurado como uma regra de saída no NSG da VNET associado à sub-rede do Ambiente do Serviço de Aplicativo. A regra necessária é a seguinte:

|Fonte|Qualquer|
|---|---|
|Porta de origem|*|
|Destino|Endereços IP|
|Endereços IP de destino|20.38.80.64, 40.82.248.64|
|Intervalos de portas de destino|443|
|Protocolo|Qualquer|
|Ação|PERMITIR|


![Arquitetura da extensão do Direct Line](./media/channels/direct-line-extension-vnet.png)

>[!NOTE]
> Os endereços IP fornecidos são explicitamente para a versão prévia. Ainda neste ano, vamos mudar para uma Marca de Serviço do Serviço de Bot do Azure que alterará essa configuração.

### <a name="configure-your-bots-app-service"></a>Configurar o Serviço de Aplicativo de seu bot

Para a versão prévia, você precisará alterar como sua Extensão do Serviço de Aplicativo do Direct Line se comunica com o Azure. Isso pode ser feito adicionando uma nova **Configuração de aplicativo do Serviço de Aplicativo** ao seu aplicativo usando o portal ou o arquivo `applicationsettings.json`:

- Propriedade: DirectLineExtensionABSEndpoint
- Valor: https://st-directline.botframework.com/v3/extension

>[!NOTE]
> Isso será necessário apenas para a versão prévia da Extensão do Serviço de Aplicativo do Direct Line.
