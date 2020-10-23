---
title: Diretrizes de segurança do bot Framework – serviço bot
description: Saiba mais sobre as diretrizes de segurança no bot Framework.
author: kamrani
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/07/2020
ms.openlocfilehash: a013a617eeda264704269202b86ec73fc29c6bff
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92413865"
---
# <a name="bot-framework-security-guidelines"></a>Diretrizes de segurança do Bot Framework

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Os bots são cada vez mais predominantes nas principais áreas comerciais, como serviços financeiros, varejo, turismo e assim por diante. Um bot pode coletar dados muito confidenciais, como cartões de crédito, SSN, contas bancárias e outras informações pessoais. Portanto, é importante que os bots sejam seguros e protejam contra ameaças e vulnerabilidades comuns.

Você pode tomar algumas medidas preventivas padrão para melhorar a segurança do bot. Algumas medidas de segurança são semelhantes àquelas usadas em outros sistemas de software, enquanto outras são específicas para o bot Framework.

## <a name="security-issues-in-a-nutshell"></a>Problemas de segurança em resumo

Este artigo agrupa problemas de segurança em duas categorias:

- **Ameaças**: as táticas que alguém pode usar para comprometer seu bot, como falsificação, violação, informações de divulgação, negação de serviço e assim por diante.

- **Vulnerabilidades**: as maneiras pelas quais o bot ou o gerenciamento do bot podem ser suscetíveis a tais táticas, como bugs ou a uma segurança negligente.

Reduzir suas vulnerabilidades é uma boa maneira de mitigar ameaças, e uma maneira conhecida de reduzir as vulnerabilidades é implementar pontos de verificação de segurança no processo de desenvolvimento e implantação.

## <a name="common-security-guidelines"></a>Diretrizes de segurança comuns

As áreas a seguir são cobertas por algumas práticas recomendadas de segurança padrão comuns a todos os aplicativos.

### <a name="protocols-and-encryption"></a>Protocolos e criptografia

Os dados podem ser adulterados durante a transmissão. Existem protocolos que fornecem criptografia para resolver problemas de uso indevido e adulteração.
Nesse sentido, os bots devem se comunicar apenas por canais criptografados. Isso torna difícil para qualquer pessoa que não seja o receptor e o remetente ver qualquer parte da mensagem ou da transação.

A criptografia é um dos métodos mais robustos para garantir que a segurança de bot e as empresas devam garantir proativamente sua eficácia, tomando medidas para despersonalização e criptografia de dados confidenciais.

