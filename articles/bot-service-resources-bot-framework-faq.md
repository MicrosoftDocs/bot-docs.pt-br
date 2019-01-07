---
title: Perguntas frequentes sobre o Serviço de Bot | Microsoft Docs
description: Uma lista de perguntas frequentes sobre os elementos do Bot Framework e uma previsão de quando novos recursos serão disponibilizados.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/28/2018
ms.openlocfilehash: 660c10c400bb68e807f39eb1b7ccc38f500991e6
ms.sourcegitcommit: 0f65857943d318da48df0a8654cde53e9a699116
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53389309"
---
# <a name="bot-framework-frequently-asked-questions"></a>Perguntas frequentes sobre o Bot Framework

Este artigo contém respostas a algumas perguntas frequentes sobre o Bot Framework.

## <a name="background-and-availability"></a>Informações e disponibilidade
### <a name="why-did-microsoft-develop-the-bot-framework"></a>Por que a Microsoft desenvolveu o Bot Framework?

Embora a CUI (Interface do Usuário de Conversa) já esteja entre nós, no momento, poucos desenvolvedores têm o conhecimento e as ferramentas necessárias para criar experiências de conversa ou habilitar aplicativos e serviços existentes com uma interface de conversa que os usuários possam aproveitar. Criamos o Bot Framework para facilitar para os desenvolvedores criar e conectar bots excelentes aos usuários, independentemente de onde eles conversarem, incluindo os canais premier da Microsoft.

### <a name="what-is-the-v4-sdk"></a>O que é o SDK v4?
O SDK v4 do Construtor de Bot baseia-se nos comentários e nas lições aprendidas dos SDKs anteriores do Construtor de Bot. Ele apresenta os níveis certos de abstração, ao mesmo tempo que permite a "componentização" avançada dos blocos de construção de bot. Comece com um bot simples e aumente a sofisticação dele usando uma estrutura modular e extensível. Encontre as [perguntas frequentes](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) sobre o SDK no GitHub.

## <a name="bot-framework-sdk-version-3-lifetime-support"></a>Suporte de tempo de vida do SDK Versão 3 do Bot Framework 
Os bots do SDK V3 continuam sendo executados e tendo suporte do Serviço de Bot do Azure.  Desde o lançamento do SDK V4 do Bot Framework, assim como acontece com outras estruturas, continuamos com o suporte para o SDK V3 com correções de segurança e de bugs de alta prioridade e atualizações na camada de conector/protocolo.  Os clientes podem contar com o suporte para a v3 ao longo do ano de 2019.

### <a name="what-is-microsoft-plan-for-supporting-existing-v3-bots-what-happens-to-my-v3-bots-will-my-v3-bots-stop-working"></a>Qual é o plano da Microsoft para dar suporte aos bots V3 existentes? O que acontece com meus bots V3? Os meus bots V3 deixarão de funcionar?
Os bots do SDK V3 continuam sendo executados e tendo suporte do Serviço de Bot do Azure.  Desde o lançamento do SDK V4 do Bot Framework, assim como acontece com outras estruturas, continuamos com o suporte para o SDK V3 com correções de segurança e de bugs de alta prioridade e atualizações na camada de conector/protocolo.  Os clientes podem contar com o suporte para a v3 ao longo do ano de 2019.
- O Serviço de Bot do Azure e o Bot Framework V3 são ambos produtos de GA e possuem suporte total. As bibliotecas de protocolo e de conector do Bot Framework não foram alteradas e são compartilhadas entre os SDKs V3 e V4.  
- Os bots criados com o SDK V3 do Bot Framework (BotBuilder) continuarão a ter suporte ao longo do ano de 2019. 
- Os clientes podem continuar criando bots V3 usando o portal do Azure ou as ferramentas de CLI do Azure.

### <a name="what-happens-to-my-bot-written-to-rest--bot-framework-protocol-31"></a>O que acontece com meu bot gravado no REST & Bot Framework Protocol 3.1?
- O Serviço de Bot do Azure e o Bot Framework V3 são ambos produtos de GA e possuem suporte total.
- O protocolo do Bot Framework não foi alterado e é compartilhado entre os SDKs V3 e V4.  

### <a name="will-there-be-more-updates-additional-development-for-the-v3-sdk-or-just-bugfixes"></a>Haverá mais atualizações ou desenvolvimento adicional para o SDK V3 ou simplesmente correções de bugs?  
- Atualizaremos a V3 com pequenas melhorias, principalmente na camada do conector e com correções de segurança e de bugs de prioridade alta.  
- As atualizações para a V3 serão lançadas duas vezes por ano e conforme necessário, com base nas correções de bugs e/ou nas alterações de protocolo necessárias. 
- O plano atual é publicar versões secundárias e de patch do V3 NuGet e NPM para nossos SDKs de C# e JavaScript.

