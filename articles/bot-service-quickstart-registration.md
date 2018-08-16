---
title: Criar um Registro de Canais de Bot com o Serviço de Bot | Microsoft Docs
description: Saiba como registrar um bot existente com o Serviço de Bot.
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ec16b89a6c25c4113048a40f6b9b3edb6ce2af5e
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296814"
---
# <a name="register-a-bot-with-bot-service"></a>Registrar um bot com o Serviço de Bot
Se você já tiver um bot hospedado em outro lugar e quiser usar o Serviço de Bot para conectá-lo a outros canais, é preciso registrar o seu bot com o Serviço de Bot. Neste tópico, saiba como registrar o seu bot com o Serviço de Bot criando um serviço de bot do **Registro de Canais de Bot**.

> [!IMPORTANT] 
> Você só precisará registrar o seu bot se ele não estiver hospedado no Azure. Se você [criou um bot](bot-service-quickstart.md) pelo Portal do Azure, o seu bot já está registrado com o Serviço de Bot.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no [Portal do Azure](http://portal.azure.com).

> [!TIP]
> Se você ainda não tiver uma assinatura, poderá se inscrever para uma <a href="https://azure.microsoft.com/en-us/free/" target="_blank">conta gratuita</a>.

## <a name="create-a-bot-channels-registration"></a>Criar um Registro de Canais de Bot
Você precisa de um serviço de bot de **Registro de Canais de Bot** para poder usar a funcionalidade do Serviço de Bot. Um bot de registro permite conectar o seu bot aos canais.

Para criar um **Registro de Canais de Bot**, faça o seguinte:

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure e selecione **IA + Serviços Cognitivos > Registro de Canais de Bot**. 

2. Uma nova folha abrirá com informações sobre o **Registro de Canais de Bot**. Clique no botão **Criar** para iniciar o processo de criação. 

3. Na folha **Serviço de Bot**, forneça as informações solicitadas sobre o seu bot conforme especificado na tabela abaixo da imagem.  <br/>
   ![Criar uma folha de bot de registro](~/media/azure-bot-quickstarts/registration-create-bot-service-blade.png)


   |                    Configuração                     |         Valor sugerido         |                                                                                                  DESCRIÇÃO                                                                                                  |
   |------------------------------------------------|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |           <strong>Nome do bot</strong>            |     O nome de exibição do seu bot     |                                                  O nome de exibição do bot que é exibido nos canais e diretórios. Esse nome pode ser alterado a qualquer momento.                                                  |
   |         <strong>Assinatura</strong>          |        Sua assinatura        |                                                                                Selecione a assinatura do Azure que deseja usar.                                                                                 |
   |        <strong>Grupo de recursos</strong>         |         myResourceGroup         |                                 Você pode criar um novo [grupo de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou escolher um existente.                                  |
   |                    Localização                    |             Oeste dos EUA             |                                                        Escolha um local perto de onde o seu bot está implantado ou perto de outros serviços que o seu bot acessará.                                                         |
   |         <strong>Tipo de preços</strong>          |               F0                |             Selecione um tipo de preço. Você pode atualizar a camada de preço a qualquer momento. Para saber mais, veja [Preços do Serviço de Bot](https://azure.microsoft.com/en-us/pricing/details/bot-service/).              |
   |      <strong>Ponto de extremidade de mensagens</strong>       |               URL               |                                                                               Insira a URL do ponto de extremidade de mensagens do seu bot.                                                                                |
   |     <strong>Application Insights</strong>      |               Por                | Decida se você deseja <strong>Ativar</strong> ou <strong>Desativar</strong> o [Application Insights](bot-service-manage-analytics.md). Se você selecionar <strong>Ativar</strong>, será preciso especificar também um local regional. |
   | <strong>ID e senha do Aplicativo da Microsoft</strong> | Criar automaticamente um ID e uma senha de Aplicativo |              Use esta opção se você precisar inserir manualmente um ID e uma senha de Aplicativo da Microsoft. Caso contrário, um novo ID e uma nova senha de Aplicativo da Microsoft serão criados para você no processo de criação do bot.               |


4. Clique em **Criar** para criar o serviço e registrar o ponto de extremidade de mensagens do seu bot.

Confirme que o registro foi criado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique em **Ir para recurso** para abrir a folha de recursos do bot. 

## <a name="bot-channels-registration-password"></a>Senha do Registro de Canais de Bot

O serviço de bot do **Registro de Canais de Bot** não tem um serviço de aplicativo associado a ele. Por causa disso, o serviço de bot só tem apenas um *MicrosoftAppID*. Você precisa gerar a senha manualmente e salvá-la por conta própria. Você precisará dessa senha se quiser testar o seu bot usando o [emulador ](bot-service-debug-emulator.md).

Para gerar uma MicrosoftAppPassword, faça o seguinte:

1. Na folha **Configurações**, clique em **Gerenciar**. Este é o link que é exibido pelo **ID do Aplicativo da Microsoft**. Este link abrirá uma janela onde você pode gerar uma nova senha. <br/>
  ![Gerenciar o link na folha Configurações](~/media/azure-bot-quickstarts/registration-settings-manage-link.png)

2. Clique em **Gerar Nova Senha**. Isso irá gerar uma nova senha para o seu bot. Copie esta senha e salve-a em um arquivo. Esta é a única vez que você verá a senha. Se você não tiver a senha completa salva, será preciso repetir o processo para criar uma nova senha caso precise dela posteriormente. <br/>
  ![Gerar Senha de Aplicativo da Microsoft](~/media/azure-bot-quickstarts/registration-generate-app-password.png)

## <a name="update-the-bot"></a>Atualizar o bot

Se você estiver usando o SDK do Construtor de Bot para Node.js, defina as seguintes variáveis de ambiente:

* MICROSOFT_APP_ID
* MICROSOFT_APP_PASSWORD

Se você estiver usando o SDK do Construtor de Bot para .NET, defina os seguintes valores de chave no arquivo web.config:

* MicrosoftAppId
* MicrosoftAppPassword

## <a name="test-the-bot"></a>Testar o bot

Agora que o serviço de bot foi criado, [teste-o no Webchat](bot-service-manage-test-webchat.md). Digite uma mensagem e o bot deverá responder.

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu como registrar o seu bot hospedado no Serviço de Bot. A próxima etapa é saber como gerenciar seu Serviço de Bot.

> [!div class="nextstepaction"]
> [Gerenciar um bot](bot-service-manage-overview.md)