Para trocar dados na conexão, qualquer sistema seguro deve usar o protocolo **https** , que transfere dados por http em conexões criptografadas protegidas pela TLS ( [segurança da camada de transporte](https://tools.ietf.org/html/rfc5246) ) ou pelo [protocolo SSL](https://tools.ietf.org/html/rfc6101) (SSL).  Consulte também [RFC 2818-http sobre TLS](https://tools.ietf.org/html/rfc2818).

### <a name="self-destructing-messages"></a>Destruindo mensagens automaticamente

Exclua permanentemente todos os dados confidenciais assim que eles não forem mais necessários, geralmente depois que a troca de mensagens terminar ou após um determinado período de tempo. Isso pode incluir informações de identificação pessoal, IDs, PINs, senhas, perguntas de segurança e respostas, e assim por diante.

### <a name="data-storage"></a>Armazenamento de dados

A prática recomendada chama o armazenamento de informações em um estado seguro por um determinado período de tempo e, em seguida, descartando-a posteriormente depois de ter atendido sua finalidade.

Algumas técnicas de segurança comuns são listadas abaixo.

#### <a name="database-firewalls"></a>Firewalls de banco de dados

- Por padrão, os firewalls negam o acesso ao tráfego. O único tráfego permitido deve originar-se de aplicativos específicos ou servidores Web que precisam acessar os dados.
- Você também deve implantar um firewall do aplicativo Web. Isso ocorre porque os ataques, como ataques de injeção de SQL direcionados a um aplicativo Web, podem ser usados para exfiltrar ou excluir dados do banco de dado.

#### <a name="database-hardening"></a>Proteção de banco de dados

- Verifique se o banco de dados ainda tem suporte do fornecedor e se você está executando a versão mais recente do banco de dados com todos os patches de segurança instalados para remover vulnerabilidades conhecidas.
- Desinstale ou desabilite os recursos ou serviços que você não precisa e certifique-se de alterar as senhas de todas as contas padrão de seus valores padrão; ou melhor, exclua as contas padrão que você não precisa.
- Verifique se todos os controles de segurança do banco de dados fornecidos pelo banco de dados estão habilitados, a menos que haja um motivo específico para que qualquer seja desabilitado.

#### <a name="minimize-valuable-information"></a>Minimizar informações valiosas

- Certifique-se de que você não está armazenando informações confidenciais que não precisam estar no banco de dados.
- Os dados retidos para fins de conformidade ou outras finalidades podem ser movidos para um armazenamento mais seguro, talvez offline, o que é menos suscetível a ameaças de segurança de banco de dados.
- Certifique-se de excluir todos os arquivos de histórico que são gravados por um servidor durante o procedimento de instalação original. Se a instalação for bem-sucedida, esses arquivos não terão nenhum valor, mas poderão conter informações que podem ser exploradas potencialmente.

### <a name="education"></a>Educação

Os bots fornecem uma ferramenta de interação inovadora entre uma empresa e seus clientes. Mas podem, potencialmente, fornecer um backdoor para adulterar o site de uma empresa. Portanto, uma empresa deve garantir que seus desenvolvedores compreendam a importância da segurança de bot como parte da segurança do site. Além disso, os erros dos usuários também podem ser um problema. Isso exigirá alguma educação sobre como os bots podem ser usados com segurança, por exemplo:

- Para os desenvolvedores, uma estratégia deve incluir treinamento interno sobre como usar o bot com segurança.
- Os clientes podem receber diretrizes detalhando como interagir com o bot com segurança.

## <a name="bot-specific-security-guidelines"></a>Diretrizes de segurança específicas do bot

As áreas a seguir são cobertas por algumas práticas recomendadas de segurança padrão para aplicativos do bot Framework.
As diretrizes a seguir descrevem as medidas de segurança de práticas recomendadas do bot Framework. Para obter mais informações, consulte as [perguntas frequentes sobre segurança e privacidade](~/bot-service-resources-faq-security.md).

### <a name="bot-connector-authentication"></a>Autenticação do conector de bot

O serviço de conector de bot usa nativamente o HTTPS para trocar mensagens entre um bot e canais (usuários). o SDK do bot Framework automatiza a autenticação básica de bot para canal para você.

> [!WARNING]
> Se você estiver escrevendo seu próprio código de autenticação, é crítico implementar corretamente todos os procedimentos de segurança. Ao implementar todas as etapas descritas no artigo de [autenticação](~/rest-api/bot-framework-rest-connector-authentication.md) , você pode reduzir o risco de um invasor ser capaz de ler mensagens que são enviadas para o bot, enviar mensagens que representam o bot e roubar chaves secretas.

### <a name="user-authentication"></a>Autenticação de usuário

A **autenticação do Serviço de Bot do Azure** permite que você autentique usuários e obtenha **tokens de acesso** de uma variedade de provedores de identidade, como o *Azure Active Directory*, *GitHub*, *Uber* e assim por diante. Você também pode configurar a autenticação de um provedor de identidade **OAuth2** personalizado. Tudo isso permite que você escreva **uma parte do código de autenticação** que funciona em todos os provedores de identidade e canais com suporte. Para utilizar essas funcionalidades, é necessário seguir estas etapas:

1. Configure estatisticamente o `settings` em seu bot que contém os detalhes do seu registro de aplicativo com um provedor de identidade.
1. Use um `OAuthCard`, respaldado pelas informações do aplicativo fornecidas na etapa anterior, para conectar um usuário.
1. Recupere tokens de acesso por meio da **API do Serviço de Bot do Azure**. Uma prática recomendada é inserir um limite de tempo em quanto tempo um usuário autenticado pode permanecer *conectado*.

Para obter mais informações, consulte o artigo [autenticação de usuário](~/v4sdk/bot-builder-concept-authentication.md) .

### <a name="web-chat"></a>Chat na Web

Quando você usa a *autenticação do Serviço de Bot do Azure* com o [Webchat](~/bot-service-channel-connect-webchat.md), há algumas considerações importantes sobre segurança que você precisa ter em mente.

1. **Representação**. Representação aqui significa um invasor que faz o bot achar que ele é outra pessoa. No Webchat, um invasor pode representar outra pessoa, **alterando a ID de usuário** da sua instância de Webchat. Para evitar isso, é recomendável que os desenvolvedores de bot tornem a **ID de usuário indecifrável**.

    Habilite as opções de **autenticação avançadas** do canal de linha direta para permitir que o serviço de bot do Azure detecte e rejeite ainda mais qualquer alteração de ID de usuário. Isso significa que a ID de usuário ( `Activity.From.Id` ) nas mensagens da linha direta para o bot será sempre a mesma que você inicializou o controle de chat Web. Observe que esse recurso requer que a ID de usuário comece com `dl_`.

    > [!NOTE]
    > Quando uma *User.Id* é fornecida durante a troca de um segredo para um token, essa *User.Id* é inserida no token. A linha direta garante que as mensagens enviadas para o bot tenham essa ID como a *from.ID*da atividade. Se um cliente enviar uma mensagem para uma linha direta com um *from.ID*diferente, ele será alterado para a **ID no token** antes de encaminhar a mensagem para o bot. Portanto, você não pode usar outra ID de usuário depois que um segredo de canal é inicializado com uma ID de usuário.

1. **Identidades do usuário**. Você deve saber que está lidando com duas identidades de usuário:

    1. A identidade do usuário em um canal.
    1. A identidade do usuário em um provedor de identidade no qual o bot está interessado.

    Quando um bot solicita ao usuário um em um canal para entrar em um provedor de identidade P, o processo de entrada deve garantir que o usuário a é aquele que entra em P. Se outro usuário B tiver permissão para entrar, então o usuário A teria acesso ao recurso do usuário B por meio do bot. No Webchat, temos dois mecanismos para verificar se o usuário correto entrou conforme descrito a seguir.

    1. No final da credencial, no passado, o usuário recebia um código de seis dígitos gerado aleatoriamente (também conhecido como código mágico). O usuário deveria digitar esse código na conversa que iniciava a conexão para concluir o processo de entrada. Esse mecanismo tende a resultar em uma experiência de usuário inadequada. Além disso, ele ainda está suscetível a ataques de phishing. Um usuário mal-intencionado pode induzir outro usuário a se conectar e a obter o código mágico por meio de phishing.

    2. Devido aos problemas com a abordagem anterior, o Serviço de Bot do Azure acabou com a necessidade do código mágico. O Serviço de Bot do Azure garante que o processo de conexão só possa ser concluído na **mesma sessão do navegador** que a do próprio Webchat.
    Para habilitar essa proteção, como desenvolvedor de bot, você deve iniciar o Webchat com um **token do Direct Line** que contenha uma **lista de domínios confiáveis que possam hospedar o cliente do Webchat do bot**. Antes, você só poderia obter esse token passando um parâmetro opcional não documentado para a API de token do Direct Line. Agora, com as opções de autenticação avançada, você pode especificar estaticamente a lista de domínios confiáveis (origem) na página de configuração do Direct Line.


## <a name="additional-information"></a>Informações adicionais

- [Autenticação de usuário](~/v4sdk/bot-builder-concept-authentication.md)
- [Adicionar autenticação ao seu bot por meio do Serviço de Bot do Azure](~/v4sdk/bot-builder-authentication.md)
- [Habilitar a segurança e o teste no localhost](~/bot-service-troubleshoot-authentication-problems.md#step-3-enable-security-and-test-on-localhost-)
- [Segredos e tokens](~/rest-api/bot-framework-rest-direct-line-3-0-authentication.md#secrets-and-tokens)
- [Tecnologias de autenticação](~/rest-api/bot-framework-rest-connector-authentication.md#authentication-technologies)
- [Recursos avançados de autenticação de linha direta](https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features)
- [Recomendações de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)
- [Proteção contra ameaças na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection)
- [Segurança de dados da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-data-security)
- [Segurança de contêineres na Central de Segurança](https://docs.microsoft.com/azure/security-center/container-security)
