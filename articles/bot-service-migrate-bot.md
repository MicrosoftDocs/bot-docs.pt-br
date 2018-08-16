---
title: Migrar bot para Azure | Microsoft Docs
description: Saiba como migrar o bot do portal do Bot Framework herdado para um serviço de bot no portal do Azure.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 6/22/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6154ac5c77f818192503e5586986b37062b105bf
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574513"
---
# <a name="migrate-your-bot-to-azure"></a>Migrar bot para Azure

[!INCLUDE [pre-release-label](includes/pre-release-label-v3.md)]

Todos os bots do **Serviço de Bot do Azure (versão prévia)** criados no [portal do Bot Framework](http://dev.botframework.com) devem migrar para o novo Serviço de Bot no Azure. O serviço foi disponibilizado de forma geral (GA) em dezembro de 2017. 

Observe que os bots de registro conectados apenas aos canais a seguir *não* precisam migrar: **Teams**, **Skype** ou **Cortana**. Por exemplo, um bot de registro conectado ao **Facebook** e **Skype** *é necessário migrar*, mas um bot de registro conectado ao **Skype** e à **Cortana** *não precisa migrar*.

> [!IMPORTANT]
> Antes de migrar um bot do Azure Functions criado com o Node.js, será necessário usar o **Pacote do Azure Functions** para empacotar os módulos **node_modules** em conjunto. Isso aumentará o desempenho durante a migração e execução do bot do Functions depois que for migrado. Para empacotar os módulos, consulte [Empacotar um bot do Functions com Funcpack](#package-a-functions-bot-with-funcpack).

Para migrar o bot, faça conforme a seguir:

1. Entre no [portal do Bot Framework](http://dev.botframework.com) e clique em **Meus bots**.
2. Clique no botão **Migrar** para o bot que você quer migrar.
3. Aceite os **Termos** e clique em **Migrar** para iniciar o processo de migração ou clique em **Cancelar** para cancelar essa ação.

> [!IMPORTANT]
> Enquanto a tarefa de migração estiver em andamento, não encerre a navegação da página nem atualize a página. Se fizer isso, a tarefa de migração será interrompida prematuramente e será necessário executar a ação novamente. Para garantir que a migração seja concluída com êxito, aguarde a mensagem de confirmação.

Depois que o processo de migração for concluído com êxito, o **Status da migração** indicará que o bot foi migrado e um botão **Reverter migração** estará disponível por uma semana após a data de migração em caso de problemas.

Ao clicar no nome de um bot migrado abrirá o bot no [portal do Azure](http://portal.azure.com).

## <a name="package-a-functions-bot-with-funcpack"></a>Empacotar um bot do Functions com Funcpack

Bots do Functions criados com Node.js deverão ser empacotados usando [Funcpack](https://github.com/Azure/azure-functions-pack) antes da migração. Para usar Funcpack no projeto, siga estas etapas:

1.  [Baixe](bot-service-build-download-source-code.md#download-bot-source-code) o código localmente, caso ainda não tenha feito isso.
2.  Atualize todos os pacotes npm em **packages.json** para obter as últimas versões e, em seguida, execute `npm install`.
3.  Abra **messages/index.js** e altere `module.exports = { default: connector.listen() }` para `module.exports = connector.listen();`
4.  Instale o Funcpack via npm: `npm install -g azure-functions-pack`
5.  Para empacotar o diretório **node_modules** execute o comando a seguir: `funcpack pack ./`
6.  Teste o bot localmente, executando o bot do Functions usando o Bot Framework Emulator. Para obter mais informações sobre como executar o bot do *funcpack*, consulte [aqui](https://github.com/Azure/azure-functions-pack#how-to-run). 
7.  Carregue o código de volta no Azure. Certifique-se de que o diretório `.funcpack` está carregado. Não é necessário fazer upload do diretório **node_modules**.
8. Teste o bot remoto para certificar-se de que responde conforme o esperado.
9. [Migre o bot](#migrate-your-bot-to-azure) usando as etapas acima.

## <a name="migration-under-the-hood"></a>O que acontece na migração

Dependendo do tipo de bot que você está migrando, a lista abaixo pode ajudá-lo a compreender melhor o que está acontecendo.

* **Bot de aplicativo Web** ou **Bot do Functions**: para esses tipos de bots, o projeto do código-fonte é copiado do bot antigo para o novo bot. Outros recursos como armazenamento do bot, Application Insights, LUIS, etc. permanecem como estão. Nesses casos, o novo bot contém uma cópia de IDs/chaves/senhas para esses recursos existentes. 
* **Registro de canais de bots**: para esses tipo de bots, o processo de migração simplesmente cria um novo **Registro de canais de bots** e copia o ponto de extremidade do bot antigo. 
* Independentemente dos tipos de bots que você está migrando, o processo de migração não modifica o estado do bot existente. Isso permite que você reverta com segurança, caso seja necessário.
* Se a [implantação contínua](bot-service-build-continuous-deployment.md) estiver configurada, será necessário configurá-la novamente para que o controle do código-fonte conecte o novo bot.

## <a name="understanding-azure-resources-after-migration"></a>Noções básicas sobre os Recursos do Azure após a migração
Depois que a migração for concluída, o **Grupo de Recursos** conterá vários recursos do Azure necessários para que o bot funcione. O tipo e o número de recursos dependem do tipo de bot que você migrou. Consulte as seções a seguir para saber mais.

### <a name="registration-bot"></a>Bot de registro

Esse é o tipo mais simples. O Grupo de Recursos no Azure conterá apenas um recurso do tipo “Registro de Canal de Bot”. Esse é o equivalente ao registro de bot anterior no Portal do Desenvolvedor do Bot Framework.

![Listagens de bots do Registro de Canal de Bot no Azure](~/media/bot-service-migrate-bot/channel-registration-bot.png)

### <a name="web-app-bot"></a>Bot de aplicativo Web
A migração do bot do Aplicativo Web provisionará um recurso de Serviço de Bot do tipo "Bot de Aplicativo Web" e um novo Aplicativo Web do Serviço de Aplicativo (em verde na captura de tela abaixo). O bot anterior de Serviço de Bot do Azure (versão prévia) ainda permanecerá e poderá ser excluído (em vermelho na captura de tela abaixo).

![Listagens de bots de Aplicativo Web no Azure](~/media/bot-service-migrate-bot/web-app-bot.png)

### <a name="functions-bot"></a>Bot do Functions
A migração do bot do Functions provisionará um recurso de Serviço de Bot do tipo “Bot do Functions” e um novo Aplicativo do Functions do Serviço de Aplicativo (em verde na captura de tela abaixo). O bot anterior de Serviço de Bot do Azure (versão prévia) ainda permanecerá e poderá ser excluído (em vermelho na captura de tela abaixo).

![Listagens de bots do Functions no Azure](~/media/bot-service-migrate-bot/functions-bot.png)


## <a name="roll-back-migration"></a>Reverter migração

Se algum problema ocorrer com o bot durante a migração, ou após ser migrado, será possível **Reverter a migração**. Para reverter uma migração, faça conforme a seguir:

1. Entre no [portal do Bot Framework](http://dev.botframework.com) e clique em **Meus bots**.
2. Clique em **Reverter migração** para o bot que você quer reverter. Um aviso será exibido.
3. Clique em **Sim, reverter** para continuar ou em **Cancelar** para cancelar a ação de reversão.

> [!NOTE]
> A funcionalidade Reverter estará disponível por uma semana após a migração e deverá ser usada somente se você localizar problemas no bot migrado.

## <a name="migration-troubleshootingknown-issues"></a>Solução de problemas de migração/problemas conhecidos
O bot do node.js/functions migrou com êxito, mas falha ao responder:

* **Verificar ponto de extremidade**
  * Acesse a folha **Configurações** no recurso do bot e verifique se o ponto de extremidade de bot tem um parâmetro querystring “code” com um valor exibido. Se não, será necessário adicioná-lo.
* **Verificar a pasta de segredos no kudu para backups**
  * Em alguns casos raros, podem existir arquivos de segredo de backup que estejam criando um conflito. Acesse a pasta **home\data\Functions\secrets** no Kudu e exclua quaisquer arquivos **host.snapshot** (ou **host.backup**) que você localizar. Deverá haver apenas um **host.json** e um **messages.json**. Por fim, reinicie o Serviço de Aplicativo e tente novamente conversar com o bot.

Para qualquer outro problema, envie um CRI ao Suporte do Azure ou registre um problema no [GitHub](https://github.com/MicrosoftDocs/bot-framework-docs/issues).


## <a name="next-steps"></a>Próximas etapas

Agora que o bot foi migrado, saiba como gerenciar o bot no portal do Azure.

> [!div class="nextstepaction"]
> [Gerenciar um bot](bot-service-manage-overview.md)
