---
title: Solicitar pagamento (JS v3) – Serviço de Bot
description: Saiba como usar os bots para enviar solicitações de pagamento. Consulte exemplos de código que usam o SDK do bot Framework para Node.js adicionar cartões avançados a mensagens e processar pagamentos.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 188aa47806af8b78736f61c91f1839d5b126b6da
ms.sourcegitcommit: ac3a7ee8979fc942f9d7420b2f6845c726b6661a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362259"
---
# <a name="request-payment"></a>Solicitar pagamento

> [!NOTE]
> O serviço de pagamento mencionado neste artigo será preterido em 1º de dezembro de 2019.


> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-request-payment.md)
> - [Node.js](../nodejs/bot-builder-nodejs-request-payment.md)

Se o bot permitir que os usuários comprem itens, ele poderá solicitar o pagamento incluindo um tipo especial de botão em [rich card](bot-builder-nodejs-send-rich-cards.md). Este artigo descreve como enviar uma solicitação de pagamento usando o SDK do Bot Framework para Node.js.

## <a name="prerequisites"></a>Pré-requisitos

Antes de enviar uma solicitação de pagamento usando o SDK do Bot Framework para Node.js, você deve concluir essas tarefas de pré-requisito.

### <a name="register-and-configure-your-bot"></a>Registrar e configurar o bot

Atualize as variáveis de ambiente do bot para `MicrosoftAppId` e `MicrosoftAppPassword` para os valores de ID e senha do aplicativo que foram gerados para o bot durante o processo de [registro](~/bot-service-quickstart-registration.md). 

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** do seu bot, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

### <a name="create-and-configure-merchant-account"></a>Crie e configure uma conta de comerciante

1. <a href="https://dashboard.stripe.com/register" target="_blank">Criar e ativar uma conta do Stripe, se você ainda não tiver um.</a>

2. <a href="https://seller.microsoft.com/dashboard/registration/seller/?accountprogram=botframework" target="_blank">Faça login na Central do vendedor com sua conta da Microsoft.</a>

3. No Centro do vendedor, conecte sua conta ao Stripe.

4. No Centro do vendedor, navegue até o Painel e copie o valor de **MerchantID**.

5. Atualize a variável de ambiente `PAYMENTS_MERCHANT_ID` para o valor que você copiou do Painel do Centro de Vendas. 

[!INCLUDE [Payment process overview](../includes/snippet-payment-process-overview.md)]

## <a name="payment-bot-sample"></a>Exemplo de Bot de pagamento

O exemplo de <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Bot de Pagamento</a> fornece um exemplo de um bot que envia uma solicitação de pagamento usando Node.js. Para ver esse exemplo de bot em ação, é possível <a href="https://webchat.botframework.com/embed/paymentsample?s=d39Bk7JOMzQ.cwA.Rig.dumLki9bs3uqfWFMjXPn5PFnQVmT2VAVR1Zl1iPi07k" target="_blank">experimentá-lo no Webchat </a>, <a href="https://join.skype.com/bot/9fbc0f17-43eb-40fe-bf3b-af151e6ce45e" target="_blank">adicioná-lo como um contato do Skype</a>, ou baixar o exemplo de bot de pagamento e executá-lo localmente usando o Bot Framework Emulador. 

> [!NOTE]
> Para concluir o processo de pagamento de ponta a ponta usando a amostra **Payment Bot** no bate-papo da web ou no Skype, você deve especificar um cartão de crédito ou cartão de débito válido em sua conta da Microsoft (por exemplo, um cartão válido dos EUA). emissor do cartão). O cartão não será cobrado e o CVV do cartão não será verificado porque o exemplo de **Bot de Pagamento** executa em modo de teste (ou seja `PAYMENTS_LIVEMODE` é definido como `false` em **.env**).

As próximas seções deste artigo descrevem as três partes do processo de pagamento, no contexto do **Payment Bot** exemplo.

## <a name="requesting-payment"></a><a id="request-payment"></a> Solicitar pagamento

