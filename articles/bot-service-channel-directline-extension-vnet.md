---
title: Usar a extensão de serviço de aplicativo do Direct Line com uma VNET
titleSuffix: Bot Service
description: Saiba como usar uma extensão de serviço de aplicativo de linha direta com uma rede virtual do Azure. Consulte como criar um ambiente e configurar uma conexão de saída.
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: c04fb8f7564e25a67af67556fd14413f7389b3a3
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456630"
---
# <a name="use-direct-line-app-service-extension-within-a-vnet"></a>Usar a extensão de serviço de aplicativo do Direct Line com uma VNET

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Este artigo descreve como usar a Extensão de Serviço de Aplicativo do Direct Line com uma VNET (Rede Virtual do Azure).

## <a name="create-an-app-service-environment-and-other-azure-resources"></a>Criar um Ambiente do Serviço de Aplicativo e outros recursos do Azure

1. A extensão do Serviço de Aplicativo do Direct Line está disponível em todos os **Serviços de Aplicativos do Azure**, incluindo aqueles hospedados em um **Ambiente do Serviço de Aplicativo do Azure**. Um Ambiente do Serviço de Aplicativo do Azure oferece isolamento e é ideal para trabalhar em uma VNET.
    - As instruções para criar um Ambiente do Serviço de Aplicativo externo podem ser encontradas no artigo [Criar um Ambiente do Serviço de Aplicativo externo](https://docs.microsoft.com/azure/app-service/environment/create-external-ase).
    - As instruções para criar um Ambiente do Serviço de Aplicativo interno podem ser encontradas no artigo [Criar e usar um Ambiente do Serviço de Aplicativo de Balanceador de Carga Interno](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).
1. Depois de criar seu Ambiente do Serviço de Aplicativo, você precisará adicionar um Plano do Serviço de Aplicativo a ele, em que você possa implantar seus bots (e, assim, executar a Extensão do Serviço de Aplicativo do Direct Line). Para fazer isso:
    - Acesse https://portal.azure.com/
    - Crie um novo recurso "plano do serviço de aplicativo".
    - Em Região, selecione seu Ambiente do Serviço de Aplicativo
    - Conclua a criação do Plano do Serviço de Aplicativo

## <a name="configure-the-vnet-network-security-groups-nsg"></a>Configure os NSGs (Grupos de Segurança de Rede) da VNET

1. A Extensão do Serviço de Aplicativo do Direct Line requer uma conexão de saída para que possa emitir solicitações HTTP. Isso pode ser configurado como uma regra de saída em seu NSG VNET associado à sub-rede do Ambiente do Serviço de Aplicativo. A regra necessária é a seguinte:

|Campo|Valor|
|---|---|
|Fonte|Qualquer|
|Porta de origem|*|
|Destino|Marca de serviço|
|Marca de serviço de destino|AzureBotService|
|Intervalos de portas de destino|443|
|Protocolo|Qualquer|
|Ação|Allow|
