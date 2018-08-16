## <a name="payment-process-overview"></a>Visão geral do processo de pagamento

O processo de pagamento é composto por três partes distintas:

1. O bot envia uma solicitação de pagamento.

2. O usuário entra com uma conta Microsoft para fornecer as informações de pagamento, envio e contato. Os retornos de chamada são enviados para o bot para indicar quando o bot precisa realizar algumas operações (atualizar endereço de envio, atualizar opção de envio, concluir o pagamento).

3. O bot processa os retornos de chamada que recebe, incluindo a atualização do endereço de envio, a atualização da opção de envio e a conclusão do pagamento. 

Seu bot deve implementar apenas a etapa um e a etapa três deste processo; a etapa dois é realizada fora do contexto de seu bot. 
