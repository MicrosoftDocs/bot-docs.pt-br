---
title: Solicitar pagamento (C# v3) – Serviço de Bot
description: Saiba como enviar uma solicitação de pagamento usando o SDK do Bot Framework para .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 026b6ad3b6b9037a09b2727838e7499562b83fba
ms.sourcegitcommit: 9d77f3aff9521d819e88efd0fbd19d469b9919e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "75797991"
---
# <a name="request-payment"></a>Solicitar pagamento

> [!NOTE]
> O serviço de pagamento mencionado neste artigo será preterido em 1º de dezembro de 2019.

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-request-payment.md)
> - [Node.js](../nodejs/bot-builder-nodejs-request-payment.md)

Se o bot permitir que os usuários comprem itens, ele poderá solicitar o pagamento incluindo um tipo especial de botão em [rich card](bot-builder-dotnet-add-rich-card-attachments.md). Este artigo descreve como enviar uma solicitação de pagamento usando o SDK do Bot Framework para .NET.

## <a name="prerequisites"></a>Pré-requisitos

Antes de enviar uma solicitação de pagamento usando o SDK do Bot Framework para .NET, você deve concluir estas tarefas de pré-requisito.

### <a name="update-webconfig"></a>Atualizar Web. config

Atualize o **arquivo Web.config** do seu bot para definir `MicrosoftAppId` e  `MicrosoftAppPassword` para os valores de ID e senha do aplicativo que foram gerados para seu bot durante o processo de [registro](~/bot-service-quickstart-registration.md). 

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** do seu bot, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

### <a name="create-and-configure-merchant-account"></a>Crie e configure uma conta de comerciante

1. <a href="https://dashboard.stripe.com/register" target="_blank">Criar e ativar uma conta do Stripe, se você ainda não tiver um.</a>

2. <a href="https://seller.microsoft.com/dashboard/registration/seller/?accountprogram=botframework" target="_blank">Faça login na Central do vendedor com sua conta da Microsoft.</a>

3. No Centro do vendedor, conecte sua conta ao Stripe.

4. No Centro do vendedor, navegue até o Painel e copie o valor de **MerchantID**.

5. Atualizar seu bot **Web. config** para definir `MerchantId` como o valor que você copiou do painel da Central de vendedor. 

[!INCLUDE [Payment process overview](../includes/snippet-payment-process-overview.md)]

## <a name="payment-bot-sample"></a>Exemplo de Bot de pagamento

A amostra <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Payment Bot</a> fornece um exemplo de um bot que envia uma solicitação de pagamento usando o .NET. Para ver esse exemplo de bot em ação, você pode <a href="https://webchat.botframework.com/embed/paymentsample?s=d39Bk7JOMzQ.cwA.Rig.dumLki9bs3uqfWFMjXPn5PFnQVmT2VAVR1Zl1iPi07k" target="_blank">testá-lo no bate-papo</a>, <a href="https://join.skype.com/bot/9fbc0f17-43eb-40fe-bf3b-af151e6ce45e" target="_blank">adicioná-lo como um contato do Skype</a> ou fazer download do exemplo de bot de pagamento e executá-lo localmente usando o Emulador Bot Framework . 

> [!NOTE]
> Para concluir o processo de pagamento de ponta a ponta usando a amostra **Payment Bot** no bate-papo da web ou no Skype, você deve especificar um cartão de crédito ou cartão de débito válido em sua conta da Microsoft (por exemplo, um cartão válido dos EUA). emissor do cartão). Seu cartão não será cobrado e CVV do cartão não será verificada, porque o **Bot de pagamento** exemplo é executado no modo de teste (ou seja, `LiveMode` é definido como `false` na **Web. config**).

As próximas seções deste artigo descrevem as três partes do processo de pagamento, no contexto do **Payment Bot** exemplo.

## <a name="requesting-payment"></a><a id="request-payment"></a> Solicitar pagamento

Seu bot pode solicitar o pagamento de um usuário enviando uma mensagem que contém um [anexo de cartão rico](bot-builder-dotnet-add-rich-card-attachments.md) com um botão que especifica `CardAction.Type` de "pagamento". Esse snippet de código do exemplo de **Bot de Pagamento** cria uma mensagem que contém um cartão Hero com um botão **Comprar**, no qual o usuário pode clicar (ou tocar) para iniciar o processo de pagamento. 

