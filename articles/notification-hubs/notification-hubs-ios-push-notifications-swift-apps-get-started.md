---
title: Pushmeldingen verzenden naar Swift iOS-apps die Azure Notification Hubs gebruiken | Microsoft Documenten
description: Meer informatie over het pushen van meldingen naar Swift iOS-apps die Azure Notification Hubs gebruiken.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336642"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Zelfstudie: Pushmeldingen verzenden naar Swift iOS-apps met de Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In deze zelfstudie gebruikt u Azure Notification Hubs om meldingen naar een Swift-gebaseerde iOS-toepassing te pushen met behulp van de [REST API.](/rest/api/notificationhubs/) U maakt ook een lege iOS-app die pushmeldingen ontvangt met behulp van de [Apple Push Notification-service (APNs).](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

Deze zelfstudie neemt je mee door de volgende stappen:

> [!div class="checklist"]
> * Het certificaatondertekeningsaanvraagbestand genereren.
> * Vraag uw app aan voor pushmeldingen.
> * Maak een inrichtingsprofiel voor de app.
> * Een Notification Hub maken.
> * Configureer de meldingshub met APNs-informatie.
> * Verbind je iOS-app met een meldingshub.
> * Test de oplossing.

De volledige code voor deze tutorial is te vinden [op GitHub.](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest)

## <a name="prerequisites"></a>Vereisten

Om mee te gaan, heb je het volgende nodig:

