---
title: Criptografia do serviço de bot do Azure para dados em serviço de bot de repouso
description: O Serviço de Bot do Azure protege seus dados criptografando-os automaticamente antes de mantê-los na nuvem com as chaves de criptografia fornecidas pela Microsoft.
ms.service: bot-service
ms.date: 11/19/2020
ms.topic: conceptual
author: JonathanFingold
ms.author: jameslew
ms.openlocfilehash: fc0373bb8daaa64c9d230c813f6366704ce16df3
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456400"
---
# <a name="azure-bot-service-encryption-for-data-at-rest"></a>Criptografia do Serviço de Bot do Azure para dados em repouso

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

O serviço de bot do Azure criptografa automaticamente seus dados quando eles são persistidos na nuvem para proteger seus dados e atender aos compromissos de conformidade e segurança organizacional.

A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Os dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Fornecemos a opção também para você gerenciar seu recurso de bot com suas próprias chaves chamadas CMK (chaves gerenciadas pelo cliente). O CMK oferece maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso para os armazenamentos do serviço de bot de dados. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Ao criptografar dados, o serviço de bot do Azure criptografa com dois níveis de criptografia. No caso em que CMK não está habilitado, ambas as chaves usadas são chaves gerenciadas pela Microsoft. Quando o CMK é configurado, os dados são criptografados com o CMK e uma chave gerenciada pela Microsoft.  

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Para utilizar o recurso de chaves gerenciadas pelo cliente, você deve armazená-los e gerenciá-los no Azure Key Vault. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou usar as APIs de Azure Key Vault para gerar chaves. O registro de bot e o cofre de chaves devem estar no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](/azure/key-vault/key-vault-overview).

Ao usar uma chave gerenciada pelo cliente, o serviço de bot do Azure criptografa seus dados em nosso armazenamento, de modo que, se o acesso a essa chave for revogado ou a chave for excluída, o bot deixará de usar o serviço de bot para enviar ou receber mensagens e você não poderá acessar ou editar a configuração do seu registro de bot no portal do serviço de bot do Azure.

> [!IMPORTANT]
> A equipe do serviço de bot do Azure não pode recuperar um bot de chave de criptografia gerenciado pelo cliente sem acesso à chave.

## <a name="what-data-is-encrypted"></a>Quais dados são criptografados?

O serviço de bot do Azure armazena dados do cliente sobre o bot, os canais que ele usa, definições de configuração que o desenvolvedor define e quando necessário, um registro de conversas atualmente ativas. Ele também é transitório, por menos de 24 horas, armazena as mensagens enviadas pela linha direta ou canais de bate-papo da Web e todos os anexos carregados.

Todos os dados do cliente são criptografados com duas camadas de criptografia no serviço de bot do Azure; com as chaves de criptografia gerenciadas pela Microsoft ou com as chaves de criptografia gerenciadas pela Microsoft e pelo cliente. O serviço de bot criptografa dados armazenados transitórios usando as chaves de criptografia gerenciadas pela Microsoft e criptografa dados de longo prazo usando as chaves de criptografia gerenciadas pela Microsoft ou pelo cliente, dependendo da configuração do registro de bot.

> [!NOTE]
> Como o serviço de bot do Azure existe para fornecer aos clientes a capacidade de entregar mensagens de e para usuários em outros serviços fora do serviço de bot do Azure, nossa criptografia não se estende a esses serviços. Praticamente, isso significa que, embora sob nosso controle, os dados serão armazenados criptografados de acordo com as diretrizes deste documento; no entanto, ao deixar nosso serviço para entregar a outro serviço, os dados são descriptografados e enviados usando a criptografia TLS 1,2 para o serviço de destino.

## <a name="how-to-configure-your-azure-key-vault-instance"></a>Como configurar sua instância do Azure Key Vault

O uso de chaves gerenciadas pelo cliente com o serviço de bot do Azure exige que você habilite duas propriedades na instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia: **exclusão reversível** e **limpeza da proteção**. Esses recursos funcionam em parceria para garantir que, se, por algum motivo, sua chave for excluída acidentalmente, você poderá recuperá-la. Para obter mais informações sobre exclusão reversível e proteção de limpeza, consulte a [visão geral da exclusão de Azure Key Vault reversível](/azure/key-vault/general/soft-delete-overview).

> [!div class="mx-imgBorder"]
> ![Captura de tela de exclusão reversível e proteção de limpeza habilitada](media/key-vault/encryption-settings.png)

