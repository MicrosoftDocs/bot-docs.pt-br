---
title: Dar as boas-vindas ao usuário | Microsoft Docs
description: Saiba como projetar seu bot para fornecer uma ótima experiência do usuário.
keywords: visão geral, design, experiência do usuário, Boas-vindas, experiência personalizada
author: dashel
ms.author: dashel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 8/30/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f9c58762e0733fbb379b43375710e651424095fc
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49998743"
---
# <a name="welcoming-the-user"></a>Como dar as boas-vindas ao usuário

Ao criar um bot, o principal objetivo é envolver seu usuário em uma conversa produtiva. Uma das melhores maneiras de fazer isso é garantir que, a partir do momento em que o usuário se conecta pela primeira vez, ele entenda o objetivo e os recursos principais do bot, a razão pela qual seu bot foi criado.

## <a name="show-your-purpose"></a>Mostrar sua finalidade

Agora, imagine a experiência de um usuário que se conecta ao seu bot, que possui o rótulo "Informações turísticas de Chicago", na esperança de encontrar hotéis em Chicago. Ele nem desconfia, mas, na verdade, seu bot serve para apreciadores de pizza, e só oferece dicas de restaurantes. Ao perceber que as perguntas não estão recebendo as respondidas corretas, o usuário se desconecta rapidamente e avalia como ruim a experiência online oferecida pelo seu site. Uma experiência do usuário insatisfatória como essa poderia ser evitada se você desse as boas-vindas aos usuários com informações suficientes para que eles entendessem os principais recursos e a finalidade do seu bot. 

![Mensagem de boas-vindas](./media/welcome_message.png)

Ao ler sua mensagem de boas-vindas, se o bot não oferecer o tipo de informação que o usuário deseja, ele não perderá tempo, nem passará por uma interação frustrante.
Uma mensagem de boas-vinda deve ser gerada sempre que os usuários interagir pela primeira vez com seu bot. Para fazer isso, monitore os tipos de **atividade** do bot e observe as novas conexões. Cada nova conexão pode gerar até duas atividades de atualização da conversa, dependendo do canal.

- Uma quando o bot do usuário estiver conectado à conversa.
- Uma quando o usuário se juntar à conversa.

Gerar uma mensagem de boas-vindas sempre que uma nova atualização da conversa for detectada soa tentador, mas isso pode trazer resultados inesperados quando seu bot for acessado em vários canais.

## <a name="design-for-different-channels"></a>Projetar para canais diferentes

Você tem controle total sobre as informações apresentadas pelo bot, mas talvez não tenha controle sobre a forma como os diferentes canais implementam a apresentação dessas informações. Alguns canais criam uma atualização da conversa quando um usuário se conecta inicialmente ao canal e uma atualização da conversa separada apenas depois que uma mensagem de entrada inicial é recebida do usuário. Outros canais geram essas duas atividades quando o usuário se conecta inicialmente ao canal. Se você só monitorar eventos de atualização da conversa e exibir uma mensagem de boas-vindas em um canal com duas atividades de atualização da conversa, seu usuário poderá receber o seguinte:

![Mensagem dupla de boas-vindas](./media/double_welcome_message.png)

Para evitar essa mensagem duplicada, gere uma mensagem de boas-vindas inicial somente para o segundo evento de atualização da conversa. O segundo evento pode ser detectado quando:
- Um evento de atualização da conversa tiver ocorrido.
- Um novo membro (usuário) tiver sido adicionado à conversa.

Também é importante considerar quando a entrada do usuário pode realmente conter informações úteis. Isso também pode variar por canal. Se um canal gerar atividades de atualização da conversa na conexão inicial de um bot, a primeira entrada do usuário poderá ser avaliada como uma resposta ao prompt da sua mensagem de boas-vindas, como a conversa mostrada abaixo.

![Resposta de entrada única](./media/single_input_response.png)

No entanto, se um canal aguardar uma entrada inicial do usuário antes de gerar um segundo evento de atualização da conversa, o mesmo código usado acima produziria a seguinte experiência do usuário.

![Resposta errada de entrada única](./media/single_input_wrong_response.png)

Para garantir que seus usuários tenham uma boa experiência em todos os canais possíveis, uma prática recomendada é não contar com informações úteis na entrada de conversas iniciais de um usuário. Em vez disso, considere a entrada inicial como dados descartáveis e, após o recebimento, solicite que o usuário forneça as informações necessárias para continuar a conversa. Essa técnica produzirá uma experiência de usuário consistente, independentemente do canal usado para acessar inicialmente o bot.

![Descartar a primeira entrada](./media/no_first_input_response.png)

## <a name="personalize-the-user-experience"></a>Personalizar a experiência do usuário

Nada parece menos acolhedor e mais impessoal para um usuário do que receber continuamente solicitações de informações que ele já forneceu. Se o seu bot preservar informações de usuários que já visitaram o site, uma boa prática seria verificar essas informações primeiro e, se estiverem disponíveis, receber o usuário novamente usando o nome armazenado na visita anterior. 

![Mensagem de bem-vindo de volta](./media/welcome_back.png)

Nesse caso, sua lógica de conversa ignora a solicitação de nome e passa para a próxima atividade da conversa.

Seu bot também pode personalizar a experiência do usuário usando uma resposta oportuna para entradas inesperadas do usuário, como solicitações para encerrar uma conversa.

![Responder à solicitação de saída](./media/respond_to_exit.png)

Manter interações oportunas e conversacionais ajuda os usuários a experimentarem uma interação mais agradável e prazerosa com seu bot.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Enviar mensagem de boas-vinda aos usuários](bot-builder-send-welcome-message.md)
