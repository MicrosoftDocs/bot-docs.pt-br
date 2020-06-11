---
title: Perguntas Frequentes sobre o Serviço de Bot – Serviço de Bot
description: Uma lista de perguntas frequentes sobre os elementos do Bot Framework e uma previsão de quando novos recursos serão disponibilizados.
author: scheyal
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
ms.openlocfilehash: 0b41b6738726c5565edce50acdef9ddfe16a6abe
ms.sourcegitcommit: 5add21ad3daf0ce894612a22b951b98350961720
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420647"
---
# <a name="bot-framework-frequently-asked-questions"></a>Perguntas frequentes sobre o Bot Framework

Este artigo contém respostas a algumas perguntas frequentes sobre o Bot Framework.

## <a name="background-and-availability"></a>Informações e disponibilidade

### <a name="why-did-microsoft-develop-the-bot-framework"></a>Por que a Microsoft desenvolveu o Bot Framework?

Criamos o Bot Framework para facilitar para os desenvolvedores criar e conectar bots excelentes aos usuários, independentemente do local em que eles conversarem, incluindo os canais premier da Microsoft.

### <a name="what-is-the-v4-sdk"></a>O que é o SDK v4?
O SDK do Bot Framework v4 baseia-se nos comentários e nas lições aprendidas dos SDKs anteriores do Bot Framework. Ele apresenta os níveis certos de abstração, ao mesmo tempo que permite a "componentização" avançada dos blocos de construção de bot. Comece com um bot simples e aumente a sofisticação dele usando uma estrutura modular e extensível. Encontre as [perguntas frequentes](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) sobre o SDK no GitHub.

### <a name="running-a-bot-offline"></a>Como executar um bot offline

<!-- WIP -->
Antes de falar sobre o uso de um bot offline, o que significa que um bot não foi implantado no Azure ou em outros serviços de host, mas no local, vamos esclarecer alguns pontos.

- Um bot é um serviço Web que não tem uma interface do usuário, portanto, ele deve interagir com o bot por outros meios, na forma de canais, que usam o [Bot Framework Service](rest-api/bot-framework-rest-connector-concepts.md). O conector funciona como um *proxy* para retransmitir mensagens entre um cliente e o bot.
- O **conector** é um aplicativo global hospedado em nós do Azure e distribuído geograficamente para obter disponibilidade e escalabilidade. 
- Use o [Registro do Canal de Bot](bot-service-quickstart-registration.md) para registrar o bot com o conector.
    >[!NOTE]
    > O bot deve ter o ponto de extremidade acessível publicamente pelo conector.

É possível executar um bot offline com funcionalidades limitadas. Por exemplo, caso queira usar um bot offline que tenha a funcionalidade LUIS, você deverá criar um contêiner para o bot, as ferramentas necessárias e um contêiner para LUIS. Ambos conectados por meio da rede de transição Docker Compose.

Esta é uma solução offline "parcial" porque um contêiner de Serviços Cognitivos precisa de uma conexão online periódica.

> [!NOTE]
> O serviço QnA não tem suporte em um bot de execução offline.

Para obter mais informações, consulte:

- [Como implantar o contêiner do LUIS (Reconhecimento Vocal) nas Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/cognitive-services/luis/deploy-luis-on-container-instances)
- [Suporte de contêiner nos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)

## <a name="bot-framework-sdk-version-3-lifetime-support-and-deprecation-notice"></a>Suporte de tempo de vida do SDK Versão 3 do Bot Framework e aviso de substituição
O SDK V4 do Microsoft Bot Framework foi lançado em setembro de 2018 e, desde então, enviamos algumas melhorias em atualizações secundárias. Conforme anunciado anteriormente, o SDK V3 está sendo desativado. Assim sendo, não haverá mais desenvolvimento em repositórios V3. **As cargas de trabalho de bot V3 existentes continuarão a ser executadas sem interrupção. Não temos planos de interromper nenhuma carga de trabalho em execução**.

Conforme mencionado anteriormente, os bots do SDK V3 do Bot Builder continuam sendo executados e tendo suporte do Serviço de Bot do Azure. O SDK V3 do Bot Builder será compatível apenas para correções críticas de bugs de segurança, atualizações de conector e de compatibilidade de camada de protocolo.  

