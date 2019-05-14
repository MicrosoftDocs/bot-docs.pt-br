---
title: Solucionar problemas de autenticação do Bot Framework | Microsoft Docs
description: Saiba como solucionar erros de autenticação com seu bot.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 04/30/2019
ms.openlocfilehash: 756e24409532de1473e546e3f771be416cb44c78
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033648"
---
# <a name="troubleshooting-bot-framework-authentication"></a>Solucionar problemas de autenticação do Bot Framework

Este guia pode ajudar você a solucionar problemas de autenticação com seu bot por meio da avaliação uma série de cenários para determinar onde há o problema. 

> [!NOTE]
> Para concluir todas as etapas deste guia, é preciso baixar e usar o [Bot Framework Emulator][Emulator] e ter acesso às configurações de registro do bot no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

## <a id="PW"></a> ID de aplicativo e senha

A segurança do bot é configurada pela **ID do aplicativo Microsoft** e pela **Senha do aplicativo Microsoft** que você obtém quando registra seu bot com o Bot Framework. Normalmente, esses valores são especificados no arquivo de configuração do bot e usados para recuperar tokens de acesso do serviço Microsoft Account. 

Se isso ainda não tiver sido feito, [implante seu bot no Azure](~/bot-builder-howto-deploy-azure.md) para obter uma **ID do aplicativo Microsoft** e **Senha do aplicativo Microsoft** que podem ser usados para autenticação. 

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** de um bot já implantado, consulte [MicrosoftAppID e MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

## <a name="step-1-disable-security-and-test-on-localhost"></a>Etapa 1: desabilitar a segurança e testar no localhost

Nesta etapa, você verificará se seu bot fica acessível e funcional no localhost quando a segurança estiver desabilitada. 

> [!WARNING]
> Desabilitar a segurança do seu bot pode permitir que invasores desconhecidos se passem pelos usuários. Só implemente o procedimento a seguir se estiver operando em um ambiente de depuração protegido.

### <a id="disable-security-localhost"></a> Desabilitar a segurança

Para desabilitar a segurança do bot, edite suas definições de configuração para remover os valores da ID e da senha do aplicativo. 

::: moniker range="azure-bot-service-3.0"

Se você estiver usando o SDK do Bot Framework para .NET, edite estas configurações no arquivo Web.config: 

```xml
<appSettings>
  <add key="MicrosoftAppId" value="" />
  <add key="MicrosoftAppPassword" value="" />
</appSettings>
```

Se você estiver usando o SDK do Bot Framework para Node.js, edite estes valores (ou atualize as variáveis de ambiente correspondentes):

```javascript
var connector = new builder.ChatConnector({
  appId: null,
  appPassword: null
});
```

::: moniker-end

::: moniker range="azure-bot-service-4.0"

Se você estiver usando o SDK do Bot Framework para .NET, edite estas configurações no arquivo `appsettings.json`:

```json
  "MicrosoftAppId": "<your app ID>",
  "MicrosoftAppPassword": "<your app password>"
```

Se você estiver usando o SDK do Bot Framework para Node.js, edite estes valores (ou atualize as variáveis de ambiente correspondentes):

```javascript
const adapter = new BotFrameworkAdapter({
    appId: null,
    appPassword: null
});
```

::: moniker-end

### <a name="test-your-bot-on-localhost"></a>Testar o bot no localhost 

Em seguida, teste o bot usando o localhost usando o Emulador do Bot Framework.

1. Inicie o bot no localhost.
2. Instale o Emulador do Bot Framework.
3. Conecte-se ao seu bot usando o emulador.
    - Digite `http://localhost:port-number/api/messages` na barra de endereços do emulador, em que **port-number** corresponde ao número da porta mostrado no navegador em que seu aplicativo está sendo executado. 
    - Verifique se os campos **ID do aplicativo da Microsoft** e **Senha do aplicativo da Microsoft** estão em branco.
    - Clique em **Conectar**.
4. Para testar a conectividade com seu bot, digite algum texto no emulador e pressione Enter.

Se o bot responder à entrada e não houver erros na janela do chat, você acabou de verificar que o bot fica acessível e funcional no localhost quando a segurança está desabilitada. Vá para a [Etapa 2](#step-2).

Se um ou mais erros forem indicados na janela de chat, clique nos erros para obter detalhes. Entre os problemas comuns, há:

* As configurações do emulador especificam um ponto de extremidade incorreto do bot. Verifique se foram incluídos o número da porta apropriada na URL e o caminho apropriado no final da URL (por exemplo, `/api/messages`).
* As configurações do emulador especificam um ponto de extremidade do bot que começa com `https`. No localhost, o ponto de extremidade deve começar com `http`.
* As configurações do emulador especificam um valor para o campo **ID do aplicativo Microsoft** e/ou o campo **Senha do aplicativo Microsoft**. Ambos os campos devem ficar vazios.
* A segurança não foi desabilitada para o bot. [Verifique](#disable-security-localhost) se o bot não especifica um valor para a ID ou a senha do aplicativo.

## <a id="step-2"></a> Etapa 2: verificar a ID e a senha do aplicativo do bot

Nesta etapa, você verificará se a ID e a senha do aplicativo e que seu bot usará para autenticação são válidas. (Caso não saiba quais são esses valores, [obtenha-os](#PW) agora.) 

> [!WARNING]
> As instruções a seguir desabilitam a verificação de SSL para `login.microsoftonline.com`. Só execute esse procedimento em uma rede segura e cogite alterar a senha do aplicativo depois disso.

### <a name="issue-an-http-request-to-the-microsoft-login-service"></a>Emitir uma solicitação HTTP para o serviço de logon da Microsoft

Essas instruções descrevem como usar a [cURL](https://curl.haxx.se/download.html) para emitir uma solicitação HTTP ao serviço de logon da Microsoft. É possível usar uma ferramenta alternativa, como o Postman; apenas verifique se a solicitação está em conformidade com o [protocolo de autenticação](~/rest-api/bot-framework-rest-connector-authentication.md) do Bot Framework.

Para verificar se a ID e senha do aplicativo do seu bot são válidas, emita a solicitação a seguir usando **cURL** e substituindo `APP_ID` e `APP_PASSWORD` pela ID e senha do aplicativo do seu bot.

> [!TIP]
> Sua senha pode conter caracteres especiais que tornam inválida a chamada a seguir. Nesse caso, tente converter sua senha para a codificação de URL.

```cmd
curl -k -X POST https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token -d "grant_type=client_credentials&client_id=APP_ID&client_secret=APP_PASSWORD&scope=https%3A%2F%2Fapi.botframework.com%2F.default"
```

Essa solicitação tenta trocar a ID e senha do aplicativo do seu bot senha por um token de acesso. Se a solicitação for bem-sucedida, você receberá um payload JSON que contém uma propriedade `access_token`, entre outras. 

```json
{"token_type":"Bearer","expires_in":3599,"ext_expires_in":0,"access_token":"eyJ0eXAJKV1Q..."}
```

Se a solicitação for bem-sucedida, você verificou que a ID e a senha do aplicativo especificadas na solicitação são válidas. Vá para a [Etapa 3](#step-3).

Se você receber um erro em resposta à solicitação, examine a resposta para identificar a causa do erro. Se a resposta indicar que a ID ou a senha do aplicativo é inválida, [obtenha os valores corretos](#PW) no Portal do Bot Framework e emita novamente a solicitação com os novos valores para confirmar se eles são válidos. 

## Etapa 3: habilitar a segurança e testar em localhost <a id="step-3"></a>

Neste ponto, você verificou que seu bot fica acessível e funcional no localhost quando a segurança é desabilitada e confirmou que a ID e a senha do aplicativo que o bot usará para autenticação são válidas. Nesta etapa, você verificará se seu bot fica acessível e funcional no localhost quando a segurança estiver habilitada.

### <a id="enable-security-localhost"></a> Habilitar segurança

A segurança do seu bot se baseia em serviços da Microsoft, mesmo quando seu bot é executado somente no localhost. Para habilitar a segurança do bot, edite as definições de configuração para preencher a ID e senha do aplicativo com os valores que você verificou na [Etapa 2](#step-2).  Além disso, verifique se os pacotes estão atualizados, especificamente `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Tokens`.

Se estiver usando o SDK do Bot Framework para .NET, preencha essas configurações em seu `appsettings.config` ou os valores correspondentes no arquivo `appsettings.json`:

```xml
<appSettings>
  <add key="MicrosoftAppId" value="APP_ID" />
  <add key="MicrosoftAppPassword" value="PASSWORD" />
</appSettings>
```

Se você estiver usando o SDK do Bot Framework para Node.js, preencha estas configurações (ou atualize as variáveis de ambiente correspondentes):

```javascript
var connector = new builder.ChatConnector({
  appId: 'APP_ID',
  appPassword: 'PASSWORD'
});
```

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** do seu bot, consulte [MicrosoftAppID e MicrosoftAppPassword](bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

### <a name="test-your-bot-on-localhost"></a>Testar o bot no localhost 

Em seguida, teste o bot usando o localhost usando o Emulador do Bot Framework.

1. Inicie o bot no localhost.
2. Instale o Emulador do Bot Framework.
3. Conecte-se ao seu bot usando o emulador.
    - Digite `http://localhost:port-number/api/messages` na barra de endereços do emulador, em que **port-number** corresponde ao número da porta mostrado no navegador em que seu aplicativo está sendo executado. 
    - Insira a ID do aplicativo do seu bot no campo **ID do aplicativo Microsoft**.
    - Insira a senha do aplicativo do seu bot no campo **Senha do aplicativo Microsoft**.
    - Clique em **Conectar**.
4. Para testar a conectividade com seu bot, digite algum texto no emulador e pressione Enter.

Se o bot responder à entrada e não houver erros na janela do chat, você acabou de verificar que o bot fica acessível e funcional no localhost quando a segurança está habilitada.  Vá para a [Etapa 4](#step-4).

Se um ou mais erros forem indicados na janela de chat, clique nos erros para obter detalhes. Entre os problemas comuns, há:

* As configurações do emulador especificam um ponto de extremidade incorreto do bot. Verifique se foram incluídos o número da porta apropriada na URL e o caminho apropriado no final da URL (por exemplo, `/api/messages`).
* As configurações do emulador especificam um ponto de extremidade do bot que começa com `https`. No localhost, o ponto de extremidade deve começar com `http`.
* Nas configurações do emulador, os campos **ID do aplicativo Microsoft** e/ou **Senha do aplicativo Microsoft** não contêm valores válidos. Ambos os campos devem ser preenchidos, e cada um deve conter o valor correspondente que você verificou ao [Etapa 2](#step-2).
* A segurança não foi habilitada para o bot. [Verifique](#enable-security-localhost) se as definições de configuração do bot especificam valores para a ID e para a senha do aplicativo.

## Etapa 4: testar seu bot na nuvem <a id="step-4"></a>

Neste ponto, você verificou que seu bot fica acessível e funcional no localhost quando a segurança é desabilitada, confirmou que a ID e senha do aplicativo do seu bot são válidas e verificou que seu bot fica acessível e funcional no localhost, quando a segurança é habilitada. Nesta etapa, você implantará seu bot na nuvem e verificará se ele fica acessível e funcional nesse ambiente com a segurança habilitada. 

### <a name="deploy-your-bot-to-the-cloud"></a>Implantar seu bot na nuvem

O Bot Framework exige que bots sejam acessíveis pela Internet, portanto, você deve implantar seu bot em uma plataforma de hospedagem de nuvem, como o Azure. Não se esqueça de habilitar a segurança do bot antes da implantação, conforme descrito em [Etapa 3](#step-3).

> [!NOTE]
> Caso ainda não tenha um provedor de hospedagem de nuvem, é possível se inscrever para uma <a href="https://azure.microsoft.com/free/" target="_blank">conta gratuita</a>. 

Caso implante o bot no Azure, o SSL será configurado automaticamente para o aplicativo, habilitando o ponto de extremidade **HTTPS** que o Bot Framework exige. Se você implantar em outro provedor de hospedagem de nuvem, verifique se o aplicativo está configurado para SSL para que o bot tenha um ponto de extremidade **HTTPS**.

### <a name="test-your-bot"></a>Testar seu bot 

Para testar seu bot na nuvem com a segurança habilitada, conclua as etapas a seguir.

1. Verifique se seu bot foi implantado com êxito e está em execução. 
2. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.
3. Clique em **Meus Bots**.
4. Selecione o bot que você deseja testar.
5. Clique em **Testar** para abrir o bot em um controle de chat da Web incorporado.
6. Para testar a conectividade ao seu bot, digite algum texto no controle de chat da Web e pressione Enter.

Se for indicado um erro na janela de bate-papo, use a mensagem de erro para determinar sua causa. Entre os problemas comuns, há: 

* O **Ponto de extremidade de mensagens** especificado na página **Configurações** de seu bot no Portal do Bot Framework está incorreto. Verifique se foi incluído o caminho apropriado no final da URL (por exemplo, `/api/messages`).
* O **Ponto de extremidade de mensagens** especificado na página **Configurações** de seu bot no Portal do Bot Framework não começa com `https` ou não é confiável de acordo com o Bot Framework. Seu bot deve ter um certificado válido e de cadeia confiável.
* O bot é configurado com valores ausentes ou incorretos para a ID ou senha do aplicativo. [Verifique](#enable-security-localhost) se as definições de configuração do bot especificam valores válidos para a ID e senha do aplicativo.

Se o bot responder apropriadamente à entrada, você verificou que ele fica acessível e funcional na nuvem com a segurança habilitada. Neste ponto, seu bot está pronto para se [conectar a um canal](~/bot-service-manage-channels.md) com segurança, como Skype, Facebook Messenger, Direct Line e outros.

## <a name="additional-resources"></a>Recursos adicionais

Caso ainda esteja enfrentando problemas depois de concluir as etapas acima, você pode:

* Examine as instruções sobre como [depurar um bot](bot-service-debug-bot.md) e outros artigos sobre depuração nesta seção.
* [Depurar seu bot na nuvem](~/bot-service-debug-emulator.md) usando o Bot Framework Emulator e o software de encapsulamento <a href="https://ngrok.com/" target="_blank">ngrok</a>. *O ngrok não é um produto da Microsoft.*
* Usar uma ferramenta de criação de proxies como o [Fiddler](https://www.telerik.com/fiddler) para inspecionar o tráfego HTTPS de entrada e saída do seu bot. *O Fiddler não é um produto da Microsoft.*
* Examine o [Guia de autenticação do conector de Bot][BotConnectorAuthGuide] para saber mais sobre as tecnologias de autenticação que o Bot Framework usa.
* Solicitar ajuda de outras pessoas usando os recursos de [suporte][Support] do Bot Framework. 

[BotConnectorAuthGuide]: ~/rest-api/bot-framework-rest-connector-authentication.md
[Support]: bot-service-resources-links-help.md
[Emulator]: bot-service-debug-emulator.md