### <a name="why-v4-is-not-backwards-compatible-with-v3"></a>Por que a V4 não é retrocompatível com a V3?
- No nível do protocolo, a comunicação entre seu aplicativo de conversação (também conhecido como seu bot) e diferentes canais usa o protocolo de Atividade do Bot Framework, que é idêntico entre a V3 e a V4. A mesma infraestrutura subjacente do Serviço de Bot do Azure (SERVIÇO DE BOT DO AZURE) é compatível com os bots da V3 e da V4.
- O SDK V4 do Bot Framework oferece uma experiência de desenvolvimento voltado para conversação com uma arquitetura SDK que é modular e extensível, capacitando aos desenvolvedores a criar aplicativos de bate-papo robustos e sofisticados. O design extensível da V4 foi baseado nos comentários dos clientes, que sugeriram que os modelos de caixa de diálogo e os primitivos do SDK V3 eram muito rígidos e restringiam a extensibilidade.  

### <a name="what-is-the-general-migration-strategy-i-have-a-v3-bot-how-can-i-migrate-it-to-v4-can-i-migrate-my-v3-bot-to-v4"></a>O que é a estratégia de migração geral? Eu tenho um bot V3, como posso migrá-lo para V4 / Posso migrar meu bot V3 para V4?
- Neste momento, a ajuda para a migração de bots criados com o SDK V3 para o SDK V4 virá na forma de documentações e exemplos. Atualmente, não temos planos para fornecer nenhuma camada de compatibilidade do SDK V3 no SDK V4 que permitiria que a V3 criasse bots para funcionar dentro de um bot V4. 
- Se você já tiver bots do SDK V3 do Bot Framework em produção, não se preocupe, eles continuarão a funcionar normalmente até segunda ordem. 
- O SDK V4 do Bot Framework é uma evolução do muito bem-sucedido SDK V3. O V4 representa o lançamento de uma versão principal, que inclui alterações da falha que impedem a execução de bots V3 no mais recente SDK V4. 

