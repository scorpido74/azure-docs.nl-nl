---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156293"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Het doel en Info.plist voor Runner configureren

1. In **Visual Studio Code**, **Beheer** + **Klik** op de map **ios** en kies **Openen in Xcode**.

1. In **Xcode** klikt u op **Runner** (**xcodeproj** bovenaan, niet de map) en selecteert u het doel **Runner** en vervolgens **Signing & Capabilities**. Terwijl de build-configuratie **All** is geselecteerd, kiest u uw ontwikkelaarsaccount voor het **Team**. Controleer of de optie "Automatically manage signing" is geselecteerd en uw handtekeningcertificaat en inrichtingsprofiel automatisch zijn geselecteerd.

    > [!NOTE]
    > Als u de nieuwe waarde voor Provisioning Profile niet ziet, probeer dan de profielen voor de Signing Identity te vernieuwen door **Xcode** > **Preferences** > **Account** te selecteren en vervolgens de knop **Download Manual Profiles** te selecteren om de profielen te downloaden.

1. Klik op **+ Capability** en zoek vervolgens naar **Push Notifications**. **Dubbelklik** op **Push Notifications** om deze mogelijkheid toe te voegen.

1. Open **Info.plist** en stel **de minimale systeemversie** in op **13.0**.

    > [!NOTE]
    > Alleen apparaten met **iOS 13.0 en hoger** worden ondersteund voor deze zelfstudie, maar u kunt deze ook uitbreiden om apparaten te ondersteunen waarop oudere versies worden uitgevoerd.

1. Open **Runner.entitlements** en controleer of de instelling **APS Environment** is ingesteld op **development**.

### <a name="handle-push-notifications-for-ios"></a>Pushmeldingen voor iOS verwerken

1. **Beheer** + **Klik** op de map **Runner** (in het Runner-project) en kies **Nieuwe groep** met **Services** als de naam.

1. **Beheren** + **Klik** op de map **Services** en kies vervolgens **Nieuw bestand...** . Kies vervolgens **Swift-bestand** en klik op **Volgende**. Geef **DeviceInstallationService** op als naam en klik vervolgens op **Maken**.

1. Implementeer **DeviceInstallationService.swift** met de volgende code.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/deviceinstallation`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **DeviceInstallationService.dart**. In dit geval worden de aanroepen vanuit de gemeenschappelijke code gedaan naar de systeemeigen host. Zorg ervoor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.
    >
    > Deze klasse verstrekt een unieke id (met de waarde [UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor)) als onderdeel van de nettolading voor registratie van de meldingen-hub.

1. Voeg nog een **Swift-bestand** aan de map **Services** toe met de naam *NotificationRegistrationService* en voeg vervolgens de volgende code toe.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/notificationregistration`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **NotificationRegistrationService.dart**. In dit geval worden de aanroepen vanuit de systeemeigen host gedaan naar de algemene code. Zorg er wederom voor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.

1. Voeg nog een **Swift-bestand** aan de map **Services** toe met de naam *NotificationActionService* en voeg vervolgens de volgende code toe.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Met deze klasse implementeert u het platformspecifieke equivalent voor het `com.<your_organization>.pushdemo/notificationaction`-kanaal. Dit is gedefinieerd in het Flutter-gedeelte van de app in **NotificationActionService.dart**. In dit geval vinden aanroepen in beide richtingen plaats. Zorg ervoor dat u **<your_organization>** vervangt door de naam van uw eigen organisatie waar dit van toepassing is.

1. Voeg in **AppDelegate.swift** variabelen toe om een verwijzing op te slaan naar de services die u eerder hebt gemaakt.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Voeg een functie met de naam **processNotificationActions** toe voor het verwerken van de meldingsgegevens. Activeer die actie conditioneel of sla deze op voor later gebruik als de actie tijdens het starten van de app wordt verwerkt.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Vervang de functie **didRegisterForRemoteNotificationsWithDeviceToken** die de waarde van **token** vervangt voor de **DeviceInstallationService**. Roep vervolgens **refreshRegistration** aan op de **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Overschrijf de functie **didReceiveRemoteNotification** die het door het argument **userInfo** door te geven aan de functie **processNotificationActions**.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Overschrijf de functie **didFailToRegisterForRemoteNotificationsWithError** om de fout te registreren.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Dit is een tijdelijke aanduiding. U moet de juiste logboekregistratie en foutafhandeling implementeren voor productiescenario's.

1. In **didFinishLaunchingWithOptions** geeft u waarden op voor de variabelen **deviceInstallationService**, **notificationRegistrationService** en **notificationActionService**.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. In dezelfde functie vraagt u voorwaardelijke autorisatie aan en registreert u zich voor externe meldingen.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Als **launchOptions** de sleutel **remoteNotification** bevat, roept u **processNotificationActions** aan het einde van de functie **didFinishLaunchingWithOptions** aan. Geef het resulterende **userInfo**-object door en gebruik *true* als *launchAction*-argument. De waarde *true* geeft aan dat de actie tijdens het starten van de app wordt verwerkt.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
