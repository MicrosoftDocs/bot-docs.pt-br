---
title: Criar um bot usando o SDK do Bot Builder para Java | Microsoft Docs
description: Crie rapidamente um bot usando o SDK do Bot Builder para Java.
keywords: SDK do Bot Builder, criar um bot, guia de início rápido, java, introdução
author: jonathanfingold
ms.author: jonathanfingold
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 05/02/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ca4714d3b3988fd08021f55a4905d9426996b7eb
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39296828"
---
# <a name="create-a-bot-with-the-bot-builder-sdk-for-java"></a>Criar um bot com o SDK do Bot Builder para Java
[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

O SDK do Bot Builder para Java fornece uma maneira familiar para desenvolvedores de Java escreverem os bots. O SDK v4 está em versão prévia, para obter mais informações, visite o [repositório GitHub](https://github.com/Microsoft/botbuilder-java) do Java.

> [!NOTE]
> No momento, nossos exemplos de código e documentos se destinam ao Java versão 1.8.

## <a name="getting-started"></a>Introdução

O SDK v4 consiste em uma série de [bibliotecas](https://github.com/Microsoft/botbuilder-java/tree/master/libraries). Para criá-los localmente, veja [Como criar o SDK](https://github.com/Microsoft/botbuilder-java/wiki/building-the-sdk).

- Instalar o [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases)

### <a name="create-echobot"></a>Criar EchoBot

No arquivo App.java, adicione o seguinte:

```Java
package com.microsoft.bot.connector.sample;

import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.microsoft.aad.adal4j.AuthenticationException;
import com.microsoft.bot.connector.customizations.CredentialProvider;
import com.microsoft.bot.connector.customizations.CredentialProviderImpl;
import com.microsoft.bot.connector.customizations.JwtTokenValidation;
import com.microsoft.bot.connector.customizations.MicrosoftAppCredentials;
import com.microsoft.bot.connector.implementation.ConnectorClientImpl;
import com.microsoft.bot.schema.models.Activity;
import com.microsoft.bot.schema.models.ActivityTypes;
import com.microsoft.bot.schema.models.ResourceResponse;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpServer;

import java.io.IOException;
import java.io.InputStream;
import java.net.InetSocketAddress;
import java.net.URLDecoder;
import java.util.logging.Level;
import java.util.logging.Logger;

public class App {
    private static final Logger LOGGER = Logger.getLogger( App.class.getName() );
    private static String appId = "";       // <-- app id -->
    private static String appPassword = ""; // <-- app password -->

    public static void main( String[] args ) throws IOException {
        CredentialProvider credentialProvider = new CredentialProviderImpl(appId, appPassword);
        HttpServer server = HttpServer.create(new InetSocketAddress(3978), 0);
        server.createContext("/api/messages", new MessageHandle(credentialProvider));
        server.setExecutor(null);
        server.start();
    }

    static class MessageHandle implements HttpHandler {
        private ObjectMapper objectMapper;
        private CredentialProvider credentialProvider;
        private MicrosoftAppCredentials credentials;

        MessageHandle(CredentialProvider credentialProvider) {
            this.objectMapper = new ObjectMapper()
                    .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                    .findAndRegisterModules();
            this.credentialProvider = credentialProvider;
            this.credentials = new MicrosoftAppCredentials(appId, appPassword);
        }

        public void handle(HttpExchange httpExchange) throws IOException {
            if (httpExchange.getRequestMethod().equalsIgnoreCase("POST")) {
                Activity activity = getActivity(httpExchange);
                String authHeader = httpExchange.getRequestHeaders().getFirst("Authorization");
                try {
                    JwtTokenValidation.assertValidActivity(activity, authHeader, credentialProvider);

                    // send ack to user activity
                    httpExchange.sendResponseHeaders(202, 0);
                    httpExchange.getResponseBody().close();

                    if (activity.type().equals(ActivityTypes.MESSAGE)) {
                        // reply activity with the same text
                        ConnectorClientImpl connector = new ConnectorClientImpl(activity.serviceUrl(), this.credentials);
                        ResourceResponse response = connector.conversations().sendToConversation(activity.conversation().id(),
                                new Activity()
                                        .withType(ActivityTypes.MESSAGE)
                                        .withText("Echo: " + activity.text())
                                        .withRecipient(activity.from())
                                        .withFrom(activity.recipient())
                        );
                    }
                } catch (AuthenticationException ex) {
                    httpExchange.sendResponseHeaders(401, 0);
                    httpExchange.getResponseBody().close();
                    LOGGER.log(Level.WARNING, "Auth failed!", ex);
                } catch (Exception ex) {
                    LOGGER.log(Level.WARNING, "Execution failed", ex);
                }
            }
        }

        private String getRequestBody(HttpExchange httpExchange) throws IOException {
            StringBuilder buffer = new StringBuilder();
            InputStream stream = httpExchange.getRequestBody();
            int rByte;
            while ((rByte = stream.read()) != -1) {
                buffer.append((char)rByte);
            }
            stream.close();
            if (buffer.length() > 0) {
                return URLDecoder.decode(buffer.toString(), "UTF-8");
            }
            return "";
        }

        private Activity getActivity(HttpExchange httpExchange) {
            try {
                String body = getRequestBody(httpExchange);
                LOGGER.log(Level.INFO, body);
                return objectMapper.readValue(body, Activity.class);
            } catch (Exception ex) {
                LOGGER.log(Level.WARNING, "Failed to get activity", ex);
                return null;
            }

        }
    }
}
```

Se estiver usando o Maven, você poderá copiar o arquivo pom.xml da pasta samples nesse repositório. Depois de iniciar a execução de seu executável, inicie o Emulador do Bot Framework.

### <a name="start-the-emulator-and-connect-your-bot"></a>Iniciar o emulador e conectar seu bot

Neste ponto, seu bot está em execução localmente.
Em seguida, inicie o emulador e conecte-se ao seu bot no emulador:

1. Clique no link **criar uma nova configuração de bot** na guia "Boas-vindas" do emulador. 

2. Insira um **Nome de Bot** e digite o caminho do diretório para seu código de bot. O arquivo de configuração de bot será salvo nesse caminho.

3. Digite `http://localhost:port-number/api/messages` no campo **URL do Ponto de Extremidade**, onde *port-number* corresponde ao número da porta no navegador onde seu aplicativo está sendo executado.

4. Clique em **Conectar** para se conectar ao seu bot. Você não precisará especificar a **ID do Aplicativo Microsoft** nem a **Senha do Aplicativo Microsoft**. Você pode deixar esses campos em branco por enquanto. Você receberá essas informações posteriormente quando registrar seu bot.

### <a name="interact-with-your-bot"></a>Interagir com seu bot
Envie "Olá" para seu bot e o bot ecoará a mensagem de volta.

## <a name="next-steps"></a>Próximas etapas

Em seguida, vá diretamente para os conceitos que explicam um bot e como ele funciona.

> [!div class="nextstepaction"]
> [Conceitos básicos de Bot](../v4sdk/bot-builder-basics.md)
