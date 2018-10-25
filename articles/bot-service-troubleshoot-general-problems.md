---
title: Solucionar problemas de bots | Microsoft Docs
description: Solucione problemas gerais no desenvolvimento de bots usando as perguntas técnicas frequentes.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/26/2018
ms.openlocfilehash: 3d9c41d0c0c51d00dc5ce86dfb774228e53ff3a3
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49999063"
---
# <a name="troubleshooting-general-problems"></a>Solução de problemas gerais
Essas perguntas frequentes podem ajudá-lo a solucionar problemas comuns de desenvolvimento de bot ou operacionais.

## <a name="how-can-i-troubleshoot-issues-with-my-bot"></a>Como é possível solucionar problemas com o bot?

1. Depure o código-fonte do bot com [Visual Studio Code](debug-bots-locally-vscode.md) ou [Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/navigating-through-code-with-the-debugger?view=vs-2017).
2. Teste o bot usando o [emulador](bot-service-debug-emulator.md) antes de implantá-lo na nuvem.
3. Implante o bot em uma plataforma de hospedagem na nuvem, como o Azure e, em seguida, teste a conectividade com o bot usando o controle de webchat interno no painel do bot no <a href="https://dev.botframework.com" target="_blank">portal do Bot Framework</a>. Se você encontrar problemas com o bot depois de implantá-lo no Azure, considere usar este artigo de blog: [Entendendo a solução de problemas e suporte do Azure](https://azure.microsoft.com/en-us/blog/understanding-azure-troubleshooting-and-support/).
4. Exclua a [autenticação][TroubleshootingAuth] como um possível problema.
5. Teste o bot no Skype. Isso irá ajudá-lo a validar a experiência do usuário de ponta a ponta.
6. Considere testar o bot em canais que tenham requisitos de autenticação adicionais como Direct Line ou Webchat.

## <a name="how-can-i-troubleshoot-authentication-issues"></a>Como é possível solucionar problemas de autenticação?

Para obter detalhes sobre como solucionar problemas de autenticação com o bot, consulte [solucionar problemas][TroubleshootingAuth] de autenticação do Bot Framework.

## <a name="im-using-the-bot-builder-sdk-for-net-how-can-i-troubleshoot-issues-with-my-bot"></a>Estou usando o SDK do Bot Builder para .NET. Como é possível solucionar problemas com o bot?

**Procure exceções.**  
No Visual Studio 2017, acesse **Depurara** > **Windows** > **Configurações de Exceção**. Na janela **Configurações de Exceção**, marque a caixa de seleção **Interromper Quando Lançado** próximo a **Exceções do Common Language Runtime**. Também é possível ver a saída de diagnóstico na janela Saída quando houver exceções lançadas ou não tratadas.

**Verifique a pilha de chamadas.**  
No Visual Studio, é possível escolher se está depurando [Apenas Meu Código](https://msdn.microsoft.com/en-us/library/dn457346.aspx) ou não. Examinar a pilha de chamadas completa pode fornecer informações adicionais sobre quaisquer problemas.

**Assegure-se de que todos os métodos de diálogo terminem com um plano para manipular a próxima mensagem.**  
Todas as etapas do diálogo precisam alimentar a próxima etapa da cascata, ou encerrar o diálogo atual para retirá-lo na pilha. Se uma etapa não for tratada corretamente, a conversa não continuará como você espera. Dê uma olhada no artigo de conceito sobre [diálogos](v4sdk/bot-builder-concept-dialog.md) para saber mais sobre diálogos.

## <a name="why-doesnt-the-typing-activity-do-anything"></a>Por que a atividade de Digitação não gera nenhuma ação?
Alguns canais não dão suporte a atualizações de digitação transitórias nos clientes.

## <a name="what-is-the-difference-between-the-connector-library-and-builder-library-in-the-sdk"></a>Qual é a diferença entre a biblioteca do Connector e a biblioteca do Builder no SDK?

A biblioteca do Connector é a exposição da API REST. A biblioteca do Builder inclui o modelo de programação de diálogo conversacional e outros recursos, como prompts, cascatas, cadeias e preenchimento de formulário guiado. A biblioteca do Builder também fornece acesso a serviços cognitivos como o LUIS.

## <a name="what-causes-an-error-with-http-status-code-429-too-many-requests"></a>O que causa um erro com o código de status HTTP 429 "Muitas solicitações"?

Uma resposta de erro com o código de status HTTP 429 indica que muitas solicitações foram emitidas em um determinado período de tempo. O corpo da resposta deve incluir uma explicação do problema e também especificar o intervalo mínimo requerido entre as solicitações. Uma possível fonte para esse erro é [ngrok](https://ngrok.com/). Se estiver em um plano gratuito e executando nos limites do ngrok, acesse a página de limites e preços no site para obter mais [opções](https://ngrok.com/product#pricing). 

## <a name="why-arent-my-bot-messages-getting-received-by-the-user"></a>Por que as mensagens do meu bot não estão sendo recebidas pelo usuário?

A atividade de mensagem gerada em resposta deve ser encaminhada corretamente, caso contrário, não chegará ao destino pretendido. Na grande maioria dos casos, você não precisará lidar com isso explicitamente. O SDK se encarrega do encaminhamento da atividade de mensagem para você. 

Tratar corretamente uma atividade significa incluir os detalhes de *referência de conversa* apropriados juntamente com detalhes sobre o remetente e o destinatário. Na maioria dos casos, a atividade de mensagem é enviada em resposta a uma que chegou. Portanto, os detalhes de encaminhamento podem ser retirados da atividade de entrada. 

Se você examinar os rastreamentos ou logs de auditoria, poderá verificar se as mensagens foram encaminhadas corretamente. Se não tiverem sido, defina um ponto de interrupção em seu bot e veja onde as IDs estão sendo definidas para a mensagem.

## <a name="how-can-i-run-background-tasks-in-aspnet"></a>Como é possível executar tarefas em segundo plano no ASP.NET? 

Em alguns casos, convém iniciar uma tarefa assíncrona que aguarda alguns segundos e, em seguida, executa algum código para limpar o perfil do usuário ou redefinir o estado de diálogo/conversa. Para obter detalhes sobre como realizar isso, consulte [Como executar tarefas em segundo plano no ASP.NET](https://www.hanselman.com/blog/HowToRunBackgroundTasksInASPNET.aspx). Em particular, considere o uso de [HostingEnvironment.QueueBackgroundWorkItem](https://msdn.microsoft.com/en-us/library/dn636893(v=vs.110).aspx). 


## <a name="how-do-user-messages-relate-to-https-method-calls"></a>Como as mensagens do usuário estão relacionadas às chamadas de método HTTPS?

Quando o usuário enviar uma mensagem através de um canal, o serviço Web do Bot Framework emitirá um HTTPS POST para o ponto de extremidade do serviço Web do bot. O bot pode enviar zero, uma ou muitas mensagens de volta ao usuário naquele canal, emitindo um HTTPS POST separado para o Bot Framework para cada mensagem que envia.

## <a name="my-bot-is-slow-to-respond-to-the-first-message-it-receives-how-can-i-make-it-faster"></a>O bot demora a responder à primeira mensagem que recebe. Como é possível torná-lo mais rápido?

Os bots são serviços Web e algumas plataformas de hospedagem, incluindo o Azure, colocam automaticamente o serviço em suspensão se não receberem tráfego por um determinado período de tempo. Se isso acontecer com o bot, ele deverá reiniciar a partir do zero na próxima vez em que receber uma mensagem, o que tornará a resposta muito mais lenta do que se já estivesse em execução.

Algumas plataformas de hospedagem permitem que você configure o serviço para que não seja colocado em suspensão. Para fazer isso no Azure, navegue até o serviço do bot no [portal do Azure](https://portal.azure.com), selecione **Configurações de aplicativo** e, em seguida selecione **AlwaysOn**. Essa opção está disponível na maioria dos planos de serviço, mas não em todos.

## <a name="how-can-i-guarantee-message-delivery-order"></a>Como é possível garantir a ordem de entrega da mensagem?

O Bot Framework irá preservar a ordem das mensagens, tanto quanto possível. Por exemplo, se você enviar a mensagem A e aguardar a conclusão dessa operação HTTP antes de iniciar outra operação HTTP para enviar a mensagem B, o Bot Framework reconhecerá automaticamente que a mensagem A deve preceder a mensagem B. No entanto, em geral, a ordem de entrega da mensagem não pode ser garantida, pois o canal é o responsável, em última instância, pela entrega da mensagem e pode reordenar as mensagens. Para reduzir o risco das mensagens serem entregues na ordem errada, é possível implementar um intervalo de tempo entre as mensagens.

## <a name="how-can-i-intercept-all-messages-between-the-user-and-my-bot"></a>Como é possível interceptar todas as mensagens entre o usuário e o bot?

Usando o SDK do Bot Builder para .NET, é possível fornecer implementações das interfaces `IPostToBot` e `IBotToUser` interfaces para o contêiner de injeção de dependência`Autofac`. Com o SDK do Bot Builder para qualquer linguagem, é possível usar o middleware para a mesma finalidade. O repositório [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure) contém as bibliotecas do C# e Node.js que registrarão esses dados em uma tabela do Azure.

## <a name="why-are-parts-of-my-message-text-being-dropped"></a>Por que partes do texto da mensagem estão sendo removidas?

O Bot Framework e muitos canais interpretam o texto como se estivesse formatado com [Markdown](https://en.wikipedia.org/wiki/Markdown). Verifique se o texto contém caracteres que podem ser interpretados como sintaxe de Markdown.

## <a name="how-can-i-support-multiple-bots-at-the-same-bot-service-endpoint"></a>Como dar suporte a vários bots no mesmo ponto de extremidade de serviço de bot? 

Esse [exemplo](https://github.com/Microsoft/BotBuilder/issues/2258#issuecomment-280506334) mostra como configurar o `Conversation.Container` com o `MicrosoftAppCredentials` correto e usar um `MultiCredentialProvider` simples para autenticar várias IDs do Aplicativo e senhas.

## <a name="identifiers"></a>Identificadores

## <a name="how-do-identifiers-work-in-the-bot-framework"></a>Como os identificadores funcionam no Bot Framework?

Para mais detalhes sobre os identificadores no Bot Framework, consulte o [guia para identificadores][BotFrameworkIDGuide] do Bot Framework.

## <a name="how-can-i-get-access-to-the-user-id"></a>Como é possível obter acesso à ID de usuário?

SMS e mensagens de email fornecerão a ID de usuário bruta na propriedade `from.Id`. Em mensagens do Skype, a propriedade `from.Id` conterá uma ID exclusiva para o usuário, que difere da ID do Skype do usuário. Se for necessário conectar uma conta existente, você poderá usar um cartão de entrada e implementar seu próprio fluxo do OAuth para conectar a ID de usuário à ID de usuário do seu próprio serviço.

## <a name="why-are-my-facebook-user-names-not-showing-anymore"></a>Por que meus nomes de usuário do Facebook não estão mais sendo exibidos?

Você alterou a senha do Facebook? Ao fazer isso, invalidará o token de acesso e será necessário atualizar as configurações do bot para o canal do Facebook Messenger no <a href="https://dev.botframework.com" target="_blank">portal do Bot Framework</a>.

## <a name="why-is-my-kik-bot-replying-im-sorry-i-cant-talk-right-now"></a>Por que meu bot no Kik está respondendo "Desculpe, não posso falar agora"?

São permitidos 50 assinantes para bots em desenvolvimento no Kik. Após 50 usuários exclusivos terem interagido com o bot, qualquer novo usuário que tentar conversar com o bot receberá a mensagem "Desculpe, não posso falar agora." Para obter mais informações, consulte [Documentação do Kik](https://botsupport.kik.com/hc/en-us/articles/225764648-How-can-I-share-my-bot-with-Kik-users-while-in-development-).

## <a name="how-can-i-use-authenticated-services-from-my-bot"></a>Como é possível usar serviços autenticados do meu bot?

Para a autenticação do Azure Active Directory, consulte Adicionar autenticação [V3](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-3.0&tabs=csharp) | [V4](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-4.0&tabs=csharp). 

> [!NOTE] 
> Se você adicionar a funcionalidade de autenticação e segurança ao bot, certifique-se de que os padrões implementados no código estejam em conformidade com os padrões de segurança apropriados ao aplicativo.

## <a name="how-can-i-limit-access-to-my-bot-to-a-pre-determined-list-of-users"></a>Como é possível limitar o acesso ao bot para uma lista pré-determinada de usuários?

Alguns canais, como SMS e email, fornecem endereços sem escopo. Nesses casos, as mensagens do usuário conterão a ID de usuário bruta na propriedade `from.Id`.

Outros canais, como Skype, Facebook e Slack, fornecem endereços com escopo ou locados de forma a impedir que um bot possa prever a ID de um usuário com antecedência. Nesses casos, você precisará autenticar o usuário por meio de um link de logon ou segredo compartilhado para determinar se estão ou não autorizados a usar o bot.

## <a name="why-does-my-direct-line-11-conversation-start-over-after-every-message"></a>Por que minha conversa com Direct Line 1.1 inicia novamente após cada mensagem?

Se a conversa com Direct Line parece iniciar novamente após cada mensagem, a propriedade `from` provavelmente está ausente ou `null` nas mensagens que o cliente Direct Line enviou para o bot. Quando um cliente Direct Line enviar uma mensagem com a propriedade `from` ausente ou `null`, o serviço Direct Line alocará automaticamente uma ID, portanto, todas as mensagens enviadas pelo cliente parecerão originar de um novo usuário diferente.

Para corrigir isso, defina a propriedade `from` em cada mensagem que o cliente Direct Line envia para um valor estável que represente exclusivamente o usuário que está enviando a mensagem. Por exemplo, se um usuário já estiver conectado a um aplicativo ou página da Web, será possível usar essa ID de usuário existente como o valor da propriedade `from` nas mensagens que o usuário enviar. Como alternativa, é possível optar por gerar uma ID de usuário aleatória no carregamento da página ou carregamento do aplicativo, armazenar a ID em um estado do dispositivo ou cookie e usar essa ID como o valor da propriedade `from` nas mensagens que o usuário enviar.

## <a name="what-causes-the-direct-line-30-service-to-respond-with-http-status-code-502-bad-gateway"></a>O que faz com que o serviço Direct Line 3.0 responda com o código de status HTTP 502 "Gateway Incorreto"?
O Direct Line 3.0 retorna o código de status HTTP 502 quando tenta contatar o bot mas a solicitação não é concluída com êxito. Esse erro indica que o bot retornou um erro ou a solicitação atingiu o tempo limite. Para obter mais informações sobre erros que o bot gera, acesse o painel do bot no <a href="https://dev.botframework.com" target="_blank">portal do Bot Framework</a> e clique no link "Problemas" do canal afetado. Se você tiver o Application Insights configurado para o bot, também poderá localizar informações detalhadas sobre erros. 

::: moniker range="azure-bot-service-3.0"

## <a name="what-is-the-idialogstackforward-method-in-the-bot-builder-sdk-for-net"></a>O que é o método IDialogStack.Forward no SDK do Bot Builder para .NET?

A finalidade principal de `IDialogStack.Forward` é reutilizar um diálogo filho existente que seja frequentemente "reativo", em que o diálogo filho (em `IDialog.StartAsync`) aguarda um objeto `T` com algum manipulador `ResumeAfter`. Em particular, se você tiver um diálogo filho que aguarda um `IMessageActivity` `T`, poderá encaminhar `IMessageActivity` de entrada (já recebido por algum diálogo pai) usando o método `IDialogStack.Forward`. Por exemplo, para encaminhar um `IMessageActivity` de entrada para um `LuisDialog`, chame `IDialogStack.Forward` para efetuar push de `LuisDialog` para a pilha de diálogo, execute o código em `LuisDialog.StartAsync` até agendar uma espera pela próxima mensagem e, em seguida, imediatamente satisfazer essa espera com o `IMessageActivity` encaminhado.

`T` é geralmente um `IMessageActivity`, uma vez que `IDialog.StartAsync` é normalmente construído para aguardar esse tipo de atividade. É possível usar `IDialogStack.Forward` como `LuisDialog` como um mecanismo para interceptar mensagens do usuário para algum processamento antes de encaminhar a mensagem para um `LuisDialog` existente. Alternativamente, também é possível usar `DispatchDialog` com `ContinueToNextGroup` para essa finalidade.

Você esperaria encontrar o item encaminhado no primeiro manipulador `ResumeAfter` (p. ex. `LuisDialog.MessageReceived`) agendado por `StartAsync`.

::: moniker-end

## <a name="what-is-the-difference-between-proactive-and-reactive"></a>Qual é a diferença entre "proativo" e "reativo"?

Do ponto de vista do bot, "reativo" significa que o usuário inicia a conversa enviando uma mensagem para o bot e o bot reage respondendo a essa mensagem. Em contraste, "proativo" significa que o bot inicia a conversa, enviando a primeira mensagem ao usuário. Por exemplo, um bot pode enviar uma mensagem proativa para notificar um usuário quando um temporizador expirar ou ocorrer um evento.

## <a name="how-can-i-send-proactive-messages-to-the-user"></a>Como enviar mensagens proativas ao usuário?

Para obter exemplos que mostram como enviar mensagens proativas, consulte [Exemplos em C# V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) e [Exemplos em Node.js V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) no repositório BotBuilder-Samples no GitHub.

## <a name="how-can-i-reference-non-serializable-services-from-my-c-dialogs"></a>Como é possível referenciar serviços não serializáveis dos meus diálogos em C#?

Há várias opções:

* Resolva a dependência através de `Autofac` e `FiberModule.Key_DoNotSerialize`. Essa é a solução mais limpa.
* Use atributos [NonSerialized](https://msdn.microsoft.com/en-us/library/system.nonserializedattribute(v=vs.110).aspx) e [OnDeserialized](https://msdn.microsoft.com/en-us/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx) para restaurar a dependência da desserialização. Essa é a solução mais simples.
* Não armazene essa dependência, para que não seja serializada. Essa solução, embora tecnicamente viável, não é recomendável.
* Use o substituto de serialização de reflexão. Essa solução pode não ser viável em alguns casos e corre o risco de serializar demasiadamente.

::: moniker range="azure-bot-service-3.0"

## <a name="where-is-conversation-state-stored"></a>Onde o estado de conversa é armazenado?

Os dados nos recipientes de propriedades de conversa privada, conversa e usuário são armazenados usando a interface `IBotState` do Connector. Cada recipiente de propriedades é delimitado pela ID do bot. O recipiente de propriedades do usuário é inserido pela ID de usuário, o recipiente de propriedades de conversa é inserido pela ID da conversa e o recipiente de propriedades de conversa privada é inserida por ambas ID de usuário e ID da conversa. 

Se você usar o SDK do Bot Builder para .NET ou SDK do Bot Builder para Node.js para compilar o bot, a pilha de diálogo e os dados de diálogo serão automaticamente armazenados como entradas no recipiente de propriedades de conversa privada. A implementação do C# usa serialização binária e a implementação do Node.js usa a serialização JSON.

A interface REST `IBotState` é implementada por dois serviços.

* O Bot Framework Connector fornece um serviço de nuvem que implementa essa interface e armazena dados no Azure.  Esses dados são criptografados em repouso e não expiram intencionalmente.
* O Bot Framework Emulator fornece uma implementação na memória dessa interface para depurar o bot. Esses dados expiram quando o processo do emulador encerra.

Se quiser armazenar esses dados nos datacenters, forneça uma implementação personalizada do serviço do estado. Isso pode ser feito pelo menos de duas maneiras:

* Use a camada REST para fornecer um serviço `IBotState`.
* Use as interfaces do Builder na camada de linguagem (Node.js ou C#).

> [!IMPORTANT]
> A API do Serviço do Estado do Bot Framework não é recomendável para ambientes de produção e poderá ser preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para saber mais, consulte o tópico **Gerenciar dados de estado** para implementação de [.NET](~/dotnet/bot-builder-dotnet-state.md) ou [Node](~/nodejs/bot-builder-nodejs-state.md).

::: moniker-end

## <a name="what-is-an-etag--how-does-it-relate-to-bot-data-bag-storage"></a>O que é um ETag?  Qual a relação disso com o armazenamento de recipiente de dados de bot?

Um [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) é um mecanismo para [controle de simultaneidade otimista](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). O armazenamento de recipiente de dados de bot usa ETags para impedir atualizações conflitantes nos dados. Um erro de ETag com código de status HTTP 412 "Falha na Pré-Condição" indica que havia várias sequências de "leitura-modificação-gravação" executando simultaneamente para esse recipiente de dados de bot.

O estado e a pilha de diálogo são armazenados em recipientes de dados de bot. Por exemplo, será possível ver o erro de ETag "Falha na Pré-Condição" se o bot ainda estiver processando uma mensagem anterior quando receber uma nova mensagem para essa conversa.

## <a name="what-causes-an-error-with-http-status-code-412-precondition-failed-or-http-status-code-409-conflict"></a>O que causa um erro com o código de status HTTP 412 "Falha na Pré-Condição" ou código de status HTTP 409 "Conflito"?

O serviço `IBotState` do Connector é usado para armazenar os recipientes de dados de bot (ou seja, o usuário, a conversa e os recipientes de dados de bot privados, em que os recipientes de dados de bot privados incluem o estado de "fluxo de controle" da pilha de diálogo). O controle de simultaneidade no serviço `IBotState` é gerenciado pela simultaneidade otimista via ETags. Se houver um conflito de atualização (devido a uma atualização simultânea de um único recipientes de dados de bot) durante uma sequência "leitura-modificação-gravação", então:

* Se os ETags forem preservados, um erro com o código de status HTTP 412 "Falha na Pré-Condição" será lançado do serviço `IBotState`. Esse é o comportamento padrão no SDK do Bot Builder para .NET.
* Se os ETags não forem preservados (ou seja, o ETag estiver definido como `\*`), a política "última gravação vence" estará em vigor, o que evitará o erro "Falha na Pré-Condição", mas arriscará em perda de dados. Esse é o comportamento padrão no SDK do Bot Builder para Node.js.

## <a name="how-can-i-fix-precondition-failed-412-or-conflict-409-errors"></a>Como corrigir erros de "Falha na Pré-Condição" (412) ou "Conflito" (409)?

Esses erros indicam que o bot processou várias mensagens para a mesma conversa de uma só vez. Se o bot estiver conectado a serviços que exigem mensagens ordenadas com precisão, considere bloquear o estado de conversa para garantir que as mensagens não sejam processadas em paralelo. 

::: moniker range="azure-bot-service-3.0"

O SDK do Bot Builder para .NET fornece um mecanismo (classe `LocalMutualExclusion` que implementa `IScope`) para serializar pessimisticamente a manipulação de uma conversa única com um sinal na memória. É possível estender essa implementação para usar uma concessão do Redis, com escopo definido pelo endereço da conversa.

Se o bot não estiver conectado a serviços externos ou se o processamento de mensagens em paralelo a partir da mesma conversa for aceitável, você poderá adicionar esse código para ignorar quaisquer colisões que ocorram na API de Estado do Bot. Isso permitirá que a última resposta defina o estado de conversa.

```cs
var builder = new ContainerBuilder();
builder
    .Register(c => new CachingBotDataStore(c.Resolve<ConnectorStore>(), CachingBotDataStoreConsistencyPolicy.LastWriteWins))
    .As<IBotDataStore<BotData>>()
    .AsSelf()
    .InstancePerLifetimeScope();
builder.Update(Conversation.Container);
```
::: moniker-end

## <a name="is-there-a-limit-on-the-amount-of-data-i-can-store-using-the-state-api"></a>Há um limite na quantidade de dados que posso armazenar usando a API de Estado?

Sim, cada armazenamento de estado (ou seja, usuário, conversa e recipientes de dados de bot privados) pode conter até 64 KB de dados. Para obter mais informações, consulte [Gerenciar dados de estado][StateAPI].

::: moniker range="azure-bot-service-3.0"

## <a name="how-do-i-version-the-bot-data-stored-through-the-state-api"></a>Como fazer a versão dos dados de bot armazenados através da API de Estado?

> [!IMPORTANT]
> A API de Serviço de Estado do Bot Framework não é recomendada para ambientes de produção ou bots v4, e poderá ser totalmente preterida em uma versão futura. É recomendável que você atualize o código do bot para que ele use o armazenamento em memória para fins de teste ou use uma das **Extensões do Azure** para bots de produção. Para obter mais informações, consulte o tópico [Gerenciar dados de estado](v4sdk/bot-builder-howto-v4-state.md).

O serviço de Estado permite que você persista o progresso através dos diálogos em uma conversa para que um usuário possa retornar a uma conversa com um bot posteriormente sem perder a posição. Para preservar isso, os recipientes de propriedades de dados de bot armazenados por meio da API de Estado não são automaticamente excluídos quando você modifica o código do bot. É necessário decidir se os dados do bot devem ou não ser limpos, dependendo se o código modificado é compatível com as versões mais antigas dos dados. 

* Se você quiser redefinir manualmente a pilha de diálogo e o estado de conversa durante o desenvolvimento do bot, poderá usar o comando ` /deleteprofile` para excluir os dados de estado. Certifique-se de incluir o espaço inicial neste comando para evitar que o canal o interprete.
* Depois que o bot for implantado na produção, você poderá criar uma versão dos dados de bot para que, se você aumentar a versão, os dados de estado associados sejam limpos. Com o SDK do Bot Builder para Node.js, isso pode ser realizado usando middleware e com o SDK do Bot Builder para .NET, isso pode ser feito usando uma implementação `IPostToBot`.

> [!NOTE]
> Se a pilha de diálogo não puder ser desserializada corretamente, devido a alterações no formato de serialização ou porque o código foi alterado demasiadamente, o estado de conversa será redefinido.

::: moniker-end

## <a name="what-are-the-possible-machine-readable-resolutions-of-the-luis-built-in-date-time-duration-and-set-entities"></a>Quais são as possíveis resoluções legíveis por computador da data, hora, duração interna do LUIS e conjunto de entidades?

Para obter uma lista de exemplos, consulte a seção [Entidades criadas previamente ][LUISPreBuiltEntities] da documentação do LUIS.

## <a name="how-can-i-use-more-than-the-maximum-number-of-luis-intents"></a>Como posso usar mais que o número máximo de intenções de LUIS?

Você pode considerar dividir o modelo e chamar o serviço de LUIS em série ou paralelo.

## <a name="how-can-i-use-more-than-one-luis-model"></a>Como é possível usar mais de um modelo de LUIS?

Tanto o SDK do Bot Builder para Node.js quanto o SDK do Bot Builder para .NET dão suporte a chamada de vários modelos de LUIS a partir de um único diálogo de intenção de LUIS. Tenha em mente as seguintes advertências:

* O uso de vários modelos de LUIS assume que os modelos de LUIS possuem conjuntos de intenções não sobrepostos.
* O uso de vários modelos de LUIS pressupõe que as pontuações de diferentes modelos sejam comparáveis para selecionar a "melhor intenção combinada" em vários modelos.
* Usar vários modelos de LUIS significa que, se uma intenção corresponder a um modelo, também corresponderá fortemente à intenção "nenhum" dos outros modelos. Evite selecionar a intenção "nenhum" nessa situação, já que o SDK do Bot Builder para Node.js reduzirá automaticamente a pontuação para "nenhum" para evitar esse problema.

## <a name="where-can-i-get-more-help-on-luis"></a>Onde é possível obter mais ajuda sobre o LUIS?

* [Introdução ao LUIS (Serviço Inteligente de Reconhecimento Vocal) - Serviços Cognitivos da Microsoft](https://www.youtube.com/watch?v=jWeLajon9M8) (vídeo)
* [Sessão de Aprendizado Avançado para LUIS (Serviço Inteligente de Reconhecimento Vocal) ](https://www.youtube.com/watch?v=39L0Gv2EcSk) (vídeo)
* [Documentação do LUIS](/azure/cognitive-services/LUIS/Home)
* [Fórum do Serviço Inteligente de Reconhecimento Vocal](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=LUIS) 


## <a name="what-are-some-community-authored-dialogs"></a>Quais são alguns diálogos criados pela comunidade?

* [BotAuth](https://www.nuget.org/packages/BotAuth) - Autenticação do Azure Active Directory
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) - Expedição com base em expressões regulares de texto do usuário para métodos de diálogo

## <a name="what-are-some-community-authored-templates"></a>Quais são alguns modelos criados pela comunidade?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) - Modelo do Bot Builder ES6

## <a name="why-do-i-get-an-authorizationrequestdenied-exception-when-creating-a-bot"></a>Por que recebo uma exceção Authorization_RequestDenied ao criar um bot?

A permissão para criar bots do Serviço de Bot do Azure é gerenciada por meio do portal do AAD (Azure Active Directory). Se as permissões não estiverem configuradas corretamente no [portal do AAD](http://aad.portal.azure.com), os usuários receberão a exceção **Authorization_RequestDenied** ao tentarem criar um serviço de bot.

Primeiro, verifique se você é um "Convidado" do diretório:

1. Entre no [portal do Azure](http://portal.azure.com).
2. Clique em **Todos os serviços** e pesquise *ativo*.
3. Selecione **Azure Active Directory**.
4. Clique em **Usuários**.
5. Localize o usuário na lista e certifique-se de que o **Tipo de Usuário** não é um **Convidado**.

![Tipo de usuário do Azure Active Directory](~/media/azure-active-directory/user_type.png)

Após verificar que você não é um **Convidado**, em seguida, para garantir que os usuários em um diretório ativo possam criar um serviço de bot, o administrador do diretório precisará definir as configurações a seguir:

1. Entre no [portal do AAD](http://aad.portal.azure.com). Acesse **Usuários e grupos** e selecione **Configurações de usuário**.
2. Na seção **Registro do aplicativo**, defina **Os usuários podem registrar aplicativos**  para **Sim**. Isso permite que os usuários no diretório criem o serviço de bot.
3. Na seção **Usuários externos**, defina **Permissões de usuários convidados são limitadas** para **Não**. Isso permite que os usuários convidados no diretório criem o serviço de bot.

![Centro de administração do Azure Active Directory](~/media/azure-active-directory/admin_center.png)

## <a name="why-cant-i-migrate-my-bot"></a>Por que não consigo migrar o bot?

Se você estiver tendo problemas para migrar o bot, pode ser porque o bot pertence a um diretório diferente do diretório padrão. Experimente estas etapas:

1. No diretório de destino, adicione um novo usuário (via endereço de email) que não seja membro do diretório padrão, conceda a função de colaborador do usuário nas assinaturas que são o destino da migração.

2. No [Portal do Desenvolvedor](https://dev.botframework.com), adicione o endereço de email do usuário como coproprietários do bot que deve ser migrado. Em seguida, saia da seção.

3. Entre no [Portal do Desenvolvedor](https://dev.botframework.com) como o novo usuário e prossiga para migrar o bot.

## <a name="where-can-i-get-more-help"></a>Onde é possível obter mais ajuda?

* Aproveite as informações das perguntas respondidas anteriormente no [Stack Overflow](https://stackoverflow.com/questions/tagged/botframework) ou poste suas próprias perguntas usando a marca`botframework`. Observe que o Stack Overflow tem diretrizes como a exigência de um título descritivo, uma declaração completa e concisa do problema e detalhes suficientes para reproduzi-lo. Solicitações de recursos ou perguntas muito abrangentes não são tópicos pertinentes; os novos usuários devem visitar o [Centro de Ajuda do Stack Overflow](https://stackoverflow.com/help/how-to-ask) para obter mais detalhes.
* Consulte os [problemas do BotBuilder](https://github.com/Microsoft/BotBuilder/issues) no GitHub para obter informações sobre problemas conhecidos com o SDK do Bot Builder ou para relatar um novo problema.
* Aproveite as informações na discussão da comunidade do BotBuilder sobre [Gitter](https://gitter.im/Microsoft/BotBuilder).




[LUISPreBuiltEntities]: /azure/cognitive-services/luis/pre-builtentities
[BotFrameworkIDGuide]: bot-service-resources-identifiers-guide.md
[StateAPI]: ~/rest-api/bot-framework-rest-state.md
[TroubleshootingAuth]: bot-service-troubleshoot-authentication-problems.md

