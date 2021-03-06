---
ms.topic: conceptual
ms.author: kamrani
ms.service: bot-service
title: Diretrizes de revisão de bot
description: Saiba mais sobre os requisitos mínimos que sua integração de aplicativos deve atender antes que ele possa ser publicado em um canal da Microsoft, como o Microsoft Teams.
ms.openlocfilehash: b2a254d4637afbefef9440ad9974d9b3f519ea3e
ms.sourcegitcommit: 71e7c93a312c21f0559005656e7b237e5a74113c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95456160"
---
# <a name="bot-review-guidelines"></a>Diretrizes de revisão de bot

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Agradecemos você e obrigado por investir em seus talentos e tempo na criação de bot, botlets, aplicativos Web, suplementos ou habilidades ("integrações de aplicativos") para os canais da Microsoft. Esses são os requisitos mínimos que a integração de aplicativo deve atender antes de ser publicada em um canal da Microsoft, como o Microsoft Teams. Cada canal pode ter requisitos específicos, além dos requisitos detalhados abaixo. Se aplicável, você encontrará termos específicos do canal na página de configuração de cada canal e poderá ser solicitado a se inscrever no serviço de um canal antes de poder publicar um bot nesse canal.

## <a name="app-integration-policies"></a>Políticas de integração de aplicativo

### <a name="1-value-representation-security-and-usability"></a>1. valor, representação, segurança e usabilidade

A integração de aplicativo e os metadados associados devem:

- ter metadados informativos e distintos e fornecer uma experiência de usuário valiosa e de qualidade;
- refletir com precisão e clareza a origem, funcionalidade e os recursos da integração do aplicativo e descrever quaisquer limitações importantes;
- não utilizar um nome ou ícone semelhante ao de outros aplicativos e não declarar ser de uma empresa, órgão governamental ou outra entidade se você não tiver permissão para fazer essa representação;
- não colocar em risco nem comprometer a segurança do usuário, ou a segurança ou funcionalidade do canal da Microsoft;
- não tentar alterar ou estender a funcionalidade descrita em violação a essas políticas ou aos termos aplicáveis do canal da Microsoft;
- não incluir nem habilitar malwares;
- ser testável;
- continuar em execução e permanecer responsivo à entrada do usuário;
- incluir um link funcional para os Termos de Serviço;
- operar conforme estabelecido na descrição, perfil, termos de uso e política de privacidade;
- operar em conformidade com os termos aplicáveis ao Microsoft Bot Framework (ou outros termos aplicáveis ao desenvolvimento) e aos Termos do Canal da Microsoft (ou outros termos aplicáveis ao canal no qual a integração do aplicativo é publicada);
- informar aos usuários se a integração do aplicativo inclui interação humana (por exemplo, atendimento ao cliente ou suporte com uma pessoa ao vivo);
- ser localizado para todos os idiomas com suporte. O texto da descrição da integração de aplicativos deve ser localizado em cada idioma que você declarar.

### <a name="2privacy"></a>2. privacidade

- Se a integração do aplicativo gerenciar informações pessoais dos usuários (informações pessoais incluem todas as informações ou dados que identificam ou podem ser utilizados para identificar uma pessoa ou que estejam associados a essas informações ou dados. Exemplos de informações pessoais incluem: nome e endereço, número de telefone, identificadores biométricos, localização, contatos, fotos, gravações de áudio e vídeo, documentos, SMS, email ou outras comunicações por texto, capturas de tela e, em alguns casos, histórico de navegação combinado), você deverá fornecer um link proeminente da integração do aplicativo a uma política de privacidade aplicável e essa política de privacidade deverá estar em conformidade com todas as leis, regulamentos e requisitos de políticas aplicáveis. Essa política deverá abranger todos os dados que você estiver coletando ou transmitindo, o que você fará com esses dados e (se aplicável) com quem estará compartilhando. Se você não tiver uma declaração de privacidade, aqui estão alguns recursos de terceiros * que podem ser de ajuda:

