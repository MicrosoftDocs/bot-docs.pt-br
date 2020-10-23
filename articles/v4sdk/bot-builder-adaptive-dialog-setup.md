---
title: Criar um projeto de bot para caixas de diálogo adaptáveis-serviço bot
description: Saiba como criar um projeto ao qual você pode adicionar caixas de diálogo adaptáveis no SDK do bot Framework.
keywords: declarativo, adaptável
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/18/2020
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5025c5c43b5423f395765f6cf2a9920cf04cac13
ms.sourcegitcommit: 7213780f3d46072cd290e1d3fc7c3a532deae73b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92416977"
---
# <a name="create-a-bot-project-for-adaptive-dialogs"></a>Criar um projeto de bot para caixas de diálogos adaptáveis  

[!INCLUDE [applies-to-v4](../includes/applies-to-v4-current.md)]

Se você quiser escrever uma caixa de diálogo adaptável do zero ou consumir uma caixa de diálogo que foi criada no bot Framework Composer, precisará de certas scaffolding em vigor que não seja necessária para outros tipos de projetos de bot.

Este artigo mostra como criar um projeto de bot em C#, começando com um modelo de **bot vazio** . Posteriormente, você poderá adicionar caixas de diálogo adaptáveis, incluindo caixas de diálogo declarativas geradas pelo Composer.

