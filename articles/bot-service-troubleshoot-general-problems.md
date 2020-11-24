---
title: Solucionar problemas de bots – Serviço de Bot
description: Solucione problemas gerais no desenvolvimento de bots usando as perguntas técnicas frequentes.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
ms.openlocfilehash: dd9e169a11bd52364570c63c64c6ebd7f860e517
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456040"
---
# <a name="troubleshoot-general"></a>Solucionar problemas gerais

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Essas perguntas frequentes podem ajudá-lo a solucionar problemas comuns de desenvolvimento de bot ou operacionais.

## <a name="how-can-i-troubleshoot-issues-with-my-bot"></a>Como é possível solucionar problemas com o bot?

1. Depure o código-fonte do bot com [Visual Studio Code](debug-bots-locally-vscode.md) ou [Visual Studio](https://docs.microsoft.com/visualstudio/debugger/navigating-through-code-with-the-debugger).
1. Teste o bot usando o [emulador](bot-service-debug-emulator.md) antes de implantá-lo na nuvem.
1. Implante seu bot em uma plataforma de hospedagem na nuvem, como o Azure e, em seguida, teste a conectividade com o bot usando o controle de webchat interno do painel do bot no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>. Se você encontrar problemas com o bot depois de implantá-lo no Azure, considere usar este artigo de blog: [Entendendo a solução de problemas e suporte do Azure](https://azure.microsoft.com/blog/understanding-azure-troubleshooting-and-support/).
1. Exclua a [autenticação][TroubleshootingAuth] como um possível problema.
1. Teste seu bot no Webchat, no Teams ou em qualquer outro canal que você pretende usar com o bot. Isso irá ajudá-lo a validar a experiência do usuário de ponta a ponta.
1. Considere testar o bot em canais que tenham requisitos de autenticação adicionais como Direct Line ou Webchat.
1. Leia as instruções sobre como [depurar um bot](bot-service-debug-bot.md) e outros artigos sobre depuração nesta seção.

## <a name="how-can-i-troubleshoot-authentication-issues"></a>Como é possível solucionar problemas de autenticação?

Para obter detalhes sobre como solucionar problemas de autenticação com o bot, consulte [solucionar problemas][TroubleshootingAuth] de autenticação do Bot Framework.

## <a name="im-using-the-bot-framework-sdk-for-net-how-can-i-troubleshoot-issues-with-my-bot"></a>Estou usando o SDK do Bot Framework para .NET. Como é possível solucionar problemas com o bot?

**Procure exceções.**
No Visual Studio 2019, acesse **Depurar** > **Windows** > **Configurações de Exceção**. Na janela **Configurações de Exceção**, marque a caixa de seleção **Interromper Quando Lançado** próximo a **Exceções do Common Language Runtime**. Também é possível ver a saída de diagnóstico na janela Saída quando houver exceções lançadas ou não tratadas.

**Verifique a pilha de chamadas.**
No Visual Studio, é possível escolher se está depurando [Apenas Meu Código](https://msdn.microsoft.com/library/dn457346.aspx) ou não. Examinar a pilha de chamadas completa pode fornecer informações adicionais sobre quaisquer problemas.

**Assegure-se de que todos os métodos de diálogo terminem com um plano para manipular a próxima mensagem.**
Todas as etapas do diálogo precisam alimentar a próxima etapa da cascata, ou encerrar o diálogo atual para retirá-lo na pilha. Se uma etapa não for tratada corretamente, a conversa não continuará como você espera. Dê uma olhada no artigo de conceito sobre [diálogos](v4sdk/bot-builder-concept-dialog.md) para saber mais sobre diálogos.


## <a name="what-causes-an-error-with-http-status-code-429-too-many-requests"></a>O que causa um erro com o código de status HTTP 429 "Muitas solicitações"?

Uma resposta de erro com o código de status HTTP 429 indica que muitas solicitações foram emitidas em um determinado período de tempo. O corpo da resposta deve incluir uma explicação do problema e também especificar o intervalo mínimo requerido entre as solicitações. Uma possível origem desse erro é a ferramenta [ngrok](https://ngrok.com/). Se estiver em um plano gratuito e executando nos limites do ngrok, acesse a página de limites e preços no site para obter mais [opções](https://ngrok.com/product#pricing).

## <a name="why-arent-my-bot-messages-getting-received-by-the-user"></a>Por que as mensagens do meu bot não estão sendo recebidas pelo usuário?

A atividade de mensagem gerada em resposta deve ser resolvida corretamente, caso contrário, ela não chegará ao destino pretendido. Na grande maioria dos casos, você não precisará lidar com isso explicitamente. O SDK se encarrega do encaminhamento da atividade de mensagem para você.

Tratar corretamente uma atividade significa incluir os detalhes de *IDs de conversa* apropriados juntamente com detalhes sobre o remetente. Na maioria dos casos, a atividade de mensagem é enviada em resposta a uma que chegou. Portanto, os detalhes de encaminhamento podem ser retirados da atividade de entrada.

Se você examinar os rastreamentos ou logs de auditoria, poderá verificar se as mensagens foram encaminhadas corretamente. Se não tiverem sido, defina um ponto de interrupção em seu bot e veja onde as IDs estão sendo definidas para a mensagem.

## <a name="how-can-i-run-background-tasks-in-aspnet"></a>Como é possível executar tarefas em segundo plano no ASP.NET?

Em alguns casos, convém iniciar uma tarefa assíncrona que aguarda alguns segundos e, em seguida, executa algum código para limpar o perfil do usuário ou redefinir o estado de diálogo/conversa. Para obter detalhes sobre como realizar isso, consulte [Como executar tarefas em segundo plano no ASP.NET](https://www.hanselman.com/blog/HowToRunBackgroundTasksInASPNET.aspx). Em particular, considere o uso de [HostingEnvironment.QueueBackgroundWorkItem](https://msdn.microsoft.com/library/dn636893(v=vs.110).aspx).

## <a name="my-bot-is-slow-to-respond-to-the-first-message-it-receives-how-can-i-make-it-faster"></a>O bot demora a responder à primeira mensagem que recebe. Como é possível torná-lo mais rápido?

Os bots são serviços Web e algumas plataformas de hospedagem, incluindo o Azure, colocam automaticamente o serviço em suspensão se não receberem tráfego por um determinado período de tempo. Se isso acontecer com o bot, ele deverá reiniciar a partir do zero na próxima vez em que receber uma mensagem, o que tornará a resposta muito mais lenta do que se já estivesse em execução.

Algumas plataformas de hospedagem permitem que você configure o serviço para que não seja colocado em suspensão. Se o bot estiver hospedado nos Aplicativos Web do Serviço de Bot do Azure, navegue até o serviço do bot no [portal do Azure](https://portal.azure.com), selecione **Configurações do aplicativo** e, em seguida, selecione **Always On**. Essa opção está disponível na maioria dos planos de serviço, mas não em todos.

## <a name="how-can-i-guarantee-message-delivery-order"></a>Como é possível garantir a ordem de entrega da mensagem?

O Bot Framework irá preservar a ordem das mensagens, tanto quanto possível. Por exemplo, se você enviar a mensagem A e aguardar a conclusão dessa operação HTTP antes de iniciar outra operação HTTP para enviar a mensagem B. Alguns canais, como SMS e email, não garantem a ordenação para o dispositivo do usuário.

## <a name="why-are-parts-of-my-message-text-being-dropped"></a>Por que partes do texto da mensagem estão sendo removidas?

O Bot Framework e muitos canais interpretam o texto como se estivesse formatado com [Markdown](https://en.wikipedia.org/wiki/Markdown). Verifique se o texto contém caracteres que podem ser interpretados como sintaxe de Markdown.

## <a name="how-can-i-support-multiple-bots-at-the-same-bot-service-endpoint"></a>Como dar suporte a vários bots no mesmo ponto de extremidade de serviço de bot?

Esse [exemplo](https://github.com/Microsoft/BotBuilder/issues/2258#issuecomment-280506334) mostra como configurar o `Conversation.Container` com o `MicrosoftAppCredentials` correto e usar um `MultiCredentialProvider` simples para autenticar várias IDs do Aplicativo e senhas.

## <a name="identifiers"></a>Identificadores
___

## <a name="how-do-identifiers-work-in-the-bot-framework"></a>Como os identificadores funcionam no Bot Framework?

Para mais detalhes sobre os identificadores no Bot Framework, consulte o [guia para identificadores][BotFrameworkIDGuide] do Bot Framework.

## <a name="how-can-i-get-access-to-the-user-id"></a>Como é possível obter acesso à ID de usuário?

Os canais do Bot Framework apresentam a ID do usuário no campo `from.Id` de qualquer Atividade enviada pelo usuário. SMS e mensagens de email fornecerão a ID de usuário bruta nessa propriedade. Alguns canais ocultam a propriedade `from.Id`, portanto, ela contém uma ID exclusiva para o usuário que difere da ID do usuário no canal. Se for necessário conectar uma conta existente, você poderá usar um cartão de entrada e implementar seu próprio fluxo do OAuth para conectar a ID de usuário à ID de usuário do seu próprio serviço.

## <a name="why-are-my-facebook-user-names-not-showing-anymore"></a>Por que meus nomes de usuário do Facebook não estão mais sendo exibidos?

Você alterou a senha do Facebook? Isso invalidará o token de acesso e será necessário atualizar as configurações do bot para o canal do Facebook Messenger no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

## <a name="why-is-my-kik-bot-replying-im-sorry-i-cant-talk-right-now"></a>Por que meu bot no Kik está respondendo "Desculpe, não posso falar agora"?

São permitidos 50 assinantes para bots em desenvolvimento no Kik. Após 50 usuários exclusivos terem interagido com o bot, qualquer novo usuário que tentar conversar com o bot receberá a mensagem "Desculpe, não posso falar agora." Para obter mais informações, consulte [Documentação do Kik](https://botsupport.kik.com/hc/articles/225764648-How-can-I-share-my-bot-with-Kik-users-while-in-development-).

## <a name="how-can-i-use-authenticated-services-from-my-bot"></a>Como é possível usar serviços autenticados do meu bot?

Para Azure Active Directory autenticação, consulte o tutorial [Adicionar autenticação ao bot](v4sdk/bot-builder-authentication.md) .

> [!NOTE]
> Se você adicionar a funcionalidade de autenticação e segurança ao bot, certifique-se de que os padrões implementados no código estejam em conformidade com os padrões de segurança apropriados ao aplicativo.

## <a name="how-can-i-limit-access-to-my-bot-to-a-pre-determined-list-of-users"></a>Como é possível limitar o acesso ao bot para uma lista pré-determinada de usuários?

Alguns canais, como SMS e email, fornecem endereços sem escopo. Nesses casos, as mensagens do usuário conterão a ID de usuário bruta na propriedade `from.Id`.

Outros canais, como o Facebook e a margem de atraso, fornecem endereços com escopo ou locatários de forma a impedir que um bot possa prever a ID de um usuário antes do tempo. Nesses casos, você precisará autenticar o usuário por meio de um link de logon ou segredo compartilhado para determinar se estão ou não autorizados a usar o bot.

## <a name="why-does-my-direct-line-11-conversation-start-over-after-every-message"></a>Por que minha conversa com Direct Line 1.1 inicia novamente após cada mensagem?

> [!NOTE]
>
> Estas seções não se aplicam à versão mais recente do protocolo do Direct Line, 3.0

Se a conversa com Direct Line parece iniciar novamente após cada mensagem, a propriedade `from` provavelmente está ausente ou `null` nas mensagens que o cliente Direct Line enviou para o bot. Quando um cliente Direct Line enviar uma mensagem com a propriedade `from` ausente ou `null`, o serviço Direct Line alocará automaticamente uma ID, portanto, todas as mensagens enviadas pelo cliente parecerão originar de um novo usuário diferente.

Para corrigir isso, defina a propriedade `from` em cada mensagem que o cliente Direct Line envia para um valor estável que represente exclusivamente o usuário que está enviando a mensagem. Por exemplo, se um usuário já estiver conectado a um aplicativo ou página da Web, será possível usar essa ID de usuário existente como o valor da propriedade `from` nas mensagens que o usuário enviar. Como alternativa, é possível optar por gerar uma ID de usuário aleatória no carregamento da página ou carregamento do aplicativo, armazenar a ID em um estado do dispositivo ou cookie e usar essa ID como o valor da propriedade `from` nas mensagens que o usuário enviar.

## <a name="what-causes-the-direct-line-30-service-to-respond-with-http-status-code-502-bad-gateway"></a>O que faz com que o serviço Direct Line 3.0 responda com o código de status HTTP 502 "Gateway Incorreto"?
O Direct Line 3.0 retorna o código de status HTTP 502 quando tenta contatar o bot mas a solicitação não é concluída com êxito. Esse erro indica que o bot retornou um erro ou a solicitação atingiu o tempo limite. Para obter mais informações sobre erros que o bot gera, acesse o painel do bot no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> e clique no link "Problemas" do canal afetado. Se você tiver o Application Insights configurado para o bot, também poderá localizar informações detalhadas sobre erros.

## <a name="why-do-i-get-an-authorization_requestdenied-exception-when-creating-a-bot"></a>Por que recebo uma exceção Authorization_RequestDenied ao criar um bot?

A permissão para criar bots do Serviço de Bot do Azure é gerenciada por meio do portal do AAD (Azure Active Directory). Se as permissões não estiverem configuradas corretamente no [portal do AAD](https://aad.portal.azure.com), os usuários receberão a exceção **Authorization_RequestDenied** ao tentarem criar um serviço de bot.

Primeiro, verifique se você é um "Convidado" do diretório:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Todos os serviços** e pesquise *ativo*.
3. Selecione **Azure Active Directory**.
4. Clique em **Usuários**.
5. Localize o usuário na lista e certifique-se de que o **Tipo de Usuário** não é um **Convidado**.

![Tipo de usuário do Azure Active Directory](~/media/azure-active-directory/user_type.png)

Após verificar que você não é um **Convidado**, em seguida, para garantir que os usuários em um diretório ativo possam criar um serviço de bot, o administrador do diretório precisará definir as configurações a seguir:

1. Entre no [portal do AAD](https://aad.portal.azure.com). Acesse **Usuários e grupos** e selecione **Configurações de usuário**.
2. Na seção **Registro do aplicativo**, defina **Os usuários podem registrar aplicativos**  para **Sim**. Isso permite que os usuários no diretório criem o serviço de bot.
3. Na seção **Usuários externos**, defina **Permissões de usuários convidados são limitadas** para **Não**. Isso permite que os usuários convidados no diretório criem o serviço de bot.

![Centro de administração do Azure Active Directory](~/media/azure-active-directory/admin_center.png)

## <a name="why-cant-i-migrate-my-bot"></a>Por que não consigo migrar o bot?

Se o bot estiver registrado em dev.botframework.com e você quiser migrá-lo para o Azure, mas estiver com problemas para fazer isso, talvez o bot pertença a um diretório diferente do diretório padrão. Experimente estas etapas:

1. No diretório de destino, adicione um novo usuário (via endereço de email) que não seja membro do diretório padrão, conceda a função de colaborador do usuário nas assinaturas que são o destino da migração.

2. No [portal de desenvolvimento](https://dev.botframework.com), adicione o endereço de email do usuário como coproprietários do bot que deve ser migrado. Em seguida, saia da seção.

3. Entre no [Portal do Desenvolvedor](https://dev.botframework.com) como o novo usuário e prossiga para migrar o bot.

## <a name="where-can-i-get-more-help"></a>Onde é possível obter mais ajuda?

* Aproveite as informações das perguntas respondidas anteriormente no [Stack Overflow](https://stackoverflow.com/questions/tagged/botframework) ou poste suas próprias perguntas usando a marca`botframework`. Observe que o Stack Overflow tem diretrizes como a exigência de um título descritivo, uma declaração completa e concisa do problema e detalhes suficientes para reproduzi-lo. Solicitações de recursos ou perguntas muito abrangentes não são tópicos pertinentes; os novos usuários devem visitar o [Centro de Ajuda do Stack Overflow](https://stackoverflow.com/help/how-to-ask) para obter mais detalhes.
* Consulte os [Problemas do BotBuilder](https://github.com/Microsoft/BotBuilder/issues) no GitHub para obter informações sobre problemas conhecidos com o SDK do Bot Framework ou para relatar um novo problema.
* Aproveite as informações na discussão da comunidade do BotBuilder sobre [Gitter](https://gitter.im/Microsoft/BotBuilder).



[LUISPreBuiltEntities]: /azure/cognitive-services/luis/pre-builtentities
[BotFrameworkIDGuide]: bot-service-resources-identifiers-guide.md
[StateAPI]: ~/rest-api/bot-framework-rest-state.md
[TroubleshootingAuth]: bot-service-troubleshoot-authentication-problems.md