### <a name="should-i-build-new-a-bot-using-v3-or-v4"></a>Devo criar um novo bot usando V3 ou V4?
- Para novas experiências de conversação, é recomendável que você inicie um novo bot usando o SDK V4 do Bot Framework.
- Se você já estiver familiarizado com o SDK V3 do Bot Framework, dedique algum tempo para aprender sobre a nova versão e os recursos oferecidos com o novo [SDK V4 do Bot Framework](http://aka.ms/botframeowrkoverview).
- Se você já tiver bots do SDK V3 do Bot Framework em produção, não se preocupe, eles continuarão a funcionar normalmente até segunda ordem.
- Você pode criar bots do SDK V4 do Bot Framework e da V3 mais antiga por meio do portal do Azure e da linha de comando do Azure. 

## <a name="channels"></a>Canais
### <a name="when-will-you-add-more-conversation-experiences-to-the-bot-framework"></a>Quando vocês adicionarão mais experiências de conversa ao Bot Framework?

Estamos planejando fazer melhorias contínuas no Bot Framework, incluindo canais adicionais, mas não podemos fornecer um prazo no momento.  
Caso deseje que um canal específico seja adicionado à estrutura, [fale conosco][Support].

### <a name="i-have-a-communication-channel-id-like-to-be-configurable-with-bot-framework-can-i-work-with-microsoft-to-do-that"></a>Tenho um canal de comunicação que desejo tornar configurável com o Bot Framework. Posso trabalhar com a Microsoft para fazer isso?

Não fornecemos um mecanismo geral para os desenvolvedores adicionarem novos canais ao Bot Framework, mas você pode conectar o bot ao seu aplicativo por meio da [API de Linha Direta][DirectLineAPI]. Se você for um desenvolvedor de um canal de comunicação e desejar trabalhar conosco para habilitar seu canal no Bot Framework, [adoraríamos ouvir sua opinião][Support].

### <a name="if-i-want-to-create-a-bot-for-skype-what-tools-and-services-should-i-use"></a>Se eu quiser criar um bot para o Skype, quais ferramentas e serviços deverei usar?

O Bot Framework foi projetado para criar, conectar e implantar bots escalonáveis, dinâmicos e de alto desempenho e alta qualidade no Skype e em muitos outros canais. O SDK pode ser usado para criar bots compatíveis com texto/SMS, imagem, botão e cartão (que constituem a maior parte das interações de bot hoje nas experiências de conversa), bem como interações de bot específicas do Skype, como experiências avançadas de áudio e vídeo.

Caso você já tenha um bot excelente e deseje alcançar o público-alvo do Skype, o bot poderá ser conectado com facilidade ao Skype (ou a qualquer canal compatível) por meio do Construtor de Bot para API REST (desde que ele tenha um ponto de extremidade REST acessível pela Internet).

## <a name="security-and-privacy"></a>Segurança e privacidade
### <a name="do-the-bots-registered-with-the-bot-framework-collect-personal-information-if-yes-how-can-i-be-sure-the-data-is-safe-and-secure-what-about-privacy"></a>Os bots registrados no Bot Framework coletam informações pessoais? Em caso afirmativo, como posso ter certeza de que os dados estão seguros e protegidos? E quanto à privacidade?

Cada bot é seu próprio serviço, e os desenvolvedores desses serviços são obrigados a fornecer Termos de Serviço e Políticas de Privacidade de acordo com seu Código de Conduta do Desenvolvedor.  Acesse essas informações no cartão do bot no Diretório de Bots.

para fornecer o serviço de E/S, o Bot Framework transmite sua mensagem e o conteúdo da mensagem (incluindo sua ID) do serviço de chat usado para o bot.

### <a name="can-i-host-my-bot-on-my-own-servers"></a>Pode hospedar meu bot em meus próprios servidores?
Sim. Seu bot pode ser hospedado em qualquer lugar na Internet. Em seus próprios servidores, no Azure ou em qualquer outro datacenter. O único requisito é que o bot deve expor um ponto de extremidade HTTPS publicamente acessível.

### <a name="how-do-you-ban-or-remove-bots-from-the-service"></a>Como os bots são vetados ou removidos do serviço?

Os usuários têm uma maneira de relatar o comportamento inadequado de um bot por meio do cartão de visita do bot no diretório. Os desenvolvedores precisam respeitar os termos de serviço da Microsoft para participar do serviço.

### <a name="which-specific-urls-do-i-need-to-whitelist-in-my-corporate-firewall-to-access-bot-framework-services"></a>Quais URLs específicas preciso incluir na lista de permissões de meu firewall corporativo para acessar os serviços Bot Framework?
Se tiver um firewall de saída bloqueando o tráfego do seu bot com a Internet, você precisará colocar as URLs a seguir no firewall:
- login.botframework.com (autenticação do Bot)
- login.microsoftonline.com (autenticação do Bot)
- westus.api.cognitive.microsoft.com (para a integração NLP do Luis.ai)
- state.botframework.com (armazenamento de estado do Bot para criação de protótipos)
- cortanabfchanneleastus.azurewebsites.net (canal da Cortana)
- cortanabfchannelwestus.azurewebsites.net (canal da Cortana)
- *.botframework.com (canais)

### <a name="can-i-block-all-traffic-to-my-bot-except-traffic-from-the-bot-connector-service"></a>Posso bloquear todo o tráfego para o meu bot, exceto o tráfego do Serviço de Conector do Bot?
 Não. Esse tipo de lista de permissões de DNS ou de endereço IP é inviável. O serviço Bot Framework Connector é hospedado em datacenters do Azure em todo o mundo e a lista de IPs do Azure está em constante mudança. A lista de permissões de determinados endereços IP pode funcionar um dia e parar no próximo, à medida que os Endereços IP do Azure mudam.
 
### <a name="what-keeps-my-bot-secure-from-clients-impersonating-the-bot-framework-connector-service"></a>O que mantém o meu bot protegido de clientes que representam o Serviço Bot Framework Connector ?
1. O token de segurança que acompanha todas as solicitações feitas para seu bot tem a ServiceUrl codificada dentro dele, o que significa que, mesmo se um invasor obtiver acesso ao token, eles não conseguirá redirecionar a conversa para uma nova ServiceUrl. Isso é imposto por todas as implementações do SDK e documentado em nossos materiais de [referência](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) de autenticação.

2. Se o token de entrada estiver ausente ou se for malformado, o SDK do Bot Framework não irá gerar um token na resposta. Isso limita o dano que poderá ocorrer se o bot for configurado incorretamente.
3. Dentro do bot, você pode verificar manualmente a ServiceUrl fornecida no token. Isso torna o bot mais frágil em caso de alterações de topologia de serviço, por isso que é possível, mas não recomendado.


Observe que essas são as conexões de saída do bot para a Internet. Não há uma lista de nomes DNS ou Endereços IP que o Serviço Bot Framework Connector usará para se comunicar com o bot. Não há suporte para a entrada na lista de permissões do Endereço IP.

## <a name="rate-limiting"></a>Limitação de taxa
### <a name="what-is-rate-limiting"></a>O que é a limitação de taxa?
O serviço Bot Framework precisa proteger a si próprio e a seus clientes contra padrões de chamada abusivos (por exemplo, ataque de negação de serviço), de modo que nenhum bot possa prejudicar o desempenho de outros bots. Para obter esse tipo de proteção, adicionamos limites de taxa (também conhecidos como limitação) a todos os pontos de extremidade. Impondo um limite de taxa, podemos restringir a frequência com a qual um bot pode fazer uma chamada específica. Por exemplo: com a limitação de taxa habilitada, se um bot desejar postar um grande número de atividades, ele precisará espaçá-las por um período. Observe que a finalidade da limitação de taxa não é limitar o volume total de um bot. Ela se destina a prevenir abusos da infraestrutura de conversa que não seguem os padrões de conversa humana.

### <a name="how-will-i-know-if-im-impacted"></a>Como saberei se foi afetado?
É improvável que você enfrentará a limitação de taxa, mesmo com um alto volume. A maior parte da limitação de taxa só ocorrerá devido ao envio em massa de atividades (de um bot ou de um cliente), um teste de carga extrema ou um bug. Quando uma solicitação é limitada, uma resposta HTTP 429 (Número Excessivo de Solicitações) é retornada juntamente com um cabeçalho Retry-After, que indica o tempo (em segundos) que é necessário aguardar antes do êxito da nova tentativa da solicitação. Colete essas informações habilitando a análise no bot por meio do Azure Application Insights. Se preferir, adicione um código ao bot para registrar mensagens em log. 

### <a name="how-does-rate-limiting-occur"></a>Como ocorre a limitação de taxa?
Isso pode ocorrer se:
-   um bot envia mensagens com muita frequência
-   um cliente de um bot envia mensagens com muita frequência
-   os clientes da Linha Direta solicitam um novo Soquete da Web com muita frequência

### <a name="what-are-the-rate-limits"></a>Quais são os limites de taxa?
Estamos continuamente ajustando os limites de taxa para torná-los os mais brandos possíveis, ao mesmo tempo que protegemos nosso serviço e nossos usuários. Como os limites serão alterados ocasionalmente, não estamos publicando os números no momento. Caso você seja afetado pela limitação de taxa, fique à vontade para falar conosco pelo email [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

## <a name="related-services"></a>Serviços Relacionados
### <a name="how-does-the-bot-framework-relate-to-cognitive-services"></a>Como o Bot Framework está relacionado aos Serviços Cognitivos?

O Bot Framework e os [Serviços Cognitivos](http://www.microsoft.com/cognitive) são novas funcionalidades introduzidas no [Microsoft Build 2016](http://build.microsoft.com) que também serão integradas ao Cortana Intelligence Suite em GA. Ambos os serviços foram criados após muitos anos de pesquisa e uso em produtos populares da Microsoft. Essas funcionalidades combinadas com o ‘Cortana Intelligence’ permitem a todas as organizações aproveitar o poder dos dados, da nuvem e da inteligência a fim de criar seus próprios sistemas inteligentes que revelam novas oportunidades, aumentar a velocidade dos negócios e liderar os setores nos quais elas atendem a seus clientes.

### <a name="what-is-cortana-intelligence"></a>O que é o Cortana Intelligence?

O Cortana Intelligence é um pacote de Inteligência, de Análise Avançada e de Big Data totalmente gerenciado que transforma seus dados em ação inteligente.  
É um pacote abrangente que reúne tecnologias fundadas após muitos anos de pesquisa e inovação em toda a Microsoft (abrangendo análise avançada, aprendizado de máquina, armazenamento de Big Data e processamento na nuvem) e:

* Permite que você colete, gerencie e armazene todos os seus dados que podem aumentar de forma contínua e econômica ao longo do tempo de uma maneira segura e escalonável.
* Fornece análises fáceis e acionáveis ativadas pela nuvem que permite prever, prescrever e automatizar a tomada de decisões para os problemas mais difíceis.
* Permite sistemas inteligentes por meio de serviços cognitivos e agentes que possibilitam que você veja, ouça, interprete e entenda o mundo ao seu redor de maneiras mais naturais e contextuais.

Com o Cortana Intelligence, esperamos ajudar nossos clientes empresariais a descobrir novas oportunidades, aumentar a velocidade dos negócios e serem líderes em seus setores.

### <a name="what-is-the-direct-line-channel"></a>O que é o canal da Linha Direta?

Linha Direta é uma API REST que permite que você adicione o bot ao serviço, ao aplicativo móvel ou à página da Web.

Você pode escrever um cliente para a API de Linha Direta em qualquer linguagem. Basta codificar no [protocolo da Linha Direta][DirectLineAPI], gerar um segredo na página de configuração da Linha Direta e se comunicar com o bot de qualquer local em que o código reside.

A Linha Direta é adequada para:

* Aplicativos móveis no iOS, no Android e no Windows Phone e outros
* Aplicativos da área de trabalho no Windows, no OSX e muito mais
* Páginas da Web em que você precisa de mais personalização do que aquela oferecida pelo [canal de Webchat incorporável][WebChat]
* Aplicativos de serviço a serviço

[DirectLineAPI]: https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts
[Support]: bot-service-resources-links-help.md
[WebChat]: bot-service-channel-connect-webchat.md

