---
title: Reconhecer intenções e entidades com o LUIS | Microsoft Docs
description: Saiba como habilitar seu bot para entender o idioma natural usando diálogos do LUIS no SDK do Bot Framework para .NET.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 7e7eb36d0cb3cdbf18037f05b4960b240cb70d8d
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70167398"
---
# <a name="recognize-intents-and-entities-with-luis"></a>Reconhecer intenções e entidades com o LUIS 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Este artigo usa o exemplo de um bot para fazer anotações, visando demonstrar como o Reconhecimento Vocal ([LUIS][LUIS]) ajuda o bot a responder adequadamente à entrada de idioma natural. Um bot detecta o que um usuário deseja fazer identificando a **intenção** desse usuário. A intenção é determinada por entradas faladas ou textuais, ou por **declarações**. A intenção mapeia declarações para ações executadas pelo bot. Por exemplo, um bot de anotações reconhece uma intenção `Notes.Create` de invocar a funcionalidade para a criação de uma anotação. Um bot também pode precisar extrair **entidades**, que são palavras importantes em declarações. No exemplo de um bot de anotações, a entidade `Notes.Title` identifica o título de cada nota.

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de Reconhecimento vocal com o serviço de bot

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso** na folha de menu e clique em **Ver tudo**.

    ![Criar novo recurso](../media/bot-builder-dotnet-use-luis/bot-service-creation.png)

2. Na caixa de pesquisa, pesquise **Bot do aplicativo Web**. 

    ![Criar novo recurso](../media/bot-builder-dotnet-use-luis/bot-service-selection.png)

