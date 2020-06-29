---
title: Pushmeldingen verzenden naar Swift iOS-apps met Azure Notification Hubs en de REST API's
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs en de REST API's pushmeldingen verzendt naar iOS-apparaten.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127102"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Zelfstudie: Pushmeldingen verzenden naar Swift iOS-apps met REST API's van Notification Hubs

In deze zelfstudie wordt beschreven hoe u Azure Notification Hubs gebruikt om met REST API's pushmeldingen te verzenden naar een iOS-toepassing.

Deze zelfstudie bestaat uit de volgende stappen:

- Maak een voorbeeld iOS-app.
- Verbind de iOS-app met Azure Notification Hubs.
- Verzend testpushmeldingen.
- Controleer of de app meldingen ontvangt.

U kunt de complete code voor deze zelfstudie downloaden van [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.

- Een iPhone of iPad waarop iOS versie 10 of hoger wordt uitgevoerd.

- Uw fysieke apparaat dat is geregistreerd in de [Apple Portal](https://developer.apple.com/) en is gekoppeld aan uw certificaat.

Zorg dat u voordat u verder gaat, de vorige zelfstudie over hoe u aan de slag gaat met [Azure Notification Hubs voor iOS-apps](https://go.microsoft.com/fwlink/?linkid=2129801) hebt doorlopen, zodat u weet hoe u pushreferenties instelt en configureert in uw meldingenhub. Zelfs als u geen ervaring hebt met iOS-ontwikkeling, zou u deze stappen moeten kunnen volgen.

> [!NOTE]
> Vanwege configuratievereisten voor pushmeldingen moet u pushmeldingen op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de iOS-emulator.

In de volgende secties bouwt u een iOS-app die verbinding maakt met de meldingenhub.

## <a name="create-an-ios-project"></a>Een iOS-project maken

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon  **Single View Application** (Toepassing met één weergave).

2. Bij het instellen van de opties voor het nieuwe project:
   - Geef de **product naam** (PushDemo) en **organisatie-id** (`com.<organization>`) op die u hebt gebruikt bij het instellen van de **bundel-id** in de Apple ontwikkelaarsportal.
   - Kies het **team** waarvoor de **App-ID** is ingesteld.
   - Stel de **taal** in op **Swift**.
   - Selecteer  **Volgende**.

3. Maak een nieuwe map met de naam **SupportingFiles**.

4. Maak een nieuw p-list-bestand met de naam **devsettings.plist** in de map **SupportingFiles** . Zorg ervoor dat u deze map toevoegt aan uw **gitignore** -bestand, zodat het niet wordt doorgevoerd wanneer u met een Git-opslagplaats werkt. In een productie-app kunt u deze geheimen instellen als onderdeel van een geautomatiseerd bouwproces. Deze instellingen worden niet behandeld in deze zelfstudie.

5. Werk **devsettings.plist** bij met de volgende configuratie-items door uw eigen waarden op te nemen uit de meldingenhub die u hebt ingericht:

   | **Sleutel**                  | **Type** | **Waarde**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Tekenreeks   | `<hubKey>`       |
   | notificationHubKeyName   | Tekenreeks   | `<hubKeyName>`   |
   | notificationHubName      | Tekenreeks   | `<hubName>`      |
   | notificationHubNamespace | Tekenreeks   | `<hubNamespace>` |

   U vindt de vereiste waarden door te navigeren naar de resource van de meldingenhub in de Azure-portal. Met name de waarden van **notificationHubName** en **notificationHubNamespace** vindt u in de rechterbovenhoek van de samenvatting **Essentials** op de pagina **Samenvatting** .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Vereiste waarden":::

   U vindt ook de waarden van **notificationHubKeyName** en **notificationHubKey** door te navigeren naar **Access Policies** en het desbetreffende **Access Policy**te selecteren, zoals **DefaultFullSharedAccessSignature**. Daarna kopieert u uit  **Primary Connection String** de waarde met het voorvoegsel `SharedAccessKeyName=` voor **notificationHubKeyName**, en de waarde met het voorvoegsel `SharedAccessKey=` voor de **notificationHubKey**. De verbindingsreeks moet de volgende indeling hebben:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Om het eenvoudig te houden, geeft u **DefaultFullSharedAccessSignature**op, zodat u het token kunt gebruiken om meldingen te verzenden. In de praktijk is de **DefaultListenSharedAccessSignature** een betere keuze voor situaties waarin u alleen meldingen wilt ontvangen.

6. Selecteer onder **Project Navigator**de **projectnaam** en selecteer vervolgens het tabblad **General** .

7. Zoek **Identity** en stel de waarde van **Bundle Identifier** zo in dat deze overeenkomt met `com.<organization>.PushDemo`; dit is de waarde die in een vorige stap is gebruikt voor de **App ID** .

8. Zoek **Signing & Capabilities**en selecteer het juiste **Team** voor uw **Apple ontwikkelaarsaccount**. De waarde voor  **Team** moet overeenkomen met die waaronder u uw certificaten en profielen hebt gemaakt.

9. Xcode moet op basis van de **Bundle Identifier** automatisch de juiste waarde voor  **Provisioning Profile** downloaden. Als u de nieuwe waarde voor  **Provisioning Profile** niet ziet, probeer dan om de profielen voor de **Signing Identity** te vernieuwen door achtereenvolgens **Xcode**,  **Preferences**, **Account**te selecteren en vervolgens de knop **Download Manual Profiles** te selecteren om de profielen te downloaden.

10. Als het tabblad  **Signing & Capabilities** nog is geopend, klikt u op de knop **+ Capability** en dubbeltikt u op **Push Notifications** in de lijst om te controleren of **Push Notifications** is ingeschakeld.

11. Open het bestand **AppDelegate.swift** om het protocol **UNUserNotificationCenterDelegate** te implementeren en voeg de volgende code boven de klasse toe:

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

    U gaat deze leden later gebruiken. U gaat met name het lid **tags** gebruiken als onderdeel van de registratie met behulp van een **aangepaste sjabloon**. Zie voor meer informatie over tags [Tags voor registraties](notification-hubs-tags-segment-push-message.md) en [Sjabloonregistraties](notification-hubs-templates-cross-platform-push-messages.md).

12. Voeg in hetzelfde bestand de volgende code toe aan de functie **didFinishLaunchingWithOptions**:

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

    Met deze code worden de instellingen opgehaald uit **devsettings.plist**, wordt de klasse **AppDelegate**  ingesteld als de **UNUserNotificationCenter** -gemachtigde, wordt autorisatie aangevraagd voor pushmeldingen en wordt **registerForRemoteNotifications**aangeroepen.

    Ter vereenvoudiging ondersteunt de code alleen iOS 10 en hoger. U kunt ondersteuning voor eerdere iOS-versies toevoegen door voorwaardelijk de respectieve API's en benaderingen te gebruiken, net zoals u normaal gesproken zou doen.

13. Voeg in hetzelfde bestand de volgende code toe:

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

    Deze code gebruikt de waarden van **installationId** en **pushChannel** om te registreren bij de meldingenhub. In dit geval gebruikt u **UIDevice.current.identifierForVendor** om een unieke waarde op te geven voor de identificatie van het apparaat en construeert u vervolgens het **deviceToken** om de gewenste **pushChannel** -waarde op te geven. De functie **showAlert** dient alleen om berichttekst weer te geven voor demonstratiedoeleinden.

14. Nog steeds in het bestand **AppDelegate.swift** voegt u de functies **willPresent** en **didReceive** toe aan **UNUserNotificationCenterDelegate**. Deze functies geven een waarschuwing weer wanneer een app op de voorgrond of achtergrond wordt uitgevoerd.

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

15. Voeg `print`-instructies onderaan de functie **didRegisterForRemoteNotificationsWithDeviceToken** toe om te controleren of aan **installationId** en **pushChannel** waarden zijn toegewezen:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Maak de mappen **Models**, **Services**en **Utilities** voor de basisonderdelen die u later gaat toevoegen aan het project.

17. Controleer of het project wordt gebouwd en uitgevoerd op een fysiek apparaat. Pushmeldingen kunnen niet worden getest met behulp van de simulator.

## <a name="create-models"></a>Modellen maken

In deze stap maakt u een set modellen om de nettoladingen van de  [REST API van Notification Hubs](/rest/api/notificationhubs/) te vertegenwoordigen en de vereiste SAS-tokengegevens (Shared Access Signature) op te slaan.

1. Voeg een nieuw Swift-bestand met de naam **PushTemplate.swift** toe aan de map **Models** . Dit model definieert een structuur die de **hoofdtekst** van een afzonderlijke sjabloon vertegenwoordigt als onderdeel van de **DeviceInstallation** -nettolading.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Voeg een nieuw Swift-bestand met de naam **DeviceInstallation.swift** toe aan de map **Models** . Dit bestand definieert een structuur die de nettolading vertegenwoordigt voor het maken of bijwerken van een  **apparaatinstallatie**. Voeg de volgende code toe aan het bestand:

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

3. Voeg een nieuw Swift-bestand met de naam **TokenData.swift** toe aan de map **Models** . Dit model wordt gebruikt om een SAS-token met de vervaldatum ervan op te slaan. Voeg de volgende code toe aan het bestand:

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

## <a name="generate-a-sas-token"></a>Een SAS-token genereren

Notification Hubs gebruikt dezelfde beveiligingsinfrastructuur als Azure Service Bus. Om de REST API aan te roepen, [genereert u programmatisch een SAS-token](/rest/api/eventhub/generate-sas-token) dat in de **autorisatie** header van de aanvraag kan worden gebruikt.

Het resulterende token heeft de volgende indeling:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Het proces zelf omvat dezelfde zes stappen:

1. Bereken het verloop in de  [UNIX Epoche](https://en.wikipedia.org/wiki/Unix_time) -tijdnotatie. Dit is het aantal seconden dat is verstreken sinds middernacht Universal Coordinated Time, 1 januari 1970.

2. Construeer de **ResourceUrl** die de resource vertegenwoordigt die u probeert te openen, zodat deze een percentagecodering heeft en kleine letters bevat. De **ResourceUrl** heeft de indeling `https://<namespace>.servicebus.windows.net/<hubName>`.

3. Bereid de **StringToSign**voor; deze is opgemaakt als `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`.

4. Bereken en codeer in base64 de **Signature** met behulp van de HMAC-SHA256-hash van de **StringToSign** -waarde. De hash-waarde wordt gebruikt met het **sleutel** gedeelte van de **verbindingsreeks** voor de respectieve **autorisatieregel**.

5. Construeer de  **Signature** met Base64-codering zodat deze is gecodeerd met percentage.

6. Construeer het token in de verwachte notatie met behulp van de waarden van  **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**en **UrlEncodedResourceUrl** .

Raadpleeg de [documentatie van Azure Service Bus](../service-bus-messaging/service-bus-sas.md) voor een uitgebreider overzicht van de handtekeningen voor gedeelde toegang en hoe Azure Service Bus en Notification Hubs deze gebruiken.

Voor de doeleinden van dit Swift-voorbeeld gebruikt u de Apple open-source **CommonCrypto** -bibliotheek om te helpen bij het hashen van de handtekening. Omdat het een C-bibliotheek is, is deze niet zonder meer toegankelijk vanuit Swift. U kunt de bibliotheek beschikbaar maken met behulp van een overbruggingsheader.

Zo kunt u de overbruggingsheader toevoegen en configureren:

1. Selecteer in Xcode achtereenvolgens **File**,  **New**,  **File**en  **Header File**. Noem het headerbestand **BridgingHeader.h**.

2. Bewerk het bestand om **CommonHMAC.h**te importeren:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Werk de **Build Settings** van het doel zo bij dat deze naar de overbruggingsheader verwijzen:

   1. Selecteer het **PushDemo** -project en scrol omlaag naar de sectie **Swift Compiler** .

   2. Controleer of de optie **Install Objective-C Compatibility Header** is ingesteld op **Yes**.

   3. Geef  `<ProjectName>/BridgingHeader.h` in de optie **Objective-C bridging Header** het bestandspad op. Dit is het bestandspad naar de overbruggingsheader.

   Als u deze opties niet kunt vinden, controleer dan of de weergave **All** is geselecteerd, en niet  **Basic** of **Customized**.

   Er zijn veel open-source wrapper-bibliotheken beschikbaar die het gebruik van **CommonCrypto** iets eenvoudiger kunnen maken. De bespreking van deze bibliotheken valt echter buiten het bereik van dit artikel.

4. Voeg een nieuw Swift-bestand met de naam **TokenUtility.swift** toe in de map **Utilities** en voeg daaraan de volgende code toe:

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

   Zoals eerder beschreven, regelt de functie **getSasToken** de hoofdstappen die vereist zijn om het token voor te bereiden. De functie wordt later in deze zelfstudie aangeroepen door de installatieservice.

   De andere twee functies worden door de functie **getSasToken** : **sha256HMac** aangeroepen om de handtekening te berekenen, en **urlEncodedString** om de bijbehorende URL-tekenreeks te coderen. De functie **urlEncodedString** is vereist, omdat het niet mogelijk is om de vereiste uitvoer te verkrijgen met behulp van de ingebouwde functie **addingPercentEncoding** .

   De [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) is een uitstekend voorbeeld van hoe u deze bewerkingen kunt benaderen in Objective-C. Meer informatie over Azure Service Bus SAS-tokens vindt u in de documentatie voor [Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

5. Voeg in **AppDelegate.swift**de volgende code toe aan de functie `didRegisterForRemoteNotificationsWithDeviceToken` om te controleren of de **TokenUtility.getSasToken** een geldig token genereert

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Zorg ervoor dat u de tijdelijke aanduidingen in de **baseAddress** -tekenreeks vervangt door uw eigen waarden.

## <a name="verify-the-sas-token"></a>Het SAS-token verifiëren

Voordat u de installatieservice in de client implementeert, controleert u met behulp van een HTTP-hulpprogramma of uw app het SAS-token correct genereert. Voor de doeleinden van deze zelfstudie kiezen we het hulpprogramma **Postman**.

Noteer de waarden van  **installationId** en **token** die door de app worden gegenereerd.

Volg deze stappen om de API van de **installaties** aan te roepen:

1. Open een nieuw tabblad in **Postman**.
2. Stel de aanvraag in op  **GET** en voer het volgende adres in:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Configureer de aanvraagheaders als volgt:

   | **Sleutel**       | **Waarde**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorisatie | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Selecteer de knop **Code** die rechtsboven onder de knop **Save**  wordt weergegeven. De aanvraag moet er ongeveer uitzien als in het volgende voorbeeld:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Selecteer de knop **Send** .

Er bestaat op dit moment geen registratie voor de opgegeven **installationId** . De verificatie moet resulteren in de reactie '404 niet gevonden' in plaats van een '401 Unauthorized'. Dit resultaat moet bevestigen dat het SAS-token is geaccepteerd.

## <a name="implement-the-installation-service-class"></a>De installatieserviceklasse implementeren

Implementeer vervolgens een eenvoudige wrapper rond de [REST API van de installaties](/rest/api/notificationhubs/create-overwrite-installation).

Voeg een nieuw Swift-bestand toe met de naam **NotificationRegistrationService.swift** in de map **Services** en voeg de volgende code toe aan dit bestand:

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

De vereiste details worden gegeven als onderdeel van de initialisatie. Tags en sjablonen worden optioneel aan de functie **register** doorgegeven om deel uit te maken van de JSON-nettolading van de **Device Installation** .

De functie **register** roept de andere persoonlijke functies aan om de aanvraag voor te bereiden. Nadat een antwoord is ontvangen, wordt de voltooiing aangeroepen en wordt aangegeven of de registratie is geslaagd.

Het eindpunt van de aanvraag wordt samengesteld door de functie **getBaseAddress** . De constructie maakt gebruik van de parameters *namespace* en *name* van de meldingenhub die tijdens de initialisatie zijn opgegeven.

De functie **getSasToken** controleert of het opgeslagen token geldig is. Als het token ongeldig is, roept de functie **TokenUtility** aan om een nieuw token te genereren; dit token wordt vervolgens opgeslagen voordat een waarde wordt geretourneerd.

Ten slotte converteert **encodeToJson** de respectieve modelobjecten naar JSON voor gebruik als onderdeel van de aanvraagtekst.

## <a name="invoke-the-notification-hubs-rest-api"></a>REST API voor Notification Hubs activeren

De laatste stap is het bijwerken van **AppDelegate** om **NotificationRegistrationService** te gebruiken voor registratie bij de **NotificationHub**.

1. Open **AppDelegate.swift** en voeg variabelen op klasseniveau toe om een verwijzing op te slaan naar de **NoficiationRegistrationService** en de algemene **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. Werk in hetzelfde bestand de functie **didRegisterForRemoteNotificationsWithDeviceToken** bij om de **NotificationRegistrationService** te initialiseren met de vereiste parameters, en roep vervolgens de functie **register** aan.

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

   Ter vereenvoudiging maakt de code gebruik van `print`-instructies om het uitvoervenster bij te werken met het resultaat van de **register**- bewerking.

3. Bouw en de app en voer deze uit op een fysiek apparaat. U ziet **Registered** in het uitvoervenster.

## <a name="test-the-solution"></a>De oplossing testen

De app is op dit moment geregistreerd bij **NotificationHub** en kan pushmeldingen ontvangen. In Xcode stopt u de foutopsporing en sluit u de app als deze momenteel wordt uitgevoerd. Controleer vervolgens of de installatie van  **Device Installation** worden weergegeven zoals verwacht en of de app nu pushmeldingen kan ontvangen.

### <a name="verify-the-device-installation"></a>De apparaatinstallatie controleren

U kunt nu dezelfde aanvraag doen als u eerder hebt gedaan met behulp van **Postman** om [het SAS-token](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token)te controleren. Ervan uitgaande dat het SAS-token nog niet is verlopen, moet de reactie nu de installatiegegevens bevatten die u hebt gegeven, zoals de sjablonen en tags.

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

Als uw vorige SAS-token is verlopen, kunt u een onderbrekingspunt toevoegen aan regel 24 van de **TokenUtility** -klasse om een nieuw SAS-token op te halen en de **autorisatie** header met die nieuwe waarde bij te werken.

### <a name="send-a-test-notification-azure-portal"></a>Een testmelding verzenden (Azure-portal)

De snelste manier om te testen of u nu meldingen kunt ontvangen, is door naar de meldingenhub in de Azure-portal te bladeren:

1. Ga in de Azure-portal naar het tabblad **Samenvatting** van uw meldingenhub.

2. Selecteer **Verzending testen**, boven de samenvatting **Essentials** in de linkerbovenhoek van het portalvenster:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Verzending testen in Essentials-samenvatting van Notification Hubs":::

3. Kies **Aangepaste sjabloon** in de lijst **Platformen** .

4. Voer **12345** in voor **Verzenden naar Tagexpressie**. U hebt deze tag eerder opgegeven in uw installatie.

5. U kunt ook het **bericht** bewerken in de JSON-nettolading:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs - Verzenden testen":::

6. Selecteer  **Verzenden**. In de portal wordt aangegeven of de melding met succes naar het apparaat is verzonden:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Resultaat van Verzending testen":::

   Ervan uitgaande dat de app niet op de voorgrond wordt uitgevoerd, moet er ook een melding worden weergegeven in het **meldingencentrum** op uw apparaat. Tik op de melding om de app te openen en de waarschuwing weer te geven.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Testmelding":::

### <a name="send-a-test-notification-mail-carrier"></a>Een testmelding verzenden (e-mailprovider)

U kunt meldingen verzenden via de [REST API](/rest/api/notificationhubs/) met **Postman**, wat een handigere manier is om te testen.

1. Open een nieuw tabblad in **Postman**.

2. Stel de aanvraag in op  **POST** en voer het volgende adres in:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Configureer de aanvraagheaders als volgt:

   | Sleutel                           | Waarde                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | Autorisatie                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | sjabloon                       |
   | Tags                          | "12345"                        |

4. Configureer de  **hoofdtekst** van de aanvraag voor gebruik van **RAW - JSON (application.json)**  met de volgende JSON-nettolading:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Selecteer de knop **Code** . Deze bevindt zich onder de knop **Opslaan** in de rechterbovenhoek van het venster. De aanvraag moet er ongeveer uitzien als in het volgende voorbeeld:

   ```xml
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

6. Selecteer de knop **Send** .

Als het goed is, ontvangt u de succes-statuscode **201 Created** en de melding op het clientapparaat.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een eenvoudige iOS Swift-app via de [Rest API voor Notification Hubs](/rest/api/notificationhubs/) verbonden met een meldingenhub en u kunt berichten verzenden en ontvangen. Ga verder met de volgende zelfstudie als u meer wilt weten over het verzenden van meldingen naar specifieke apparaten:

- [Zelfstudie: Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [REST API's voor Notification Hubs](/rest/api/notificationhubs/)
- [Notification Hubs SDK voor back-endbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK op GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registreren bij back-end van toepassing](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Werken met tags](notification-hubs-tags-segment-push-message.md)
- [Werken met aangepaste sjablonen](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus-toegangsbeheer met handtekeningen voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md)
- [Programmatisch SAS-tokens genereren](/rest/api/eventhub/generate-sas-token)
- [Apple-beveiliging: algemene cryptografie](https://developer.apple.com/security/)
- [UNIX Epoche-tijd](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
