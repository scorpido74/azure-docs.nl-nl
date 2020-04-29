---
title: Push meldingen verzenden naar SWIFT iOS-apps die Azure Notification Hubs gebruiken | Microsoft Docs
description: Meer informatie over het pushen van meldingen naar SWIFT iOS-apps die gebruikmaken van Azure Notification Hubs.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336642"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Zelf studie: Push meldingen verzenden naar SWIFT iOS-apps met behulp van Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In deze zelf studie gebruikt u Azure Notification Hubs om meldingen te pushen naar een op SWIFT gebaseerde iOS-toepassing met behulp van de [rest API](/rest/api/notificationhubs/). U maakt ook een lege iOS-app die push meldingen ontvangt via de [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

In deze zelf studie gaat u aan de hand van de volgende stappen:

> [!div class="checklist"]
> * Genereer het aanvraag bestand voor certificaat ondertekening.
> * Vraag uw app voor push meldingen.
> * Maak een inrichtings profiel voor de app.
> * Een Notification Hub maken.
> * De notification hub configureren met APNs-gegevens.
> * Verbind uw iOS-app met een notification hub.
> * Test de oplossing.

De volledige code voor deze zelf studie vindt u [op github](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Vereisten

Als u wilt volgen, hebt u het volgende nodig:

- Als u niet bekend bent met de service, kunt u het [overzicht van Azure notification hubs](notification-hubs-push-notification-overview.md) door lopen.
- Voor meer informatie over [registraties en installatie](notification-hubs-push-notification-registration-management.md).
- Een actief [Apple-ontwikkelaars account](https://developer.apple.com).
- Een Mac met Xcode samen met een geldig ontwikkelaars certificaat dat is geïnstalleerd in uw sleutel hanger.
- Een fysiek iPhone-apparaat dat u kunt uitvoeren en fouten opsporen met, omdat u push meldingen niet kunt testen met de Simulator.
- Uw fysieke iPhone-apparaat is geregistreerd in de [Apple-Portal](https://developer.apple.com) en is gekoppeld aan uw certificaat.
- Een [Azure-abonnement](https://portal.azure.com) waar u resources kunt maken en beheren.

Zelfs als u geen ervaring hebt met iOS-ontwikkeling, kunt u de stappen volgen om dit voor beeld van de eerste principes te maken. U profiteert echter van vertrouwd met de volgende concepten:

- IOS-apps bouwen met Xcode en Swift.
- Een [Azure notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) configureren voor IOS.
- De [Apple-ontwikkelaars Portal](https://developer.apple.com) en de [Azure Portal](https://portal.azure.com).

> [!NOTE]
> De notification hub wordt geconfigureerd voor gebruik van de **sandbox** -verificatie modus. Gebruik deze verificatie modus niet voor productie werkbelastingen.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Uw iOS-app verbinden met een notification hub

In deze sectie maakt u de iOS-app die verbinding maakt met de notification hub.  

### <a name="create-an-ios-project"></a>Een iOS-project maken

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View Application** (Toepassing met één weergave).

1. Bij het instellen van de opties voor het nieuwe project:

   1. Geef de **product naam** (PushDemo) en de **organisatie-id** (`com.<organization>`) op die u hebt gebruikt bij het instellen van de **bundel-id** in de Apple Developer-portal.

   1. Kies het **team** waarvoor de **App-ID** is ingesteld.

   1. Stel de **taal** in op **Swift**.

   1. Selecteer **Next**.

1. Maak een nieuwe map met de naam **SupportingFiles**.

1. Maak een nieuw p-lijst bestand met de naam **devsettings. plist** in de map **SupportingFiles** . Zorg ervoor dat u deze map toevoegt aan uw **gitignore** -bestand, zodat deze niet wordt doorgevoerd wanneer u met een Git-opslag plaats werkt. In een productie-app zou u deze geheimen waarschijnlijk voorwaardelijk instellen als onderdeel van een geautomatiseerd bouw proces. Deze instellingen zijn niet in dit overzicht opgenomen.

1. Werk **devsettings. plist** bij om de volgende configuratie vermeldingen op te nemen met uw eigen waarden van de notification hub die u hebt ingericht:

   | Sleutel                            | Type                     | Waarde                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Tekenreeks                   | \<hubKey>                  |
   | notificationHubKeyName         | Tekenreeks                   | \<hubKeyName>              |
   | notificationHubName            | Tekenreeks                   | \<hubName>                 |
   | notificationHubNamespace       | Tekenreeks                   | \<hubNamespace>            |

   U kunt de vereiste waarden vinden door te navigeren naar de notification hub-resource in het Azure Portal. Met name de waarden **notificationHubName** en **notificationHubNamespace** bevinden zich in de rechter bovenhoek van de **Essentials** -samen vatting op de pagina **overzicht** .

   ![Samen vatting van Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   U kunt ook de waarden voor **notificationHubKeyName** en **notificationHubKey** vinden door te navigeren naar **toegangs beleid** en het betreffende **toegangs beleid**te selecteren, `DefaultFullSharedAccessSignature`zoals. Daarna kopieert u vanuit de **primaire verbindings reeks** de waarde die is voorafgegaan door `SharedAccessKeyName=` voor `notificationHubKeyName` en de waarde die wordt voorafgegaan `SharedAccessKey=` door voor `notificationHubKey`de.

   De connection string moet de volgende indeling hebben:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Om het eenvoudig te laten, `DefaultFullSharedAccessSignature` geeft u op dat u het token kunt gebruiken om meldingen te verzenden. In de praktijk `DefaultListenSharedAccessSignature` is het een betere keuze voor situaties waarin u alleen meldingen wilt ontvangen.

1. Selecteer onder **project Navigator**de naam van het **project** en selecteer vervolgens het tabblad **Algemeen** .

1. Zoek **identiteit** en stel de **bundel-id** -waarde zo in dat deze `com.<organization>.PushDemo`overeenkomt met de waarde die wordt gebruikt voor de **App-ID** uit een vorige stap.

1. Zoek de **functies voor ondertekening &** en selecteer vervolgens het juiste **team** voor uw **Apple Developer-account**. De **team** waarde moet overeenkomen met de naam waaronder u uw certificaten en profielen hebt gemaakt.

1. Xcode moet de juiste **inrichtings profiel** waarde automatisch door halen op basis van de **bundel-id**. Als u de nieuwe waarde voor het **inrichtings profiel** niet ziet, vernieuwt u de profielen voor de **identiteit voor ondertekening** door **Xcode** > **Preferences** > -**account** te selecteren en vervolgens de knop **hand matige profielen downloaden** te selecteren om de profielen te downloaden.

1. Klik op het tabblad **functies voor ondertekening &** op de knop **+ mogelijkheid** en Dubbeltik op **Push meldingen** in de lijst om ervoor te zorgen dat **Push meldingen** worden ingeschakeld.

1. Open uw **AppDelegate. Swift** -bestand om het **UNUserNotificationCenterDelegate** -protocol te implementeren en voeg de volgende code toe boven aan de klasse:

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

    U gebruikt deze leden later. Met name gebruikt u het lid **Tags** als onderdeel van de registratie met behulp van een **aangepaste sjabloon**. Zie [Tags voor registraties](notification-hubs-tags-segment-push-message.md) en [sjabloon registraties](notification-hubs-templates-cross-platform-push-messages.md)voor meer informatie over tags.

1. Voeg in hetzelfde bestand de volgende code toe aan de functie **didFinishLaunchingWithOptions** :

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

    Met deze code worden de instellings waarden opgehaald van **devsettings. plist**, wordt de klasse **AppDelegate** ingesteld als de **UNUserNotificationCenter** gemachtigde, wordt autorisatie aangevraagd voor push meldingen en wordt vervolgens **registerForRemoteNotifications**aangeroepen.

    Om het eenvoudig te kunnen blijven, ondersteunt de code *alleen IOS 10 en hoger*. U kunt ondersteuning voor eerdere versies van het besturings systeem toevoegen door de respectieve Api's en benaderingen te gebruiken zoals u dat normaal zou doen.

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

    De code gebruikt de **installationId** -en **pushChannel** -waarden om te registreren bij de notification hub. In dit geval gebruikt u **UIDevice. current. identifierForVendor** om een unieke waarde op te geven voor het identificeren van het apparaat en vervolgens het format teren van de **deviceToken** om de gewenste **pushChannel** -waarde op te geven. De functie **showAlert** bestaat alleen om een bericht tekst weer te geven voor demonstratie doeleinden.

1. Voeg nog steeds in het bestand **AppDelegate. Swift** de functies **willPresent** en **didReceive** toe aan **UNUserNotificationCenterDelegate**. Met deze functies wordt een waarschuwing weer gegeven wanneer wordt gemeld dat een app wordt uitgevoerd op de voor grond of op de achtergrond.

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

1. Voeg afdruk instructies aan de onderkant van de functie **didRegisterForRemoteNotificationsWithDeviceToken** toe om te controleren of aan **installationId** en **pushChannel** waarden worden toegewezen.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Maak de mappen **modellen**, **Services**en **hulpprogram ma's** voor de kern onderdelen die u later wilt toevoegen aan het project.

1. Controleer of het project is gebouwd en wordt uitgevoerd op een fysiek apparaat. Push meldingen kunnen niet worden getest met behulp van de Simulator.

### <a name="create-models"></a>Modellen maken

In deze stap maakt u een set modellen voor de [Notification Hubs rest API](/rest/api/notificationhubs/) payloads en voor het opslaan van de vereiste SAS-token gegevens (Shared Access Signature).

1. Voeg een nieuw Swift-bestand met de naam **PushTemplate. Swift** toe aan de map **modellen** . Dit model bevat een struct die de **hoofd tekst** van een afzonderlijke sjabloon vertegenwoordigt als onderdeel van de **DeviceInstallation** -nettolading.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Voeg een nieuw Swift-bestand met de naam **DeviceInstallation. Swift** toe aan de map **modellen** . Dit bestand definieert een struct die de payload vertegenwoordigt voor het maken of bijwerken van een installatie van een **apparaat**. Voeg de volgende code toe aan het bestand:

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

1. Voeg een nieuw Swift-bestand met de naam **TokenData. Swift** toe aan de map **modellen** . Dit model wordt gebruikt voor het opslaan van een SAS-token en de verval datum.

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

Notification Hubs dezelfde beveiligings infrastructuur als Azure Service Bus gebruiken. Als u de REST API wilt aanroepen, moet u [programmatisch een SAS-token genereren](/rest/api/eventhub/generate-sas-token) dat kan worden gebruikt in de **autorisatie** -header van de aanvraag.  

Het resulterende token heeft de volgende indeling:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Het proces zelf omvat dezelfde zes belang rijke stappen:  

1. Het berekenen van het verloop in de [UNIX-epoche tijd](https://en.wikipedia.org/wiki/Unix_time) notatie is het aantal seconden dat is verstreken sinds middernacht Coordinated Universal Time, 1 januari 1970.
1. De **ResourceUrl** van de resource die u probeert te openen, wordt opgemaakt zodat deze een percentage van de code ring en kleine letters bevat. De **ResourceUrl** heeft het formulier `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. De **StringToSign**wordt voor bereid, die is `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`opgemaakt als.
1. Computing and base64: code ring van de **hand tekening** met behulp van de HMAC-sha256-hash van de **StringToSign** -waarde. De hash-waarde wordt gebruikt in combi natie met het **sleutel** gedeelte van de **verbindings reeks** voor de respectieve **autorisatie regel**.
1. De base64-gecodeerde **hand tekening** opmaken zodat het percentage is gecodeerd.
1. Het token samen stellen in de verwachte indeling met behulp van de waarden **UrlEncodedSignature**, **ExpiryEpoch**, sleutel **naam**en **UrlEncodedResourceUrl** .

Raadpleeg de [documentatie van Azure service bus](../service-bus-messaging/service-bus-sas.md) voor een uitgebreid overzicht van de hand tekening voor gedeelde toegang en hoe Azure Service Bus en notification hubs gebruiken.

Voor de doel einden van dit snelle voor beeld gaat u de open-source **CommonCrypto** -bibliotheek van Apple gebruiken om de hashing van de hand tekening te helpen. Omdat het een C-bibliotheek is, is deze niet toegankelijk in het vak snel beschikbaar. U kunt de bibliotheek beschikbaar maken met behulp van een bridging-header.

De bridging-header toevoegen en configureren:

1. **Selecteer** > in Xcode het bestand**Nieuw** > **bestand** > met de bestands-**header**. Noem het header-bestand **BridgingHeader. h**.

1. Bewerk het bestand om **CommonHMAC. h**te importeren:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Werk de **opbouw instellingen** van het doel bij om te verwijzen naar de bridging-header:

   1. Tik op het project **PushDemo** en schuif omlaag naar de sectie **Swift compiler** .

   1. Zorg ervoor dat de optie voor het installeren van de **compatibiliteits header-C** is ingesteld op **Ja**.

   1. Geef het bestandspad `'<ProjectName>/BridgingHeader.h'` op in de optie voor het **overbruggen van de objectief-C-header** . Dit is het bestandspad naar onze bridging-header.

   Als u deze opties niet kunt vinden, moet u ervoor zorgen dat de weer gave **alle** is geselecteerd in plaats van **basis** of **aangepast**.

   Er zijn veel open-source wrapper-bibliotheken van derden beschikbaar die het gebruik van **CommonCrypto** iets eenvoudiger maken. De bespreking van dergelijke bibliotheken valt echter buiten het bereik van dit artikel.

1. Voeg een nieuw Swift-bestand met de naam **TokenUtility. Swift** in de map **Utilities** toe en voeg de volgende code toe:

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

   Met dit hulp programma wordt de logica ingekapseld die verantwoordelijk is voor het genereren van het SAS-token.

   Zoals eerder beschreven, coördineert de functie **getSasToken** de stappen op hoog niveau die nodig zijn om het token voor te bereiden. De functie wordt later in deze zelf studie aangeroepen door de installatie service.

   De andere twee functies worden aangeroepen door de functie **getSasToken** : **sha256HMac** voor het berekenen van de hand tekening en **urlEncodedString** voor het coderen van de bijbehorende URL-teken reeks. De functie **urlEncodedString** is vereist omdat het niet mogelijk is om de vereiste uitvoer te maken met behulp van de ingebouwde functie **addingPercentEncoding** .

   De [Azure Storage IOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) is een uitstekend voor beeld van hoe u deze bewerkingen kunt benaderen in doel-C. Meer informatie over Azure Service Bus SAS-tokens vindt u in de [Azure service bus-documentatie](../service-bus-messaging/service-bus-sas.md).

1. Voeg in **AppDelegate. Swift**de volgende code toe aan de functie *didRegisterForRemoteNotificationsWithDeviceToken* om te controleren of de **TokenUtility. getSasToken** een geldig token genereert
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Zorg ervoor dat u de waarden van de tijdelijke aanduidingen in de teken reeks **baseAddress** vervangt door uw eigen waarde

### <a name="verify-the-sas-token"></a>Het SAS-token controleren

Voordat u de installatie service in de-client implementeert, moet u controleren of de SAS-token correct door de app wordt gegenereerd met behulp van het HTTP-hulp programma van Choice. In het kader van deze zelf studie is het hulp programma van Choice een **bericht**.

Noteer de **installationId** -en **token** waarden die door de app worden gegenereerd.

Volg deze stappen om de **Installations** API aan te roepen:

1. Open in **postman**een nieuw tabblad.

1. Stel de aanvraag in om het volgende adres op te **halen** en op te geven:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configureer de aanvraag headers als volgt:

   | Sleutel           | Waarde            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorisatie | \<sasToken>       |
   | x-MS-version  | 2015-01          |

1. Selecteer de knop **code** die rechtsboven wordt weer gegeven onder de knop **Opslaan** . De aanvraag moet er ongeveer uitzien als in het volgende voor beeld:

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

Er bestaat op dit moment geen registratie voor de opgegeven **installationId** . De verificatie moet resulteren in een ' 404 niet gevonden ' antwoord in plaats van een ' 401 niet-geautoriseerde ' reactie. Dit resultaat moet bevestigen dat het SAS-token is geaccepteerd.

### <a name="implement-the-installation-service-class"></a>De installatie service klasse implementeren

Vervolgens implementeert u onze Basic-wrapper rond de [installaties rest API](/rest/api/notificationhubs/create-overwrite-installation).  

Voeg een nieuw Swift-bestand met de naam **NotificationRegistrationService. Swift** toe onder de map **Services** en voeg de volgende code toe aan dit bestand:

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

De vereiste details worden gegeven als onderdeel van de initialisatie. Tags en sjablonen worden optioneel door gegeven aan de functie **REGI ster** om onderdeel te vormen van de JSON-nettolading van de installatie van het **apparaat** .  

De functie **registreren** roept de andere persoonlijke functies aan om de aanvraag voor te bereiden. Nadat een antwoord is ontvangen, wordt de voltooiing aangeroepen en wordt aangegeven of de registratie is geslaagd.  

Het aanvraag eindpunt wordt samengesteld door de functie **getBaseAddress** . De constructie maakt gebruik van de naam *ruimte* en *naam* van de notification hub die tijdens de initialisatie zijn opgegeven.  

De functie **getSasToken** controleert of het momenteel opgeslagen token geldig is. Als het token niet geldig is, roept de functie **TokenUtility** op om een nieuw token te genereren en vervolgens opgeslagen voordat een waarde wordt geretourneerd.

Ten slotte converteert **encodeToJson** de respectieve model objecten naar JSON voor gebruik als onderdeel van de aanvraag tekst.

### <a name="invoke-the-notification-hubs-rest-api"></a>De Notification Hubs-REST API aanroepen

De laatste stap is het bijwerken van **AppDelegate** om **NotificationRegistrationService** te gebruiken om bij onze **NotificationHub**te registreren.

1. Open **AppDelegate. Swift** en voeg variabelen op klasse niveau toe om een verwijzing naar de **NoficiationRegistrationService** en de algemene **PushTemplate**op te slaan:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Werk in hetzelfde bestand de functie **didRegisterForRemoteNotificationsWithDeviceToken** bij om de **NotificationRegistrationService** te initialiseren met de vereiste para meters en roep vervolgens de functie **REGI ster** aan.

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

    Om het eenvoudig te maken, gaat u enkele afdruk instructies gebruiken om het uitvoer venster bij te werken met het resultaat van de **registratie** bewerking.

1. Bouw nu de app op een fysiek apparaat en voer deze uit. U ziet ' geregistreerd ' in het uitvoer venster.

## <a name="test-the-solution"></a>De oplossing testen

Onze app in deze fase is geregistreerd bij **NotificationHub** en kan push meldingen ontvangen. In Xcode stopt u de fout opsporing en sluit u de app als deze momenteel wordt uitgevoerd. Controleer vervolgens of de details van de installatie van het **apparaat** juist zijn en of de app nu Push meldingen kan ontvangen.  

### <a name="verify-the-device-installation"></a>De installatie van het apparaat controleren

U kunt nu dezelfde aanvraag maken zoals u eerder hebt gedaan met behulp van **postman** voor [het verifiëren van het SAS-token](#verify-the-sas-token). Ervan uitgaande dat het SAS-token nog niet is verlopen, moet het antwoord nu de installatie gegevens bevatten die u hebt gegeven, zoals de sjablonen en tags.

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

Als uw vorige **SAS-token** is verlopen, kunt u een **onderbrekings punt** toevoegen aan **regel 24** van de klasse **TokenUtility** om een nieuw **SAS-token** op te halen en de **autorisatie** -header met die nieuwe waarde bij te werken.

### <a name="send-a-test-notification-azure-portal"></a>Een test melding verzenden (Azure Portal)

De snelste manier om te testen of u nu meldingen kunt ontvangen, is door te bladeren naar de notification hub in de Azure Portal:

1. Blader in het Azure Portal naar het tabblad **overzicht** op de notification hub.

1. Selecteer **Test verzenden**, die boven de samen vatting **Essentials** in de linkerbovenhoek van het portal-venster wordt weer gegeven:

    ![Knop voor het testen van de samen vatting van Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Kies **aangepaste sjabloon** in de lijst **platformen** .

1. Voer **12345** in voor de **expressie Send to tag**. U hebt dit label eerder opgegeven in onze installatie.

1. Bewerk eventueel het **bericht** in de JSON-nettolading:

    ![Notification Hubs test verzenden](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecteer **Verzenden**. De portal geeft aan of de melding is verzonden naar het apparaat:

    ![Resultaten van Notification Hubs test verzenden](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Ervan uitgaande dat de app niet op de voor grond wordt uitgevoerd, ziet u ook een melding in het **meldingen centrum** op uw apparaat. Als u op de melding tikt, wordt de App geopend en wordt de waarschuwing weer gegeven.

    ![Voor beeld van een ontvangen melding](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Een test melding verzenden (e-mail provider)

U kunt via de [rest API](/rest/api/notificationhubs/) meldingen verzenden met behulp van **postman**, wat een handigere manier is om te testen.

1. Open een nieuw tabblad in het **bericht**.

1. Stel de aanvraag in op **post**en voer het volgende adres in:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configureer de aanvraag headers als volgt:

   | Sleutel                            | Waarde                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json; charset = UTF-8 |
   | Autorisatie                  | \<sasToken>                     |
   | ServiceBusNotification-indeling  | sjabloon                       |
   | Tags                           | "12345"                        |

1. Configureer de aanvraag **tekst** voor het gebruik van **RAW-JSON (Application. json)** met de volgende JSON-nettolading:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecteer de knop **code** . Deze bevindt zich onder de knop **Opslaan** in de rechter bovenhoek van het venster. De aanvraag moet er ongeveer uitzien als in het volgende voor beeld:

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

U moet een **201** -status code voor geslaagde pogingen ophalen en de melding op het client apparaat ontvangen.

## <a name="next-steps"></a>Volgende stappen
U hebt nu een eenvoudige, snelle iOS-app die is verbonden met een notification hub via de [rest API](/rest/api/notificationhubs/) en meldingen kunt verzenden en ontvangen. Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST-Api's](/rest/api/notificationhubs/)
- [Notification Hubs SDK voor back-end-bewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK op GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registreren bij back-end van toepassing](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Werken met Tags](notification-hubs-tags-segment-push-message.md) 
- [Werken met aangepaste sjablonen](notification-hubs-templates-cross-platform-push-messages.md)
- [Toegangs beheer Service Bus met hand tekeningen voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md)
- [Programmatisch SAS-tokens genereren](/rest/api/eventhub/generate-sas-token)
- [Apple-beveiliging: algemene crypto grafie](https://developer.apple.com/security/)
- [UNIX-epoche tijd](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
