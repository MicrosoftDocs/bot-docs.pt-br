---
title: Linha de base de segurança do Azure para serviço de bot do Azure
description: A linha de base de segurança do serviço de bot do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: bot-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2622dd41a09f12ecfe0bcb45e52547107c47a584
ms.sourcegitcommit: aa5cc175ff15e7f9c8669e3b1398bc5db707af6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98576548"
---
# <a name="azure-security-baseline-for-azure-bot-service"></a>Linha de base de segurança do Azure para serviço de bot do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 2,0](https://docs.microsoft.com/azure/security/benchmarks/overview) para Microsoft Azure serviço bot. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao serviço de bot do Azure. Os **controles** não aplicáveis ao serviço de bot do Azure foram excluídos.

Para ver como o serviço de bot do Azure é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do serviço de bot do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: ao implantar recursos do serviço de bot do Azure, você deve criar ou usar uma rede virtual existente. Verifique se todas as redes virtuais do Azure seguem um princípio de segmentação empresarial que se alinha aos riscos de negócios. Qualquer sistema que possa incorrer em risco maior para a organização deve ser isolado em sua própria rede virtual e suficientemente protegido com um NSG (grupo de segurança de rede) e/ou o Firewall do Azure.

- [Usar a extensão de serviço de aplicativo do Direct Line com uma VNET](bot-service-channel-directline-extension-vnet.md)

