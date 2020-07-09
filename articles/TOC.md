# [Documentação do Serviço de Bot do Azure](index.yml)
# Visão geral
## [Sobre o Serviço de Bot do Azure](bot-service-overview-introduction.md)
## [Novidades](what-is-new.md)
# Guia de Início Rápido
## [Criar um bot usando o .NET](dotnet/bot-builder-dotnet-sdk-quickstart.md)
## [Criar um bot usando JavaScript](javascript/bot-builder-javascript-quickstart.md)
## [Criar um bot usando Python](python/bot-builder-python-quickstart.md)
## [Criar um bot usando o Serviço de Bot do Azure](v4sdk/abs-quickstart.md)
# Tutoriais
## [1. Criar e implantar um bot básico](v4sdk/bot-builder-tutorial-basic-deploy.md)
## [2. Adicionar o QnA Maker e reimplantar um bot](v4sdk/bot-builder-tutorial-add-qna.md)
## [Adicionar autenticação ao seu bot ](bot-builder-tutorial-authentication.md)
# Exemplos
## [Repositório de exemplos do Bot Framework no GitHub](https://github.com/Microsoft/BotBuilder-Samples/blob/master/README.md)
# Conceitos
## Bot
### [Como funcionam os bots](v4sdk/bot-builder-basics.md)
### [Gerenciar estado](v4sdk/bot-builder-concept-state.md)
### [Biblioteca de caixas de diálogo](v4sdk/bot-builder-concept-dialog.md)
### [Middleware](v4sdk/bot-builder-concept-middleware.md)
## Caixas de diálogo adaptáveis
### [Introdução às caixas de diálogo adaptáveis](v4sdk/bot-builder-adaptive-dialog-Introduction.md)
### [Eventos e gatilhos em caixas de diálogo adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-Triggers.md)
### [Ações em caixas de diálogo adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-Actions.md)
### [Solicitar entrada do usuário usando caixas de diálogo adaptáveis ](v4sdk/bot-builder-concept-adaptive-dialog-Inputs.md)
### [Reconhecedores em caixas de diálogo adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-recognizers.md)
### [Geração de linguagem em caixas de diálogo adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-generators.md)
### [Escopos de memória e gerenciamento de estado em caixas de diálogo adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-memory-states.md)
### [Como usar ativos declarativos em diálogos adaptáveis](v4sdk/bot-builder-concept-adaptive-dialog-declarative.md)
## Autenticação
### [Autenticação de bot](v4sdk/bot-builder-concept-authentication.md)
### [Provedores de Identidade](v4sdk/bot-builder-concept-identity-providers.md)
### [Logon único](v4sdk/bot-builder-concept-sso.md)
## [Gerenciar recursos do bot](v4sdk/bot-file-basics.md)
## [Como funcionam os bots do Microsoft Teams](v4sdk/bot-builder-basics-teams.md)
## Habilidades
### [Sobre as habilidades](v4sdk/skills-conceptual.md)
### [Sobre bots de habilidades](v4sdk/skills-about-skill-bots.md)
### [Sobre consumidores de habilidades](v4sdk/skills-about-skill-consumers.md)
## [Expressões adaptáveis](v4sdk/bot-builder-concept-adaptive-expressions.md)
## [Geração de Linguagem](v4sdk/bot-builder-concept-language-generation.md)
<!-- [Language understanding](v4sdk/bot-builder-concept-luis.md) -->
## [Modelos de Serviço de Bot](bot-service-concept-templates.md)
## [Serviços Cognitivos](bot-service-concept-intelligence.md)
## [Principais cenários para bots](bot-service-scenario-overview.md)
### [Comércio de bots](bot-service-scenario-commerce.md)
### [Bot de habilidades da Cortana](bot-service-scenario-cortana-skill.md)
### [Bot de Produtividade Empresarial](bot-service-scenario-enterprise-productivity.md)
### [Bot de informações](bot-service-scenario-informational.md)
### [Bot de Internet das Coisas](bot-service-scenario-internet-things.md)
# Instruções
## [Design](design/TOC.md)
## Desenvolver
<!-- ## [Best practice for welcoming the user](v4sdk/bot-builder-welcome-user.md) -->
### [Enviar e receber mensagens de texto](v4sdk/bot-builder-howto-send-messages.md)
### [Adicionar mídia às mensagens](v4sdk/bot-builder-howto-add-media-attachments.md)
### [Adicionar botões para orientar a ação do usuário](v4sdk/bot-builder-howto-add-suggested-actions.md)
### [Salvar dados de usuário e de conversa](v4sdk/bot-builder-howto-v4-state.md)
### [Solicitar aos usuários por entrada](v4sdk/bot-builder-primitive-prompts.md)
### [Enviar mensagem de boas-vinda aos usuários](v4sdk/bot-builder-send-welcome-message.md)
### [Enviar notificações proativas para os usuários](v4sdk/bot-builder-howto-proactive-message.md)
### [Implementar o fluxo de conversa sequencial](v4sdk/bot-builder-dialog-manage-conversation-flow.md)
### [Adicionar o reconhecimento de idioma natural ao seu bot](v4sdk/bot-builder-howto-v4-luis.md)
### [Responder às perguntas do usuário usando o QnA Maker](v4sdk/bot-builder-howto-qna.md)
### [Usar vários modelos de LUIS e QnA](v4sdk/bot-builder-tutorial-dispatch.md)
### [Gerenciar a complexidade da caixa de diálogo](v4sdk/bot-builder-compositcontrol.md)
### [Criar fluxo de conversa avançado usando branches e loops](v4sdk/bot-builder-dialog-manage-complex-conversation-flow.md)
<!--#### [Implement a greeting dialog](v4sdk/bot-builder-dialogs-greeting.md)--TODO: Add once there's a sample.-->
### [Manipular interrupções do usuário](v4sdk/bot-builder-howto-handle-user-interrupt.md)
### Caixas de diálogo adaptáveis
#### [Criar um bot usando caixas de diálogo adaptáveis](v4sdk\bot-builder-dialogs-adaptive.md)
#### [Criar um bot usando caixas de diálogo adaptáveis, de componente, de cascata e personalizadas](v4sdk/bot-builder-mixed-dialogs.md)
#### [Criar um bot usando diálogos adaptáveis declarativos](v4sdk/bot-builder-dialogs-declarative.md)
### [Gravar diretamente no armazenamento](v4sdk/bot-builder-howto-v4-storage.md)
### Autenticação
#### [Adicionar autenticação a um bot](v4sdk/bot-builder-authentication.md)
#### [Adicionar logon único a um bot](v4sdk/bot-builder-authentication-sso.md)
### [Implementar um armazenamento personalizado para seu bot](v4sdk/bot-builder-custom-storage.md)
### Geração de Linguagem
#### [Usar modelos de geração de linguagem em seu bot](language-generation/bot-builder-howto-use-lg-templates.md)
### Habilidades
#### [Implementar uma habilidade](v4sdk/skill-implement-skill.md)
#### [Escrever um manifesto de habilidades v2.1](v4sdk/skills-write-manifest-2-1.md)
#### [Escrever um manifesto de habilidades v2.0](v4sdk/skills-write-manifest-2-0.md)
#### [Usar caixas de diálogo dentro de uma habilidade](v4sdk/skill-actions-in-dialogs.md)
#### [Implementar um consumidor de habilidades](v4sdk/skill-implement-consumer.md)
#### [Usar uma caixa de diálogo para consumir uma habilidade](v4sdk/skill-use-skilldialog.md)
<!--
#### [Use middleware in a skill consumer](v4sdk/skill-middleware-in-consumer.md)
#### [Add claims validation](v4sdk/skill-add-claims-validation.md)
-->
### [Adicionar telemetria ao seu bot](v4sdk/bot-builder-telemetry.md)
### [Adicionar telemetria ao seu bot do QnA](v4sdk/bot-builder-telemetry-QnAMaker.md)
### [Analisar os dados telemétricos do seu bot](v4sdk/bot-builder-telemetry-analytics-queries.md)
### [Use o Direct Line por Fala no seu bot](directline-speech-bot.md)
### [.NET](dotnet/TOC.md)
### [Node.js](nodejs/TOC.md)
## Teste
### [Bots de teste de unidade](v4sdk/unit-test-bots.md)
### [Adicionar atividades de rastreamento ao bot](v4sdk/using-trace-activities.md)
## [Depurar](debug/TOC.md)
## Implantar
### [Implantar seu bot no Azure](bot-builder-deploy-az-cli.md)
### [Configurar a implantação contínua](bot-service-build-continuous-deployment.md)
## [Gerenciar](manage/TOC.md)
## [Migrar](v4sdk/migration/TOC.md)
# Referência
## Referência de SDK da API
### v4
#### [SDK v4 do .NET](https://aka.ms/botframework-v4-cs-sdk)
#### [SDK v4 do JavaScript](https://aka.ms/bot-jssdk-v4)
#### [SDK v4 do Python](https://aka.ms/botframework-v4-python-sdk)
### v3
#### [SDK v3 do .NET](https://aka.ms/botframework-v3-cs-sdk)
#### [SDK v3 do Node.js](https://aka.ms/bot-jssdk-v3)
## [REST](rest-api/TOC.md)
## [Ferramenta da CLI do Bot Framework](v4sdk/bf-cli-overview.md)
## Caixas de diálogo adaptáveis
### [Eventos e gatilhos](adaptive-dialog/adaptive-dialog-prebuilt-triggers.md)
### [Ações](adaptive-dialog/adaptive-dialog-prebuilt-actions.md)
### [Entradas](adaptive-dialog/adaptive-dialog-prebuilt-inputs.md)
### [Reconhecedores](adaptive-dialog/adaptive-dialog-prebuilt-recognizers.md)
### [Escopos de memória](adaptive-dialog/adaptive-dialog-prebuilt-memory-states.md)
## Expressões adaptáveis
### [Funções predefinidas](adaptive-expressions/adaptive-expressions-prebuilt-functions.md)
### [Referência de API](adaptive-expressions/adaptive-expressions-API-reference.md)
## LG
### [Referência de API](language-generation/language-generation-API-reference.md)
### [Formato de arquivo .lg](file-format/bot-builder-lg-file-format.md)
### [Modelo de resposta estruturado](language-generation/language-generation-structured-response-template.md)
### [Funções injetadas de LG](language-generation/functions-injected-from-language-generation.md)
## Formatos de arquivo
### [Formato de arquivo .lu](file-format/bot-builder-lu-file-format.md)
### [Formato de arquivo .qna](file-format/bot-builder-qna-file-format.md)
## [Entidades e tipos de atividades](bot-service-activities-entities.md)
# [Recursos](resources/TOC.md)
