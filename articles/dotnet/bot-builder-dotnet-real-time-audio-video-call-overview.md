---
title: Construa um bot de mídia em tempo real para o Skype | Microsoft Docs
description: Aprenda a criar um bot que realize chamadas de áudio/vídeo em tempo real com o Skype, usando o SDK do Bot Framework para .NET e o SDK do Bot Builder-RealTimeMediaCalling para .NET.
author: MalarGit
ms.author: malarch
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/17
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a539ca3258e8c9a8020bfa7c939f6eddbdd30feb
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54225651"
---
# <a name="build-a-real-time-media-bot-for-skype"></a>Crie um bot de mídia em tempo real para o Skype

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

A Plataforma de Mídia em Tempo Real para Bots é um recurso avançado que permite ao bot enviar e receber conteúdo de voz e vídeo quadro a quadro. O bot tem acesso "bruto" às modalidades de voz, vídeo e compartilhamento de tela em tempo real. Este artigo fornece uma visão geral da criação de um bot de chamada de áudio / vídeo e do acesso às modalidades em tempo real.

Neste artigo, o bot está sendo executado em um Serviço de Nuvem do Azure, como uma Função da Web ou uma Função de Trabalho que hospeda automaticamente a estrutura da API da Web do ASP.NET.

> [!IMPORTANT]
> O conteúdo deste artigo é preliminar; por favor, consulte a pasta Samples no <a href="https://github.com/Microsoft/BotBuilder-RealTimeMediaCalling">repositório GitHub do BotBuilder-RealTimeMediaCalling</a> para o código completo de exemplos de bots de mídia em tempo real.

## <a name="configure-the-service-hosting-the-real-time-media-bot"></a>Configurar o serviço que hospeda o bot de mídia em tempo real

Para usar a plataforma de mídia em tempo real, as seguintes configurações de serviço são necessárias.

* O bot deve conhecer o Nome de Domínio Totalmente Qualificado (FQDN) de seu serviço. Isso não é fornecido pela API do RoleEnvironment do Azure; em vez disso, o FQDN deve ser armazenado na configuração do Serviço Cloud do bot e lido durante a inicialização do serviço.

* O serviço de bot deve ter um certificado emitido por uma autoridade de certificação reconhecida. A impressão digital do certificado deve ser armazenada na configuração do Serviço Cloud do bot e lida durante a inicialização do serviço.

* Uma pública <a href="/azure/cloud-services/cloud-services-enable-communication-role-instances#instance-input-endpoint">ponto de extremidade de entrada do instância</a> devem ser provisionados. Isso atribui uma porta pública exclusiva a cada instância de máquina virtual (VM) no serviço do bot. Essa porta é usada pela Real-Time Media Platform para se comunicar com o Skype Calling Cloud.
  ```xml
  <InstanceInputEndpoint name="InstanceMediaControlEndpoint" protocol="tcp" localPort="20100">
    <AllocatePublicPortFrom>
    <FixedPortRange max="20200" min="20101" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
  ```

  Também é útil criar outro ponto de extremidade de entrada de instância para retornos de chamada e notificações relacionados a chamadas. O uso de um ponto de extremidade de entrada de instância garante que os retornos de chamada e as notificações sejam entregues para a mesma instância de VM na implantação de serviço que está hospedando a sessão de mídia em tempo real para a chamada.
  ```xml
  <InstanceInputEndpoint name="InstanceCallControlEndpoint" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
    <FixedPortRange max="10200" min="10101" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
  ```

* Cada instância de VM deve ter um endereço IP público no nível da instância (ILPIP). Durante a inicialização, o bot deve descobrir o endereço ILPIP atribuído a cada instância do serviço. Ver <a href="/azure/virtual-network/virtual-networks-instance-level-public-ip">ILPIP</a> para obter mais informações sobre como obter e configurar um ILPIP.
  ```xml
  <NetworkConfiguration>
  <AddressAssignments>
    <InstanceAddress roleName="WorkerRole">
    <PublicIPs>
        <PublicIP name="InstancePublicIP" domainNameLabel="InstancePublicIP" />
    </PublicIPs>
    </InstanceAddress>
  </AddressAssignments>
  </NetworkConfiguration>
  ```

* Durante a inicialização da instância de serviço, o script `MediaPlatformStartupScript.bat` (fornecido como parte do pacote Nuget) precisa ser executado como uma tarefa de inicialização sob privilégios elevados. A execução do script deve ser concluída antes que o método de inicialização da plataforma seja chamado. 

