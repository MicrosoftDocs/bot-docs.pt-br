Se você estiver usando serviços como o LUIS, você também precisará passar `luisAuthoringKey`. Se você quiser usar o grupo de recursos existente no Azure, use o argumento `groupName` com o comando acima.

É altamente recomendável que você use a opção `verbose` para ajudar a solucionar problemas que podem ocorrer durante a implantação do bot. As opções adicionais usadas com o comando `msbot clone services` são descritas abaixo:

| Argumentos    | DESCRIÇÃO |
|--------------|-------------|
| `folder`     | Localização do arquivo `bot.receipe`. Por padrão, o arquivo de recebimento é criado no `DeploymentsScript/MSBotClone`. NÃO MODIFIQUE esse arquivo.|
| `location`   | Localização geográfica usada para criar recursos do serviço do bot. Por exemplo, eastus, westus, westus2, etc.|
| `proj-file`  | Para o bot em C#, é o arquivo .csproj. Para o bot JS, é o nome do arquivo de projeto de inicialização (por exemplo, index.js) do seu bot local.|
| `name`       | Um nome exclusivo que é usado para implantar o bot no Azure. Ele pode ser o mesmo nome do seu bot local. NÃO inclua espaços no nome.|

Antes de poder criar recursos do Azure, você será solicitado a concluir a autenticação. Siga as instruções na tela para concluir essa etapa.

Observe que a etapa acima demora _de segundos a minutos_ para ser concluída, e os recursos que são criados no Azure sofrem de decoração do nome. Para saber mais sobre de decoração do nome, confira o [problema # 796](https://github.com/Microsoft/botbuilder-tools/issues/796) no repositório do GitHub.
