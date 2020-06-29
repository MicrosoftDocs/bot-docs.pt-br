---
title: Serviços Cognitivos – Serviço de Bot
description: Saiba como adicionar inteligência artificial aos bots com os Serviços Cognitivos da Microsoft para torná-los mais úteis e interativas.
keywords: reconhecimento vocal, extração de conhecimento, reconhecimento de fala, pesquisa na Web
author: RobStand
ms.author: rstand
manager: rstand
ms.topic: article
ms.service: bot-service
ms.date: 12/17/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6243d74aabd7fe67b1c7b8bfcebc8bd04511d8b5
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75792948"
---
# <a name="cognitive-services"></a>Serviços Cognitivos

Os Serviços Cognitivos da Microsoft permitem explorar uma coleção crescente de algoritmos de IA avançados desenvolvidos por especialistas nas áreas de Pesquisa Visual Computacional, fala, processamento de linguagem natural, extração de conhecimento e pesquisa na Web. Os serviços simplificam uma variedade de tarefas baseadas em AI, fornecendo uma maneira rápida de adicionar tecnologias de inteligência de última geração aos bots com apenas algumas linhas de código. As APIs integram-se nas linguagens e plataformas mais modernas. As APIs também estão constantemente melhorando, aprendendo e ficando mais inteligentes, por isso as experiências estão sempre atualizadas. 

Os bots inteligentes respondem como se pudessem ver o mundo como as pessoas o veem. Eles descobrem informações e extraem conhecimento de diferentes fontes para fornecer respostas úteis e, melhor de tudo, aprendem na medida em que adquirem mais experiência para melhorar continuamente seus recursos. 

## <a name="language-understanding"></a>Reconhecimento vocal

A interação entre usuários e bots é principalmente de forma livre, portanto, os bots precisam reconhecer a linguagem naturalmente e contextualmente. As APIs de Linguagem de Serviço Cognitivo fornecem modelos de linguagem avançados para determinar o que os usuários desejam, identificar conceitos e entidades em uma determinada frase e, finalmente, permitir que os bots respondam com a ação apropriada. As cinco APIs dão suporte a vários recursos de análise de texto como verificação ortográfica, detecção de sentimento, modelo de linguagem e extração de informações precisas e avançadas do texto. 

Os Serviços Cognitivos fornecem estas APIs para o reconhecimento vocal:

- O LUIS <a href="https://www.microsoft.com/cognitive-services/language-understanding-intelligent-service-luis" target="_blank">(Serviço Inteligente de Reconhecimento Vocal)</a> é capaz de processar linguagem natural usando modelos de linguagem pré-construídos ou personalizados. Mais detalhes podem ser encontrados em [Reconhecimento vocal para bots](v4sdk/bot-builder-concept-luis.md)

- A <a href="https://www.microsoft.com/cognitive-services/text-analytics-api" target="_blank">API de Análise de Texto</a> detecta sentimento, frases-chave, tópicos e o idioma do texto.

- A <a href="https://www.microsoft.com/cognitive-services/bing-spell-check-api" target="_blank">API de Verificação Ortográfica do Bing</a> fornece recursos de verificação ortográfica avançados e é capaz de reconhecer a diferença entre nomes, nomes de marcas e gírias.

- Os <a href="https://docs.microsoft.com/azure/machine-learning/studio/text-analytics-module-tutorial" target ="_blank">Modelos de Análise de Texto no Azure Machine Learning Studio</a> permitem compilar e operacionalizar modelos de análise de texto como a lematização e pré-processamento de texto. Esses modelos podem ajudá-lo a resolver questões como problemas de classificação de documentos ou análise de sentimentos.

Saiba mais sobre o [reconhecimento vocal][language] com os Serviços Cognitivos da Microsoft.

## <a name="knowledge-extraction"></a>Extração de conhecimento

Os Serviços Cognitivos fornecem quatro APIs de conhecimento que permitem identificar entidades nomeadas ou frases em texto não estruturado, adicionar recomendações personalizadas, fornecer sugestões de preenchimento automático com base na interpretação natural de consultas do usuário e pesquisar trabalhos acadêmicos e outras pesquisas como um serviço personalizado de perguntas frequentes.

- O <a href="https://www.microsoft.com/cognitive-services/entity-linking-intelligence-service" target="_blank">Serviço de Inteligência de Vinculação de Entidade</a> anota texto não estruturado com as entidades relevantes mencionadas no texto. Dependendo do contexto, a mesma palavra ou frase pode referir-se a coisas distintas. Esse serviço compreende o contexto do texto fornecido e identificará cada entidade no texto.    

