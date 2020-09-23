---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: fa7fd73a7d8019919a89dd9e9522b7389dc9c18f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936473"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde communicatie Services-resource. [Maak een communicatie Services-resource](../../create-communication-resource.md).
- A `User Access Token` om de aanroep-client in te scha kelen. Voor meer informatie over [het verkrijgen van een `User Access Token` ](../../access-tokens.md)
- Optioneel: Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van een oproep aan uw toepassing](../getting-started-with-calling.md)

## <a name="setting-up"></a>Instellen

### <a name="creating-the-xcode-project"></a>Het Xcode-project maken

Maak in Xcode een nieuw iOS-project en selecteer de **app-sjabloon voor één weer gave** . Deze Snelstartgids maakt gebruik van het [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/), dus u moet de **taal** instellen op **Swift** en de **gebruikers interface** op **SwiftUI**. U gaat tijdens deze Quick Start geen eenheids tests of UI-tests maken. Schakel het selectie vakje **include-tests toevoegen** uit en schakel ook **ui-tests**uit.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Scherm afbeelding met het venster Nieuw nieuw project maken in Xcode.":::

### <a name="install-the-package"></a>Het pakket installeren

Voeg de Azure Communication Services-client bibliotheek en de bijbehorende afhankelijkheden (Azurebinnen. Framework en AzureCommunication. Framework) toe aan uw project.

> [!NOTE]
> Met de release van AzureCommunicationCalling SDK kunt u een bash-script vinden `BuildAzurePackages.sh` . Bij het uitvoeren van het script krijgt `sh ./BuildAzurePackages.sh` u het pad naar de gegenereerde Framework pakketten die in de volgende stap moeten worden geïmporteerd in de voor beeld-app. Houd er rekening mee dat u Xcode-opdracht regel Programma's moet instellen als u dit nog niet hebt gedaan voordat u het script uitvoert: start Xcode, Selecteer voor keuren-> locaties. Kies uw Xcode-versie voor de opdracht regel Programma's.

1. Down load de Azure Communication Services-client bibliotheek voor iOS.
2. Klik in Xcode op het project bestand en selecteer het build-doel om de project instellingen-editor te openen.
3. Schuif op het tabblad **Algemeen** naar de sectie **frameworks, Bibliotheken en Inge sloten inhoud** en klik op het pictogram **' + '** .
4. Kies in de linkerbenedenhoek van het dialoog venster **bestanden toevoegen**, ga naar de map **AzureCommunicationCalling. Framework** van het niet-gezipte client bibliotheek pakket.
    1. Herhaal de laatste stap voor het toevoegen van **azurebinnen. Framework** en **AzureCommunication. Framework**.
5. Open het tabblad **Build Settings** van de editor project instellingen en schuif naar het gedeelte **Zoek paden** . Voeg een nieuwe vermelding voor het **zoeken naar Framework-paden** toe voor de map met **AzureCommunicationCalling. Framework**.
    1. Voeg nog een framework-Zoek paden vermelding toe aan de map die de afhankelijkheden bevat.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Scherm opname van het bijwerken van de Framework-Zoek paden in XCode.":::

### <a name="request-access-to-the-microphone"></a>Toegang tot de microfoon aanvragen

Als u toegang wilt krijgen tot de microfoon van het apparaat, moet u de eigenschappen lijst van de app bijwerken met een `NSMicrophoneUsageDescription` . U stelt de gekoppelde waarde in op een `string` die wordt opgenomen in het dialoog venster dat door het systeem wordt gebruikt om aanvragen voor toegang van de gebruiker aan te vragen.

Klik met de rechter muisknop op de `Info.plist` vermelding van de project structuur en selecteer **openen als**  >  **bron code**. Voeg de volgende regels toe aan de `<dict>` sectie op het hoogste niveau en sla het bestand op.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand **ContentView. Swift** van het project en voeg `import` boven aan het bestand een declaratie toe om de te importeren `AzureCommunicationCalling library` . Daarnaast `AVFoundation` moet u dit voor de code van de audio-machtiging in het programma importeren.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services die de client bibliotheek voor iOS aanroepen.


| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | De ACSCallClient is het belangrijkste ingangs punt voor de aanroepende client bibliotheek.|
| ACSCallAgent | De ACSCallAgent wordt gebruikt om aanroepen te starten en te beheren. |
| CommunicationUserCredential | De CommunicationUserCredential wordt gebruikt als de token referentie voor het instantiëren van de CallAgent.| 
| CommunicationIndentifier | De CommunicationIndentifier wordt gebruikt om de identiteit van de gebruiker te vertegenwoordigen. Dit kan een van de volgende zijn: CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Bij het implementeren van gebeurtenis gemachtigden moet de toepassing een sterke verwijzing bevatten naar de objecten waarvoor gebeurtenis abonnementen zijn vereist. Als er bijvoorbeeld een `ACSRemoteParticipant` object wordt geretourneerd bij het aanroepen van de- `call.addParticipant` methode en de toepassing de gemachtigde instelt op luistert `ACSRemoteParticipantDelegate` , moet de toepassing een sterke verwijzing naar het `ACSRemoteParticipant` object bevatten. Als dit object wordt verzameld, wordt door de gemachtigde een fatale uitzonde ring gegenereerd wanneer de aanroepende SDK het object probeert aan te roepen.

## <a name="initialize-the-acscallagent"></a>De ACSCallAgent initialiseren

Als u een `ACSCallAgent` exemplaar van wilt maken `ACSCallClient` , moet u `callClient.createCallAgent` een methode gebruiken die een `ACSCallAgent` object asynchroon retourneert nadat het is geïnitialiseerd

Als u een aanroep-client wilt maken, moet u een object door geven `CommunicationUserCredential` .

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Geef het CommunicationUserCredential-object dat hierboven is gemaakt door voor ACSCallClient

```swift

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error != nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Een uitgaande oproep plaatsen

Als u een gesprek wilt maken en starten, moet u een van de Api's aanroepen `ACSCallAgent` en de communicatie Services-identiteit opgeven van een gebruiker die u hebt ingericht met de client bibliotheek voor communicatie Services-beheer.

Aanroepen maken en starten is synchroon. U ontvangt een gespreks exemplaar waarmee u zich kunt abonneren op alle gebeurtenissen in de aanroep.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Plaats een 1:1-aanroep naar een gebruiker of een 1: n-gesprek met gebruikers en het PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen
Als u de oproep naar het PSTN wilt plaatsen, moet u het telefoon nummer opgeven dat u hebt verkregen met communicatie Services
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Plaats een 1:1-aanroep met met video
Meer informatie over een instantie van Apparaatbeheer vindt u [hier](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Aan een groeps aanroep toevoegen
Als u wilt samen voegen met een gesprek, moet u een van de Api's aanroepen op *CallAgent*

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

```

## <a name="push-notification"></a>Pushmelding

Mobiele push meldingen is de pop-upmelding die u op het mobiele apparaat ontvangt. Voor aanroepen worden we geadviseerd op VoIP (Voice-over Internet Protocol) Push meldingen. We bieden u de mogelijkheid om u te registreren voor push meldingen, om Push meldingen te verwerken en om de registratie van push meldingen ongedaan te maken.

### <a name="prerequisite"></a>Vereiste

- Stap 1: Xcode->-ondertekening & mogelijkheden: > invoeg toepassingen toevoegen-> "push meldingen"
- Stap 2: Xcode & mogelijkheden voor het ondertekenen van >-> mogelijkheden toevoegen-> "achtergrond modi"
- Stap 3: "achtergrond modi"-> Selecteer "Voice over IP" en "externe meldingen"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Scherm afbeelding die laat zien hoe u mogelijkheden toevoegt in Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registreren voor push meldingen

Als u zich wilt registreren voor push meldingen, roept u registerPushNotification () aan op een *CallAgent* -exemplaar met een apparaat registratie token.

