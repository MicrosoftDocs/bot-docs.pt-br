---
title: Criptografia do Serviço de Bot do Azure para dados em repouso | Microsoft Docs
description: O Serviço de Bot do Azure protege seus dados criptografando-os automaticamente antes de mantê-los na nuvem com as chaves de criptografia fornecidas pela Microsoft.
ms.service: bot-service
ms.date: 07/21/2020
ms.topic: conceptual
ms.author: jameslew
ms.openlocfilehash: 132d9b7a09ffdf41c2c98da49c69111ebd372b63
ms.sourcegitcommit: 757ddf4f3fd3557ad9746dfa83ca742d54ac4763
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86876546"
---
# <a name="azure-bot-service-encryption-for-data-at-rest"></a>Criptografia do Serviço de Bot do Azure para dados em repouso

O Serviço de Bot do Azure criptografa automaticamente seus dados ao mantê-los na nuvem. A criptografia protege seus dados e ajuda a atender aos compromissos de conformidade e segurança de sua organização. Os dados no Serviço de Bot do Azure são criptografados de maneira transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis, além de estar em conformidade com o FIPS 140-2. Uma técnica de soma de verificação também é usada para detectar a adulteração de dados não criptografados, como GUIDs.

## <a name="types-of-data-encrypted-in-the-azure-bot-service"></a>Tipos de dados criptografados no Serviço de Bot do Azure

O Serviço de Bot do Azure criptografa seus dados no serviço bot. Os exemplos incluem, entre outros:

1) Todos os dados de conta de desenvolvedor, como nome ou email
2) Todos os dados confidenciais sobre o bot (incluindo URLs, credenciais de canal de terceiros, nomes e descrições de bot)
3) Todos os dados armazenados temporariamente ao entregar atividades, incluindo anexos e dados de conversa

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

O Serviço de Bot do Azure criptografa dados com as chaves fornecidas pela Microsoft que são giradas de modo predefinido.  

|                                        |    Chaves gerenciadas pela Microsoft                             | 
|----------------------------------------|-------------------------------------------------------|
|    Operações de criptografia/descriptografia    |    Azure                                              |
|    Armazenamento de chave                         |    Cofre de Chave do Azure                              |
|    Responsabilidade de rotação de chave         |    Microsoft                                          |
|    Uso de chave                           |    Microsoft                                          |
|    Acesso à chave                          |    Somente Microsoft                                     |

As seções a seguir descrevem mais detalhadamente cada uma das opções de gerenciamento de chaves.

## <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

O recurso de Serviço de Bot do Azure usa chaves de criptografia gerenciadas pela Microsoft. O Serviço de Bot do Azure está em conformidade com as melhores práticas da Microsoft para acesso de armazenamento de Chave, algoritmos de criptografia e rotação de chaves.

## <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

No momento, o Serviço de Bot do Azure não oferece capacidade de chaves de criptografia gerenciadas pelo cliente para dados em repouso.