3. Na folha **Serviço de Bot**, forneça as informações necessárias e clique em **Criar**. Isso cria e implanta o serviço de bot e o aplicativo LUIS no Azure. 
   * Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net). Esse nome também é usado como o nome do aplicativo LUIS associado ao bot. Copie-o para uso posterior, de modo a localizar o aplicativo LUIS associado ao bot.
   * Selecione a assinatura, o [grupo de recursos](/azure/azure-resource-manager/resource-group-overview), o plano do serviço de aplicativo e o [local](https://azure.microsoft.com/regions/).
   * Selecione o modelo **Reconhecimento vocal (C#)** para o campo **Modelo de bot**.

     ![Folha Serviço de bot](../media/bot-builder-dotnet-use-luis/bot-service-setting-callout-template.png)

   * Marque a caixa para confirmar os termos de serviço.

4. Confirme que o serviço de bot foi implantado.
    * Clique em Notificações (o ícone de sino que fica ao longo da borda superior do portal do Azure). A notificação será alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    * Depois que a notificação mudar para **Implantação bem-sucedida**, clique em **Ir para o recurso** nessa notificação.

## <a name="try-the-bot"></a>Experimentar o bot

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir a folha de recursos do bot.

Depois que o bot está registrado, clique em **Teste no chat da Web** para abrir o painel do chat da Web. Digite "hello" no Webchat.

  ![Testar o bot no Webchat](../media/bot-builder-dotnet-use-luis/bot-service-web-chat.png)

O bot responde dizendo "Você atingiu a Saudação. Você disse: Olá". Isso confirma que o bot recebeu sua mensagem e a passou para um aplicativo LUIS padrão que ele criou. Este aplicativo LUIS padrão detectou uma intenção de Saudação.

## <a name="modify-the-luis-app"></a>Modificar o aplicativo LUIS

Faça logon no [https://www.luis.ai](https://www.luis.ai) usando a mesma conta usada para fazer logon no Azure. Clique em **Meus aplicativos**. Na lista de aplicativos, localize o aplicativo que começa com o nome especificado em **Nome do aplicativo** na folha **Serviço de Bot** quando você criou o Serviço de Bot. 

O aplicativo LUIS inicia com 4 intenções: Cancelar: Saudação, Ajuda e Nenhuma. <!-- picture -->

As etapas a seguir adicionam as intenções Note.Create, Note.ReadAloud e Note.Delete: 

1. Clique em **Domínios Pré-criados** no canto inferior esquerdo da página. Encontre o domínio **Note** e clique em **Adicionar domínio**.

2. Este tutorial não usa todas as intenções incluídas no domínio pré-criado **Note**. Na página **Intenções**, clique em cada um dos nomes de intenção a seguir e, em seguida, clique no botão **Excluir Intenção**.
   * Note.ShowNext
   * Note.DeleteNoteItem
   * Note.Confirm
   * Note.Clear
   * Note.CheckOffItem
   * Note.AddToNote

   As únicas intenções que devem permanecer no aplicativo LUIS são as seguintes: 
   * Note.ReadAloud
   * Note.Create
   * Note.Delete
   * Nenhum
   * Ajuda
   * Saudação
   * Cancelar 

     ![intenções mostradas no aplicativo LUIS](../media/bot-builder-dotnet-use-luis/luis-intent-list.png)

3. Clique no botão **Treinar** no canto superior direito para treinar o aplicativo.
4. Clique em **PUBLISH** na barra de navegação superior para abrir a página **Publicar**. Clique no botão **Publicar no slot de produção**. Após a publicação bem-sucedida, copie a URL exibida na coluna **Ponto de extremidade**, na página **Publicar Aplicativo**, na linha que começa com o Nome de Recurso Starter_Key. Salve essa URL para usar posteriormente no código do seu bot. A URL tem um formato semelhante a este exemplo: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?subscription-key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx&timezoneOffset=0&verbose=true&q=`

## <a name="modify-the-bot-code"></a>Modificar o código do bot

Clique em **Criar** e, em seguida, clique em **Abrir editor de código online**.
    ![Abrir editor de código online](../media/bot-builder-dotnet-use-luis/bot-service-build.png)

No editor de código, abra `BasicLuisDialog.cs`. Ele contém o código a seguir para lidar com as intenções do aplicativo LUIS.
```cs
using System;
using System.Configuration;
using System.Threading.Tasks;

using Microsoft.Bot.Builder.Dialogs;
using Microsoft.Bot.Builder.Luis;
using Microsoft.Bot.Builder.Luis.Models;

namespace Microsoft.Bot.Sample.LuisBot
{
    // For more information about this template visit http://aka.ms/basic-luis-dialog
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            ConfigurationManager.AppSettings["LuisAppId"], 
            ConfigurationManager.AppSettings["LuisAPIKey"], 
            domain: ConfigurationManager.AppSettings["LuisAPIHostName"])))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        // Go to https://luis.ai and create a new intent, then train/publish your luis app.
        // Finally replace "Greeting" with the name of your newly created intent in the following handler
        [LuisIntent("Greeting")]
        public async Task GreetingIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        [LuisIntent("Cancel")]
        public async Task CancelIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        [LuisIntent("Help")]
        public async Task HelpIntent(IDialogContext context, LuisResult result)
        {
            await this.ShowLuisResult(context, result);
        }

        private async Task ShowLuisResult(IDialogContext context, LuisResult result) 
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
}
```
### <a name="create-a-class-for-storing-notes"></a>Criar uma classe para armazenar anotações

Adicione a seguinte instrução `using` em BasicLuisDialog.cs.

```cs
using System.Collections.Generic;
```

Adicione o seguinte código na classe `BasicLuisDialog`, após a definição do construtor.

```cs
        // Store notes in a dictionary that uses the title as a key
        private readonly Dictionary<string, Note> noteByTitle = new Dictionary<string, Note>();
        
        [Serializable]
        public sealed class Note : IEquatable<Note>
        {

            public string Title { get; set; }
            public string Text { get; set; }

            public override string ToString()
            {
                return $"[{this.Title} : {this.Text}]";
            }

            public bool Equals(Note other)
            {
                return other != null
                    && this.Text == other.Text
                    && this.Title == other.Title;
            }

            public override bool Equals(object other)
            {
                return Equals(other as Note);
            }

            public override int GetHashCode()
            {
                return this.Title.GetHashCode();
            }
        }

        // CONSTANTS        
        // Name of note title entity
        public const string Entity_Note_Title = "Note.Title";
        // Default note title
        public const string DefaultNoteTitle = "default";
