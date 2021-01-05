---
ms.openlocfilehash: 6bbb7593b3748236c14569d9e5667918efc1b331
ms.sourcegitcommit: 8c1f6682241589ecb55d05ded62d798a761067bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97758884"
---
Para funcionar corretamente, atribua um tipo de ação a cada item clicável em um cartão Hero. Esta tabela lista e descreve os tipos de ação disponíveis e quais devem estar na propriedade de valor associada.
A `messageBack` ação do cartão tem um significado mais generalizado do que as outras ações do cartão. Consulte a seção [ação do cartão](https://github.com/Microsoft/botframework-sdk/blob/main/specs/botframework-activity/botframework-activity.md#card-action) do [esquema da atividade](https://github.com/Microsoft/botframework-sdk/blob/main/specs/botframework-activity/botframework-activity.md) para obter mais informações sobre o `messageBack` e outros tipos de ação do cartão.

| Tipo         | Descrição                                                                  | Valor                                                              |
|:-------------|:-----------------------------------------------------------------------------|:-------------------------------------------------------------------|
| chamada         | Inicia uma chamada telefônica.                                                      | Destino de uma chamada telefônica nesse formato: `tel:123123123123`. |
| downloadFile | Baixa um arquivo.                                                            | A URL do arquivo para download.                                   |
| imBack       | Envia uma mensagem para o bot e posta uma resposta visível no bate-papo.        | Texto da mensagem a ser enviada.                                       |
| messageBack  | Representa uma resposta de texto a ser enviada por meio do sistema de bate-papo.                   | Um valor opcional de programação para incluir em mensagens geradas.   |
| openUrl      | Abre uma URL no navegador interno.                                         | A URL para abrir.                                                   |
| playAudio    | Reproduz áudio.                                                                 | A URL do áudio para reproduzir.                                      |
| playVideo    | Reproduz um vídeo.                                                               | A URL do vídeo para reproduzir.                                          |
| postBack     | Envia uma mensagem para o bot e não pode postar uma resposta visível no bate-papo. | Texto da mensagem a ser enviada.                                       |
| showImage    | Exibe uma imagem.                                                           | A URL da imagem para exibir.                                   |
| signin       | Inicia um processo de entrada do OAuth.                                           | A URL do fluxo de OAuth para iniciar.                             |