```xml
<Startup>
<Task commandLine="MediaPlatformStartupScript.bat" executionContext="elevated" taskType="simple" />      
</Startup> 
```

## <a name="initialize-the-media-platform-on-service-startup"></a>Inicializar a plataforma de mídia de inicialização do serviço

Durante a inicialização da instância de serviço, a plataforma de mídia em tempo real deve ser inicializada. Isso deve ser feito apenas uma vez antes que o bot possa aceitar qualquer chamada de áudio / vídeo do Skype nessa instância. A inicialização da plataforma de mídia requer o fornecimento de várias configurações de serviço, incluindo o FQDN do serviço, o endereço público ILPIP, o valor da porta do terminal de entrada da instância e o **ID do Microsoft App** do robô.

> [!NOTE]
> Para localizar o **AppID** e **AppPassword** do seu bot, consulte [MicrosoftAppID e MicrosoftAppPassword](~/bot-service-manage-overview.md#microsoftappid-and-microsoftapppassword).

```cs
var mediaPlatformSettings = new MediaPlatformSettings()
{
    MediaPlatformInstanceSettings = new MediaPlatformInstanceSettings()
    {
        CertificateThumbprint = certificateThumbprint,
        InstanceInternalPort = instanceMediaControlEndpointInternalPort,
        InstancePublicIPAddress = instancePublicIPAddress,
        InstancePublicPort = instanceMediaControlEndpointPublicPort,
        ServiceFqdn = serviceFqdn
    },

    ApplicationId = MicrosoftAppId
};

MediaPlatform.Initialize(mediaPlatformSettings);            
```

## <a name="register-to-receive-incoming-call-requests"></a>Registre-se para receber solicitações de entrada chamada

Definir um `CallController` classe. Isso permite que o serviço de bot se registre para chamadas de entrada do Skype e garanta que as solicitações de retorno de chamada e notificação sejam encaminhadas para o objeto `RealTimeMediaCall` apropriado.

```cs
[BotAuthentication]
[RoutePrefix("api/calling")]
public class CallController : ApiController
{
    static CallController()
    {
        RealTimeMediaCalling.RegisterRealTimeMediaCallingBot(
            c => { return new RealTimeMediaCall(c); },
            new RealTimeMediaCallingBotServiceSettings()
        );
    }

    [Route("call")]
    public async Task<HttpResponseMessage> OnIncomingCallAsync()
    {
        // forwards the incoming call to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.IncomingCall);
    }

    [Route("callback")]
    public async Task<HttpResponseMessage> OnCallbackAsync()
    {
        // forwards the incoming callback to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.CallingEvent);
    }

    [Route("notification")]
    public async Task<HttpResponseMessage> OnNotificationAsync()
    {
        // forwards the incoming notification to the associated RealTimeMediaCall object
        return await RealTimeMediaCalling.SendAsync(this.Request, RealTimeMediaCallRequestType.NotificationEvent);
    }
}
```

`RealTimeMediaCallingBotServiceSettings`implementa`IRealTimeMediaCallServiceSettings` e fornece links do webhook para eventos de retorno de chamada e notificação.

## <a name="register-for-incoming-events-for-the-call"></a>Registre-se para eventos de entrada para a chamada

Defina uma classe `RealTimeMediaCall` que implemente `IRealTimeMediaCall`. Para cada chamada que é recebida pelo bot, uma instância de `RealTimeMediaCall` é criado pela estrutura do Bot. O objeto `IRealTimeMediaCallService` passado para o construtor permite que o bot se registre em eventos para manipular eventos associados à chamada de mídia em tempo real.

```cs
internal class RealTimeMediaCall : IRealTimeMediaCall
{
     public RealTimeMediaCall(IRealTimeMediaCallService callService)
     {
         if (callService == null)
             throw new ArgumentNullException(nameof(callService));

         CallService = callService;
         CorrelationId = callService.CorrelationId;
         CallId = CorrelationId + ":" + Guid.NewGuid().ToString();

         // Register for the call events
         CallService.OnIncomingCallReceived += OnIncomingCallReceived;
         CallService.OnAnswerAppHostedMediaCompleted += OnAnswerAppHostedMediaCompleted;
         CallService.OnCallStateChangeNotification += OnCallStateChangeNotification;
         CallService.OnRosterUpdateNotification += OnRosterUpdateNotification;
         CallService.OnCallCleanup += OnCallCleanup;
     }
}
```

## <a name="create-audio-and-video-sockets"></a>Criar soquetes de áudio e vídeos
Antes que o bot possa aceitar uma chamada de entrada de áudio / vídeo do Skype, ele deve criar objetos `AudioSocket` e `VideoSocket` para suportar o envio e recebimento de mídia em tempo real. (Se o bot não quiser suportar vídeo, ele deve criar apenas um AudioSocket.)

O bot deve decidir antecipadamente quais modalidades deseja suportar e criar os objetos AudioSocket e VideoSocket apropriados. O bot não pode alterar quais modalidades ele suporta para a chamada depois que a chamada recebida é aceita.

Para cada AudioSocket e VideoSocket, o bot especifica se o soquete de mídia deve suportar mídia de envio e recebimento, envio ou recebimento apenas. Por exemplo, o bot pode querer enviar e receber áudio ("Sendrecv"), mas enviar apenas para vídeo ("Sendonly"). O bot também deve especificar quais formatos de mídia são suportados para cada soquete de mídia. Para um AudioSocket, o formato atualmente suportado é "Pcm16K": codificação PCM de 16 bits (assinada), taxa de amostragem de 16 KHz. Para um VideoSocket, os formatos de mídia para envio e recebimento são especificados separadamente. Apenas o formato "NV12" é suportado para receber vídeos, enquanto vários formatos diferentes são suportados para envio.

```cs
_audioSocket = new AudioSocket(new AudioSocketSettings
{
    StreamDirections = StreamDirection.Sendrecv,
    SupportedAudioFormat = AudioFormat.Pcm16K,
    CallId = correlationId
});

_videoSocket = new VideoSocket(new VideoSocketSettings
{
    StreamDirections = StreamDirection.Sendrecv,
    ReceiveColorFormat = VideoColorFormat.NV12,
    SupportedSendVideoFormats = new VideoFormat[]
    {
        VideoFormat.Yuy2_1280x720_30Fps,
        VideoFormat.Yuy2_720x1280_30Fps,
    },
    CallId = correlationId
});

_audioSocket.AudioMediaReceived += OnAudioMediaReceived;
_audioSocket.AudioSendStatusChanged += OnAudioSendStatusChanged;
_audioSocket.DominantSpeakerChanged += OnDominantSpeakerChanged;
_videoSocket.VideoMediaReceived += OnVideoMediaReceived;
_videoSocket.VideoSendStatusChanged += OnVideoSendStatusChanged;
```                             

## <a name="create-a-mediaconfiguration"></a>Criar um MediaConfiguration
Uma vez que os soquetes de mídia tenham sido criados, o bot deve, em seguida, criar um objeto `MediaConfiguration` que seja necessário para associar os soquetes de mídia a uma chamada de entrada de áudio / vídeo do Skype.

```cs
var mediaConfiguration = MediaPlatform.CreateMediaConfiguration(_audioSocket, _videoSocket);
```

##  <a name="answer-an-incoming-audiovideo-call"></a>Atender uma chamada de áudio / vídeo recebida
O evento `OnIncomingCallReceived` é chamado para permitir que o bot aceite a chamada de áudio / vídeo do Skype. Para fazer isso, o bot cria um objeto `AnswerAppHostedMedia` com o objeto `MediaConfiguration`. O bot registra as notificações associadas a esta chamada do Skype.

```cs
private Task OnIncomingCallReceived(RealTimeMediaIncomingCallEvent incomingCallEvent)
{
    // ... create Audio/VideoSocket objects and MediaConfiguration ...

    incomingCallEvent.RealTimeMediaWorkflow.Actions = new ActionBase[]
    {
        new AnswerAppHostedMedia
        {
            MediaConfiguration = mediaConfiguration,
            OperationId = Guid.NewGuid().ToString()
        }
    };

    // subscribe for roster and call state changes
    incomingCallEvent.RealTimeMediaWorkflow.NotificationSubscriptions = new NotificationType[]
    {
        NotificationType.CallStateChange,
        NotificationType.RosterUpdate
    };
}
```

## <a name="outcome-of-the-call"></a>Resultado da chamada
`OnAnswerAppHostedMediaCompleted` é gerado quando a ação `AnswerAppHostedMedia` é concluída. A propriedade `Outcome` no `AnswerAppHostedMediaOutcomeEvent` indica sucesso ou falha. Se a chamada não puder ser estabelecida, o bot deverá dispor os objetos AudioSocket e VideoSocket criados para a chamada.

## <a name="receive-audio-media"></a>Receber mídia de áudio
Se o `AudioSocket` foi criado com a capacidade de receber áudio, o evento `AudioMediaReceived` será invocado toda vez que um quadro de áudio for recebido. O bot deve esperar lidar com esse evento aproximadamente 50 vezes por segundo, independentemente do peer que possa estar obtendo conteúdo de áudio (já que buffers de ruído de conforto são gerados localmente se nenhum áudio for recebido do peer). Cada pacote de conteúdo de áudio é entregue em um objeto `AudioMediaBuffer`. Esse objeto contém um ponteiro para um buffer de memória alocado para heap nativo que contém o conteúdo de áudio decodificado. 

```cs
void OnAudioMediaReceived(
            object sender,
            AudioMediaReceivedEventArgs args)
{
   var buffer = args.Buffer;

   // native heap-allocated memory containing decoded content
   IntPtr rawData = buffer.Data;            
}
```

O manipulador de eventos deve retornar rapidamente. É recomendável que a fila de aplicativos a `AudioMediaBuffer` para serem processadas assincronamente. `OnAudioMediaReceived` eventos serão serializados na plataforma de mídia em tempo real (ou seja, o próximo evento não ocorrerá até que retorne atual). Uma vez um `AudioMediaBuffer` tenha sido consumido, o aplicativo deverá chamar método Dispose para que a memória não gerenciada subjacente pode ser recuperada pela plataforma de mídia do buffer. 

```cs
   // release/dispose buffer when done 
   buffer.Dispose();
```

> [!IMPORTANT]
> O bot não deve chamar o método Dispose do buffer até terminar de acessar o buffer.

## <a name="receive-video-media"></a>Receber mídia de vídeo
Receber vídeo é semelhante a receber áudio, exceto que o número de buffers por segundo dependerá do valor da taxa de quadros. `VideoMediaBuffer` tem `VideoFormat` e `OriginalVideoFormat` propriedades. `OriginalVideoFormat` representa o formato original do buffer quando ele foi originado. Só está disponível durante o recebimento de buffers de vídeo por meio de `IVideoSocket.VideoMediaReceived` manipulador de eventos. Se o buffer foi redimensionado antes de ser transmitido, a propriedade `OriginalVideoFormat` terá a Largura e Altura originais, enquanto que `VideoFormat` terá as atuais após o redimensionamento. Se as propriedades Width e Height de `OriginalVideoFormat` forem diferentes da propriedade `VideoFormat`, o consumidor do `VideoMediaBuffer` gerado no evento `VideoMediaReceived` deve redimensionar o buffer para caber no tamanho `OriginalVideoFormat`. Atualmente, apenas o formato NV12 é suportado para recebimento.

## <a name="send-audio-media"></a>Envie a mídia de áudio
Se o `AudioSocket` está configurado para enviar a mídia, o bot deve se registrar para o `AudioSendStatusChanged` manipulador de eventos no `AudioSocket` obter notificações sobre alterações de status de envio. O bot deve começar a enviar áudio somente após as alterações de status de envio para o Active Directory.

```cs
void AudioSocket_OnSendStatusChanged(
             object sender,
             AudioSendStatusChangedEventArgs args)
{
    switch (args.MediaSendStatus)
    {
    case MediaSendStatus.Active:
        // notify bot to begin sending audio 
        break;
     
    case MediaSendStatus.Inactive:
        // notify bot to stop sending audio
        break;
    }
}
```

Para enviar a mídia de áudio, supõe-se que o conteúdo de áudio de PCM está contido dentro de um buffer de alocados no heap nativo. O bot deve derivar da classe abstrata `AudioMediaBuffer`. A mídia é enviada de forma assíncrona pelo método `Send` do AudioSocket e a plataforma chamará `Dispose` no `AudioMediaBuffer` quando o envio for concluído. O bot não deve liberar (ou retornar a um alocador de pool) os recursos não gerenciados quando o `Send` retorna. Ele deve aguardar `Dispose` a ser chamado.

## <a name="send-video-media"></a>Envie a mídia de vídeo
Enviar a mídia de vídeo é semelhante da mídia de áudio. O bot deve se registrar para o evento `VideoSendStatusChanged` e esperar que `MediaSendStatus` seja `Active`. O bot não deve liberar ou recuperar os recursos não gerenciados do buffer até que o método `Dispose` seja chamado. Os formatos de cores RGB24, NV12 e Yuy2 são suportados.

Enquanto vários `VideoFormat` s são suportados para enviar vídeo, o `VideoFormat` que é atualmente preferido é comunicado ao bot através do evento `VideoSendStatusChanged`. O `VideoFormat` preferido para enviar vídeo pode mudar devido às condições de largura de banda da rede ou ao cliente peer redimensionar sua janela de vídeo.

```cs
void VideoSocket_OnSendStatusChanged(
            object sender,
            VideoSendStatusChangedEventArgs args)
{
    VideoFormat preferredVideoFormat;

    switch (args.MediaSendStatus)
    {
    case MediaSendStatus.Active:
        // notify bot to begin sending audio 
        // bot is recommended to use this format for sourcing video content.
        preferredVideoFormat = args.PreferredVideoSourceFormat;
        break;
     
    case MediaSendStatus.Inactive:
        // notify bot to stop sending audio
        break;
    }
}
```

Cada `VideoMediaBuffer` tem uma propriedade VideoFormat para indicar o formato do conteúdo de vídeo para esse buffer específico. Embora a propriedade `VideoFormat` não precise corresponder à propriedade `PreferredVideoSourceFormat` indicada no evento `VideoSendStatusChanged`, é altamente recomendável usar o `PreferredVideoSourceFormat` indicado para evitar ciclos dispendiosos de CPU gastos no redimensionamento do quadro de vídeo.

## <a name="call-notifications"></a>Notificações de chamada
### <a name="call-state-changes"></a>Alterações de estado de chamada
O bot pode receber notificações de mudança de estado de chamada inscrevendo-se no `NotificationType.CallStateChange` em `NotificationSubscriptions` de `RealTimeMediaIncomingCallEvent.RealTimeMediaWorkflow`.

```cs
private Task OnCallStateChangeNotification(CallStateChangeNotification callStateChangeNotification)
{
    if (callStateChangeNotification.CurrentState == CallState.Terminated)
    {   
        // stop sending media and dispose the media sockets
        _audioSocket.Dispose();
        _videoSocket.Dispose();
    }

    return Task.CompletedTask;
}
 ```

### <a name="roster-update"></a>Atualização de lista
Se o bot for adicionado a uma conferência, ele poderá ouvir a lista da conferência inscrevendo-se `NotificationType.RosterUpdate` em `NotificationSubscriptions` de `RealTimeMediaIncomingCallEvent.RealTimeMediaWorkflow`. O `RosterUpdateNotification` tem os detalhes de todos os participantes da conferência. O bot pode optar por aguardar uma notificação com um participante enviando vídeo e, em seguida, `Subscribe` com o vídeo do participante em um de seus `VideoSocket` objetos.

```cs
private async Task OnRosterUpdateNotification(RosterUpdateNotification rosterUpdateNotification)
{
    // Find a video source in the conference to subscribe
    foreach (RosterParticipant participant in rosterUpdateNotification.Participants)
    {
        if (participant.MediaType == ModalityType.Video
            && (participant.MediaStreamDirection == "sendonly" || participant.MediaStreamDirection == "sendrecv")
            )
        {
            var videoSubscription = new VideoSubscription
            {
                ParticipantIdentity = participant.Identity,
                OperationId = Guid.NewGuid().ToString(),
                SocketId = _videoSocket.SocketId,
                VideoModality = ModalityType.Video,
                VideoResolution = ResolutionFormat.Hd720p
            };

            await CallService.Subscribe(videoSubscription).ConfigureAwait(false);
            break;
        }
    }  
}
```

## <a name="end-the-call"></a>Encerrar a chamada

### <a name="handle-call-termination-from-the-caller"></a>Identificador de encerramento de chamada do chamador
Quando o usuário desconecta a chamada para o bot em uma conversa ponto a ponto ou quando o bot é removido da conferência, o bot recebe uma notificação de mudança de estado de chamada com o CallState como Terminada. O bot deve descartar qualquer soquete de mídia criado por ele.

### <a name="terminate-the-call-from-the-bot"></a>Encerrar a chamada do bot
O bot pode optar por encerrar a chamada chamando `EndCall` em `IRealTimeMediaCallService`.

### <a name="handle-call-clean-up-by-the-bot-framework"></a>Manipular a chamada de limpeza pela estrutura do Bot
Em condições de erro (por exemplo, se `AnswerAppHostedMediaOutcomeEvent` não for recebido dentro de um tempo razoável), o Bot Framework poderá encerrar a chamada. O bot deve registrar-se para o evento `OnCallCleanup` e descartar os soquetes de mídia.