```

### <a name="handle-the-notecreate-intent"></a>Lidar com a intenção Note.Create
Para lidar com a intenção Note.Create, adicione o seguinte código à classe `BasicLuisDialog`.

```cs
        private Note noteToCreate;
        private string currentTitle;
        [LuisIntent("Note.Create")]
        public Task NoteCreateIntent(IDialogContext context, LuisResult result)
        {
            EntityRecommendation title;
            if (!result.TryFindEntity(Entity_Note_Title, out title))
            {
                // Prompt the user for a note title
                PromptDialog.Text(context, After_TitlePrompt, "What is the title of the note you want to create?");
            }
            else
            {
                var note = new Note() { Title = title.Entity };
                noteToCreate = this.noteByTitle[note.Title] = note;

                // Prompt the user for what they want to say in the note           
                PromptDialog.Text(context, After_TextPrompt, "What do you want to say in your note?");
            }
            
            return Task.CompletedTask;
        }
        
        
        private async Task After_TitlePrompt(IDialogContext context, IAwaitable<string> result)
        {
            EntityRecommendation title;
            // Set the title (used for creation, deletion, and reading)
            currentTitle = await result;
            if (currentTitle != null)
            {
                title = new EntityRecommendation(type: Entity_Note_Title) { Entity = currentTitle };
            }
            else
            {
                // Use the default note title
                title = new EntityRecommendation(type: Entity_Note_Title) { Entity = DefaultNoteTitle };
            }

            // Create a new note object 
            var note = new Note() { Title = title.Entity };
            // Add the new note to the list of notes and also save it in order to add text to it later
            noteToCreate = this.noteByTitle[note.Title] = note;

            // Prompt the user for what they want to say in the note           
            PromptDialog.Text(context, After_TextPrompt, "What do you want to say in your note?");

        }

        private async Task After_TextPrompt(IDialogContext context, IAwaitable<string> result)
        {
            // Set the text of the note
            noteToCreate.Text = await result;
            
            await context.PostAsync($"Created note **{this.noteToCreate.Title}** that says \"{this.noteToCreate.Text}\".");
            
            context.Wait(MessageReceived);
        }
```

### <a name="handle-the-notereadaloud-intent"></a>Lidar com a intenção Note.ReadAloud
O bot pode usar a intenção `Note.ReadAloud` para mostrar o conteúdo de uma anotação, ou de todas as anotações, se o título da anotação não for detectado.

Cole o código a seguir na classe `BasicLuisDialog`.
```cs
        [LuisIntent("Note.ReadAloud")]
        public async Task NoteReadAloudIntent(IDialogContext context, LuisResult result)
        {
            Note note;
            if (TryFindNote(result, out note))
            {
                await context.PostAsync($"**{note.Title}**: {note.Text}.");
            }
            else
            {
                // Print out all the notes if no specific note name was detected
                string NoteList = "Here's the list of all notes: \n\n";
                foreach (KeyValuePair<string, Note> entry in noteByTitle)
                {
                    Note noteInList = entry.Value;
                    NoteList += $"**{noteInList.Title}**: {noteInList.Text}.\n\n";
                }
                await context.PostAsync(NoteList);
            }

            context.Wait(MessageReceived);
        }
        
        public bool TryFindNote(string noteTitle, out Note note)
        {
            bool foundNote = this.noteByTitle.TryGetValue(noteTitle, out note); // TryGetValue returns false if no match is found.
            return foundNote;
        }
        
        public bool TryFindNote(LuisResult result, out Note note)
        {
            note = null;

            string titleToFind;

            EntityRecommendation title;
            if (result.TryFindEntity(Entity_Note_Title, out title))
            {
                titleToFind = title.Entity;
            }
            else
            {
                titleToFind = DefaultNoteTitle;
            }

            return this.noteByTitle.TryGetValue(titleToFind, out note); // TryGetValue returns false if no match is found.
        }