Todos os novos recursos e funcionalidades são desenvolvidos exclusivamente no [SDK V4 do Bot Framework](https://github.com/microsoft/botframework-sdk).  Os clientes são incentivados a migrar seus bots para V4 assim que possível.

É altamente recomendável começar a migrar seus bots da V3 para a V4. Para dar suporte a essa migração, criamos a documentação relacionada e forneceremos suporte estendido para iniciativas de migração (por meio dos canais padrão como o Stack Overflow e Suporte ao Cliente Microsoft).


Para obter mais informações, confira as referências a seguir:
* [Diretrizes essenciais para migração](https://aka.ms/bf-migration-overview)
* Repositórios v4 primários para desenvolver bots do Bot Framework
  * [Bot Builder para dotnet](https://github.com/microsoft/botbuilder-dotnet)
  * [Bot Builder para JS](https://github.com/microsoft/botbuilder-js) 
* As bibliotecas do QnA Maker foram substituídas pelas seguintes bibliotecas V4:
  * [Bibliotecas para dotnet](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.AI.QnA)
  * [Bibliotecas para JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-ai/src/qnaMaker.ts)
* As bibliotecas do Azure foram substituídas pelas seguintes bibliotecas V4:
  * [Bot Builder para JS Azure](https://github.com/Microsoft/botbuilder-js/tree/master/libraries/botbuilder-azure)
  * [Bot Builder para dotnet Azure](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.Azure)

### <a name="v3-status-summary"></a>Resumo do Status V3

#### <a name="abs-service"></a>Serviço de ABS
1.    O lado do serviço do ABS continuará a dar suporte a bots do V3 em execução sem nenhum fim de vida planejado. Eventuais bots em execução não serão interrompidos. 
2.    Os canais permanecerão compatíveis com V3 sem interrupção nem plano de fim de vida útil.
3.    A criação de novos bots V3 está desabilitada no portal. No entanto, os usuários especialistas que desejarem implantar seus bots V3 de modo independente e sem o ABS (por exemplo, como serviço de aplicativo Web) poderão fazê-lo.

#### <a name="sdk-and-tools"></a>SDK e Ferramentas
1.    Não estamos investindo na V3 do lado do SDK e aplicaremos apenas correções de segurança críticas às ramificações do SDK no futuro próximo (exceção: planejamos adicionar um conector de habilidades para permitir que os bots V4 chamem bots V3 herdados).
2.    O desenvolvimento de SDKs e ferramentas é exclusivamente no V4, sem nenhum trabalho V3 feito ou planejado (portanto, já é o cenário em que estamos).
3.    Não impedimos que ninguém execute ferramentas antigas para gerenciar seus próprios bots V3. 


## <a name="how-can-i-migrate-azure-bot-service-from-one-region-to-another"></a>Como migrar o Serviço de Bot do Azure de uma região para outra?

O Serviço de Bot do Azure não dá suporte à mudança de região. Ele é um serviço global que não está vinculado a nenhuma região específica.

## <a name="channels"></a>Canais
### <a name="when-will-you-add-more-conversation-experiences-to-the-bot-framework"></a>Quando vocês adicionarão mais experiências de conversa ao Bot Framework?

Estamos planejando fazer melhorias contínuas no Bot Framework, incluindo canais adicionais, mas não podemos fornecer um prazo no momento.  
Caso deseje que um canal específico seja adicionado à estrutura, [fale conosco][Support].

### <a name="i-have-a-communication-channel-id-like-to-be-configurable-with-bot-framework-can-i-work-with-microsoft-to-do-that"></a>Tenho um canal de comunicação que desejo tornar configurável com o Bot Framework. Posso trabalhar com a Microsoft para fazer isso?

Não fornecemos um mecanismo geral para os desenvolvedores adicionarem novos canais ao Bot Framework, mas você pode conectar o bot ao aplicativo por meio da [Direct Line API][DirectLineAPI]. Se você for um desenvolvedor de um canal de comunicação e desejar trabalhar conosco para habilitar seu canal no Bot Framework, [adoraríamos ouvir sua opinião][Support].

### <a name="if-i-want-to-create-a-bot-for-microsoft-teams-what-tools-and-services-should-i-use"></a>Se eu quiser criar um bot para o Microsoft Teams, quais ferramentas e serviços deverei usar?

O Bot Framework foi projetado para criar, conectar e implantar bots escalonáveis, dinâmicos e de alto desempenho e alta qualidade no Teams e em muitos outros canais. O SDK pode ser usado para criar bots compatíveis com texto/SMS, imagem, botão e cartão (que constituem a maior parte das interações de bot hoje nas experiências de conversa), bem como interações de bot específicas do Teams, tais como experiências avançadas de áudio e vídeo.

Caso você já tenha um bot excelente, mas queira alcançar o público-alvo do Teams, o bot poderá ser conectado com facilidade ao Teams (ou a qualquer canal compatível) por meio do Bot Framework para a API REST (desde que ele tenha um ponto de extremidade REST acessível pela Internet).

### <a name="how-do-i-create-a-bot-that-uses-the-us-government-data-center"></a>Como fazer para criar um bot que usa o data center do Governo dos EUA?

Há duas etapas principais necessárias para criar um bot que usa um data center do Governo dos EUA.
1. Adicione uma configuração "provedor de canal" em seu appsettings.json (ou as Configurações do Serviço de Aplicativo). Isso precisa ser definido especificamente para esta constante de nome/valor: ChannelService = "https://botframework.azure.us". Um exemplo de uso de appsetting.json é mostrado abaixo.

```json
{
  "MicrosoftAppId": "", 
  "MicrosoftAppPassword": "",
  "ChannelService": "https://botframework.azure.us"
}
```
2. Se estiver usando o .NET Core, você precisará adicionar um ConfigurationChannelProvider em seu arquivo startup.cs. O modo como você faz isso varia de acordo com qual versão do SDK você está usando.

- Para a 4.3 e versões superiores, no método ConfigureServices, você precisará criar uma instância de ConfigurationChannelProvider. Ao usar a classe BotFrameworkHttpAdapter, você injeta isso como singleton na coleção de serviços desta maneira:

```csharp  
services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();
```
- Para versões anteriores à 4.3, no método ConfigureServices, encontre o método AddBot. Ao definir as opções, verifique se você adicionou:

```csharp
options.ChannelProvider = new ConfigurationChannelProvider();
```
Você pode encontrar mais informações sobre serviços Governamentais [aqui](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#azure-bot-service)

## <a name="security-and-privacy"></a>Segurança e privacidade
### <a name="do-the-bots-registered-with-the-bot-framework-collect-personal-information-if-yes-how-can-i-be-sure-the-data-is-safe-and-secure-what-about-privacy"></a>Os bots registrados no Bot Framework coletam informações pessoais? Em caso afirmativo, como posso ter certeza de que os dados estão seguros e protegidos? E quanto à privacidade?

Cada bot é seu próprio serviço, e os desenvolvedores desses serviços são obrigados a fornecer Termos de Serviço e Políticas de Privacidade de acordo com seu Código de Conduta do Desenvolvedor. Para obter mais informações, confira as [diretrizes de análise do bot](https://docs.microsoft.com/azure/bot-service/bot-service-review-guidelines?view=azure-bot-service-4.0).

### <a name="can-i-host-my-bot-on-my-own-servers"></a>Pode hospedar meu bot em meus próprios servidores?
Sim. Seu bot pode ser hospedado em qualquer lugar na Internet. Em seus próprios servidores, no Azure ou em qualquer outro datacenter. O único requisito é que o bot deve expor um ponto de extremidade HTTPS publicamente acessível.

### <a name="how-do-you-ban-or-remove-bots-from-the-service"></a>Como os bots são vetados ou removidos do serviço?

Os usuários têm um modo para relatar o comportamento inadequado de um bot, que é o cartão de visita do bot no diretório. Os desenvolvedores precisam respeitar os termos de serviço da Microsoft para participar do serviço.

### <a name="which-specific-urls-do-i-need-to-whitelist-in-my-corporate-firewall-to-access-bot-framework-services"></a>Quais URLs específicas preciso incluir na lista de permissões de meu firewall corporativo para acessar os serviços Bot Framework?

Se tiver um firewall de saída bloqueando o tráfego do seu bot com a Internet, você precisará colocar as URLs a seguir no firewall:

- `login.botframework.com` (Autenticação do bot)
- `login.microsoftonline.com` (Autenticação do bot)
- `westus.api.cognitive.microsoft.com` (para integração do NLP no Luis.ai)
- `cortanabfchanneleastus.azurewebsites.net` (canal da Cortana)
- `cortanabfchannelwestus.azurewebsites.net` (Canal da Cortana)
- `*.botframework.com` (canais)
- `state.botframework.com` (compatibilidade com versões anteriores)
- `login.windows.net` (Logon do Windows)
- `login.windows.com` (Logon do Windows)
- `sts.windows.net` (Logon do Windows)
- URLs adicionais para canais específicos do Bot Framework

> [!NOTE]
> Você poderá usar `<channel>.botframework.com` se preferir não adicionar uma URL com um asterisco à lista de permissões. `<channel>` é igual a cada canal que seu bot usa, como `directline.botframework.com`, `webchat.botframework.com` e `slack.botframework.com`. Também vale a pena observar o tráfego em seu firewall enquanto testa o bot para verificar se nada mais está sendo bloqueado.

### <a name="can-i-block-all-traffic-to-my-bot-except-traffic-from-the-bot-framework-service"></a>Posso bloquear todo o tráfego para o meu bot, exceto o tráfego do Bot Framework Service?
Os Bot Framework Services são hospedados em datacenters do Azure em todo o mundo e a lista de IPs do Azure está em constante mudança. A lista de permissões de determinados endereços IP pode funcionar um dia e parar no próximo, à medida que os Endereços IP do Azure mudam.
 
### <a name="which-rbac-role-is-required-to-create-and-deploy-a-bot"></a>Qual função RBAC é necessária para criar e implantar um bot?

A criação de um bot no portal do Azure requer acesso de colaborador na assinatura ou em um grupo de recursos específico. Um usuário com a função de *Colaborador* em um grupo de recursos pode criar um bot nesse grupo de recursos específico. Um usuário na função de *Colaborador* para uma assinatura pode criar um bot em um grupo de recursos novo ou existente.

Usando a CLI do Azure, uma abordagem de controle de acesso baseado em função pode dar suporte a funções personalizadas. Se você quiser tornar uma função personalizada com permissões mais restritas, o conjunto a seguir permitirá que o usuário crie e implante um bot compatível também com LUIS, QnA Maker e Application Insights.

  "Microsoft.Web/ *", "Microsoft.BotService/* ", "Microsoft.Storage/ *", "Microsoft.Resources/deployments/* ", "Microsoft.CognitiveServices/ *", "Microsoft.Search/searchServices/* ", "Microsoft.Insights/ *", "Microsoft.Insights/components/* "

O LUIS e o QnA Maker exigem permissões de Serviços Cognitivos. O QnA Maker também requer permissões de Pesquisa. Ao criar uma função personalizada, qualquer permissão de *negar* herdada será substituída por permissões de *permitir*.
 
### <a name="what-keeps-my-bot-secure-from-clients-impersonating-the-bot-framework-service"></a>O que mantém o meu bot protegido de clientes que se passam pelo Bot Framework Service?
1. Todas as solicitações do Bot Framework autênticas são acompanhadas por um token JWT cuja assinatura criptográfica pode ser verificada seguindo o guia de [autenticação](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector). O token foi projetado para que os invasores não possam se passar por serviços confiáveis.

2. O token de segurança que acompanha todas as solicitações feitas para seu bot tem a ServiceUrl codificada dentro dele, o que significa que, mesmo se um invasor obtiver acesso ao token, eles não conseguirá redirecionar a conversa para uma nova ServiceUrl. Isso é imposto por todas as implementações do SDK e documentado em nossos materiais de [referência](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) de autenticação.

3. Se o token de entrada estiver ausente ou se for malformado, o SDK do Bot Framework não irá gerar um token na resposta. Isso limita o dano que poderá ocorrer se o bot for configurado incorretamente.
4. Dentro do bot, você pode verificar manualmente a ServiceUrl fornecida no token. Isso torna o bot mais frágil em caso de alterações de topologia de serviço, por isso que é possível, mas não recomendado.


Observe que essas são as conexões de saída do bot para a Internet. Não há uma lista de nomes DNS ou Endereços IP que o Serviço Bot Framework Connector usará para se comunicar com o bot. Não há suporte para a entrada na lista de permissões do Endereço IP.

## <a name="rate-limiting"></a>Limitação de taxa
### <a name="what-is-rate-limiting"></a>O que é a limitação de taxa?
O serviço Bot Framework precisa proteger a si próprio e a seus clientes contra padrões de chamada abusivos (por exemplo, ataque de negação de serviço), de modo que nenhum bot possa prejudicar o desempenho de outros bots. Para obter esse tipo de proteção, adicionamos limites de taxa (também conhecidos como limitação) aos nossos pontos de extremidade. Impondo um limite de taxa, podemos restringir a frequência com a qual um cliente ou bot pode fazer uma chamada específica. Por exemplo: com a limitação de taxa habilitada, se um bot desejar postar um grande número de atividades, ele precisará espaçá-las por um período. Observe que a finalidade da limitação de taxa não é limitar o volume total de um bot. Ela se destina a prevenir abusos da infraestrutura de conversa que não seguem os padrões de conversa humana. Por exemplo, inundar duas conversas com mais conteúdo do que duas pessoas jamais poderiam consumir.

### <a name="how-will-i-know-if-im-impacted"></a>Como poderei saber se fui afetado?
É improvável que você enfrente a limitação de taxa, mesmo com um alto volume. A maior parte da limitação de taxa só ocorrerá devido ao envio em massa de atividades (de um bot ou de um cliente), um teste de carga extrema ou um bug. Quando uma solicitação é limitada, uma resposta HTTP 429 (Número Excessivo de Solicitações) é retornada juntamente com um cabeçalho Retry-After, que indica o tempo (em segundos) que é necessário aguardar antes do êxito da nova tentativa da solicitação. Colete essas informações habilitando a análise no bot por meio do Azure Application Insights. Se preferir, adicione um código ao bot para registrar mensagens em log. 

### <a name="how-does-rate-limiting-occur"></a>Como ocorre a limitação de taxa?
Isso pode ocorrer se:
-    um bot envia mensagens com muita frequência
-    um cliente de um bot envia mensagens com muita frequência
-    os clientes da Linha Direta solicitam um novo Soquete da Web com muita frequência

### <a name="what-are-the-rate-limits"></a>Quais são os limites de taxa?
Estamos continuamente ajustando os limites de taxa para torná-los os mais brandos possíveis, ao mesmo tempo em que protegemos nosso serviço e nossos usuários. Como os limites serão alterados ocasionalmente, não estamos publicando os números no momento. Caso você seja afetado pela limitação de taxa, fique à vontade para falar conosco pelo email [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

## <a name="bot-framework-sdk"></a>SDK do Bot Framework
## <a name="why-doesnt-the-typing-activity-do-anything"></a>Por que a atividade de Digitação não gera nenhuma ação?
Alguns canais não dão suporte a atualizações de digitação transitórias nos clientes.

## <a name="what-is-the-difference-between-the-connector-library-and-builder-library-in-the-sdk"></a>Qual é a diferença entre a biblioteca do Connector e a biblioteca do Builder no SDK?

A biblioteca do Connector é a exposição da API REST. A biblioteca do Builder inclui o modelo de programação de diálogo conversacional e outros recursos, como prompts, cascatas, cadeias e preenchimento de formulário guiado. A biblioteca do Builder também fornece acesso a serviços cognitivos como o LUIS.

## <a name="how-do-user-messages-relate-to-https-method-calls"></a>Como as mensagens do usuário estão relacionadas às chamadas de método HTTPS?

Quando o usuário enviar uma mensagem através de um canal, o serviço Web do Bot Framework emitirá um HTTPS POST para o ponto de extremidade do serviço Web do bot. O bot pode enviar zero, uma ou muitas mensagens de volta ao usuário naquele canal, emitindo um HTTPS POST separado para o Bot Framework para cada mensagem que envia.


## <a name="how-can-i-intercept-all-messages-between-the-user-and-my-bot"></a>Como é possível interceptar todas as mensagens entre o usuário e o bot?

Com o SDK do Bot Framework para .NET, é possível fornecer implementações das interfaces `IPostToBot` e `IBotToUser` para o contêiner de injeção da dependência `Autofac`. Com o SDK do Bot Framework para qualquer linguagem, é possível usar o middleware para a mesma finalidade. O repositório [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure) contém as bibliotecas do C# e Node.js que registrarão esses dados em uma tabela do Azure.

## <a name="what-is-the-idialogstackforward-method-in-the-bot-framework-sdk-for-net"></a>O que é o método IDialogStack.Forward no SDK do Bot Framework para .NET?

A finalidade principal de `IDialogStack.Forward` é reutilizar um diálogo filho existente que seja frequentemente "reativo", em que o diálogo filho (em `IDialog.StartAsync`) aguarda um objeto `T` com algum manipulador `ResumeAfter`. Em particular, se tiver um diálogo filho que aguarda um `IMessageActivity``T`, você poderá encaminhar o `IMessageActivity` de entrada (já recebido por algum diálogo pai) usando o método `IDialogStack.Forward`. Por exemplo, para encaminhar um `IMessageActivity` de entrada para um `LuisDialog`, chame `IDialogStack.Forward` para efetuar push de `LuisDialog` para a pilha de diálogo, execute o código em `LuisDialog.StartAsync` até agendar uma espera pela próxima mensagem e, em seguida, imediatamente satisfazer essa espera com o `IMessageActivity` encaminhado.

`T` é geralmente um `IMessageActivity`, uma vez que `IDialog.StartAsync` é normalmente construído para aguardar esse tipo de atividade. É possível usar `IDialogStack.Forward` como `LuisDialog` como um mecanismo para interceptar mensagens do usuário para algum processamento antes de encaminhar a mensagem para um `LuisDialog` existente. Alternativamente, também é possível usar `DispatchDialog` com `ContinueToNextGroup` para essa finalidade.

Você esperaria encontrar o item encaminhado no primeiro manipulador `ResumeAfter` (p. ex. `LuisDialog.MessageReceived`) agendado por `StartAsync`.


## <a name="what-is-the-difference-between-proactive-and-reactive"></a>Qual é a diferença entre "proativo" e "reativo"?

Do ponto de vista do bot, "reativo" significa que o usuário inicia a conversa enviando uma mensagem para o bot e o bot reage respondendo a essa mensagem. Em contraste, "proativo" significa que o bot inicia a conversa, enviando a primeira mensagem ao usuário. Por exemplo, um bot pode enviar uma mensagem proativa para notificar um usuário quando um temporizador expirar ou ocorrer um evento.

## <a name="how-can-i-send-proactive-messages-to-the-user"></a>Como enviar mensagens proativas ao usuário?

Para obter exemplos que mostram como enviar mensagens proativas, consulte [Exemplos em C# V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) e [Exemplos em Node.js V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) no repositório BotBuilder-Samples no GitHub.

## <a name="how-can-i-reference-non-serializable-services-from-my-c-dialogs-in-sdk-v3"></a>Como é possível referenciar serviços não serializáveis dos meus diálogos em C#? no SDK v3?

Há várias opções:

* Resolva a dependência através de `Autofac` e `FiberModule.Key_DoNotSerialize`. Essa é a solução mais limpa.
* Use atributos [NonSerialized](https://msdn.microsoft.com/library/system.nonserializedattribute(v=vs.110).aspx) e [OnDeserialized](https://msdn.microsoft.com/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx) para restaurar a dependência da desserialização. Essa é a solução mais simples.
* Não armazene essa dependência, para que não seja serializada. Essa solução, embora tecnicamente viável, não é recomendável.
* Use o substituto de serialização de reflexão. Essa solução pode não ser viável em alguns casos e corre o risco de serializar demasiadamente.

## <a name="what-is-an-etag--how-does-it-relate-to-bot-data-bag-storage"></a>O que é um ETag?  Qual a relação disso com o armazenamento de recipiente de dados de bot?

Um [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) é um mecanismo para [controle de simultaneidade otimista](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). O armazenamento de recipiente de dados de bot usa ETags para impedir atualizações conflitantes nos dados. Um erro de ETag com o código de status HTTP 412 "Falha na pré-condição" indica que foram recebidas várias mensagens em paralelo antes que o bot pudesse concluir a primeira operação.

O estado e a pilha de diálogo são armazenados em recipientes de dados de bot. Por exemplo, será possível ver o erro de ETag "Falha na Pré-Condição" se o bot ainda estiver processando uma mensagem anterior quando receber uma nova mensagem para essa conversa.

## <a name="what-are-the-possible-machine-readable-resolutions-of-the-luis-built-in-date-time-duration-and-set-entities"></a>Quais são as possíveis resoluções legíveis por computador da data, hora, duração interna do LUIS e conjunto de entidades?

Para obter uma lista de exemplos, consulte a seção [Entidades criadas previamente](/azure/cognitive-services/LUIS/luis-reference-prebuilt-entities) da documentação do LUIS.

## <a name="how-can-i-use-more-than-the-maximum-number-of-luis-intents"></a>Como posso usar mais que o número máximo de intenções de LUIS?

Você pode considerar dividir o modelo e chamar o serviço de LUIS em série ou paralelo.

## <a name="how-can-i-use-more-than-one-luis-model"></a>Como é possível usar mais de um modelo de LUIS?

Tanto o SDK do Bot Framework para Node.js quanto o SDK do Bot Framework para .NET dão suporte a chamada de vários modelos de LUIS a partir de um único diálogo de intenção de LUIS. Tenha em mente as seguintes advertências:

* O uso de vários modelos de LUIS assume que os modelos de LUIS possuem conjuntos de intenções não sobrepostos.
* O uso de vários modelos de LUIS pressupõe que as pontuações de diferentes modelos sejam comparáveis para selecionar a "melhor intenção combinada" em vários modelos.
* Usar vários modelos de LUIS significa que, se uma intenção corresponder a um modelo, também corresponderá fortemente à intenção "nenhum" dos outros modelos. Evite selecionar a intenção "nenhum" nessa situação, já que o SDK do Bot Framework para Node.js reduzirá automaticamente a pontuação para "nenhum" para evitar esse problema.

## <a name="where-can-i-get-more-help-on-luis"></a>Onde é possível obter mais ajuda sobre o LUIS?

* [Introdução ao LUIS (Serviço Inteligente de Reconhecimento Vocal) - Serviços Cognitivos da Microsoft](https://www.youtube.com/watch?v=jWeLajon9M8) (vídeo)
* [Sessão de Aprendizado Avançado para LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://www.youtube.com/watch?v=39L0Gv2EcSk) (vídeo)
* [Documentação do LUIS](/azure/cognitive-services/luis/)
* [Fórum do Serviço Inteligente de Reconhecimento Vocal](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 


## <a name="what-are-some-community-authored-dialogs"></a>Quais são alguns diálogos criados pela comunidade?

* [BotAuth](https://www.nuget.org/packages/BotAuth) - Autenticação do Azure Active Directory
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) - Expedição com base em expressões regulares de texto do usuário para métodos de diálogo

## <a name="what-are-some-community-authored-templates"></a>Quais são alguns modelos criados pela comunidade?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) - Modelo do Bot Builder ES6

## <a name="related-services"></a>Serviços Relacionados
### <a name="how-does-the-bot-framework-relate-to-cognitive-services"></a>Como o Bot Framework está relacionado aos Serviços Cognitivos?

O Bot Framework e os [Serviços Cognitivos](https://www.microsoft.com/cognitive) são criados com base em anos de pesquisa e uso em produtos populares da Microsoft. Essas funcionalidades permitem a todas as organizações aproveitar o poder dos dados, da nuvem e da inteligência a fim de criar os próprios sistemas inteligentes que revelam novas oportunidades, aumentar a velocidade dos negócios e liderar os setores nos quais elas atendem a seus clientes.

### <a name="what-is-the-direct-line-channel"></a>O que é o canal da Linha Direta?

Linha Direta é uma API REST que permite que você adicione o bot ao serviço, ao aplicativo móvel ou à página da Web.

Você pode escrever um cliente para a API de Linha Direta em qualquer linguagem. Basta codificar no [protocolo da Direct Line][DirectLineAPI], gerar um segredo na página de configuração da Direct Line e se comunicar com o bot de qualquer local em que o código reside.

A Linha Direta é adequada para:

* Aplicativos móveis no iOS, no Android e no Windows Phone e outros
* Aplicativos da área de trabalho no Windows, no OSX e muito mais
* Páginas da Web em que você precisa de mais personalização do que aquela oferecida pelo [canal de Webchat incorporável][WebChat]
* Aplicativos de serviço a serviço


## <a name="app-registration"></a>Registro do Aplicativo

### <a name="i-need-to-manually-create-my-app-registration-how-do-i-create-my-own-app-registration"></a>Preciso criar manualmente o meu Registro de Aplicativo. Como fazer para criar meu próprio Registro de Aplicativo?

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
