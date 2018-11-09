---
title: Localizar o conteúdo do formulário | Microsoft Docs
description: Saiba como localizar o conteúdo de formulário com o FormFlow e o SDK do Construtor de Bot para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/02/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 914c33033be3fe35db7cae6d54b5835cb032a5fd
ms.sourcegitcommit: 984705927561cc8d6a84f811ff24c8c71b71c76b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965684"
---
# <a name="localize-form-content"></a>Localizar o conteúdo do formulário

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

O idioma de localização de um formulário é determinado pelo [CurrentUICulture](https://msdn.microsoft.com/library/system.threading.thread.currentuiculture(v=vs.110).aspx) e pelo [CurrentCulture](https://msdn.microsoft.com/library/system.threading.thread.currentculture(v=vs.110).aspx) do thread atual.
Por padrão, a cultura é derivada do campo **Locale** da mensagem atual, mas você pode substituir o comportamento padrão.
Dependendo de como seu bot é construído, as informações localizadas podem vir de até três fontes diferentes:

- a localização interna de **PromptDialog** e **FormFlow**
- um arquivo de recurso que você gera para as cadeias de caracteres estáticas em seu formulário
- um arquivo de recurso que você cria com cadeias de caracteres para campos, mensagens ou confirmações dinamicamente calculados

## <a name="generate-a-resource-file-for-the-static-strings-in-your-form"></a>Gerar um arquivo de recurso para as cadeias de caracteres estáticas em seu formulário

As cadeias de caracteres estáticas em um formulário incluem as cadeias de caracteres que o formulário gera a partir das informações em sua classe de C# e das cadeias de caracteres que você especificar como os prompts, modelos, mensagens ou confirmações.
Cadeias de caracteres geradas a partir de modelos internos não são consideradas cadeias de caracteres estáticas, já que essas cadeias de caracteres já estão localizadas.
Como muitas cadeias de caracteres em um formulário são geradas automaticamente, não é viável usar recursos normais de C# diretamente.
Em vez disso, você pode gerar um arquivo de recurso para as cadeias de caracteres estáticas em seu formulário ou chamando `IFormBuilder.SaveResources` ou usando a ferramenta **RView** incluída com o SDK do BotBuilder para .NET.

### <a name="use-iformbuildersaveresources"></a>Usar IFormBuilder.SaveResources

Você pode gerar um arquivo de recurso chamando [IFormBuilder.SaveResources][saveResources] em seu formulário para salvar as cadeias de caracteres em um arquivo .resx.

### <a name="use-rview"></a>Usar RView

Como alternativa, você pode gerar um arquivo de recurso baseado em seu arquivo .dll ou .exe usando a ferramenta <a href="https://aka.ms/v3-cs-RView-library" target="_blank">RView</a> incluída no SDK do BotBuilder para .NET.
Para gerar o arquivo .resx, execute **rview** e especifique o assembly que contém seu método de criação de formulário estático e o caminho para esse método.
Este snippet de código mostra como gerar o arquivo de recurso `Microsoft.Bot.Sample.AnnotatedSandwichBot.SandwichOrder.resx` usando **RView**.

```csharp
rview -g Microsoft.Bot.Sample.AnnotatedSandwichBot.dll Microsoft.Bot.Sample.AnnotatedSandwichBot.SandwichOrder.BuildForm
```

Esse trecho mostra parte do arquivo .resx que é gerado executando esse comando **rview**.

```xml
<data name="Specials_description;VALUE" xml:space="preserve">
<value>Specials</value>
</data>
<data name="DeliveryAddress_description;VALUE" xml:space="preserve">
<value>Delivery Address</value>
</data>
<data name="DeliveryTime_description;VALUE" xml:space="preserve">
<value>Delivery Time</value>
</data>
<data name="PhoneNumber_description;VALUE" xml:space="preserve">
<value>Phone Number</value>
</data>
<data name="Rating_description;VALUE" xml:space="preserve">
<value>your experience today</value>
</data>
<data name="message0;LIST" xml:space="preserve">
<value>Welcome to the sandwich order bot!</value>
</data>
<data name="Sandwich_terms;LIST" xml:space="preserve">
<value>sandwichs?</value>
</data>
```

## <a name="configure-your-project"></a>Configurar seu projeto

Após gerar um arquivo de recurso, adicione-o ao seu projeto e, em seguida, defina o idioma neutro executando estas etapas: 

1. Clique com o botão direito do mouse em seu projeto e selecione **Aplicativo**.
2. Clique em **Informações de Assembly**.
3. Selecione o valor de **idioma neutro** que corresponde ao idioma com o qual você desenvolveu seu bot.

Após a criação do formulário, o método [IFormBuilder.Build][build] procurará automaticamente os recursos que contêm o nome do tipo de formulário e os usará para localizar as cadeias de caracteres estáticas em seu formulário. 

> [!NOTE]
> Campos calculados dinamicamente definidos com [Advanced.Field.SetDefine][setDefine] (conforme descrito em [Usar campos dinâmicos](bot-builder-dotnet-formflow-formbuilder.md#dynamically-define-field-values-confirmations-and-messages)) não podem ser localizados da mesma maneira que campos estáticos, já que as cadeias de caracteres para campos calculados dinamicamente são construídas no momento do preenchimento do formulário. No entanto, você pode localizar campos calculados dinamicamente por meio de mecanismos de localização normais em C#.

### <a name="localize-resource-files"></a>Localizar arquivos de recurso 

Depois de adicionar arquivos de recurso ao seu projeto, você pode localizá-los usando o <a href="https://developer.microsoft.com/windows/develop/multilingual-app-toolkit" target="_blank">MAT (Kit de Ferramentas de Aplicativo Multilíngue)</a>. Instale o **MAT** e habilite-o para seu projeto executando estas etapas:

1. Selecione o projeto no Gerenciador de Soluções do Visual Studio.
2. Clique em **Ferramentas**, **Kit de Ferramentas de Aplicativo Multilíngue**  e em **Habilitar**.
3. Clique com o botão direito do mouse no projeto e selecione **Kit de Ferramentas de Aplicativo Multilíngue**, **Adicionar Traduções** para selecionar as traduções. Isso criará arquivos <a href="https://en.wikipedia.org/wiki/XLIFF" target="_blank">XLF</a> padrão do setor que você pode traduzir manualmente ou automaticamente.

> [!NOTE]
> Embora este artigo descreva como usar o Kit de Ferramentas de Aplicativo Multilíngue para localizar o conteúdo, você pode implementar a localização por vários outros meios.

## <a name="see-it-in-action"></a>Veja isso em ação

Este exemplo de código tem como base o mostrado em [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md) para implementar a localização conforme descrito acima. Neste exemplo, a classe `DynamicSandwich` (não mostrada aqui) contém informações de localização para campos, mensagens e confirmações calculadas dinamicamente.

[!code-csharp[Build localized form](../includes/code/dotnet-formflow-localize.cs#buildLocalizedForm)]

Este snippet de código mostra a interação resultante entre usuário e bot quando `CurrentUICulture` for **francês**.

```console
Bienvenue sur le bot d'ordre "sandwich" !
Quel genre de "sandwich" vous souhaitez sur votre "sandwich"?
 1. BLT
 2. Jambon Forêt Noire
 3. Poulet Buffalo
 4. Faire fondre le poulet et Bacon Ranch
 5. Combo de coupe à froid
 6. Boulette de viande Marinara
 7. Poulet rôti au four
 8. Rôti de boeuf
 9. Rotisserie poulet
 10. Italienne piquante
 11. Bifteck et fromage
 12. Oignon doux Teriyaki
 13. Thon
 14. Poitrine de dinde
 15. Veggie
> 2

Quel genre de longueur vous souhaitez sur votre "sandwich"?
 1. Six pouces
 2. Pied Long
> ?
* Vous renseignez le champ longueur.Réponses possibles:
* Vous pouvez saisir un numéro 1-2 ou des mots de la description. (Six pouces, ou Pied Long)
* Retourner à la question précédente.
* Assistance: Montrez les réponses possibles.
* Abandonner: Abandonner sans finir
* Recommencer remplir le formulaire. (Vos réponses précédentes sont enregistrées.)
* Statut: Montrer le progrès en remplissant le formulaire jusqu'à présent.
* Vous pouvez passer à un autre champ en entrant son nom. ("Sandwich", Longueur, Pain, Fromage, Nappages, Sauces, Adresse de remise, Délai de livraison, ou votre expérience aujourd'hui).
Quel genre de longueur vous souhaitez sur votre "sandwich"?
 1. Six pouces
 2. Pied Long
> 1

Quel genre de pain vous souhaitez sur votre "sandwich"?
 1. Neuf grains de blé
 2. Neuf grains miel avoine
 3. Italien
 4. Fromage et herbes italiennes
 5. Pain plat
> neuf
Par pain "neuf" vouliez-vous dire (1. Neuf grains miel avoine, ou 2. Neuf grains de blé)
```

## <a name="sample-code"></a>Exemplo de código

[!INCLUDE [Sample code](../includes/snippet-dotnet-formflow-samples.md)]

## <a name="additional-resources"></a>Recursos adicionais

- [Recursos básicos do FormFlow](bot-builder-dotnet-formflow.md)
- [Recursos avançados do FormFlow](bot-builder-dotnet-formflow-advanced.md)
- [Personalizar um formulário usando FormBuilder](bot-builder-dotnet-formflow-formbuilder.md)
- [Definir um formulário usando o esquema JSON](bot-builder-dotnet-formflow-json-schema.md)
- [Personalizar a experiência do usuário com o idioma padrão](bot-builder-dotnet-formflow-pattern-language.md)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Construtor de Bot para .NET</a>

[build]: /dotnet/api/microsoft.bot.builder.formflow.formbuilder-1.build 

[setDefine]: /dotnet/api/microsoft.bot.builder.formflow.advanced.field-1.setdefine

[saveResources]: /dotnet/api/microsoft.bot.builder.formflow.iform-1.saveresources