```

### <a name="handle-the-notedelete-intent"></a>Lidar com a intenção Note.Delete
Cole o código a seguir na classe `BasicLuisDialog`.

```cs
        [LuisIntent("Note.Delete")]
        public async Task NoteDeleteIntent(IDialogContext context, LuisResult result)
        {
            Note note;
            if (TryFindNote(result, out note))
            {
                this.noteByTitle.Remove(note.Title);
                await context.PostAsync($"Note {note.Title} deleted");
            }
            else
            {                             
                // Prompt the user for a note title
                PromptDialog.Text(context, After_DeleteTitlePrompt, "What is the title of the note you want to delete?");                         
            }           
        }

        private async Task After_DeleteTitlePrompt(IDialogContext context, IAwaitable<string> result)
        {
            Note note;
            string titleToDelete = await result;
            bool foundNote = this.noteByTitle.TryGetValue(titleToDelete, out note);

            if (foundNote)
            {
                this.noteByTitle.Remove(note.Title);
                await context.PostAsync($"Note {note.Title} deleted");
            }
            else
            {
                await context.PostAsync($"Did not find note named {titleToDelete}.");
            }

            context.Wait(MessageReceived);
        }
```

## <a name="build-the-bot"></a>Criar o bot
Clique duas vezes em **build.cmd** no editor de código e escolha **Executar no Console**.

   ![Executar o build.cmd](../media/bot-builder-dotnet-use-luis/bot-service-run-console.png)

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, clique em **Testar no Webchat** para testar o bot. Tente digitar mensagens como "Criar uma nota", "ler minhas anotações" e "excluir anotações".
   ![Testar o bot de anotações no Webchat](../media/bot-builder-dotnet-use-luis/bot-service-test-notebot.png)

> [!TIP]
> Se você achar que seu bot nem sempre reconhece a intenção ou entidades corretas, melhore o desempenho do seu aplicativo LUIS dando mais declarações de exemplo para treiná-lo. É possível treinar novamente seu aplicativo LUIS sem nenhuma modificação no código do seu bot. Confira [Add example utterances](/azure/cognitive-services/LUIS/add-example-utterances) (Adicionar declarações de exemplo) e [treinar e testar seu aplicativo LUIS](/azure/cognitive-services/LUIS/train-test).

> [!TIP]
> Se o código do seu código encontrar um problema, verifique o seguinte:
> * Você [criou o bot](./bot-builder-dotnet-luis-dialogs.md#build-the-bot).
> * O código do seu bot define um manipulador para cada intenção em seu aplicativo LUIS.

## <a name="next-steps"></a>Próximas etapas

A experimentar o bot, você pode ver como as tarefas são invocadas por uma intenção do LUIS. No entanto, esse exemplo simples não permite a interrupção do diálogo ativo no momento. Permitir e manipular interrupções, como "ajuda" ou "cancelar", é um design flexível que leve em consideração o que os usuários realmente fazem. Saiba mais sobre como usar diálogos pontuáveis para que seus diálogos possam lidar com interrupções.

> [!div class="nextstepaction"]
> [Manipuladores de mensagens globais usando pontuáveis](bot-builder-dotnet-scorable-dialogs.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Diálogos](bot-builder-dotnet-dialogs.md)
- [Gerencie o fluxo de conversas com caixas de diálogo](bot-builder-dotnet-manage-conversation-flow.md)
- <a href="https://www.luis.ai" target="_blank">LUIS</a>
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>

[LUIS]: https://www.luis.ai/
[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/CSharp/Simple-LUIS-Notes-Sample
[NotesSampleJSON]: https://github.com/Microsoft/BotFramework-Samples/blob/master/docs-samples/CSharp/Simple-LUIS-Notes-Sample/Notes.json
