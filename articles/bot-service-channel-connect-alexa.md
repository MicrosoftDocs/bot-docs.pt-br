---
title: Conectar um bot ao Alexa – Serviço de Bot
description: Saiba como configurar a conexão de um bot com o Alexa.
keywords: conectar um bot, canal do bot, bot do Alexa, credenciais, configurar, telefone
author: NickEricson
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/17/2020
ms.openlocfilehash: 3110b4ea5bb791c97b4915bcf301186ca3b5a09b
ms.sourcegitcommit: 662e41dab1bb35d10f1e50f9f56bd82c901a20e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717402"
---
# <a name="connect-a-bot-to-alexa"></a>Conectar um bot ao Alexa

[!INCLUDE [applies-to-v4](includes/applies-to-v4-current.md)]

Você pode configurar o bot para se comunicar com pessoas usando dispositivos Alexa que dão suporte a Habilidades Personalizadas.

> [!IMPORTANT]
> O bot deve usar o [SDK do bot Framework](https://github.com/microsoft/botframework-sdk) versão 4,8 ou posterior. Quando você cria um novo bot por meio do portal do Azure, o bot usará a versão mais recente disponível nesse momento. Se você tiver um bot existente, talvez seja necessário atualizar a versão do SDK.

## <a name="create-an-alexa-skill"></a>Criar uma habilidade do Alexa

1. Faça logon no [Console do Desenvolvedor do Alexa](https://developer.amazon.com/alexa/console/ask) e clique no botão 'Criar Habilidade'.

1. Na próxima tela, insira um nome para a nova habilidade.  Nessa página, você pode **Escolher um modelo para adicionar à habilidade** (**Personalizado** selecionado por padrão) e **Escolher um método para hospedar os recursos de back-end da habilidade** (**Provisionar o próprio** selecionado por padrão).  Mantenha as opções padrão selecionadas e clique no botão **Criar Habilidade**.

    ![Captura de tela: escolha um modelo e uma hospedagem](./media/channels/alexa-create-skill-options.png)

1. Na próxima tela, você precisará **escolher um modelo**.  A opção **Iniciar do zero** estará selecionada por padrão. Mantenha a opção **Iniciar do zero** selecionada e clique no botão **Escolher**.

    ![Captura de tela: escolher um modelo](./media/channels/alexa-create-skill-options2.png)

1. Agora você verá o painel de habilidades. Navegue até o **Editor de JSON** na seção **Modelo de Interação** do menu à esquerda.

1. Cole o JSON abaixo no **Editor de JSON**, substituindo os seguintes valores:

    * **NOME DE INVOCAÇÃO DA HABILIDADE**: esse é o nome que os usuários usarão para invocar sua habilidade no Alexa. Por exemplo, se o nome de invocação da habilidade for 'auxiliar do adaptador', um usuário poderá dizer "Alexa, inicie o auxiliar do adaptador" para iniciar a habilidade.

    * **FRASES DE EXEMPLO**: você deverá fornecer três frases de exemplo que os usuários poderão usar para interagir com a sua habilidade.  Por exemplo, se um usuário disser "Alexa, peça ao auxiliar do adaptador que me forneça os detalhes do adaptador do Alexa", a frase de exemplo será "fornecer detalhes do adaptador do Alexa".

    ```json
    {
        "interactionModel": {
            "languageModel": {
                "invocationName": "<YOUR SKILL INVOCATION NAME>",
                "intents": [
                    {
                        "name": "GetUserIntent",
                        "slots": [
                            {
                                "name": "phrase",
                                "type": "phrase"
                            }
                        ],
                        "samples": [
                            "{phrase}"
                        ]
                    },
                    {
                        "name": "AMAZON.StopIntent",
                        "samples": []
                    }
                ],
                "types": [
                    {
                        "name": "phrase",
                        "values": [
                            {
                                "name": {
                                    "value": "<EXAMPLE PHRASE>"
                                }
                            },
                            {
                                "name": {
                                    "value": "<EXAMPLE PHRASE>"
                                }
                            },
                            {
                                "name": {
                                    "value": "<EXAMPLE PHRASE>"
                                }
                            }
                        ]
                    }
                ]
            }
        }
    }
    ```

1. Clique no botão **Salvar Modelo** e em **Criar Modelo**, o que atualizará a configuração da habilidade.

1. Obtenha sua **ID de habilidade do Alexa** da URL no portal do Alexa ou acessando o [console do desenvolvedor do Alexa](https://developer.amazon.com/alexa/console/ask) e clicando em **exibir ID da habilidade**. Sua ID de habilidade do Alexa deve ser um valor como ' amzn1. Ask. Skill. A GUID '.

1. No Portal do Bot Framework, navegue até a página Configuração do Canal do Alexa e cole a **ID de Habilidade do Alexa** no campo **Insira a ID da Habilidade**.

1. No Portal do Alexa, navegue até a seção **Ponto de Extremidade** no menu à esquerda.  Selecione **HTTPS** como o **Tipo de Ponto de Extremidade de Serviço** e defina o ponto de extremidade da **Região Padrão** com o valor do **URI de Ponto de Extremidade de Serviço do Alexa** copiado da página Configuração do Alexa no Bot Framework.

1. No menu suspenso abaixo da caixa de texto em que o ponto de extremidade foi definido, você precisará selecionar o tipo de certificado que está sendo usado. Escolha **Meu ponto de extremidade de desenvolvimento é um subdomínio de um domínio que tem um certificado curinga de uma autoridade de certificação**.

    ![Captura de tela: escolher tipo de ponto de extremidade de serviço](./media/channels/alexa-endpoint.PNG)

1. Clique no botão **Salvar Pontos de Extremidade** no Portal do Alexa.

1. Clique no botão **Salvar** na página Configuração do Canal do Alexa no Bot Framework.

Você precisará publicar sua habilidade no Alexa para que os usuários possam se comunicar com ela. Antes de publicá-la, teste sua habilidade no Alexa usando um dispositivo Alexa próprio ou na guia **Testar** da habilidade. Para acessar a guia **Testar**, navegue até a sua habilidade no [Console do Desenvolvedor do Alexa](https://developer.amazon.com/alexa/console/ask).