[!code-csharp[Request payment](../includes/code/dotnet-request-payment.cs#requestPayment)]

Neste exemplo, o tipo do botão é especificado como `PaymentRequest.PaymentActionType`, que a biblioteca do Bot Builder define como "pagamento". Valor do botão é preenchida pelo `BuildPaymentRequest` método, que retorna um `PaymentRequest` objeto que contém informações sobre métodos de pagamento com suporte, os detalhes e opções. Para obter mais informações sobre detalhes de implementação, consulte **Dialogs/RootDialog.cs** dentro de <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">pagamento Bot</a> exemplo.

Esta captura de tela mostra o cartão de Hero (com **comprar** botão) que é gerado pelo snippet de código acima. 
 
![Bot de exemplo de pagamentos](../media/payments-bot-buy.png) 

> [!IMPORTANT]
> Qualquer usuário que tenha acesso para o **comprar** botão pode usá-lo para iniciar o processo de pagamento. No contexto de uma conversa em grupo, não é possível designar um botão para uso por apenas um usuário específico. 

## <a name="user-experience"></a><a id="user-experience"></a> Experiência do usuário

Quando um usuário clica no botão **Comprar**, ele é direcionado para uma experiência de pagamento na Web para fornecer todas as informações necessárias de pagamento, envio e contato por meio de sua conta da Microsoft. 

![Pagamento da Microsoft](../media/microsoft-payment.png)

### <a name="http-callbacks"></a>Retornos de chamada HTTP

Chamadas de retorno HTTP serão enviadas ao seu bot para indicar que ele deve executar determinadas operações. Cada retorno de chamada será uma [atividade](bot-builder-dotnet-activities.md) que contenha estes valores de propriedade: 

| Propriedade | Valor |
|----|----|
| `Activity.Type` | invoke | 
| `Activity.Name` | Indica o tipo de operação que o bot deve realizar (por exemplo, atualização de endereço de envio, atualização de opção de envio, pagamento concluído). | 
| `Activity.Value` | A carga de solicitação em formato JSON. | 
| `Activity.RelatesTo` |  Descreve o canal e o usuário associados à solicitação de pagamento. | 

> [!NOTE]
> `invoke` é um tipo de atividade especial que é reservado para uso pelo Microsoft Bot Framework. O remetente de uma atividade `invoke` esperará que seu bot reconheça o retorno de chamada enviando uma resposta HTTP.

## <a name="processing-callbacks"></a><a id="process-callbacks"></a> Retornos de chamada de processamento

[!INCLUDE [Process callbacks overview](../includes/snippet-payment-process-callbacks-overview.md)]

### <a name="shipping-address-update-and-shipping-option-update-callbacks"></a>Atualização de endereço de entrega e retorno de opção de atualização

[!INCLUDE [Process shipping address and shipping option callbacks](../includes/snippet-payment-process-callbacks-1.md)]

### <a name="payment-complete-callbacks"></a>Retornos de chamada completa de pagamento

Ao receber um retorno de chamada do Payment Complete, seu bot receberá uma cópia da solicitação de pagamento inicial e não modificada, bem como os objetos de resposta de pagamento na propriedade `Activity.Value`. O objeto de resposta de pagamento conterá as seleções finais feitas pelo cliente junto com um token de pagamento. Seu bot deve aproveitar a oportunidade para recalcular a solicitação de pagamento final com base na solicitação de pagamento inicial e nas seleções finais do cliente. Supondo que as seleções do cliente sejam determinadas como válidas, o bot deve verificar o valor e a moeda no cabeçalho do token de pagamento para garantir que eles correspondam à solicitação de pagamento final.  Se o bot decidir cobrar o cliente, ele só deve cobrar o valor no cabeçalho do token de pagamento, pois esse é o preço que o cliente confirmou. Se houver uma incompatibilidade entre os valores esperados pelo bot e os valores recebidos no retorno de chamada do Payment Complete, ele poderá falhar na solicitação de pagamento enviando o código de status HTTP `200 OK` juntamente com a configuração do campo de resultado para `failure`.   

Além de verificar os detalhes do pagamento, o bot também deve verificar se o pedido pode ser atendido antes de iniciar o processamento do pagamento. Por exemplo, ela talvez queira verificar se os itens que está sendo comprados ainda estão disponíveis em estoque. Se os valores estão corretos e o processador de pagamento com êxito foi cobrado o token de pagamento, o bot deve responder com o código de status HTTP `200 OK` junto com a configuração do campo de resultado `success` para que a experiência da web de pagamento exibir a confirmação de pagamento. O token de pagamento que o bot recebe só pode ser usado uma vez, pelo comerciante que o solicitou, e deve ser submetido ao Stripe (o único processador de pagamento que o Bot Framework suporta atualmente). O envio de qualquer código de status HTTP no intervalo `400` ou `500` resultará em um erro genérico para o cliente.

O método `OnInvoke` na amostra **Payment Bot** processa os retornos de chamada que o bot recebe. 

[!code-csharp[Request payment](../includes/code/dotnet-request-payment.cs#processCallback)]

Neste exemplo, o bot examina o `Name` propriedade da atividade de entrada para identificar o tipo de operação, ele precisará executar e, em seguida, chama o método apropriado para processar o retorno de chamada. Para obter mais informações sobre detalhes de implementação, consulte **Controllers/MessagesControllers.cs** dentro de <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">pagamento Bot</a> exemplo.

## <a name="testing-a-payment-bot"></a>Testando um bot de pagamento

[!INCLUDE [Test a payment bot](../includes/snippet-payment-test-bot.md)]

Na amostra <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Payment Bot</a>, a configuração `LiveMode` em **Web.config** determina se os retornos de chamada do Payment Complete conterão tokens de pagamento emulados ou tokens de pagamento reais. Se `LiveMode`for definido como`false`, um cabeçalho será adicionado à solicitação de pagamento de saída do bot para indicar que o bot está no modo de teste e o retorno de chamada de Pagamento concluído conterá um símbolo de pagamento emulado que não pode ser cobrado. Se `LiveMode` é definido como `true`, o cabeçalho que indica que o bot está no modo de teste é omitido da solicitação de pagamento de saída do bot e o retorno de chamada completa de pagamento conterá um token de pagamento real que o bot enviará ao Stripe para pagamento processamento. Essa será uma transação real que resulta em encargos para o instrumento de pagamento. 

## <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/sample-payments" target="_blank">Exemplo de Bot de pagamento</a>
- [Visão geral das atividades](bot-builder-dotnet-activities.md)
- [Adicionar cartões ricos mensagens](bot-builder-dotnet-add-rich-card-attachments.md)
- <a href="http://www.w3.org/Payments/" target="_blank">Pagamentos da Web no W3C</a> 
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Referência do SDK do Bot Framework para .NET</a>
