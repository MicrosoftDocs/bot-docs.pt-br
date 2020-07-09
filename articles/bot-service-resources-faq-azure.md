---
title: Perguntas frequentes do bot Framework-serviço Azure-bot
description: Estrutura de bot de perguntas frequentes do Azure.
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/08/2020
ms.openlocfilehash: 57db2cb7c5b63fd58413f0e470bf7be1f20d42bf
ms.sourcegitcommit: c886b886e6fe55f8a469e8cd32a64b6462383a4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86124605"
---
# <a name="azure"></a>Azure

## <a name="how-do-i-create-my-own-app-registration"></a>Como fazer para criar meu próprio Registro de Aplicativo?

A criação de seu próprio Registro de Aplicativo será necessária para situações como as seguintes:

- Você criou seu bot no portal do Bot Framework (assim como https://dev.botframework.com/bots/new)
- Você não consegue fazer registros de aplicativo em sua organização e precisa de terceiros para criar a ID do aplicativo para o bot que você está compilando
- Caso contrário, você precisa criar sua própria ID do aplicativo (e senha)

Para criar sua própria ID do aplicativo, siga as etapas abaixo.

1. Fazer logon na sua [conta do Azure](https://portal.azure.com). Se ainda não tem uma conta do Azure, você pode [inscrever-se para uma conta gratuita](https://azure.microsoft.com/free/).
2. Vá para a [folha de Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) e clique em **Novo registro** na barra de ações localizada na parte superior.

    ![novo registro](media/app-registration/new-registration.png)

3. Insira um nome de exibição para o registro de aplicativo no campo *Nome* e selecione os tipos de conta compatíveis. O nome não precisa corresponder à ID do bot.

    > [!IMPORTANT]
    > Nos *Tipos de conta compatíveis*, selecione o botão de opção *Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Xbox, Outlook.com)* . Se qualquer uma das outras opções estiver selecionada, **o bot ficará inutilizável**.

    ![detalhes do registro](media/app-registration/registration-details.png)

4. Clique em **Registrar**

    Após alguns instantes, o registro de aplicativo recém-criado deverá abrir uma folha. Copie a *ID do Aplicativo (cliente)* na folha Visão geral e cole-a no campo ID do Aplicativo.

    ![ID do aplicativo](media/app-registration/app-id.png)

Se você estiver criando seu bot por meio do portal do Bot Framework, a configuração do registro de aplicativo estará concluída e o segredo será gerado automaticamente.

Se você estiver recriando seu bot no portal do Azure, será preciso gerar um segredo para o registro de aplicativo.

1. Clique em **Certificados e segredos** na coluna de navegação à esquerda da folha de registro de aplicativo.
2. Nessa folha, clique no botão **Novo segredo do cliente**. Na caixa de diálogo que aparece, insira uma descrição opcional para o segredo e selecione **Nunca** no grupo de botões de opção Expira em.

    ![novo segredo](media/app-registration/new-secret.png)

3. Copie o valor do seu segredo da tabela sob *Segredos do cliente* e cole-o no campo *Senha* para o seu aplicativo, depois clique em **OK** na parte inferior da folha. Em seguida, prossiga com a criação do bot.

    > [!NOTE]
    > O segredo só ficará visível nessa folha e você não será capaz de recuperá-lo depois de sair dessa página. Copie-o em um local seguro.

    ![nova ID do aplicativo](media/app-registration/create-app-id.png)


[DirectLineAPI]: https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts
[Support]: bot-service-resources-links-help.md
[WebChat]: bot-service-channel-connect-webchat.md

## <a name="deploy-a-bot"></a>Implantar um bot

## <a name="what-files-do-i-need-to-zip-up-for-deployment"></a>Quais arquivos preciso para compactar para implantação?

Você deve criar manualmente um arquivo zip com todos os arquivos no projeto, conforme descrito na [etapa: compactar o diretório de código manualmente](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp#52-zip-up-the-code-directory-manually).

Verifique se você selecionou todos os arquivos e pastas na pasta do projeto do bot. Em seguida, enquanto ainda estiver na pasta do projeto, compacte todos os arquivos e pastas selecionados, conforme mostrado na imagem abaixo.

> [!div class="mx-imgBorder"]
> ![selecionar tudo e zip](./media/deploy-bot-cli/select-all-zip.png)

## <a name="what-version-of-azure-cli-should-i-use-to-deploy-a-bot"></a>Qual versão do CLI do Azure devo usar para implantar um bot?

É recomendável que você use a versão mais recente da CLI (interface de linha de comando) do Azure, caso contrário, você receberá erros de comandos preteridos. Confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="what-should-i-do-when-getting-azure-cli-deprecation-errors"></a>O que devo fazer ao obter CLI do Azure erros de substituição?

Você deve atualizar para a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Para CLI do Azure versão [2.2.0](https://github.com/MicrosoftDocs/azure-docs-cli/blob/master/docs-ref-conceptual/release-notes-azure-cli.md#march-10-2020) ou posterior, você deve usar `az deployment sub create` e `az deployment group create` , em vez de `az deployment create` comandos e, `az group deployment create` respectivamente.

### <a name="change-log-of-the-azure-cli-commands-used-to-deploy-a-bot-to-azure"></a>Log de alterações dos comandos CLI do Azure usados para implantar um bot no Azure

|Versão da ClI do Azure | Command1 | Comando 2|
|-------|-------|-------|
|CLI do Azure 2.2.0 e versões posteriores | `az deployment group create` | `az deployment sub create` |
|CLI do Azure 2.1.0 e versões anteriores | `az group deployment create` | `az deployment create` |

Leia mais na [CLI do Azure log de alterações](https://github.com/MicrosoftDocs/azure-docs-cli/blob/master/docs-ref-conceptual/release-notes-azure-cli.md).

## <a name="what-are-the-cli-deprecated-commands-related-to-az-deployment"></a>Quais são os comandos preteridos da CLI relacionados ao `az deployment` ?

Estes são os `az deployment` comandos preteridos:

> [!div class="mx-tdBreakAll"]
> |Preterido|Substituído por|
> |-------------|----------|
> |`az deployment <create/list show/delete/validate/export/cancel>`|`az deployment **sub** <create/list/show/delete/validate/export/cancel>`|
>| `az deployment operation <list/show>` |`az deployment operation **sub** <list/show>`|
>| `az group deployment <create/list/show/delete/validate/export/cancel>` | `az deployment **group** <create/list/show/delete/validate/export/cancel>` |
> |`az group deployment operation <list/show>` |`az deployment operation **group** < list/show>` |


## <a name="how-do-i-know-whether-the-azure-cli-commands-are-deprecated"></a>Como fazer saber se os comandos de CLI do Azure foram preteridos?

Para saber se um comando CLI do Azure foi preterido, execute o comando com a `-h` opção (ajuda), conforme mostrado na imagem abaixo.  

![ajuda da CLI](./media/deploy-bot-cli/cli-help.png)


### <a name="azure-cli-change-log"></a>CLI do Azure log de alterações

Leia mais sobre [CLI do Azure log de alterações](https://github.com/MicrosoftDocs/azure-docs-cli/blob/master/docs-ref-conceptual/release-notes-azure-cli.md).

### <a name="azure-resource-management-arm"></a>Gerenciamento de recursos do Azure (ARM)

A seguir está uma lista consolidada dos comandos que se ajustam ao design de CLI do Azure atual: AZ {grupo de comandos} {? Subcommand-Group} {Operation} {Parameters}.

* `az resource`: Aprimorar os exemplos do módulo de recurso
* `az policy assignment list`: Suporte à listagem de atribuições de política no escopo do grupo de gerenciamento
* Adicionou `az deployment group` e `az deploymnent operation group` para a implantação de modelos nos grupos de recursos. Esta é uma duplicata de `az group deployment` e `az group deployment operation` . 
* Adicionou `az deployment sub` e `az deployment operation sub` para a implantação de modelos no escopo da assinatura. Esta é uma duplicata de `az deployment` e `az deployment operation` .
* Adicionou `az deployment mg` e `az deployment operation mg` para implantação de modelos nos grupos de gerenciamento. 
* Adicionou `ad deployment tenant` e `az deployment operation tenant` para implantação de modelos no escopo do locatário. 
* az policy assignment create: adicionou descrição ao parâmetro `--location`
* `az group deployment create`: Adicionar parâmetro `--aux-tenants` para dar suporte a locatários cruzados.