- O <a href="https://www.microsoft.com/cognitive-services/knowledge-exploration-service" target="_blank">Serviço de Exploração de Conhecimento</a> fornece interpretação de linguagem natural das consultas do usuário e retorna interpretações anotadas para permitir experiências de pesquisa e preenchimento automático avançados que antecipam o que o usuário está digitando. As sugestões de conclusão de consulta instantânea e os refinamentos de consulta preditiva são baseados nos próprios dados e em gramáticas específicas do aplicativo para permitir que os usuários realizem consultas rápidas.    

- A  <a href="https://www.microsoft.com/cognitive-services/academic-knowledge-api" target="_blank">API de Conhecimento Acadêmico</a> retorna documentos de pesquisa acadêmica, autores, periódicos, conferências, tópicos e universidades do <a href="https://www.microsoft.com/research/project/microsoft-academic-graph/" target="_blank">Microsoft Academic Graph</a>. Compilada como um exemplo específico de domínio do Serviço de Exploração de Conhecimento, a API de Conhecimento Acadêmico fornece uma base de dados de conhecimento usando um diálogo semelhante a um gráfico com recursos de pesquisa em centenas de milhões de entidades relacionadas à pesquisa. Pesquise um tópico, um professor, uma universidade ou uma conferência e a API fornecerá publicações relevantes e entidades relacionadas. A gramática também dá suporte a consultas naturais como "Artigos de Michael Jordan sobre aprendizado de máquina após 2010".

- O <a href="https://qnamaker.ai" target="_blank">QnA Maker</a> é uma API REST e serviço online fácil de usar que treina a IA para responder às perguntas dos usuários de maneira mais natural. Com a lógica de aprendizado de máquina otimizada e capacidade de integrar o processamento de linguagem líder do setor, o QnA Maker destila dados semiestruturados como pares de perguntas e respostas em respostas distintas e úteis.

Saiba mais sobre a [extração de conhecimento][knowledge] com os Serviços Cognitivos da Microsoft.

## <a name="speech-recognition-and-conversion"></a>Reconhecimento de fala e conversão

Use as APIs de Fala para adicionar habilidades de fala avançadas ao bot que aproveitam algoritmos líderes de mercado para conversão de fala em texto e conversão de texto em fala, bem como reconhecimento de locutor. As APIs de Fala usam linguagem interna e modelos acústicos que abrangem uma ampla variedade de cenários com alta precisão. 

Para aplicativos que exigem personalização adicional, é possível usar o CRIS (Serviço Inteligente de Reconhecimento Personalizado). Isso permite calibrar a linguagem e os modelos acústicos do reconhecedor de fala, adequando-o ao vocabulário do aplicativo ou até mesmo ao estilo de fala dos usuários.

Há três APIs de Fala disponíveis nos Serviços Cognitivos para processamento ou sintetização de voz:

- A <a href="https://www.microsoft.com/cognitive-services/speech-api" target="_blank">API de Fala do Bing</a> fornece recursos de 	conversão de fala em texto e conversão de texto em fala.
- O <a href="https://www.microsoft.com/cognitive-services/custom-recognition-intelligent-service-cris" target="_blank">CRIS (Serviço Inteligente de Reconhecimento Personalizado)</a> permite que você crie modelos personalizados de reconhecimento de voz para adaptar a conversão de fala em texto ao vocabulário de um aplicativo ou ao estilo de fala do usuário.
- A <a href="https://www.microsoft.com/cognitive-services/speaker-recognition-api" target="_blank">API de Reconhecimento do Locutor</a> permite verificação e identificação de locutor através da voz.

Os seguintes recursos fornecem informações adicionais sobre como adicionar reconhecimento de fala ao bot.

