---
title: 'Snelstart: oproepen toevoegen aan een iOS-app met behulp van Azure Communication Services'
description: In deze snelstart leert u hoe u de Azure Communication Services-clientbibliotheek voor iOS kunt gebruiken.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c67440453e5ca8395464369d75bfac418a564764
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944799"
---
In deze snelstart leert u hoe u een oproep start met behulp van de Azure Communication Services-clientbibliotheek voor iOS.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een gebruikte Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een [Token voor gebruikerstoegang](../../access-tokens.md) voor uw Azure Communication Service.

## <a name="setting-up"></a>Instellen

### <a name="creating-the-xcode-project"></a>Het Xcode-project maken

Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View-app** (Toepassing met één weergave). In deze zelfstudie wordt gebruik gemaakt van het[SwiftUI Framework](https://developer.apple.com/xcode/swiftui/). U moet dus de **taal** instellen naar **Swift** en de **Gebruikersinterface** naar **SwiftUI**. Tijdens deze snelstart gaat u geen moduletests of UI-tests maken. Voel u vrij om **Moduletest opnemen** en ook **UI-tests opnemen**uit te schakelen.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Schermafbeelding dat het nieuw aangemaakte venster Nieuw Project in Xcode aantoont.":::

### <a name="install-the-package"></a>Het pakket installeren

Voeg de Azure Communication Services-clientbibliotheek en de bijbehorende afhankelijkheden (AzureCore.framework en AzureCommunication.framework) toe aan uw project.

> [!NOTE]
> Met de release van AzureCommunicationCalling SDK vindt u een bash-script `BuildAzurePackages.sh`. Het script wanneer u `sh ./BuildAzurePackages.sh` uitvoert, geeft u het pad naar de gegenereerde Framework-pakketten die in de volgende stap moeten worden geïmporteerd in de voorbeeld-app. Houd er rekening mee dat u, voordat u het script uitvoert, Xcode-opdrachtregel Hulpprogramma's moet instellen als u dit nog niet hebt gedaan: Start Xcode, Selecteer Voorkeuren-> Locaties. Kies uw Xcode-versie voor de opdrachtregel Hulpprogramma's.

1. Download de Azure Communication Services-clientbibliotheek voor iOS.
2. Klik in Xcode op het projectbestand en selecteer het build-doel om de projectinstellingen-editor te openen.
3. Scrol op het tabblad **Algemeen** naar de secties **Frameworks, Bibliotheken en Ingesloten inhoud** en klik op het pictogram **"+"** .
4. Kies linksonder in het dialoogvenster **Bestanden toevoegen**, navigeer naar de map **AzureCommunicationCalling.framework** van het pakket met de niet-gecomprimeerde clientbibliotheek.
    1. Herhaal de laatste stap voor het toevoegen van **AzureCore.framework** en **AzureCommunication.framework**.
5. Open het tabblad **Build-instellingen** van de projectinstellingeneditor en blader naar de secties **Zoekpaden**. Voeg een nieuwe vermelding **Zoekpaden framework** toe voor de map die het **AzureCommunicationCalling.framework** bevat.
    1. Voeg nog een vermelding Zoekpaden framework toe aan de map die de afhankelijkheden bevat.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Schermopname van het bijwerken van de Zoekpaden framework in XCode.":::

### <a name="request-access-to-the-microphone"></a>Toegang tot de microfoon aanvragen

Als u toegang wilt krijgen tot de microfoon van het apparaat, moet u de eigenschappenlijst van de app bijwerken met een `NSMicrophoneUsageDescription`. U stelt de bijbehorende waarde in op een `string` die wordt opgenomen in het dialoogvenster dat het systeem gebruikt om toegang te vragen aan de gebruiker.

Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe in de bovenste sectie `<dict>` en sla het bestand op.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand **ContentView.swift** van het project en voeg boven aan het bestand een `import`-declaratie toe om de `AzureCommunicationCalling library`te importeren. Bovendien moet u `AVFoundation` importeren, we hebben dit nodig voor het aanvragen van audio-toestemming in de code.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Vervang de implementatie van de `ContentView`-struct door enkele eenvoudige UI-besturingselementen waarmee een gebruiker een oproep kan initiëren en beëindigen. We voegen bedrijfslogica toe aan deze besturingselementen in deze snelstart.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-clientbibliotheek voor oproepen:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | De CallClient is het belangrijkste ingangspunt voor de clientbibliotheek voor oproepen.|
| ACSCallAgent | De CallAgent wordt gebruikt om oproepen te starten en te beheren. |
| CommunicationUserCredential | De CommunicationUserCredential wordt gebruikt als de tokenreferentie voor het instantiëren van de CallAgent.| 
| CommunicationIndentifier | De CommunicationIndentifier wordt gebruikt om de identiteit van de gebruiker te vertegenwoordigen. Dit kan een van de volgende zijn: CommunicationUser/PhoneNumber/CallingApplication. |

## <a name="authenticate-the-client"></a>De client verifiëren

Initialiseer een `CallAgent`-instantie met een token voor gebruikerstoegang waarmee we oproepen kunnen maken en ontvangen. Voeg de volgende code toe aan de `onAppear`-call back in **ContentView.swift**:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

U moet `<USER ACCESS TOKEN>` vervangen door een geldig token voor gebruikerstoegang voor uw resource. Raadpleeg de documentatie inzake [Token voor gebruikerstoegang](../../access-tokens.md) als u nog geen token hebt.

## <a name="start-a-call"></a>Een oproep starten

De `startCall` methode wordt ingesteld als de actie die wordt uitgevoerd wanneer op de knop *Oproep starten* wordt getikt. Werk de implementatie bij om een oproep te starten met de `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

U kunt ook de eigenschappen in `ACSStartCallOptions` gebruiken om de initiële opties voor de oproep in te stellen (dat wil zeggen dat de oproep kan worden gestart met de microfoon gedempt).

## <a name="end-a-call"></a>Een oproep beëindigen

Implementeer de `endCall`-methode om de huidige oproep te beëindigen wanneer op de knop voor *Oproep beëindigen* wordt getikt.

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>De code uitvoeren

U kunt uw app bouwen en uitvoeren op een iOS-simulator door **Product** > **Uitvoeren** te selecteren of door de sneltoets (&#8984;-R) te gebruiken.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Laatste look en feel van de snelstart-app":::

U kunt een uitgaande VOIP-oproep maken door een gebruikers-ID op te geven in het tekstveld en te tikken op de knop **Oproep starten**. Door `8:echo123` te bellen, wordt u verbonden met een echo-bot. Dit is handig om aan de slag te gaan en te controleren of uw audio-apparaten werken. 

> [!NOTE]
> De eerste keer dat u een oproep doet, wordt u gevraagd om toegang tot de microfoon. In een productietoepassing moet u de `AVAudioSession`API[ gebruiken om de machtigingsstatus](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) te controleren en het gedrag van uw toepassing bij te werken wanneer geen toestemming wordt verleend.

## <a name="sample"></a>Voorbeeld

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure/Communication/tree/master/samples/AzureCommunicationCalling/iOS/Swift).