Se você estiver usando uma instância de Azure Key Vault existente, poderá verificar se essas propriedades estão habilitadas examinando a seção **Propriedades** no portal do Azure. Se qualquer uma dessas propriedades não estiver habilitada, consulte a seção **Key Vault** em [como habilitar a exclusão reversível e limpar a proteção](/azure/key-vault/general/key-vault-recovery).

### <a name="grant-the-bot-service-access-to-a-key-vault"></a>Conceder ao serviço de bot acesso a um cofre de chaves

Para que o serviço de bot do Azure tenha acesso ao cofre de chaves que você criou para essa finalidade, uma política de acesso precisa ser definida, o que dá à entidade de serviço do serviço de bot do Azure o conjunto de permissões atual. Para obter mais informações sobre Key Vault, incluindo como criar um cofre de chaves, consulte [about Azure Key Vault](/azure/key-vault/general/overview).

1. Registre o provedor de recursos do serviço de bot do Azure em sua assinatura que contém o cofre de chaves.
    1. Vá para o [Portal do Azure](https://ms.portal.azure.com).
    1. Abra a folha **assinaturas** e selecione a assinatura que contém o cofre de chaves.
    1. Abra a folha **provedores de recursos** e registre o provedor de recursos **Microsoft. BotService** .

    > [!div class="mx-imgBorder"]
    > ![Microsoft. BotService registrado como um provedor de recursos](media/key-vault/register-resource-provider.png)

1. Configure uma política de acesso em seu cofre de chaves, fornecendo ao **serviço de bot CMEK** a entidade de serviço de produção o **Get** (das **operações de gerenciamento de chaves**) e a chave de **desencapsulamento** e a tecla de **encapsulamento** (das **operações de criptografia**) permissões de chave.
    1. Abra a folha **cofres de chaves** e selecione o cofre de chaves.
    1. Certifique-se de que o aplicativo de **CMEK de produção do serviço de bot** seja adicionado como uma política de acesso e tenha essas três permissões. Talvez seja necessário adicionar o aplicativo **bot Service CMEK prod** como uma política de acesso ao cofre de chaves.
    1. Clique em **salvar** para salvar as alterações feitas.

    > [!div class="mx-imgBorder"]
    > ![Serviço de bot CMEK prod adicionado como uma política de acesso](media/key-vault/access-policies.png)

1. Permitir que Key Vault ignore o firewall.
    1. Abra a folha **cofres de chaves** e selecione o cofre de chaves.
    1. Abra a folha **rede** e vá para a guia **firewalls e redes virtuais** .
    1. Se **permitir acesso de** for definido como **ponto de extremidade privado e redes selecionadas**, defina **permitir que serviços da Microsoft confiáveis ignorem esse firewall** para **Sim**.
    1. Clique em **salvar** para salvar as alterações feitas.

    > [!div class="mx-imgBorder"]
    > ![Exceção de firewall adicionada para Key Vault](media/key-vault/firewall-exception.png)

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para criptografar o registro de bot com uma chave de criptografia gerenciada pelo cliente, siga estas etapas:

1. Abra a folha de recursos do bot.
1. Abra a folha **criptografia** do bot e selecione **chaves gerenciadas pelo cliente** para o **tipo de criptografia**.
1. Insira o URI completo da chave, incluindo a versão, ou clique em **selecionar um cofre de chaves e uma chave** para localizar sua chave.
1. Clique em **salvar** na parte superior da folha.

> [!div class="mx-imgBorder"]
> ![Recurso de bot usando criptografia gerenciada pelo cliente](media/key-vault/customer-managed-encryption.png)

Depois que essas etapas forem concluídas, o serviço de bot iniciará o processo de criptografia. Isso pode levar algum tempo para ser concluído (até 24 horas). O bot será completamente funcional durante esse período de tempo.

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Para girar uma chave de criptografia gerenciada pelo cliente, você deve atualizar o recurso de serviço de bot do Azure para usar o novo URI para a nova chave (ou nova versão da chave existente).

Como a recriptografia com a nova chave ocorre de forma assíncrona, verifique se a chave antiga permanece disponível para que os dados possam continuar a ser descriptografados; caso contrário, o bot pode parar de funcionar. Você deve reter a chave antiga por pelo menos uma semana.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso, remova a política de acesso para a entidade de serviço **CMEK prod do serviço de bot** do cofre de chaves.

> [!NOTE]
> Revogar o acesso irá interromper a maior parte da funcionalidade associada ao bot. Para desabilitar o recurso de chaves gerenciadas pelo cliente, desative o recurso antes de revogar o acesso para garantir que o bot possa continuar funcionando.

## <a name="next-steps"></a>Próximas etapas

Saiba mais [sobre o Azure Key Vault](/azure/key-vault/key-vault-overview)