- [Como criar um grupo de segurança de rede com regras de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Como implantar e configurar o Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

**Orientação**: Use o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os datacenters do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública e oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. Para VPN ponto a site e VPN site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e do Azure ExpressRoute.

Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [Quais são os modelos de conectividade do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)

- [Visão geral da VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)

- [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Diretrizes**: Proteja os recursos do serviço de bot do Azure contra ataques de redes externas, incluindo ataques de DDoS (negação de serviço distribuído), ataques específicos de aplicativos e tráfego de Internet não solicitado e potencialmente mal-intencionado. Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. Proteja seus ativos contra ataques de DDoS habilitando a proteção padrão de DDoS em suas redes virtuais do Azure. Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados aos seus recursos relacionados à rede.

Use os recursos do WAF (firewall do aplicativo Web) no gateway Aplicativo Azure, na porta frontal do Azure e na CDN (rede de distribuição de conteúdo) do Azure para proteger seus aplicativos em execução no serviço de bot do Azure contra ataques de camada de aplicativo.

- [Documentação do firewall do Azure](https://docs.microsoft.com/azure/firewall/)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Recomendações da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-network)

- [Como implantar o Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/overview)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Orientação**: Use o Firewall do Azure com a filtragem baseada em inteligência contra ameaças para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. Quando a inspeção de carga é necessária, você pode implantar uma solução de IDS/IPS de terceiros do Azure Marketplace com recursos de inspeção de conteúdo. Como alternativa, você pode optar por usar IDS/IPS baseados em host ou uma solução de EDR (detecção e resposta de ponto de extremidade) baseada em host em conjunto com o ou em vez de IDS/IPS baseados em rede.

Observação: se você tiver uma regulamentação ou outro requisito para o uso de IDS/IPS, verifique se ele sempre está ajustado para fornecer alertas de alta qualidade para sua solução SIEM.

- [Como implantar o Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [O Azure Marketplace inclui recursos de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Recurso EDR do Microsoft defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir controles de acesso à rede em grupos de segurança de rede ou firewall do Azure configurados para os recursos do serviço de bot do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo: AzureBotService) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: o serviço de bot do azure usa Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Padronize no Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:
- Recursos de nuvem da Microsoft, como o portal do Azure, o Armazenamento do Azure, as Máquinas Virtuais do Azure (Linux e Windows), o Azure Key Vault e os aplicativos PaaS e SaaS.

- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar sua postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

Observação: o Azure AD dá suporte a provedores de identidade externos, que permitem aos usuários sem uma conta Microsoft entrar nos respectivos aplicativos e recursos com a identidade externa.

- [Locação no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/single-and-multi-tenant-apps)

- [Como criar e configurar uma instância do Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Definir locatários do Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)

- [Usar provedores de identidade externos para um aplicativo](https://docs.microsoft.com/azure/active-directory/b2b/identity-providers)

- [O que é a classificação de segurança de identidade do Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: o Azure ad fornece gerenciamento de identidade e acesso para recursos do Azure, aplicativos de nuvem e aplicativos locais. O gerenciamento de identidades e acesso se aplica a identidades corporativas, como funcionários, bem como identidades externas, como parceiros, fornecedores e fornecedores.

Use o SSO (logon único) do Azure AD para gerenciar e proteger o acesso aos dados e recursos da sua organização localmente e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo, seguro e maior visibilidade e controle.

- [Entender o SSO do aplicativo com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Diretrizes**: o serviço de bot do Azure usa Azure Active Directory que dá suporte a controles de autenticação fortes por meio da MFA (autenticação multifator) e de métodos fortes de senha.
- Autenticação multifator: habilite a MFA do Azure AD, siga a identidade da central de segurança do Azure e as recomendações de gerenciamento de acesso para sua configuração de MFA. A MFA pode ser imposta em todos os usuários, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.

- Autenticação com senha: três opções de autenticação com senha estão disponíveis: Windows Hello para empresas, Microsoft Authenticator app e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, verifique se o nível mais alto do método de autenticação forte é usado, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

Se a autenticação baseada em senha herdada ainda for usada para a autenticação do Azure AD, lembre-se de que as contas somente em nuvem (contas de usuário criadas diretamente no Azure) têm uma política de senha de linha de base padrão. E contas híbridas (contas de usuário que vêm do Active Directory local) seguem as políticas de senha local. Ao usar a autenticação baseada em senha, o Azure AD fornece um recurso de proteção de senha que impede que os usuários definam senhas que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas banidas que são atualizadas com base na telemetria, e os clientes podem aumentar a lista com base em suas necessidades (por exemplo, identidade visual, referências culturais, etc.). Essa proteção por senha pode ser usada para contas híbridas e somente em nuvem.

Observação: a autenticação baseada em credenciais de senha sozinhas é suscetível a métodos de ataque populares. Para maior segurança, use autenticação forte, como MFA e uma política de senha forte. Para aplicativos de terceiros e serviços do Marketplace que podem ter senhas padrão, você deve alterá-los durante a configuração inicial do serviço.

- [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Introdução às opções de autenticação sem senha do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)

- [Política de senha padrão do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar senhas inadequadas usando a proteção de senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: o serviço de bot do Azure é integrado ao Azure Active Directory, que fornece as seguintes fontes de dados:

- Entradas: o relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e as atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM de terceiros.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura.

O ATP (Proteção Avançada contra Ameaças) do Azure é uma solução de segurança que pode usar os sinais do Active Directory para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de membros mal-intencionados.

Relatórios de atividade de auditoria no Azure Active Directory https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs

- [Como exibir entradas suspeitas do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Alertas do módulo de proteção da inteligência contra ameaças da Central de Segurança do Azure](https://docs.microsoft.com//azure/security-center/alerts-reference)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

**Diretrizes**: o serviço de bot do Azure permite que os clientes implantem/executem código ou configurações ou dados persistentes potencialmente com identidades/segredos, é recomendável implementar o verificador de credenciais para identificar as credenciais no código ou nas configurações ou nos dados persistentes. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Para o GitHub, você pode usar o recurso de verificação de segredo nativo para identificar as credenciais ou outra forma de segredos dentro do código.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Verificação de segredos do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: o serviço de bot do Azure usa o RBAC do Azure para isolar o acesso a sistemas críticos para os negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais elas estão.

Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo aos seus ativos críticos para os negócios, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas comerciais críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

Certifique-se de atribuir contas privilegiadas separadas que sejam diferentes das contas de usuário padrão usadas para tarefas de email, navegação e produtividade.

- [Componentes do Azure e modelo de referência](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Administradores de assinatura do Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o serviço de bot do Azure usa contas do Azure Active Directory (Azure AD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seus níveis de acesso sejam válidos. Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar um fluxo de trabalho de relatório de revisão de acesso que facilita o processo de revisão.

Além disso, o Azure Privileged Identity Management pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar contas de administrador que estão obsoletas ou configuradas incorretamente.

Observação: alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você deve gerenciar esses usuários separadamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-start-access-review)

- [Como usar as revisões de identidade e acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: o serviço de bot do Azure usa Azure Active Directory para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos

**Diretrizes**: o serviço de bot do Azure é integrado com o Azure Active Directory para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.

- [O que são revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

- [O que é o gerenciamento de direitos do AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas de modo central para impor a configuração protegida, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)

- [Implantar uma estação de trabalho com acesso privilegiado](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos)

**Diretrizes**: o serviço de bot do Azure é integrado ao RBAC (controle de acesso baseado em função) do Azure para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD PIM (Privileged Identity Management) e deve ser examinado periodicamente.

Use funções internas para alocar a permissão e somente crie uma função personalizada quando necessário.

O que é o controle de acesso baseado em função do Azure (RBAC do Azure) https://docs.microsoft.com/azure/role-based-access-control/overview

- [Como configurar o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

- [Como usar as revisões de identidade e acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: proteja dados confidenciais restringindo o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure), controles de acesso baseados em rede e controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados).

Para garantir um controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa. A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados.

- [RBAC (Controle de Acesso Baseado em Função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitorar a transferência não autorizada de dados confidenciais

**Diretriz**: monitore a transferência não autorizada de dados para localizações fora da visibilidade e do controle da empresa. Isso normalmente envolve o monitoramento de atividades anormais (transferências grandes ou incomuns) que podem indicar exfiltração não autorizada dos dados.

O ATP (Proteção Avançada contra Ameaças) do Armazenamento do Azure e o ATP do SQL do Azure podem alertar você sobre a transferência anormal de informações, que podem indicar transferências não autorizadas de informações confidenciais.

A AIP (Proteção de Informações do Azure) fornece funcionalidades de monitoramento para informações que foram classificadas e rotuladas.

Se necessário, para a conformidade de DLP (prevenção contra perda de dados), use uma solução de DLP baseada em host para impor controles de detecção e/ou prevenção a fim de evitar a exfiltração dos dados.

- [Habilitar o ATP do SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

- [Habilitar o ATP do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Diretrizes**: todos os pontos de extremidade do serviço de bot do Azure são expostos por http impõem o TLS 1,2. Com um protocolo de segurança imposto, os consumidores que tentarem chamar um ponto de extremidade do serviço de bot do Azure devem aderir a estas diretrizes:

- O sistema operacional do cliente (SO) precisa oferecer suporte a TLS 1,2.
- A linguagem (e plataforma) usada para fazer a chamada HTTP precisa especificar o TLS 1,2 como parte da solicitação. Dependendo do idioma e da plataforma, a especificação de TLS é feita implicitamente ou explicitamente.

Para complementar os controles de acesso, os dados em trânsito devem ser protegidos contra ataques "fora de banda" (por exemplo, captura de tráfego) usando a criptografia para garantir que os invasores não possam ler ou modificar os dados com facilidade.

Embora isso seja opcional para o tráfego em redes privadas, isso é essencial para o tráfego em redes externas e públicas. Para o tráfego HTTP, verifique se os clientes que se conectam aos recursos do Azure podem negociar TLS v 1.2 ou superior. Para gerenciamento remoto, use SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não criptografado. As versões e os protocolos de SSL, TLS e SSH obsoletos e as codificações fracas devem ser desabilitadas.

Por padrão, o Azure fornece criptografia para dados em trânsito entre os data centers do Azure.

- [Serviço de bot do Azure impondo TLS (segurança da camada de transporte) 1,2](https://azure.microsoft.com/updates/azure-bot-service-enforcing-transport-layer-security-tls-1-2/)

- [Entender a criptografia em trânsito com o Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Informações sobre a Segurança do TLS](https://docs.microsoft.com/security/engineering/solving-tls1-problem)

- [Criptografia dupla para dados do Azure em trânsito](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: criptografar dados confidenciais em repouso

**Orientação**: para complementar os controles de acesso, os dados em repouso devem ser protegidos contra ataques "fora de banda" (como o acesso ao armazenamento subjacente) usando a criptografia. Isso ajuda a garantir que os invasores não possam ler ou modificar os dados facilmente.

Por padrão, o Azure fornece dados em criptografia REST. Para dados altamente confidenciais, você tem opções para implementar criptografia adicional em repouso em todos os recursos do Azure, quando disponível. O Azure gerencia suas chaves de criptografia por padrão, mas o Azure fornece opções para gerenciar suas próprias chaves (chaves gerenciadas pelo cliente) para determinados serviços do Azure.

Se necessário para a conformidade em recursos de computação, implemente uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso aos dados, mesmo quando os dados são copiados fora de um sistema.

- [Noções básicas sobre a criptografia em repouso do Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-at-rest-in-microsoft-cloud-services)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

- [Modelo de criptografia e tabela de gerenciamento de chaves](https://docs.microsoft.com/azure/security/fundamentals/encryption-models)

- [Criptografia dupla de dados em repouso no Azure](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-at-rest)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure.

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização.

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas.

Observação: podem ser necessárias permissões adicionais a fim de obter visibilidade das cargas de trabalho e dos serviços.

- [Visão geral da função Leitor de segurança](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

O serviço de bot do Azure não oferece suporte para implantações de recursos baseadas em Azure Resource Manager e descoberta com o grafo de recursos do Azure.

- [Como criar consultas com o Azure Resource Graph Explorer](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Gerenciamento de inventário de ativos da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/asset-inventory)

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Como criar consultas com o Azure Resource Graph Explorer](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretrizes**: Não aplicável. O serviço de bot do Azure não pode ser usado para garantir a segurança de ativos em um processo de gerenciamento do ciclo de vida. É responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados de alto impacto. É recomendável que o cliente crie um processo para capturar as alterações de configuração de rede e atributo, meça o impacto de alterações e crie tarefas de correção, conforme aplicável.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure ad para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Diretrizes**: o serviço de bot do Azure não fornece recursos nativos para monitorar ameaças de segurança relacionadas a seus recursos.

Encaminhe todos os logs do serviço de bot do Azure para o SIEM, que pode ser usado para configurar as detecções de ameaças personalizadas. Verifique se você está monitorando diferentes tipos de ativos do Azure para possíveis ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir os falsos positivos para os analistas classificarem. Os alertas podem ser originados de dados de log, agentes ou outros dados.

- [Criar regras de análise personalizadas para detectar ameaças](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats-custom)

- [Inteligência contra ameaças cibernéticos com o Azure Sentinel](https://docs.microsoft.com/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Diretriz**: o Azure AD fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados ao Azure Monitor, ao Azure Sentinel ou a outras ferramentas de monitoramento/SIEM para casos de uso de monitoramento e análise mais sofisticados:
-   Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

-   Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

-   Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

-   Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como um número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de proteção contra ameaças da central de segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (como máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (como banco de dado SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você veja anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividade de auditoria no Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs)

- [Habilitar o Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

- [Proteção contra ameaças na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/threat-protection)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Orientação**: habilitar e coletar logs de recursos do NSG (grupo de segurança de rede), logs de fluxo do NSG, logs de firewall do Azure e logs de WAF (firewall do aplicativo Web) para análise de segurança para dar suporte a investigações de incidentes, busca de ameaças e geração de alertas de segurança. Você pode enviar os logs de fluxo para um espaço de trabalho Azure Monitor Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações.

O serviço de bot do Azure não produz nem processa logs de consulta DNS que precisam ser habilitados.

- [Como habilitar logs de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Como habilitar logs de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Logs e métricas do Firewall do Azure](https://docs.microsoft.com/azure/firewall/logs-and-metrics)

- [Como habilitar e usar Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Monitoramento com o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

- [Soluções de monitoramento de rede do Azure no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que estão disponíveis automaticamente, contêm todas as operações de gravação (put, post, Delete) para os recursos dos serviços de bot do Azure, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

O serviço de bot do Azure atualmente não produz logs de recursos do Azure.

- [Como coletar logs e métricas de plataforma com Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Entender o registro em log e diferentes tipos de log no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Como integrar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Orientação**: Verifique se as contas de armazenamento ou os espaços de trabalho log Analytics usados para armazenar os logs do serviço de bot do Azure têm o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Como configurar Log Analytics período de retenção do espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Diretriz**: verifique se a sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está os exercendo regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](https://docs.microsoft.com/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

**Diretriz**: configure as informações de contato de incidentes de segurança na Central de Segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes.

- [Como definir o contato de segurança da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – criar incidentes com base em alertas de alta qualidade

**Diretrizes**: verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade de alertas. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos.

Alertas de alta qualidade podem ser criados com base na experiência com incidentes passados, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas, focando e correlacionando várias fontes de sinal.

A central de segurança do Azure fornece alertas de alta qualidade em vários ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação.

Exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. Exporte os alertas e as recomendações de modo manual ou contínuo.

- [Como configurar a exportação](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detecção e análise – investigar um incidente

**Diretriz**: verifique se os analistas podem consultar e usar fontes de dados diversificadas à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversificados devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de eliminação para evitar pontos cegos.  Você também deve garantir que os insights e os aprendizados sejam capturados para outros analistas e para referência histórica futura.

As fontes de dados para investigação incluem as fontes de log centralizadas que já estão sendo coletadas dos serviços no escopo e dos sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise.

- Instantâneos dos sistemas em execução:

    - Use a funcionalidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução.

    - Use a funcionalidade de despejo de memória nativa do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a funcionalidade do seu software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência coletadas durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório.

- [Criar um instantâneo de disco de um computador Windows](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)

- [Criar um instantâneo de disco de um computador Linux](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)

- [Investigar incidentes com o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/tutorial-investigate-cases)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretriz**: forneça aos analistas o contexto dos incidentes nos quais eles devem se concentrar primeiro com base na severidade do alerta e na confidencialidade do ativo.

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Usar marcas para organizar seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretriz**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas.
Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas.

- [Configurar a automação de fluxo de trabalho na Central de Segurança](https://docs.microsoft.com/azure/security-center/workflow-automation)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/tutorial-respond-threats-playbook)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gerenciamento de postura e vulnerabilidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="backup-and-recovery"></a>Backup e recuperação

*Para obter mais informações, confira o [Azure Security Benchmark: backup e recuperação](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantir backups automatizados regulares

**Diretrizes**: Verifique se você está fazendo backup de sistemas e dados para manter a continuidade dos negócios após um evento inesperado. Isso deve ser definido por quaisquer objetivos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação).

Habilite o backup do Azure e configure a fonte de backup (por exemplo, VMs do Azure, SQL Server, bancos de dados HANA ou compartilhamentos de arquivos), bem como a frequência e o período de retenção desejados.

Para um nível mais alto de proteção, você pode habilitar a opção de armazenamento com redundância geográfica para replicar dados de backup para uma região secundária e recuperar usando a restauração entre regiões.

- [Continuidade dos negócios e recuperação de desastres de escala empresarial](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Como habilitar o backup do Azure](https://docs.microsoft.com/azure/backup/)

- [Como habilitar a restauração entre regiões](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: criptografar dados de backup

**Diretrizes**: Verifique se os backups estão protegidos contra ataques. Isso deve incluir a criptografia dos backups para proteger contra perda de confidencialidade.

Para backups locais usando o backup do Azure, a criptografia em repouso é fornecida usando a senha que você fornecer. Para backups regulares do serviço do Azure, os dados de backup são criptografados automaticamente usando chaves gerenciadas pela plataforma do Azure. Você pode optar por criptografar os backups usando a chave gerenciada pelo cliente. Nesse caso, verifique se essa chave gerenciada pelo cliente no cofre de chaves também está no escopo de backup.

Use o controle de acesso baseado em função no backup do Azure, Azure Key Vault ou outros recursos para proteger backups e chaves gerenciadas pelo cliente. Além disso, você pode habilitar recursos de segurança avançados para exigir MFA antes que os backups possam ser alterados ou excluídos.

- [Visão geral dos recursos de segurança no Backup do Azure](https://docs.microsoft.com/azure/backup/security-overview)

- [Criptografia de dados de backup usando chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/backup/encryption-at-rest-with-cmk)

- [Como fazer backup de chaves de Key Vault no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Recursos de segurança para ajudar a proteger backups híbridos contra ataques](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Verifique periodicamente se você pode restaurar as chaves gerenciadas pelo cliente de backup.

- [Como restaurar chaves de Key Vault no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: reduzir o risco de perda de chaves

**Diretrizes**: Verifique se você tem medidas em vigor para evitar e recuperar da perda de chaves. Habilite a exclusão temporária e a proteção de limpeza no Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos

**Diretriz**: documente e comunique uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança dos riscos e do inventário de ativos

-   Aprovação da organização de segurança dos serviços do Azure para uso

-   Segurança de ativos por meio do ciclo de vida

-   Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

-   Uso de funcionalidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark – Proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir uma estratégia de segmentação corporativa

**Diretriz**: estabeleça uma estratégia de toda a empresa para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitação da operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Garanta que a estratégia de segmentação seja implementada de maneira consistente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, além de modelos de acesso/permissão de aplicativo e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](https://docs.microsoft.com/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](https://docs.microsoft.com/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir uma estratégia de gerenciamento de postura de segurança

**Diretriz**: Meça e atenue continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador etc.

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções e responsabilidades da organização

**Diretriz**: documente e comunique uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instrua equipes técnicas sobre a tecnologia usada para proteger a nuvem.

- [Melhor prática de segurança do Azure 1 – Pessoas: educar as equipes na jornada de segurança na nuvem](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor prática de segurança do Azure 2 – Pessoas: educar as equipes na tecnologia de segurança na nuvem](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor prática de segurança do Azure 3 – Processo: atribuir responsabilidade por decisões de segurança na nuvem](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir uma estratégia de segurança de rede

**Diretriz**: estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

-   Gerenciamento de rede centralizado e responsabilidade pela segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída e borda da Internet

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança de rede do Azure](https://docs.microsoft.com/azure/security/fundamentals/network-overview)

- [Estratégia de arquitetura de rede corporativa](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir uma estratégia de identidade e acesso privilegiado

**Diretriz**: estabeleça abordagens de identidade e acesso privilegiado do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

-   Um sistema de identidade e autenticação centralizado e a interconectividade dele com outros sistemas de identidade internos e externos

-   Métodos de autenticação forte em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e tratamento de atividades de usuário de anomalias

-   Processo de revisão de acesso e reconciliação e identidade do usuário

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Gerenciamento de identidades](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark – Acesso privilegiado](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](https://docs.microsoft.com/azure/security/fundamentals/identity-management-overview)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir uma estratégia de log e resposta a ameaças

**Diretriz**: estabeleça uma estratégia de log e resposta a ameaças para detectar e corrigir as ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências perfeitas aos analistas para que eles possam se concentrar em ameaças em vez de etapas manuais e de integração.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

-   A função e as responsabilidades da organização de SecOps (operações de segurança)

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor

-   Captura e retenção de log para dar suporte às necessidades de conformidade, detecção de ameaças e resposta a incidentes

-   Visibilidade centralizada das ameaças e informações de correlação sobre elas por meio do SIEM, de funcionalidades nativas do Azure e de outras fontes

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas interessadas

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como log e detecção de ameaças, análise forense, além de correção e erradicação de ataques

-   Processos para tratamento de incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Log e detecção de ameaças](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark – Resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
