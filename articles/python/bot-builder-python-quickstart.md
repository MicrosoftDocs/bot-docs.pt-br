---
title: Criar um bot usando o SDK do Bot Builder para Python | Microsoft Docs
description: Crie rapidamente um bot usando o SDK do Bot Builder para Python.
keywords: SDK do Bot Builder, criar um bot, guia de início rápido, python, introdução
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 02/21/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3df88215141fa513fb2b97b8f13d1cc358660d86
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296592"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-python"></a>Criar um bot com o SDK do Bot Builder para Python
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O SDK do Construtor de Bot para Python é uma estrutura fácil de usar para desenvolvimento de bots. Este guia de início rápido oferece orientações sobre como criar um bot e testá-lo com o Bot Framework Emulator. O SDK v4 está em versão prévia, acesse o [repositório GitHub](https://github.com/Microsoft/botbuilder-python) do Python para saber mais. 

## <a name="pre-requisite"></a>Pré-requisito
- [Python 3.6.4](https://www.python.org/downloads/) 
- [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)

# <a name="create-a-bot"></a>Criar um bot
No arquivo main.py, importe os seguintes módulos padrão:

```python
import http.server
import json
import asyncio
```

E os seguintes módulos de SDK:
```python
from botbuilder.schema import (Activity, ActivityTypes)
from botframework.connector import ConnectorClient
from botframework.connector.auth import (MicrosoftAppCredentials,
                                         JwtTokenValidation, SimpleCredentialProvider)
```
Em seguida, adicione o seguinte código para criar o bot usando o ConnectorClient:
```python
APP_ID = ''
APP_PASSWORD = ''


class BotRequestHandler(http.server.BaseHTTPRequestHandler):

    @staticmethod
    def __create_reply_activity(request_activity, text):
        return Activity(
            type=ActivityTypes.message,
            channel_id=request_activity.channel_id,
            conversation=request_activity.conversation,
            recipient=request_activity.from_property,
            from_property=request_activity.recipient,
            text=text,
            service_url=request_activity.service_url)

    def __handle_conversation_update_activity(self, activity):
        self.send_response(202)
        self.end_headers()
        if activity.members_added[0].id != activity.recipient.id:
            credentials = MicrosoftAppCredentials(APP_ID, APP_PASSWORD)
            reply = BotRequestHandler.__create_reply_activity(activity, 'Hello and welcome to the echo bot!')
            connector = ConnectorClient(credentials, base_url=reply.service_url)
            connector.conversations.send_to_conversation(reply.conversation.id, reply)

    def __handle_message_activity(self, activity):
        self.send_response(200)
        self.end_headers()
        credentials = MicrosoftAppCredentials(APP_ID, APP_PASSWORD)
        connector = ConnectorClient(credentials, base_url=activity.service_url)
        reply = BotRequestHandler.__create_reply_activity(activity, 'You said: %s' % activity.text)
        connector.conversations.send_to_conversation(reply.conversation.id, reply)

    def __handle_authentication(self, activity):
        credential_provider = SimpleCredentialProvider(APP_ID, APP_PASSWORD)
        loop = asyncio.new_event_loop()
        try:
            loop.run_until_complete(JwtTokenValidation.assert_valid_activity(
                activity, self.headers.get("Authorization"), credential_provider))
            return True
        except Exception as ex:
            self.send_response(401, ex)
            self.end_headers()
            return False
        finally:
            loop.close()

    def __unhandled_activity(self):
        self.send_response(404)
        self.end_headers()

    def do_POST(self):
        body = self.rfile.read(int(self.headers['Content-Length']))
        data = json.loads(str(body, 'utf-8'))
        activity = Activity.deserialize(data)

        if not self.__handle_authentication(activity):
            return

        if activity.type == ActivityTypes.conversation_update.value:
            self.__handle_conversation_update_activity(activity)
        elif activity.type == ActivityTypes.message.value:
            self.__handle_message_activity(activity)
        else:
            self.__unhandled_activity()


try:
    SERVER = http.server.HTTPServer(('localhost', 9000), BotRequestHandler)
    print('Started http server on localhost:9000')
    SERVER.serve_forever()
except KeyboardInterrupt:
    print('^C received, shutting down server')
    SERVER.socket.close()
```


Salve o main.py. Para executar o exemplo no Windows, insira o seguinte na janela de linha de comando:
```
python main.py
```
No seu terminal local, você verá a mensagem ‘Servidor http iniciado no localhost:9000’

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar o seu bot

Em seguida, inicie o emulador e conecte-se ao seu bot no emulador:


1. Clique no link **criar uma nova configuração de bot** na guia "Boas-vindas" do emulador. 

2. Insira um **Nome de bot** e digite o caminho do diretório para o seu código de bot. O arquivo de configuração de bot será salvo nesse caminho.

3. Digite `http://localhost:port-number/api/messages` no campo **URL do Ponto de Extremidade**, onde *port-number* corresponde ao número da porta no navegador onde o seu aplicativo está sendo executado.

4. Clique em **Conectar** para se conectar ao seu bot. Você não precisará especificar a **ID do Aplicativo da Microsoft** nem a **Senha do Aplicativo da Microsoft**. Você pode deixar esses campos em branco por enquanto. Você receberá essas informações posteriormente quando registrar o seu bot.

Digite **Olá** no emulador, e o bot responderá **Você disse "Olá"**.

## <a name="next-steps"></a>Próximas etapas

Em seguida, vá diretamente para os conceitos que explicam um bot e como ele funciona.

> [!div class="nextstepaction"]
> [Conceitos básicos do Bot](../v4sdk/bot-builder-basics.md)