- Ga door [het azure notification hubs-overzicht](notification-hubs-push-notification-overview.md) als u niet bekend bent met de service.
- Voor meer informatie over [registraties en installatie](notification-hubs-push-notification-registration-management.md).
- Een actief [Apple Developer-account](https://developer.apple.com).
- Een Mac met Xcode en een geldig ontwikkelaarscertificaat dat in uw sleutelhanger is geïnstalleerd.
- Een fysiek iPhone-apparaat waarmee je draaien en debuggen, omdat je pushmeldingen niet testen met de simulator.
- Uw fysieke iPhone-apparaat is geregistreerd in de [Apple Portal](https://developer.apple.com) en gekoppeld aan uw certificaat.
- Een [Azure-abonnement](https://portal.azure.com) waar u resources maken en beheren.

Zelfs als je geen eerdere ervaring hebt met iOS-ontwikkeling, moet je in staat zijn om de stappen te volgen voor het maken van dit voorbeeld van de eerste principes. U profiteert echter van de vertrouwdheid met de volgende concepten:

- IOS-apps bouwen met Xcode en Swift.
- Een [Azure Notification-hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) voor iOS configureren.
- De [Apple Developer Portal](https://developer.apple.com) en de Azure [portal](https://portal.azure.com).

> [!NOTE]
> De meldingshub wordt geconfigureerd om alleen de **Sandbox-verificatiemodus** te gebruiken. U moet deze verificatiemodus niet gebruiken voor productieworkloads.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Uw iOS-app verbinden met een meldingshub

In deze sectie bouwt u de iOS-app die verbinding maakt met de meldingshub.  

### <a name="create-an-ios-project"></a>Een iOS-project maken

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View Application** (Toepassing met één weergave).

1. Bij het instellen van de opties voor het nieuwe project:

   1. Geef de **productnaam** (PushDemo)`com.<organization>`en **organisatie-id** op die u hebt gebruikt bij het instellen **van bundel-id** in de Apple Developer Portal.

   1. Kies het **team waarvoor** de **app-id** is ingesteld.

   1. Stel de **taal** in op **Swift.**

   1. Selecteer **Volgende**.

1. Maak een nieuwe map genaamd **SupportingFiles**.

1. Maak een nieuw p-lijstbestand genaamd **devsettings.plist** in de map **SupportingFiles.** Zorg ervoor dat u deze map toevoegt aan uw **gitignore-bestand,** zodat deze niet is vastgelegd bij het werken met een git repo. In een productie-app stelt u deze geheimen waarschijnlijk voorwaardelijk in als onderdeel van een geautomatiseerd buildproces. Dergelijke instellingen worden niet behandeld in deze walkthrough.

1. Werk **devsettings.plist** bij om de volgende configuratievermeldingen op te nemen met behulp van uw eigen waarden van de meldingshub die u hebt ingericht:

   | Sleutel                            | Type                     | Waarde                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Tekenreeks                   | \<hubKey>                  |
   | notificationHubKeyName         | Tekenreeks                   | \<hubKeyName->              |
   | notificationHubName            | Tekenreeks                   | \<hubName>                 |
   | notificationHubNamespace       | Tekenreeks                   | \<hubNamespace>            |

   U de vereiste waarden vinden door te navigeren naar de hubbron van de meldingshub in de Azure-portal. De **notificatieHubName-** en **notificationHubNamespace-waarden** bevinden zich met name in de rechterbovenhoek van het **Essentials-overzicht** op de pagina **Overzicht.**

   ![Overzicht van Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   U ook de **waarden notificationHubKeyName** en **notificationHubKey** vinden door naar **Toegangsbeleid** te navigeren en het desbetreffende **toegangsbeleid**te selecteren, zoals `DefaultFullSharedAccessSignature`. Kopieer daarna de waarde die vooraf is `SharedAccessKeyName=` vastgesteld `notificationHubKeyName` met de voor- en de waarde die `SharedAccessKey=` vooraf is vastgesteld voor de `notificationHubKey`tekenreeks Primaire **verbindingstekenreeks.**

   De verbindingstekenreeks moet zich in de volgende indeling begeven:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Geef het token `DefaultFullSharedAccessSignature` aan om het simpel te houden, zodat u het token gebruiken om meldingen te verzenden. In de `DefaultListenSharedAccessSignature` praktijk zou het een betere keuze zijn voor situaties waarin u alleen meldingen wilt ontvangen.

1. Selecteer **onder Projectnavigator**de **projectnaam** en selecteer vervolgens het tabblad **Algemeen.**

1. Zoek **Identiteit** en stel vervolgens de waarde `com.<organization>.PushDemo`van de **bundel-id** in, zodat deze overeenkomt met de waarde die wordt gebruikt voor de **App-id** van een vorige stap.

1. Zoek **mogelijkheden & voor tekenen**en selecteer vervolgens het juiste **team** voor uw **Apple Developer-account**. De **teamwaarde** moet overeenkomen met de waarde waaronder u uw certificaten en profielen hebt gemaakt.

1. Xcode moet automatisch de juiste **waarde voor inrichtingsprofiel** optrekken op basis van de **bundel-id**. Als u de nieuwe waarde **van het inrichtingsprofiel** niet ziet, probeert u de profielen voor de **ondertekeningsidentiteit** te vernieuwen door**het Account** **Xcode-voorkeuren** > **Preferences** > te selecteren en vervolgens de knop **Handmatige profielen downloaden** om de profielen te downloaden.

1. Klik nog steeds op het tabblad **Ondertekenen & mogelijkheden** op de knop + **mogelijkheden** en dubbeltik op **Pushmeldingen** uit de lijst om ervoor te zorgen dat **pushmeldingen** zijn ingeschakeld.

1. Open het bestand **AppDelegate.swift** om het **UNUserNotificationCenterDelegate-protocol** te implementeren en de volgende code aan de bovenkant van de klasse toe te voegen:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Je gebruikt deze leden later. U gebruikt het **tagslid** specifiek als onderdeel van de registratie met behulp van een **aangepaste sjabloon.** Zie [Tags voor registraties](notification-hubs-tags-segment-push-message.md) en [sjabloonregistraties](notification-hubs-templates-cross-platform-push-messages.md)voor meer informatie over tags.

1. Voeg in hetzelfde bestand de volgende code toe aan de functie **didFinishLaunchingWithOptions:**

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Met deze code worden de instellingswaarden opgehaald van **devsettings.plist,** wordt de klasse **AppDelegate** ingesteld als de delegate **UNUserNotificationCenter,** wordt autorisatie aangevraagd voor pushmeldingen en wordt **registerForRemoteNotifications aangeroepen.**

    Om het simpel te houden, ondersteunt de code *alleen iOS 10 en hoger.* U ondersteuning voor eerdere OS-versies toevoegen door voorwaardelijk gebruik te maken van de respectievelijke API's en benaderingen zoals u normaal gesproken zou doen.

1. Voeg in hetzelfde bestand de volgende functies toe:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    De code maakt gebruik van de **installatieId** en **pushChannel** waarden te registreren met de melding hub. In dit geval gebruikt u **UIDevice.current.identifierForVendor** om een unieke waarde te bieden om het apparaat te identificeren en vervolgens het **apparaatToken** op te maken om de gewenste **pushChannel-waarde** te bieden. De functie **showAlert** bestaat alleen om bepaalde berichttekst weer te geven voor demonstratiedoeleinden.

1. Voeg in het bestand **AppDelegate.swift** de functies **willPresent** en **didReceive** toe aan **UNUserNotificationCenterDelegate.** Met deze functies wordt een waarschuwing weergegeven wanneer ze een melding krijgen dat een app wordt uitgevoerd op respectievelijk de voorgrond of de achtergrond.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Voeg afdrukinstructies toe aan de onderkant van de **functie didRegisterForRemoteNotificationsWithDeviceToken** om te controleren of **de installatieId** en **pushChannel** waarden worden toegewezen.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Maak de mappen **Modellen,** **Services**en **Hulpprogramma's** voor de basiscomponenten die u later aan het project toevoegt.

1. Controleer of het project wordt gebouwd en uitgevoerd op een fysiek apparaat. Pushmeldingen kunnen niet worden getest met behulp van de simulator.

### <a name="create-models"></a>Modellen maken

In deze stap maakt u een reeks modellen om de [API-payloads van Notification Hubs TE](/rest/api/notificationhubs/) vertegenwoordigen en om de vereiste SAS-tokengegevens (Shared Access Signature) op te slaan.

1. Voeg een nieuw Swift-bestand genaamd **PushTemplate.swift** toe aan de map **Modellen.** Dit model biedt een struct die het **lichaam** van een individuele sjabloon vertegenwoordigt als onderdeel van het laadvermogen **deviceInstallation.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Voeg een nieuw Swift-bestand met de naam **DeviceInstallation.swift** toe aan de map **Modellen.** Dit bestand definieert een struct die de payload vertegenwoordigt voor het maken of bijwerken van een **apparaatinstallatie.** Voeg de volgende code toe aan het bestand:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Voeg een nieuw Swift-bestand genaamd **TokenData.swift** toe aan de map **Modellen.** Dit model wordt gebruikt om een SAS-token op te slaan, samen met de vervaldatum ervan.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Een SAS-token genereren

Meldingshubs maken gebruik van dezelfde beveiligingsinfrastructuur als Azure Service Bus. Als u de REST-API wilt aanroepen, moet u [programmatisch een SAS-token genereren](/rest/api/eventhub/generate-sas-token) dat kan worden gebruikt in de **autorisatiekop** van de aanvraag.  

Het resulterende token bevindt zich in de volgende indeling:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Het proces zelf omvat dezelfde zes belangrijke stappen:  

1. Het berekenen van het verstrijken in [UNIX Epoch-tijdformaat,](https://en.wikipedia.org/wiki/Unix_time) wat betekent dat het aantal seconden is verstreken sinds middernacht Gecoördineerde universele tijd, 1 januari 1970.
1. De **ResourceUrl opmaken** die de bron vertegenwoordigt die u probeert te openen, zodat deze is gecodeerd met een percentage en kleine letters. De **ResourceUrl** heeft `'https://<namespace>.servicebus.windows.net/<hubName>'`het formulier .
1. Het voorbereiden van de **StringToSign** `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`, die is opgemaakt als .
1. Computergegevens en Base64-codering encoderen de **handtekening** met behulp van de HMAC-SHA256-hash van de **StringToSign-waarde.** De hashwaarde wordt gebruikt met het **sleutelgedeelte** van de **verbindingstekenreeks** voor de desbetreffende **autorisatieregel**.
1. De Base64 gecodeerde **handtekening opmaken** zodat deze is gecodeerd.
1. Het token in de verwachte indeling construeren met de waarden **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**en **UrlEncodedResourceUrl.**

Raadpleeg de [Azure Service Bus-documentatie](../service-bus-messaging/service-bus-sas.md) voor een grondiger overzicht van de handtekening van gedeelde toegang en hoe Azure Service Bus- en Meldingshubs deze gebruiken.

Voor de toepassing van dit Swift-voorbeeld gaat u de open-source **CommonCrypto-bibliotheek** van Apple gebruiken om te helpen bij het hashing van de handtekening. Omdat het een C-bibliotheek is, is het niet out of the box toegankelijk in Swift. U de bibliotheek beschikbaar maken met behulp van een overbruggingskop.

Ga als u de overbruggingskop toevoegen en configureren:

1. Selecteer in Xcode **Bestand** > **nieuw** > **bestandskopbestand****File** > bestand . Geef het kopbestand **BridgingHeader.h**een naam.

1. Bewerk het bestand om **CommonHMAC.h**te importeren:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Werk de **build-instellingen** van het doel bij om naar de overbruggingskop te verwijzen:

   1. Tik op het **PushDemo-project** en blader omlaag naar de sectie **Swift Compiler.**

   1. Controleer of de optie **Compatibiliteitskop van doelstelling-C installeren** is ingesteld op **Ja**.

   1. Voer het `'<ProjectName>/BridgingHeader.h'` bestandspad in in de optie **Doelstelling-C-overbruggingsheader.** Dit is het bestandspad naar onze overbruggingsheader.

   Als u deze opties niet vinden, moet u ervoor zorgen dat de weergave **Alles** is geselecteerd in plaats **van Basic** of **Aangepast.**

   Er zijn veel open-source wrapper-bibliotheken van derden beschikbaar die het gebruik van **CommonCrypto** mogelijk een beetje eenvoudiger maken. Echter, de bespreking van dergelijke bibliotheken is buiten het bereik van dit artikel.

1. Voeg een nieuw Swift-bestand met de naam **TokenUtility.swift** toe in de map **Hulpprogramma's** en voeg de volgende code toe:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Dit hulpprogramma bevat de logica die verantwoordelijk is voor het genereren van het SAS-token.

   Zoals eerder beschreven, orkestreert de functie **getSasToken** de stappen op hoog niveau die nodig zijn om het token voor te bereiden. De functie wordt later in deze zelfstudie door de installatieservice aangeroepen.

   De andere twee functies worden aangeroepen door de **getSasToken-functie:** **sha256HMac** voor het berekenen van de handtekening en **urlEncodedString** voor het coderen van de bijbehorende URL-tekenreeks. De **urlEncodedString-functie** is vereist omdat het niet mogelijk is om de vereiste uitvoer te bereiken met behulp van de ingebouwde **addpercentencoding-functie.**

   De [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) is een uitstekend voorbeeld van hoe deze bewerkingen in Objective-C te benaderen. Meer informatie over SAS-tokens voor Azure Service Bus is te vinden in de [Azure Service Bus-documentatie.](../service-bus-messaging/service-bus-sas.md)

1. Voeg in **AppDelegate.swift**de volgende code toe aan de functie *didRegisterForRemoteNotificationsWithDeviceToken* om te controleren of de **TokenUtility.getSasToken** een geldig token genereert
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Zorg ervoor dat u de tijdelijke aanduidingswaarden in de **baseAddress-tekenreeks** vervangt door uw eigen

### <a name="verify-the-sas-token"></a>Het SAS-token verifiëren

Controleer voordat u de installatieservice in de client implementeert, of onze app het SAS-token correct genereert met behulp van uw HTTP-hulpprogramma naar keuze. Voor de toepassing van deze tutorial, onze tool van keuze zal **postbode**zijn.

Noteer de **installatieId-** en **tokenwaarden** die door de app worden gegenereerd.

Volg de volgende stappen om de **API voor installaties** aan te roepen:

1. Open **in Postman**een nieuw tabblad.

1. Stel het verzoek in **op GET** en geef het volgende adres op:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configureer de aanvraagkoppen als volgt:

   | Sleutel           | Waarde            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorisatie | \<sasToken->       |
   | x-ms-versie  | 2015-01          |

1. Selecteer de knop **Code** die rechtsboven wordt weergegeven onder de knop **Opslaan.** De aanvraag moet op het volgende voorbeeld lijken:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecteer de knop **Verzenden**.

Er bestaat op dit moment geen registratie voor de opgegeven **installatieId.** De verificatie moet resulteren in een "404 Niet gevonden" reactie in plaats van een "401 Onbevoegd" antwoord. Dit resultaat moet bevestigen dat het SAS-token is geaccepteerd.

### <a name="implement-the-installation-service-class"></a>De klasse installatieservice implementeren

Vervolgens implementeerje onze basiswrapper rond de [Installations REST API.](/rest/api/notificationhubs/create-overwrite-installation)  

Voeg een nieuw Swift-bestand met de naam **NotificationRegistrationService.swift** toe onder de map **Services** en voeg vervolgens de volgende code toe aan dit bestand:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

De vereiste details worden verstrekt als onderdeel van de initialisatie. Tags en sjablonen worden optioneel doorgegeven aan de **registerfunctie** om deel uit te maken van de **JSON-payload apparaatinstallatie.**  

De **registerfunctie** roept de andere privéfuncties aan om de aanvraag voor te bereiden. Nadat een reactie is ontvangen, wordt de voltooiing aangeroepen en wordt aangegeven of de registratie is geslaagd.  

Het aanvraageindpunt wordt geconstrueerd door de functie **getBaseAddress.** De constructie maakt gebruik van de *naamruimte* en *naam* van de meldingshub die tijdens de initialisatie zijn verstrekt.  

De **functie getSasToken** controleert of het momenteel opgeslagen token geldig is. Als het token niet geldig is, roept de functie **TokenUtility** aan om een nieuw token te genereren en slaat het vervolgens op voordat een waarde wordt teruggegeven.

Ten slotte zet **encodeToJson** de betreffende modelobjecten om in JSON voor gebruik als onderdeel van de aanvraaginstantie.

### <a name="invoke-the-notification-hubs-rest-api"></a>De API voor restvan meldingen hubs aanroepen

De laatste stap is het bijwerken van **AppDelegate** om **NotificationRegistrationService** te gebruiken om zich te registreren bij onze **NotificationHub.**

1. Open **AppDelegate.swift** en voeg klassevariabelen toe om een verwijzing op te slaan naar de **NoficiationRegistrationService** en de generieke **PushTemplate:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Werk in hetzelfde bestand de functie **didRegisterForRemoteNotificationsWithDeviceToken** bij om de **NotificationRegistrationService** te initialiseren met de vereiste parameters en vervolgens de **registerfunctie** aan te roepen.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Om het simpel te houden, gaat u een paar afdrukinstructies gebruiken om het uitvoervenster bij te werken met het resultaat van de **registerbewerking.**

1. Bouw en voer de app nu uit op een fysiek apparaat. U ziet 'Geregistreerd' in het uitvoervenster.

## <a name="test-the-solution"></a>Test de oplossing

Onze app is in dit stadium geregistreerd bij **NotificationHub** en kan pushmeldingen ontvangen. Stop in Xcode de foutopsporing en sluit de app als deze momenteel wordt uitgevoerd. Controleer vervolgens of de gegevens **van apparaatinstallatie** zijn zoals verwacht en of onze app nu pushmeldingen kan ontvangen.  

### <a name="verify-the-device-installation"></a>De installatie van het apparaat controleren

U nu hetzelfde verzoek indienen als u eerder deed door **Postman te** gebruiken voor [het verifiëren van het SAS-token.](#verify-the-sas-token) Ervan uitgaande dat het SAS-token niet is verlopen, moet het antwoord nu de installatiegegevens bevatten die u hebt opgegeven, zoals de sjablonen en tags.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Als uw vorige **SAS-token** is verlopen, u een **breekpunt** toevoegen aan **lijn 24** van de **klasse TokenUtility** om een nieuw **SAS-token** te krijgen en de **autorisatiekop** met die nieuwe waarde bij te werken.

### <a name="send-a-test-notification-azure-portal"></a>Een testmelding verzenden (Azure-portal)

De snelste manier om te testen dat u nu meldingen ontvangen, is door naar de meldingshub in de Azure-portal te bladeren:

1. Blader in de Azure-portal naar het tabblad **Overzicht** op uw meldingshub.

1. Selecteer **Testverzenden**, dat zich boven de samenvatting **Van De Essentiële zaken** bevindt in de linkerbovenhoek van het portalvenster:

    ![Knop overzichtstesttest van Meldingenhubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Kies **Aangepaste sjabloon** in de lijst **Platforms.**

1. Voer **12345** in voor de **expressie Verzenden naar tag**. U had deze tag eerder in onze installatie opgegeven.

1. Bewerk eventueel het **bericht** in de JSON-payload:

    ![Testverzenden van meldingshubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecteer **Verzenden**. De portal moet aangeven of de melding naar het apparaat is verzonden:

    ![Testresultaten van meldingenhubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Ervan uitgaande dat de app niet op de voorgrond wordt uitgevoerd, ziet u ook een melding in het **meldingscentrum** op uw apparaat. Als u op de melding tikt, moet de app worden geopend en moet de waarschuwing worden weergegeven.

    ![Voorbeeld van ontvangen melding](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Een testmelding verzenden (e-mailprovider)

U meldingen verzenden via de [REST API](/rest/api/notificationhubs/) met behulp van **Postman**, wat een handigere manier kan zijn om te testen.

1. Open een nieuw tabblad in **Postbode**.

1. Stel het verzoek in **op POST**en voer het volgende adres in:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configureer de aanvraagkoppen als volgt:

   | Sleutel                            | Waarde                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | toepassing/json;charset=utf-8 |
   | Autorisatie                  | \<sasToken->                     |
   | ServiceBusNotification-indeling  | sjabloon                       |
   | Tags                           | "12345"                        |

1. Configureer de aanvraag **BODY** om **RAW - JSON (application.json)** te gebruiken met de volgende JSON-payload:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecteer de knop **Code,** die zich onder de knop **Opslaan** rechtsboven in het venster bevindt. De aanvraag moet op het volgende voorbeeld lijken:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Selecteer de knop **Verzenden**.

U moet een **201 Gemaakte** successtatuscode krijgen en de melding ontvangen op het clientapparaat..

## <a name="next-steps"></a>Volgende stappen
Je hebt nu een basis iOS Swift-app die via de REST API is verbonden met een [meldingshub](/rest/api/notificationhubs/) en meldingen verzenden en ontvangen. Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API's](/rest/api/notificationhubs/)
- [Notification Hubs SDK voor back-endbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK op GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registreren met toepassing back-end](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Werken met tags](notification-hubs-tags-segment-push-message.md) 
- [Werken met aangepaste sjablonen](notification-hubs-templates-cross-platform-push-messages.md)
- [Toegangsbeheer servicebus met handtekeningen voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md)
- [Programmatisch SAS-tokens genereren](/rest/api/eventhub/generate-sas-token)
- [Apple-beveiliging: gemeenschappelijke crypto](https://developer.apple.com/security/)
- [UNIX-tijd voor unix-tijdperk](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
