---
title: Requisitos e considerações para bots de mídia em tempo real | Microsoft Docs
description: Entenda os requisitos e considerações importantes relacionados à criação de bots de mídia em tempo real para o Skype, usando o Construtor de bot do SDK para .NET.
author: ssulzer
ms.author: ssulzer
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 091c90da9b14c0abe70d08f45f528a3cce818cef
ms.sourcegitcommit: 2dc75701b169d822c9499e393439161bc87639d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42904030"
---
# <a name="requirements-and-considerations-for-real-time-media-bots"></a>Requisitos e considerações para bots de mídia em tempo real

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Nem todas as orientações que se aplicam ao desenvolvimento de mensagens e bots de chamadas de IVR se aplicam igualmente à criação de bots de mídia em tempo real. Este artigo descreve alguns dos requisitos e considerações importantes para o desenvolvimento de bots de mídia em tempo real. 

> [!NOTE]
> Como a Plataforma de mídia em tempo real para bots é uma Tecnologia de visualização, as diretrizes neste artigo estão sujeitas a alterações.

## <a name="real-time-media-bot-development-requires-cnet-and-windows-server"></a>O desenvolvimento de bot de mídia em tempo real requer o C#/.NET e o Windows Server

- O bot de mídia em tempo real requer a biblioteca do .NET `Microsoft.Skype.Bots.Media` (disponível por meio do <a href="https://www.nuget.org/" target="_blank">NuGet</a>) para acessar o áudio e a mídia de vídeo. Além disso, o bot deve ser implantado em uma máquina do Windows Server (ou no sistema operacional convidado do Windows Server no Azure). Portanto, o bot deve ser desenvolvido com o C# e o .NET Framework padrão e implantado no Azure. Não é possível usar as APIs do C++ e Node.js para acessar a mídia em tempo real.

- O bot de mídia em tempo real deve estar em execução em uma versão recente da biblioteca do .NET `Microsoft.Skype.Bots.Media`. O bot deve usar a versão disponível do pacote do <a href="https://www.nuget.org/" target="_blank">NuGet</a> mais recente ou uma versão que não tenha mais de três meses. As versões mais antigas da biblioteca serão preteridas e não funcionarão após alguns meses.

## <a name="real-time-media-calls-stay-on-the-machine-where-they-were-created"></a>As chamadas de mídia em tempo real serão mantidas no computador onde foram criadas

- Os bots de mídia em tempo real são objetos com muito estado. A chamada de mídia em tempo real é fixada na instância de máquina virtual (VM) que aceitou a chamada: a mídia de voz e vídeo do chamador do Skype fluirá para essa instância de VM e a mídia que o bot enviar de volta ao chamador também deve ser originada essa VM.

- Se houver alguma chamada de mídia em tempo real em andamento quando a VM estiver parada, essas chamadas serão encerradas abruptamente. Se o bot for informado do desligamento da VM pendente, poderá tentar concluir "elegantemente" as chamadas.

## <a name="real-time-media-bots-must-be-directly-accessible-on-the-internet"></a>Os bots de mídia em tempo real devem ser acessados diretamente da Internet

- Cada instância de VM que hospeda um bot de mídia em tempo real deve ser acessada diretamente da Internet. Para bots de mídia em tempo real hospedados no Azure, cada instância de VM deve ter um endereço IP público no nível de instância (ILPIP). Para obter informações sobre como obter e configurar um ILPIP, confira <a href="/azure/virtual-network/virtual-networks-instance-level-public-ip" target="_blank">Visão geral do IP público no nível de instância (Clássico)</a>. Por padrão, uma assinatura do Azure pode obter cinco endereços ILPIP. Entre em contato com o suporte do Azure para aumentar a cota da sua assinatura.

- Devido ao requisito de endereço IP público, os bots de mídia em tempo real devem ser hospedados em uma Máquina virtual do Azure "IaaS" ou em um Serviço de nuvem do Azure "clássico". Outros ambientes de tempo de execução, como o Serviço de Bot ou o Service Fabric com Conjuntos de Dimensionamento de Máquinas Virtuais, não são compatíveis, pois eles não oferecem suporte ao ILPIP.

- Os bots de mídia em tempo real também não são compatíveis com o [Bot Framework Emulator](../bot-service-debug-emulator.md).

## <a name="scalability-and-performance-considerations"></a>Considerações de escalabilidade e desempenho

- Os bots de mídia em tempo real exigem mais capacidade de computação e de rede (largura de banda) do que os bots de mensagens e podem implicar em custos operacionais significativamente mais altos. Um desenvolvedor de bots de mídia em tempo real deve ter atenção ao medir a escalabilidade do bot e verificar se ele não aceita mais chamadas simultâneas do que aquelas que ele tem capacidade de gerenciar. Um bot habilitado para vídeo só conseguirá sustentar uma ou duas chamadas simultâneas de mídia em tempo real por núcleo da CPU.

- A atual versão de visualização da Plataforma de mídia em tempo real para bots tem certas limitações de escalabilidade (ela poderá passar por aprimoramentos em versões futuras), o desenvolvedor do bot deve estar ciente que: 
  1. Uma instância de VM única não deve ter mais de 10 soquetes de vídeos criados de uma única vez.
  2. Atualmente, a Plataforma de mídia em tempo real não utiliza as GPUs (Unidades de Processamento Gráfico) disponíveis na VM para descarregar codificação/decodificação de vídeo H.264. Em vez disso, a codificação e decodificação de vídeo são feitas em software na CPU. Se houver uma GPU disponível, o bot poderá utilizar sua própria renderização de gráficos (por exemplo, se o bot estiver usando um mecanismo de gráficos 3D).

- A instância de VM que hospeda o bot de mídia em tempo real deve ter pelo menos 2 núcleos de CPU. No caso do Azure, recomendamos uma máquina virtual da série Dv2. Veja informações detalhadas sobre os tipos de VM do Azure na <a href="/azure/virtual-machines/windows/sizes-general" target="_blank">Documentação do Azure</a>. 