O bot pode solicitar o pagamento de um usuário, enviando uma mensagem que contém um [cartão avançado](bot-builder-nodejs-send-rich-cards.md) com um botão que especifica `type` de "pagamento". Esse snippet de código do exemplo de **Bot de Pagamento** cria uma mensagem que contém um cartão Hero com um botão **Comprar**, no qual o usuário pode clicar (ou tocar) para iniciar o processo de pagamento. 

[!code-javascript[Request payment](../includes/code/node-request-payment.js#requestPayment)]

Neste exemplo, o tipo do botão é especificado como `payments.PaymentActionType`, que o aplicativo define como "pagamento". O valor do botão é preenchido pela função `createPaymentRequest`, que retorna um objeto `PaymentRequest` contendo informações sobre detalhes, opções e formas de pagamento com suporte. Para obter mais informações sobre os detalhes de implementação, consulte **app.js** no exemplo <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Bot de Pagamento</a>.

Esta captura de tela mostra o cartão de Hero (com **comprar** botão) que é gerado pelo snippet de código acima. 
 
![Bot de exemplo de pagamentos](../media/payments-bot-buy.png) 

> [!IMPORTANT]
> Qualquer usuário que tenha acesso para o **comprar** botão pode usá-lo para iniciar o processo de pagamento. No contexto de uma conversa em grupo, não é possível designar um botão para uso por apenas um usuário específico. 

## <a name="user-experience"></a><a id="user-experience"></a> Experiência do usuário

Quando um usuário clicar no botão **Comprar**, o usuário será direcionado à experiência na Web de pagamento para fornecer todas as informações necessárias de pagamento, envio e contato por meio da conta Microsoft. 

![Pagamento da Microsoft](../media/microsoft-payment.png)

### <a name="http-callbacks"></a>Retornos de chamada HTTP

Chamadas de retorno HTTP serão enviadas ao seu bot para indicar que ele deve executar determinadas operações. Cada retorno de chamada será um evento contendo esses valores de propriedade: 

| Propriedade | Valor |
|----|----|
| `type` | invoke | 
| `name` | Indica o tipo de operação que o bot deve realizar (por exemplo, atualização de endereço de envio, atualização de opção de envio, pagamento concluído). | 
| `value` | A carga de solicitação em formato JSON. | 
| `relatesTo` |  Descreve o canal e o usuário associados à solicitação de pagamento. | 

> [!NOTE]
> `invoke` é um tipo de evento especial que é reservado para uso pelo Microsoft Bot Framework. O remetente de um evento `invoke` espera que o bot reconheça o retorno de chamada enviando uma resposta HTTP.

## <a name="processing-callbacks"></a><a id="process-callbacks"></a> Retornos de chamada de processamento

[!INCLUDE [Process callbacks overview](../includes/snippet-payment-process-callbacks-overview.md)]

### <a name="shipping-address-update-and-shipping-option-update-callbacks"></a>Atualização de endereço de entrega e retorno de opção de atualização

Ao receber um retorno de chamada de Atualização de Opção de Envio ou Atualização de Endereço de Envio, o bot receberá o estado atual dos detalhes de pagamento do cliente na propriedade `value`.
Como um comerciante, você deve tratar esses retornos de chamada como estáticos, considerando os detalhes de pagamento de entrada, você irá calcular alguns detalhes de pagamento de saída e falhará se o estado fornecido pelo cliente de entrada for inválido por algum motivo. 
Se o bot determinar que certas informações são válidas como estão, bastará enviar o código de status HTTP `200 OK` junto com os detalhes de pagamento não modificados. Como alternativa, o bot pode enviar o código de status HTTP `200 OK` juntamente com detalhes de pagamento atualizados que devem ser aplicados antes que o pedido possa ser processado. Em alguns casos, seu bot pode determinar que as informações atualizadas são inválidas e que o pedido não pode ser processado como está. Por exemplo, o endereço de envio do usuário pode especificar um país para o qual o fornecedor do produto não envia. Nesse caso, o bot pode enviar o código de status HTTP `200 OK` e uma mensagem ao preencher a propriedade de erro do objeto de detalhes de pagamento. O envio de qualquer código de status HTTP no intervalo `400` ou `500` resultará em um erro genérico para o cliente.

### <a name="payment-complete-callbacks"></a>Retornos de chamada completa de pagamento

Ao receber um retorno de chamada de Pagamento Concluído, o bot receberá uma cópia da solicitação de pagamento inicial não modificado, bem como os objetos de resposta de pagamento na propriedade `value`. O objeto de resposta de pagamento conterá as seleções finais feitas pelo cliente junto com um token de pagamento. Seu bot deve aproveitar a oportunidade para recalcular a solicitação de pagamento final com base na solicitação de pagamento inicial e nas seleções finais do cliente. Supondo que as seleções do cliente sejam determinadas como válidas, o bot deve verificar o valor e a moeda no cabeçalho do token de pagamento para garantir que eles correspondam à solicitação de pagamento final.  Se o bot decidir cobrar o cliente, ele só deve cobrar o valor no cabeçalho do token de pagamento, pois esse é o preço que o cliente confirmou. Se houver uma incompatibilidade entre os valores esperados pelo bot e os valores recebidos no retorno de chamada do Payment Complete, ele poderá falhar na solicitação de pagamento enviando o código de status HTTP `200 OK` juntamente com a configuração do campo de resultado para `failure`.   

Além de verificar os detalhes do pagamento, o bot também deve verificar se o pedido pode ser atendido antes de iniciar o processamento do pagamento. Por exemplo, ela talvez queira verificar se os itens que está sendo comprados ainda estão disponíveis em estoque. Se os valores estão corretos e o processador de pagamento com êxito foi cobrado o token de pagamento, o bot deve responder com o código de status HTTP `200 OK` junto com a configuração do campo de resultado `success` para que a experiência da web de pagamento exibir a confirmação de pagamento. O token de pagamento que o bot recebe só pode ser usado uma vez, pelo comerciante que o solicitou, e deve ser submetido ao Stripe (o único processador de pagamento que o Bot Framework suporta atualmente). O envio de qualquer código de status HTTP no intervalo `400` ou `500` resultará em um erro genérico para o cliente.

Esse snippet de código do exemplo de **Bot de Pagamento** processa os retornos de chamada que o bot recebe. 

[!code-javascript[Request payment](../includes/code/node-request-payment.js#processCallback)]

Neste exemplo, o bot examina a propriedade `name` do evento de entrada para identificar o tipo de operação que precisa executar e, em seguida, chama o(s) método(s) apropriado(s) para processar(em) o retorno de chamada. Para obter mais informações sobre os detalhes de implementação, consulte **app.js** no exemplo <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Bot de Pagamento</a>.

## <a name="testing-a-payment-bot"></a>Testando um bot de pagamento

[!INCLUDE [Test a payment bot](../includes/snippet-payment-test-bot.md)]

No exemplo de <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Bot de Pagamento</a>, a `PAYMENTS_LIVEMODE` variável de ambiente em **.env** determina se os retornos de chamada do Pagamento Concluído conterão tokens de pagamento emulados ou tokens de pagamento reais. Se `PAYMENTS_LIVEMODE`for definido como`false`, um cabeçalho será adicionado à solicitação de pagamento de saída do bot para indicar que o bot está no modo de teste e o retorno de chamada de Pagamento concluído conterá um símbolo de pagamento emulado que não pode ser cobrado. Se `PAYMENTS_LIVEMODE` é definido como `true`, o cabeçalho que indica que o bot está no modo de teste é omitido da solicitação de pagamento de saída do bot e o retorno de chamada completa de pagamento conterá um token de pagamento real que o bot enviará ao Stripe para pagamento processamento. Essa será uma transação real que resulta em encargos para o instrumento de pagamento. 

## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/sample-payments" target="_blank">Exemplo de Bot de pagamento</a>
- [Adicionar anexos de cartão avançado às mensagens](bot-builder-nodejs-send-rich-cards.md)
- <a href="http://www.w3.org/Payments/" target="_blank">Pagamentos da Web no W3C</a> 