* [Conversas de bot para visão geral de vídeo de Aplicativos](https://channel9.msdn.com/events/Build/2017/P4114)
* [Biblioteca Microsoft.Bot.Client para aplicativos Xamarin ou UWP](https://aka.ms/bot-client)
* [Exemplo de biblioteca de clientes de bot](https://aka.ms/trivia-bot-speech-sample)
* [Cliente de WebChat habilitado para fala](https://aka.ms/BotFramework-WebChat)

Saiba mais sobre [reconhecimento de fala e conversão][speech] com os Serviços Cognitivos da Microsoft.

## <a name="web-search"></a>Pesquisa na Web

As APIs de Pesquisa do Bing permitem adicionar recursos inteligentes de pesquisa na Web aos bots. Com algumas linhas de código, é possível acessar bilhões de páginas da Web, imagens, vídeos, notícias e outros tipos de resultados. Você pode configurar as APIs para retornar resultados por localização geográfica, mercado ou idioma para melhor relevância. É possível personalizar ainda mais a pesquisa usando os parâmetros de pesquisa com suporte como Pesquisa Segura para filtrar conteúdo para adulto e Atualização para retornar resultados de acordo com uma data específica.

Há cinco APIs de Pesquisa do Bing disponíveis nos Serviços Cognitivos.

- A <a href="https://www.microsoft.com/cognitive-services/bing-web-search-api" target="_blank">API de Pesquisa na Web do Bing</a> fornece resultados de pesquisa na Web, imagem, vídeo, notícias e resultados relacionados com uma única chamada à API.

- A <a href="https://www.microsoft.com/cognitive-services/bing-image-search-api" target="_blank">API de Pesquisa de Imagem do Bing</a> retorna resultados de imagem com metadados aprimorados (cor dominante, tipo de imagem, etc.) e dá suporte a vários filtros de imagem para personalizar os resultados.

- A <a href="https://www.microsoft.com/cognitive-services/bing-video-search-api" target="_blank">API de Pesquisa de Vídeo do Bing</a> recupera os resultados de vídeo com metadados avançados (tamanho, qualidade e preço do vídeo, etc.), visualizações de vídeo e dá suporte a vários filtros de vídeo para personalizar os resultados.

- A <a href="https://www.microsoft.com/cognitive-services/bing-news-search-api" target="_blank">API de Pesquisa de Notícias do Bing</a> localiza artigos de notícias em todo o mundo que correspondam à consulta de pesquisa ou estejam atualmente em tendência na Internet.

- A  <a href="https://www.microsoft.com/cognitive-services/bing-autosuggest-api" target="_blank">API de Sugestão Automática do Bing</a> oferece sugestões de conclusão de consulta instantânea para concluir a consulta de pesquisa mais rapidamente e com menos digitação. 

Saiba mais sobre a [pesquisa na Web][search] com Serviços Cognitivos da Microsoft.

## <a name="image-and-video-understanding"></a>Reconhecimento de imagem e vídeo

As APIs da Pesquisa Visual Computacional oferecem aos bots habilidades avançadas de reconhecimento de imagem e vídeo. Algoritmos de última geração permitem que você processe imagens ou vídeos e receba de volta informações que podem ser transformadas em ações. Por exemplo, é possível usá-los para reconhecer objetos, pessoas, idade, sexo ou até mesmo sentimentos. 

As APIs da Pesquisa Visual Computacional dão suporte a vários recursos de reconhecimento de imagem. Esses recursos podem identificar conteúdo somente para adultos ou explícito, categorizar o conteúdo das imagens, realizar reconhecimento óptico de caracteres e descrever uma imagem com frases complexas em inglês. As APIs da Pesquisa Visual Computacional também dão suporte a vários recursos de processamento de imagem e vídeo como geração inteligente de miniaturas de imagens ou vídeos ou estabilização da saída de um vídeo.

Os Serviços Cognitivos fornecem quatro APIs que podem ser usadas para processar imagens ou vídeos:

- A <a href="https://www.microsoft.com/cognitive-services/computer-vision-api" target="_blank">API da Pesquisa Visual Computacional</a> extrai informações avançadas sobre imagens (como objetos ou pessoas), determina se a imagem contém conteúdo somente para adultos ou explícito e processa texto (usando OCR) em imagens.

- A <a href="https://www.microsoft.com/cognitive-services/emotion-api" target="_blank">API de Detecção de Emoções</a> analisa faces humanas e reconhece as emoções em oito possíveis categorias de emoções humanas.

- A <a href="https://www.microsoft.com/cognitive-services/face-api" target="_blank">API de Detecção Facial</a> detecta faces humanas, compara-as a faces semelhantes e pode até mesmo organizar pessoas em grupos de acordo com a similaridade visual.

- A <a href="https://www.microsoft.com/cognitive-services/video-api" target="_blank">API de Pesquisa de Vídeo do Bing</a> analisa e processa vídeo para estabilizar a saída de vídeo, detecta movimento, rastreia faces e pode gerar um resumo em miniatura de movimento do vídeo.

Saiba mais sobre [reconhecimento de vídeo e imagem][vision] com Serviços Cognitivos da Microsoft.

## <a name="additional-resources"></a>Recursos adicionais

É possível localizar uma documentação abrangente de cada produto e as referências de API correspondentes na <a href="https://docs.microsoft.com/azure/cognitive-services" target="_blank">documentação dos Serviços Cognitivos</a>.

[language]: https://docs.microsoft.com/azure/cognitive-services/luis/home
[search]: https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web
[vision]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home
[knowledge]: https://docs.microsoft.com/azure/cognitive-services/kes/overview
[speech]: https://docs.microsoft.com/azure/cognitive-services/speech/home
[location]: https://docs.microsoft.com/azure/cognitive-services/