Futuro do Fórum de Privacidade – [Gerador de Política de Privacidade de Aplicativo](http://www.applicationprivacy.org/do-tools/privacy-policy-generator/)

Iubenda – [Gerador de Política de Privacidade](http://www.iubenda.com/en)

_Você concorda em assumir todos os riscos e responsabilidades decorrentes do uso desses recursos de terceiros e que a Microsoft não é responsável por quaisquer problemas decorrentes do uso de tais recursos._

- A integração de aplicativo não deve coletar, armazenar nem transmitir informações pessoais não relacionadas à finalidade principal, sem antes obter o consentimento expresso do usuário. Você deverá obter todos os consentimentos dos usuários para processar informações pessoais quando exigido por lei.
- Você não pode publicar uma integração de aplicativo que é direcionada a filhos sob a idade de 13 (conforme definido no ato de proteção contra privacidade online da criança), sem a permissão expressa da Microsoft.

### <a name="3-financial-transactions"></a>3. transações financeiras

- Para integrações de aplicativos habilitados para pagamento:
  - A integração de aplicativos não pode transmitir detalhes de instrumentos financeiros por meio da interface do usuário;
  - Sujeito a quaisquer restrições de canal ou plataforma de terceiros, a integração do aplicativo poderá: (a) dar suporte a pagamentos através do [Centro de Vendas da Microsoft](https://seller.microsoft.com/) de acordo com os termos do Contrato do Centro de Vendas da Microsoft; ou (b) transmitir links para outros serviços de pagamento seguro;
  - Se a integração de aplicativo permitir os mecanismos de pagamento supracitados, você deverá divulgar isso nos termos de uso e política de privacidade de integrações de aplicativos (e em qualquer site ou página de perfil para a integração de aplicativo) antes que o usuário final concorde em usar a integração de aplicativo;
  - Você deve indicar claramente que uma integração de aplicativo está habilitada para pagamento em seu perfil e fornecer aos usuários finais os detalhes do atendimento ao cliente do comerciante; e
  - Você não poderá publicar integrações de aplicativos em nenhum canal da Microsoft que inclua links ou que, de outra forma, direcione os usuários a serviços de pagamento para a compra de produtos digitais sem a permissão expressa da Microsoft.

### <a name="4-content"></a>4. conteúdo

- Todo o conteúdo na integração de aplicativo e os metadados associados devem ser originalmente criados pelo editor, devidamente licenciados pelo titular dos direitos de terceiros, usados conforme permitido pelo titular dos direitos, ou usados conforme permitido por lei.
- Você não publicará uma integração de aplicativo ou conteúdo na integração do aplicativo que:
  - for ilegal;
  - explorar, prejudicar ou ameaçar prejudicar crianças;
  - incluir publicidade, spam, postagens, mensagens ou comunicações em massa indesejadas ou não solicitadas;
  - for considerado material impróprio ou ofensivo (envolvendo, por exemplo, nudez, bestialidade, profanidade, pornografia ou conteúdo sexual explícito, violência explícita ou gratuita, produtos relacionados ao tabaco, atividades criminosas, perigosas ou irresponsáveis, drogas, violações dos direitos humanos, a construção ou uso ilegal de armas contra uma pessoa ou animal no mundo real, uso irresponsável de produtos alcoólicos);
  - for falso ou enganoso (por exemplo, pedindo dinheiro sob falsos pretextos, personificar falsamente outra pessoa);
  - for prejudicial a você, ao Canal da Microsoft, ou a outros, ou criar um risco à segurança (por exemplo, transmissão de vírus, perseguição, postagem de conteúdo terrorista, comunicação de incitamento ao ódio ou defesa de discriminação, ódio ou violência com base nas considerações de raça, etnia, nacionalidade, idioma, gênero, idade, deficiência, religião, orientação sexual, situação militar ou participação em qualquer outro grupo social);
  - violar direitos de terceiros (por exemplo, compartilhamento não autorizado de músicas com direitos autorais ou de outros materiais protegidos por direitos autorais);
  - for difamatório, injurioso, calunioso ou ameaçador;
  - violar a privacidade de outros;
  - processa informações que (a) se relacionam à condição, tratamento ou pagamento de um paciente para tratamento; (b) identifica indivíduos como ou se comunicam com pacientes, membros do plano de integridade ou beneficiários; ou (c) é, de outra forma, ' informações de integridade protegidas ' sob o ato de portabilidade e responsabilidade de seguro de saúde, conforme corrigido ("HIPAA") ou execute qualquer atividade governada pela HIPAA se você for uma "entidade coberta" ou "parceiro comercial", conforme definido sob HIPAA;
  - for ofensivo em qualquer país/região para o qual aplicativo é direcionado. O conteúdo poderá ser considerado ofensivo em determinados países/regiões devido a legislações locais ou normas culturais;
  - ajudar outros a violarem essas regras.
- Se você fizer quaisquer alterações significativas na integração do aplicativo, deverá notificar a Microsoft antecipadamente, enviando um email para o canal específico no qual você publicou.  As alterações feitas no registro do bot podem exigir que o bot seja revisado para garantir que ele continue atendendo aos requisitos declarados aqui.  A Microsoft tem o direito, a seu exclusivo critério, de revisar de forma intermitente as integrações de aplicativos em qualquer canal e remover sem aviso prévio.