Registreren voor push melding moet worden aangeroepen na een geslaagde initialisatie. Wanneer het `callAgent` object wordt vernietigd, `logout` wordt aangeroepen, waarmee push meldingen automatisch worden verwijderd.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Afhandeling van push meldingen
Als u push meldingen voor binnenkomende oproepen wilt ontvangen, roept u *handlePushNotification ()* aan op een *CallAgent* -exemplaar met een woorden lijst-nettolading.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Registratie van push melding opheffen

Toepassingen kunnen de registratie van push meldingen op elk gewenst moment ongedaan maken. Roep de `unRegisterPushNotification` methode aan op *CallAgent*.
> [!NOTE]
> De registratie van toepassingen bij een push melding bij afmelden wordt niet automatisch ongedaan gemaakt.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Mid-Call-bewerkingen

U kunt verschillende bewerkingen uitvoeren tijdens een aanroep om instellingen te beheren die betrekking hebben op video en audio.

### <a name="mute-and-unmute"></a>Dempen en dempen opheffen

U kunt de `mute` en asynchrone api's gebruiken om het lokale eind punt te dempen of te dempen `unmute` :

```swift
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Asynchrone Lokaal uitschakelen

```swift
call.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Verzenden van lokale video starten en stoppen

Als u wilt beginnen met het verzenden van lokale video naar andere deel nemers in de oproep, gebruikt u de `startVideo` API en Pass Through `localVideoStream``camera`

```swift

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Zodra u begint met het verzenden van de video, `ACSLocalVideoStream` wordt de `localVideoStreams` verzameling toegevoegd aan een instantie van een aanroep:

```swift

call.localVideoStreams[0]

```

Asynchrone Als u lokale video wilt stoppen, geeft u het `localVideoStream` resultaat van het aanroepen van `call.startVideo` :

```swift

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Beheer van externe deel nemers

Alle externe deel nemers worden vertegenwoordigd door het `ACSRemoteParticipant` type en zijn beschikbaar via de `remoteParticipants` verzameling op een instantie van een aanroep:

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Eigenschappen van externe deel nemer

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `addParticipant` . Hiermee wordt synchroon een exemplaar van een externe deel nemer geretourneerd.

```swift

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Een deel nemer uit een gesprek verwijderen
Als u een deel nemer wilt verwijderen uit een gesprek (een gebruiker of een telefoon nummer), kunt u de API aanroepen  `removeParticipant` . Dit wordt asynchroon opgelost.

```swift

call!.remove(remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Video stromen voor externe deel nemers weer geven

Externe deel nemers kunnen tijdens een aanroep video of scherm delen starten.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Externe deel nemers video/scherm delen streams afhandelen

Als u de streams van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Eigenschappen van externe video-stream

```swift

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Stroom van externe deel nemers weer geven

Rendering van externe deel nemers streams starten:

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Methoden en eigenschappen van externe video weergave

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
```

## <a name="device-management"></a>Apparaatbeheer

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep voor het verzenden van audio/video-streams. U kunt hiermee ook machtigingen van een gebruiker aanvragen voor toegang tot de microfoon/camera. U kunt toegang krijgen tot `deviceManager` het `callClient` object:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Lokale apparaten opsommen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken op de Apparaatbeheer. Opsomming is een synchrone actie.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Standaard microfoon/-spreker instellen

Met Apparaatbeheer kunt u een standaard apparaat instellen dat wordt gebruikt bij het starten van een aanroep. Als de stack-standaard waarden niet zijn ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>Voor beeld van lokale camera

U kunt gebruiken `ACSRenderer` om te beginnen met het renderen van een stream van uw lokale camera. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>Eigenschappen van voor beeld van lokale camera

De renderer heeft eigenschappen en methoden die u in staat stellen om de rendering te beheren:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Gebeurtenis model

U kunt zich abonneren op de meeste eigenschappen en verzamelingen om te worden gewaarschuwd wanneer waarden worden gewijzigd.

### <a name="properties"></a>Eigenschappen
Abonneren op `property changed` gebeurtenissen:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Verzamelingen
Abonneren op `collection updated` gebeurtenissen:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
