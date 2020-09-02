---
title: Visão geral de Migração de SDK do Bot Framework – Serviço de Bot
description: Consulte como migrar bots da versão 3 para 4 do SDK do bot Framework. Saiba mais sobre as alterações na versão 4 em gerenciamento de estado, caixas de diálogo e outras áreas.
keywords: migração do bot
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/11/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: dd8570f44e9845ed4f9d21923b33c72b65625641
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362229"
---
# <a name="migration-overview"></a>Visão geral da migração

O Bot Framework SDK v4 se baseia nos comentários dos clientes e na experiência de aprendizado de SDKs anteriores. A nova versão introduz os níveis certos de abstração, ao mesmo tempo permitindo uma arquitetura flexível dos componentes do bot. Isso, por exemplo, permite que você crie um bot simples e, em seguida, expanda-o em termos de sofisticação usando a modularidade e a extensibilidade do SDK v4 do Bot Framework.

> [!NOTE]
> O SDK v4 do Bot Framework busca manter a simplicidade e viabilizar tarefas complexas.

Uma abordagem aberta foi adotada, de modo que o SDK v4 do Bot Framework foi criado com a cooperação da comunidade. Quando envia pela primeira vez uma Solicitação de Pull, um CLA ([Contrato de Licença de Colaborador](https://cla.opensource.microsoft.com/)) determina automaticamente se você precisa de uma licença. Você só precisará fazer isso uma vez em todos os repositórios. Normalmente, há um período para estabelecer um conjunto de objetivos a serem atingidos.

## <a name="what-happens-to-bots-built-using-sdk-v3"></a>O que acontece a bots criados usando o SDK v3

O SDK v3 do Bot Framework será desativado, mas as cargas de trabalho de bot V3 existentes continuarão sendo executadas sem interrupção. Para obter mais informações, consulte: [Suporte de tempo de vida do SDK Versão 3 do Bot Framework.](https://docs.microsoft.com/azure/bot-service/bot-service-resources-bot-framework-faq?view=azure-bot-service-4.0#bot-framework-sdk-version-3-lifetime-support)

É altamente recomendável começar a migrar seus bots da V3 para a V4. Para dar suporte a essa migração, criamos a documentação relacionada e vamos oferecer suporte estendido para iniciativas de migração por meio dos canais padrão.

Se você não puder migrar de um bot v3 para um bot v4 imediatamente, talvez ainda seja interessante aproveitar a funcionalidade adicional disponível no SDK v4. Você pode converter o bot v3 em uma habilidade e criar um bot de consumidor de habilidades com base no SDK v4 para passar mensagens para o bot v3. Para obter mais informações, confira como [converter um bot v3 em uma habilidade](convert-to-skill-overview.md).

## <a name="advantages"></a>Vantagens

- Arquitetura mais avançada, flexível e aberta: possibilita um design de conversas mais flexível
- Disponibilidade: apresenta cenários adicionais com novas funcionalidades de canal
- Pessoal SME (Especialista no Assunto) expandido no ciclo de desenvolvimento: O novo designer de GUI permite que não desenvolvedores colaborem no design da conversa
- Velocidade de desenvolvimento: novas ferramentas para desenvolvedores de teste e depuração
- Insights de desempenho: novas funcionalidades de telemetria para avaliar e melhorar a qualidade da conversa
- Inteligência: melhores funcionalidades de serviços cognitivos

## <a name="why-migrate"></a>Por que migrar
<!-- [!] The declarative model introduced with Adaptive Dialogs would go great here (when ready).  -->
- Gerenciamento de conversa flexível e aprimorado
  - Adaptador de Bot para processamento de atividade
  - Gerenciamento de estado refatorado
  - Nova biblioteca de diálogos
  - Middleware para designs combináveis e extensíveis: ganchos limpos e consistentes para personalizar o comportamento
- Criado para o .NET Core
  - desempenho aprimorado
  - Compatibilidade multiplataforma (Windows/Mac/Linux)
- Modelo de programação consistente em várias linguagens de programação
- Documentação aprimorada
- O Inspetor de Bot fornece funcionalidades de depuração estendidas
- Assistente Virtual
  - Solução abrangente que simplifica a criação de bots com propósitos de conversação básicos, integração de Despacho, QnA Maker, Application Insights e uma implantação automatizada.
  - Habilidades Extensíveis. Componha experiências de conversa combinando recursos de conversa reutilizáveis, conhecidos como Habilidades.
- Estrutura de Teste: funcionalidades de teste prontas para uso com a nova arquitetura de adaptador independente de transporte
- Telemetria: obtenha insights essenciais sobre a integridade e o comportamento de seu bot com a Análise de IA de Conversa
- Em breve (versão prévia)
  - Diálogo Adaptáveis: crie conversas que podem ser alteradas dinamicamente conforme a conversa avança
  - Geração de Linguagem: defina diversas variações de uma frase
- Futuro
  - O design declarativo permite um nível de abstração para designers
  - Designer de Diálogo da GUI
- Serviço de Bot do Azure
  - Canal de Fala do Direct Line. Reunindo o Bot Framework e os Serviços de Fala da Microsoft. Isso fornece um canal que permite a transmissão bidirecional de fala e texto simplificada do cliente para o aplicativo do bot

## <a name="whats-changed"></a>O que mudou

O SDK v4 do Bot Framework é compatível com o mesmo Bot Framework Service subjacente que a v3. No entanto, a v4 é uma refatoração dos SDKs anteriores para permitir mais flexibilidade e controle sobre a criação do bot. Isso inclui o seguinte:

- Introduziu um adaptador de bot
  - O adaptador faz parte da pilha de processamento de atividade
  - Ele lida com a autenticação do Bot Framework e inicializa o contexto para cada turno
  - Ele gerencia o tráfego de entrada e saída entre um canal e o manipulador de turno de seu bot, encapsulando as chamadas para o serviço do Bot Framework Connector
  - Para obter mais detalhes, confira [Como os bots funcionam](../bot-builder-basics.md)
- Gerenciamento de estado refatorado
  - Os dados de estado não estão mais automaticamente disponíveis dentro de um bot
  - O estado agora é gerenciado por meio de objetos de gerenciamento de estado e acessadores de propriedade
  - Para obter mais detalhes, confira [Como gerenciar o estado](../bot-builder-concept-state.md)
- Introduzida uma nova biblioteca de diálogos
  - Os diálogos da v3 precisam ser reescritos para a nova biblioteca de diálogos
  - Para obter mais detalhes, confira a [Biblioteca de diálogos](../bot-builder-concept-dialog.md)

## <a name="whats-involved-in-migration-work"></a>O que está envolvido no trabalho de migração

- Atualizar a lógica de configuração
- Realizar a portabilidade de qualquer estado de usuário crítico
  - Observação: o estado confidencial do usuário não pode ser mantido em um estado de bot, em vez disso, coloque-o em um armazenamento separado sob seu controle
- Realizar a portabilidade do bot e da lógica de diálogo (confira os tópicos específicos a um idioma para obter mais detalhes)

### <a name="migration-estimation-worksheet"></a>Planilha de estimativa de migração

As planilhas a seguir podem orientá-lo na estimativa de sua carga de trabalho de migração. Na coluna **Ocorrências**, substitua *contagem* pelo seu valor numérico real. Na coluna **Camiseta**, insira valores como: *Pequeno*, *Médio*, *Grande* com base em sua estimativa.

# <a name="c"></a>[C#](#tab/csharp)

| Etapa | V3 | V4 | Ocorrências | Complexidade | Camiseta |
| -- | -- | -- | -- | -- | -- |
Para obter a atividade de entrada | IDialogContext.Activity | ITurnContext.Activity | count | Pequena
Para criar e enviar uma atividade para o usuário | activity.CreateReply(“text”) IDialogContext.PostAsync | MessageFactory.Text(“text”) ITurnContext.SendActivityAsync | count | Pequena |
Gerenciamento de estado | UserData, ConversationData, and PrivateConversationData context.UserData.SetValue context.UserData.TryGetValue botDataStore.LoadAsyn | UserState, ConversationState e PrivateConversationState  Com acessadores de propriedade | context.UserData.SetValue – count context.UserData.TryGetValue – count botDataStore.LoadAsyn – count | Médio a Grande (confira [gerenciamento de estado do usuário](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0#state-management) disponível) |
Manipula o início do diálogo | Implemente IDialog.StartAsync | Torne essa a primeira etapa de um diálogo em cascata. | count | Pequena |
Enviar uma atividade | IDialogContext.PostAsync. | Chame ITurnContext.SendActivityAsync. | count | Pequena |
Aguarde a resposta do usuário | Use um parâmetro IAwaitable<IMessageActivity>e chame IDialogContext.Wait | Retorne esperar ITurnContext.PromptAsync para iniciar um diálogo de aviso. Em seguida, recupere o resultado na etapa seguinte da cascata. | count | Médio (depende do fluxo) |
Manipula a continuação do diálogo | IDialogContext.Wait | Adicione mais etapas a um diálogo em cascata ou implemente Dialog.ContinueDialogAsync | count | grande |
Sinalize o final do processamento até a mensagem seguinte do usuário | IDialogContext.Wait | Retorne Dialog.EndOfTurn. | count | Médio |
Iniciar um diálogo filho | IDialogContext.Call | Retorne esperar o BeginDialogAsyncmethod do contexto da etapa. Se o diálogo filho retornar um valor, esse valor estará disponível na etapa seguinte da cascata por meio da propriedade Result do contexto da etapa. | count | Médio |
Substituir o diálogo atual por um novo diálogo | IDialogContext.Forward | Retorne esperar ITurnContext.ReplaceDialogAsync. | count | grande |
Sinaliza que o diálogo atual foi concluído | IDialogContext.Done | Retorne esperar o método EndDialogAsync do contexto da etapa. | count | Médio |
Falha de um diálogo. | IDialogContext.Fail | Gere uma exceção a ser capturada em outro nível do bot, encerre a etapa com um status de Cancelado ou chame a etapa ou CancelAllDialogsAsync do contexto do diálogo. | count | Pequena |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Etapa | V3 | V4 | Ocorrências | Complexidade | Camiseta |
| -- | -- | -- | -- | -- | -- |
Para obter a atividade de entrada | IMessage | TurnContext.activity | count | Pequena
Para criar e enviar uma atividade para o usuário | Chamar Session.send('message') | Chamar TurnContext.sendActivity | count | Pequena |
Gerenciamento de estado | UserState & ConversationState UserState.get(), UserState.saveChanges(), ConversationState.get(), ConversationState.saveChanges() | UserState e ConversationState com acessadores de propriedade | count | Médio a Grande (confira [gerenciamento de estado do usuário](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0#state-management) disponível) |
Manipula o início do diálogo | chamar session.beginDialog, passando a ID da caixa de diálogo | chamar DialogContext.beginDialog | count | Pequena |
Enviar uma atividade | Chamar Session.send | Chamar TurnContext.sendActivity | count | Pequena |
Aguarde a resposta do usuário | chame um prompt de dentro da etapa de cascata, ex: builder.Prompts.text(session, 'Insira seu destino'). Recupere a resposta na próxima etapa. | Retorne aguardar TurnContext.prompt para iniciar uma caixa de diálogo de prompt. Em seguida, recupere o resultado na etapa seguinte da cascata. | count | Médio (depende do fluxo) |
Manipula a continuação do diálogo | Automático | Adicione mais etapas a uma caixa de diálogo em cascata ou implemente Dialog.continueDialog | count | grande |
Sinalize o final do processamento até a mensagem seguinte do usuário | Session.endDialog | Retorne Dialog.EndOfTurn | count | Médio |
Iniciar um diálogo filho | Session.beginDialog | Retorne aguardar o método beginDialog do contexto da etapa. Se a caixa de diálogo filho retornar um valor, esse valor estará disponível na próxima etapa da cascata por meio da propriedade Result do contexto da etapa. | count | Médio |
Substituir o diálogo atual por um novo diálogo | Session.replaceDialog | ITurnContext.replaceDialog | count | grande |
Sinaliza que o diálogo atual foi concluído | Session.endDialog | Retorne aguardar o método endDialog do contexto da etapa. | count | Médio |
Falha de um diálogo. | Session.pruneDialogStack | Gere uma exceção a ser capturada em outro nível do bot, encerre a etapa com um status de Cancelado ou chame a etapa ou cancelAllDialogs do contexto da caixa de diálogo. | count | Pequena |

---

# <a name="c"></a>[C#](#tab/csharp)

O SDK v4 do Bot Framework baseia-se a mesma API REST subjacente que a v3. No entanto, a v4 é uma refatoração da versão anterior do SDK para permitir mais flexibilidade e controle sobre os bots.

É recomendável migrar para o .NET Core, já que o desempenho é muito melhor.
Porém, alguns bots V3 existentes estão usando bibliotecas externas que não têm um equivalente do .NET Core. Neste caso, o SDK v4 do Bot Framework pode ser usado com o .NET Framework versão 4.6.1 ou superior. Encontre um exemplo no local do [corebot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi).

Ao migrar um projeto da v3 para a v4, você pode escolher uma destas opções: converter no local para **.NET Framework** ou realizar a portabilidade para um novo projeto para **.NET Core**.

#### <a name="net-framework"></a>.NET Framework

- Atualizar e instalar pacotes NuGet
- Atualizar o arquivo Global.asax.cs
- Atualizar a classe MessagesController
- Converter os diálogos

Para obter mais informações, confira [Migrar um bot .NET v3 para um bot .NET Framework v4](conversion-framework.md).

#### <a name="net-core"></a>.NET Core

- Criar o projeto usando um modelo

 [!INCLUDE [VSIX templates](~/includes/vsix-templates-versions.md)]

- Instalar pacotes adicionais do NuGet conforme necessário
- Personalizar seu bot, atualizar o arquivo Startup.cs e atualizar sua classe de controlador
- Atualizar a classe de bot
- Copiar e atualizar seus modelos e caixas de diálogo


Para obter mais informações, confira [Migrar um bot .NET v3 para um bot .NET Core v4](conversion-core.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O **Bot Framework JavaScript SDK v4** introduz várias mudanças fundamentais a como os bots são criados. Essas mudanças afetam a sintaxe para o desenvolvimento de bots em JavaScript, especialmente com relação à criação de objetos de bot, à definição de caixas de diálogo e à lógica de manuseio de evento de codificação. O SDK v4 do Bot Framework baseia-se a mesma API REST subjacente que a v3. No entanto, a v4 é uma refatoração da versão anterior do SDK para dar mais flexibilidade e controle sobre os bots, em particular:

- Diálogos e instâncias de bot foram ainda mais separados. No v3, as caixas de diálogo eram registradas diretamente no construtor do bot. Na v4, agora você passa os diálogos para as instâncias do bot como argumentos, fornecendo maior flexibilidade na composição
- A v4 fornece uma classe `ActivityHandler`, que ajuda a automatizar o manuseio de diferentes tipos de atividades, como mensagem, atualização de conversa e evento
- Para a migração do bot do NodeJS, você precisará criar um novo bot do NodeJS v4 em JavaScript ou TypeScript. Recrie a lógica do bot usando os novos constructos v4 descritos na documentação de migração relacionada

#### <a name="migrate-from-v3-to-v4"></a>Migrar da v3 para a v4

- Criar o novo projeto e adicionar dependências
- Atualizar o ponto de entrada e definir constantes
- Criar os diálogos, reimplementando-os usando o SDK v4
- Atualizar o código do bot para executar os diálogos
- Realizar a portabilidade do arquivo de utilitário `store.js`

Para obter mais informações, confira [Migrar um bot do SDK v3 em Javascript para v4](conversion-javascript.md).

---

## <a name="additional-resources"></a>Recursos adicionais

Os recursos adicionais a seguir fornecem mais informações que podem ajudar durante a migração.

### <a name="c"></a>[C#](#tab/csharp)

<!-- _Mini-TOC with explainer for .NET topics_ -->
Os tópicos a seguir descrevem as diferenças entre os SDKs do v3 e v4 do Bot Framework do .NET, as principais alterações entre as duas versões e as etapas para migrar um bot da v3 para a v4.

| Tópico | Descrição |
| :--- | :--- |
| [Diferenças entre o SDK do .NET v3 e v4](migration-about.md) |Diferenças comuns entre os SDKs v3 e v4 |
| [Referência rápida de migração do .NET](net-migration-quickreference.md) |Principais alterações nos SDKs v3 em comparação à v4 |
| [Migrar um bot do .NET v3 para um bot do Framework v4](conversion-framework.md) |Migrar um bot v3 para v4 usando o mesmo tipo de projeto |
| [Migrar um bot .NET v3 para um bot Core v4](conversion-core.md) | Migrar um bot v3 para v4 em um projeto do .NET Core|

### <a name="javascript"></a>[JavaScript](#tab/javascript)

<!-- _Mini-TOC with explainer for JavaScript topics_ -->
Os tópicos a seguir descrevem as diferenças entre os SDKs v3 e v4 do Bot Framework do JavaScript, as principais alterações entre as duas versões e as etapas para migrar um bot da v3 para a v4.

| Tópico | Descrição |
| :--- | :--- |
| [Diferenças entre o SDK do JavaScript v3 e v4](migration-about-javascript.md) | Diferenças comuns entre os SDKs v3 e v4 |
| [Referência rápida de migração do JavaScript](javascript-migration-quickreference.md)| Principais alterações nos SDKs v3 em comparação à v4|
| [Migrar um bot do JavaScript v3 para v4](conversion-javascript.md) |Migrar um bot de v3 para v4 |

---

### <a name="code-samples"></a>Exemplos de código

A seguir estão exemplos de código que você pode usar para aprender o SDK V4 do Bot Framework ou iniciar seu projeto rapidamente.

| Exemplos | Descrição |
| :--- | :--- |
| [Exemplos de Migração do Bot Framework da V3 para a V4](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4) <img width="200">| Exemplos de migração do SDK V3 do Bot Framework para o SDK V4 |
| [Exemplos do .NET do Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore) | Exemplos do .NET Core em C# do Bot Builder |
| [Exemplos de JavaScript do Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs) | Exemplos de JavaScript (node.js) do Bot Builder |
| [Todos os Exemplos do Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples) | Todos os exemplos do Bot Builder |

### <a name="getting-help"></a>Obtendo ajuda

Os seguintes recursos fornecem mais informações e suporte para desenvolver bots.

[Recursos adicionais do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help?view=azure-bot-service-4.0)

### <a name="references"></a>Referências

Confira os seguintes recursos para obter mais detalhes e informações de contexto.

| Tópico | Descrição |
| :--- | :--- |
| [Novidades no Bot Framework](https://docs.microsoft.com/azure/bot-service/what-is-new?view=azure-bot-service-4.0) | Aprimoramentos e recursos principais do Bot Framework e do Serviço de Bot do Azure|
|[Como funcionam os bots](../bot-builder-basics.md)|O mecanismo interno de um bot|
|[Gerenciar estado](../bot-builder-concept-state.md)|Abstrações para facilitar o gerenciamento de estado|
|[Biblioteca de caixas de diálogo](../bot-builder-concept-dialog.md)| Conceitos centrais para gerenciar uma conversa|
|[Enviar e receber mensagens de texto](../bot-builder-howto-send-messages.md)|Principal maneira de um bot comunicar-se com usuários|
|[Enviar Mídia](../bot-builder-howto-add-media-attachments.md)|Anexos de mídia, como imagens, vídeo, áudio e arquivos|
|[Fluxo de conversa sequencial](../bot-builder-dialog-manage-conversation-flow.md)| Questionamento como o principal meio de um bot interagir com os usuários|
|[Salvar dados de usuário e de conversa](../bot-builder-howto-v4-state.md)|Como acompanhar uma conversa sem estado|
|[Fluxo Complexo](../bot-builder-dialog-manage-complex-conversation-flow.md)|Gerenciar fluxos de conversas complexos |
|[Reutilizar Caixas de Diálogo](../bot-builder-compositcontrol.md)|Criar caixas de diálogo independentes para lidar com cenários específicos|
|[Interrupções](../bot-builder-howto-handle-user-interrupt.md)| Lidar com interrupções para criar um bot robusto|
|[Esquema da Atividade](https://aka.ms/botSpecs-activitySchema)|Esquema para seres humanos e software automatizado|