## <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2019 ou posterior ou Visual Studio Code.
- Um entendimento conceitual de [caixas de diálogo adaptáveis](bot-builder-adaptive-dialog-Introduction.md).
- Para o código de exemplo, este artigo faz referência à amostra de **solicitação de várias ativações** adaptáveis em [**C#**](https://aka.ms/cs-adaptive-multi-turn-sample).

## <a name="create-the-project"></a>Criar o projeto

Para obter informações sobre como criar um projeto de bot, consulte o [início rápido do C#](../dotnet/bot-builder-dotnet-sdk-quickstart.md#templates).

1. Atualize sua cópia dos modelos do bot Framework (VSIX ou .NET Core) para a versão mais recente.
1. Crie um novo projeto com base no modelo de bot do .NET Core 3,1 **vazio** .

> [!NOTE]
> Este artigo não realça as instruções using que você precisa adicionar para obter o projeto a ser compilado.

## <a name="add-nuget-packages"></a>Adicionar pacotes NuGet

1. Abra seu projeto no IDE preferencial.
1. Adicione o pacote do NuGet **Microsoft. bot. Builder. dialogs. Adaptive** 4.10.0 ou mais recente como uma dependência.
1. Registre quaisquer pacotes adicionais que seu bot precisará:
    - Para o teste de unidade de caixa de diálogo adaptável, adicione o pacote **Microsoft. bot. Builder. dialogs. Adaptive. Testing** .
    - Para entender o idioma LUIS, adicione o pacote **Microsoft. bot. Builder. ai. Luis** .
    - Para conhecer QnA Maker idioma, adicione o pacote **Microsoft. bot. Builder. ai. QnA** .

Se o bot consumir componentes personalizados, ele poderá exigir pacotes adicionais. O autor de cada componente personalizado deve indicar claramente quais pacotes são necessários.

## <a name="register-components"></a>Registrar componentes

Para dar suporte à personalização, a biblioteca de caixas de diálogo adaptáveis usa o registro de componente para descobrir todos os componentes, tipos declarativos, escopos de memória, resolvedores de caminho e conversores de tipo.

Registre componentes adaptáveis e declarativos conforme necessário no `ConfigureServices` método para seu projeto.

| Obrigatório | Descrição | Registro de componente
| :--- | :--- | :---
| Obrigatório | Componentes comuns a todas as caixas de diálogo adaptáveis. | `ComponentRegistration.Add(new AdaptiveComponentRegistration());`
| Obrigatório | Escopos de memória comuns e resolvedores de caminho. | `ComponentRegistration.Add(new DialogsComponentRegistration());`
| Opcional | Componentes usados para caixas de diálogo adaptáveis de teste de unidade. | `ComponentRegistration.Add(new AdaptiveTestingComponentRegistration());`
| Opcional | Componentes usados para consumir caixas de diálogo declarativas. | `ComponentRegistration.Add(new DeclarativeComponentRegistration());`
| Opcional | Componentes usados para recursos de geração de linguagem.| `ComponentRegistration.Add(new LanguageGenerationComponentRegistration());`
| Opcional | Componentes usados para recursos de LUIS (reconhecimento de linguagem). | `ComponentRegistration.Add(new LuisComponentRegistration());`
| Opcional | Componentes usados para recursos de QnA Maker (compreensão da linguagem). | `ComponentRegistration.Add(new QnAMakerComponentRegistration());`
| Opcional | Componentes específicos para o canal de equipes. | `ComponentRegistration.Add(new TeamsComponentRegistration());`

Se o seu projeto não registrar um componente exigido por outra parte do bot, você poderá adicioná-lo mais tarde, mas poderá obter um erro de tempo de inicialização ou de tempo de execução.

> [!NOTE]
> Se o bot consumir componentes personalizados, também será necessário registrar esses componentes. O autor de cada componente personalizado deve indicar claramente qual método usar para registrar seus componentes.

**startup.cs**

Por exemplo, o exemplo de prompts de várias ativações adaptáveis registra esses componentes no `ConfigureServices` .

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs" range="24-31":::

## <a name="add-state"></a>Adicionar estado

Assim como acontece com outros bots, você ainda precisa criar a camada de armazenamento e os objetos de gerenciamento de estado.

Você usará o Gerenciador de caixas de diálogo para executar suas caixas de diálogo adaptáveis e precisará de referências à camada de armazenamento e aos objetos de estado de conversa e de usuário. (Ele precisa deles para configurar escopos de memória.) Embora você possa adicioná-los diretamente ao Gerenciador de caixas de diálogo, o Gerenciador de caixas de diálogo descobrirá se eles estiverem registrados corretamente no contexto de ativação.

**startup.cs**

Você atualizará o adaptador para consumir um `ICredentialProvider` , portanto, será necessário registrar um no `ConfigureServices` .

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs" range="33-34":::

Além disso, registre a camada de armazenamento e os objetos de gerenciamento de estado que o bot usará no `ConfigureServices` .

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs" range="39-46":::

**AdapterWithErrorHandler.cs**

No C#, você pode usar os `UseStorage` métodos e adaptadores `UseBotState` para adicionar referências aos objetos de gerenciamento de armazenamento e de estado para o contexto de ativação cada vez.

Modifique a assinatura para o construtor do adaptador existente.

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/AdapterWithErrorHandler.cs" range="17-19":::

Adicione as chamadas aos métodos UseStorage e UseBotState antes de definir a propriedade OnTurnError do adaptador.

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/AdapterWithErrorHandler.cs" range="21-25":::

## <a name="add-an-adaptive-dialog"></a>Adicionar uma caixa de diálogo adaptável

Crie uma pasta de **caixas de diálogo** e, em seguida, crie a caixa de diálogo raiz que o bot usará.

**RootDialog.cs de diálogos \\**

Criar uma classe de caixa de diálogo raiz. O exemplo define um `RootDialog` para usar.

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Dialogs/RootDialog.cs" range="15-18,38,134":::

Neste ponto, a caixa de diálogo ainda não responderá ao usuário. Você pode adicionar um gatilho à caixa de diálogo (no Construtor) para permitir que ele responda. Aqui está um gatilho de exemplo.

```csharp
Triggers = new List<OnCondition>
{
    new OnUnknownIntent
    {
        Actions =
        {
            new SendActivity("Hi, we are up and running!!"),
        }
    },
};
```

**startup.cs**

O exemplo registra a caixa de diálogo raiz no arquivo de inicialização no `ConfigureServices` .

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs" range="48-49":::

## <a name="add-a-dialog-manager"></a>Adicionar um Gerenciador de caixas de diálogo

Da mesma forma que faria com outros bots baseados em caixa de diálogo, projete a classe bot para pegar uma caixa de diálogo como um parâmetro de construtor. No entanto, crie e use um Gerenciador de caixas de diálogo para executar a caixa de diálogo raiz.

No exemplo de prompts de multifator adaptável, a caixa de diálogo raiz é uma caixa de diálogo adaptável. O Gerenciador de caixas de diálogo pode ser usado para executar qualquer caixa de diálogo de tipo, mas será necessário se a caixa de diálogo raiz ou qualquer um de seus filhos forem caixas de diálogo adaptáveis.

**DialogBot.cs**

Renomeie a classe **EmptyBot** padrão e o arquivo para **DialogBot**.

O exemplo define o bot e adiciona o código para criar e usar um Gerenciador de caixas de diálogo para executar a caixa de diálogo raiz cada vez. O Gerenciador de caixas de diálogo encaminhará todas as atividades para a caixa de diálogo. No exemplo de prompt de múltipla ativação, esse arquivo é **Bots \\ DialogBot.cs**.

Substitua a definição de classe existente por este código. (Se você não incluir a `using Microsoft.Extensions.Logging;` instrução, a `ILogger` referência será ambígua.)

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Bots/DialogBot.cs" range="8-9,17-36":::

**startup.cs**

O exemplo registra o objeto bot no arquivo de inicialização no `ConfigureServices` . Substitua a chamada para registrar sua classe bot ( `services.AddTransient<IBot, EmptyBot>();` ) com este código.

:::code language="csharp" source="~/../botbuilder-samples/samples/csharp_dotnetcore/adaptive-dialog/01.multi-turn-prompt/Startup.cs" range="51-52":::

## <a name="compile-and-test"></a>Compilar e testar

Neste ponto, seu bot deve compilar e executar. Você pode testá-lo no emulador. Se a caixa de diálogo adaptável raiz não definir nenhum gatilho ou ação, o bot não responderá à entrada, mas você deverá obter respostas de status HTTP 200 do bot.

## <a name="additional-information"></a>Informações adicionais

As caixas de diálogo adaptáveis são uma ferramenta poderosa e têm várias maneiras de serem personalizadas e estendidas. Consulte os artigos a seguir para obter mais informações.

- [Expressões adaptáveis](bot-builder-concept-adaptive-expressions.md)
- [Geração de linguagem](bot-builder-concept-language-generation.md) e [geradores](bot-builder-concept-adaptive-dialog-generators.md)
- [Eventos e gatilhos](bot-builder-concept-adaptive-dialog-Triggers.md)
- [Ações](bot-builder-concept-adaptive-dialog-actions.md) e [entradas](bot-builder-concept-adaptive-dialog-inputs.md)
- [Reconhecedores](bot-builder-concept-adaptive-dialog-recognizers.md)
  - [O que é o LUIS?](/azure/cognitive-services/luis/what-is-luis)
  - [O que é QnA Maker](/azure/cognitive-services/qnamaker/overview/overview)
- [Caminhos e escopos de memória](bot-builder-concept-adaptive-dialog-memory-states.md)
- [Caixas de diálogo declarativas](bot-builder-concept-adaptive-dialog-declarative.md) e tipos de conversores
  - [Criando um arquivo de esquema](bot-builder-dialogs-declarative.md#creating-the-schema-file)

<!-- Add Custom components: actions, triggers, dialogs, recognizers, and so on to the list, once they are available.-->

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um bot usando caixas de diálogo adaptáveis](bot-builder-dialogs-adaptive.md)
