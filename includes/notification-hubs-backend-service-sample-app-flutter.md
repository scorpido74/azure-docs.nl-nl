---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156297"
---
### <a name="create-the-flutter-solution"></a>De Flutter-oplossing maken

1. Open een nieuw exemplaar van **Visual Studio Code**.

1. Open het **Opdrachtpalet** (**Shift** + **Command** + **P**).

1. Selecteer de opdracht **Flutter: New Project** en druk op **Enter**.

1. Voer *push_demo* in als **Projectnaam** en selecteer een **Projectlocatie**.

1. Kies **Get packages** als dit wordt gevraagd.

1. **Beheer** + **Klik** op de map **Kotlin** (onder **app** > **src** > **main**) en kies **Reveal in Finder**. Wijzig de naam van de onderliggende mappen (onder de map **Kotlin**) vervolgens respectievelijk in ```com```, ```<your_organization>```en ```pushdemo```.

    > [!NOTE]
    > Als u de **Visual Studio Code**-sjabloon gebruikt, zijn deze mappen standaard ingesteld op **com**, **example**, **<project_name>** . Ervan uitgaande dat **mobcat** wordt gebruikt voor de **organisatie**, wordt de mapstructuur indicatief weergegeven als:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. In **Visual Studio code** werkt u de waarde van **applicationId** in **android** > **app** > **build.gradle** bij naar `com.<your_organization>.pushdemo`.

    > [!NOTE]
    > Gebruik de naam van uw eigen organisatie in plaats van de tijdelijke aanduiding *<your_organization>* . Als u bijvoorbeeld **mobcat** als organisatie gebruikt, resulteert dit in een **pakketnaam** met de waarde *com.mobcat.pushdemo*.

1. Werk het kenmerk **package** in de **AndroidManifest.xml**-bestanden, onder respectievelijk **src** > **debug**, **src** > **main** en **src** > **profile** bij. Zorg ervoor dat de waarden overeenkomen met de **applicationId** die u in de vorige stap hebt gebruikt.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Werk het kenmerk ```android:label``` in het **AndroidManifest.xml**-bestand onder **src** > **main** bij naar *PushDemo*. Voeg vervolgens het kenmerk ```android:allowBackup``` direct onder ```android:label``` toe en stel de waarde ervan in op **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Open bestand **build.gradle** op app-niveau (**android** > **app** > **build.gradle**) en werk de *compileSdkVersion* bij (vanuit de sectie **android**) om API **29** te gebruiken. Werk vervolgens de waarden voor *minSdkVersion* en *targetSdkVersion-* (in de sectie **defaultConfig**) bij naar respectievelijk **26** en **29**.

    > [!NOTE]
    > Alleen apparaten met **API-niveau 26 en hoger** worden ondersteund voor deze zelfstudie, maar u kunt deze ook uitbreiden om apparaten te ondersteunen waarop oudere versies worden uitgevoerd.

1. **Beheer** + **Klik** in de map **ios** en kies vervolgens **Openen in Xcode**.

1. Klik in **Xcode** op **Runner** (de **xcodeproj** bovenaan, niet de map). Selecteer vervolgens het doel **Runner** en klik op het tabblad **General**. Terwijl de build-configuratie **All** is geselecteerd, werkt u de **Bundle Identifier** naar `com.<your_organization>.PushDemo`.

    > [!NOTE]
    > Gebruik de naam van uw eigen organisatie in plaats van de tijdelijke aanduiding *<your_organization>* . Als u bijvoorbeeld **mobcat** als organisatie gebruikt, resulteert dit in een **Bundle Identifier** met de waarde *com.mobcat.PushDemo*.

1. Klik op **Info.plist** en werkt de waarde van **Bundle name** bij naar **PushDemo**

1. Sluit **Xcode** en ga terug naar **Visual Studio Code**.

1. Terug in **Visual Studio Code** opent u **pubspec.yaml** en voegt u de **Dart-pakketten** [http](https://pub.dev/packages/http) en [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) als afhankelijkheden toe. Sla het bestand vervolgens op en klik op **Get packages** wanneer dit wordt gevraagd.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. In **Terminal** wijzigt u de map in de map **ios** (voor uw Flutter-project). Voer vervolgens de opdracht [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) uit om nieuwe pods te installeren (vereist door het pakket [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)).

1. **Beheer** + **Klik** op de map **lib** en kies **Nieuw bestand** in het menu met *main_page.dart* als bestandsnaam. Voeg vervolgens de volgende code toe.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. Vervang in **main.dart** de sjablooncode door het volgende.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. In **Terminal** kunt u de app bouwen en uitvoeren op elk doelplatform om de uitvoeringen van de sjabloon-app op uw apparaat (apparaten) te testen. Zorg ervoor dat de ondersteunde apparaten zijn verbonden.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>De platformoverschrijdende onderdelen implementeren

1. **Beheer** + **Klik** op de map **lib** en kies **Nieuwe map** in het menu met *modellen* als **Mapnaam**.

1. **Beheer** + **Klik** op de map **models** en kies **Nieuw bestand** in het menu met *device_installation.dart* als bestandsnaam. Voeg vervolgens de volgende code toe.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Voeg een nieuw bestand toe aan de map **models** met de naam *push_demo_action.dart* dat de opsomming definieert van die in dit voorbeeld worden ondersteund.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Voeg een nieuwe map met de naam **services** toe aan het project en voeg vervolgens een nieuw bestand met de naam *device_installation_service.dart* en de volgende implementatie toe aan die map.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Gebruik de naam van uw eigen organisatie in plaats van de tijdelijke aanduiding *<your_organization>* . Als u bijvoorbeeld **mobcat** als organisatie gebruikt, resulteert dit in de [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) met de naam *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Deze klasse omvat een samenwerking met het onderliggende systeemeigen platform om de vereiste details van de apparaatinstallatie te verkrijgen. Een [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) faciliteert bidirectionele asynchrone communicatie met de onderliggende systeemeigen platformen. Het platformspecifieke equivalent voor dit kanaal wordt in de volgende stappen gemaakt.

1. Voeg nog een bestand aan de map toe met de naam *notification_action_service.dart* met de volgende implementatie.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Dit wordt gebruikt als een eenvoudig mechanisme om de verwerking van meldingsacties te centraliseren, zodat ze op een platform overschrijdende manier kunnen worden verwerkt met een sterk getypeerde opsomming. De service maakt mogelijk dat het onderliggende systeemeigen platform een actie kan activeren als er een in de nettolading van de melding is opgegeven. De service maakt ook mogelijk dat de algemene code zodra Flutter gereed is om acties te verwerken, retrospectief kan controleren of er tijdens het starten van de toepassing een actie is opgegeven. Bijvoorbeeld als de app is gestart door in het meldingencentrum op een melding te tikken.

1. Voeg aan de map **services** een nieuw bestand toe met de naam *notification_registration_service.dart* met de volgende implementatie.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Deze klasse omvat het gebruik van de **DeviceInstallationService** en de aanvragen naar de back-endservice om de vereiste acties voor registreren, registratie verwijderen en registratie vernieuwen uit te voeren. Het argument **apiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.

1. Voeg aan de map **lib** een nieuw bestand toe met de naam *config.dart* met de volgende implementatie.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Dit wordt gebruikt als een eenvoudige manier om app-geheimen te definiëren. Vervang de plaatsaanduiding voor de waarden door uw eigen. U moet hiermee rekening houden wanneer u de back-end-service hebt gemaakt. De **URL van de API-app** moet `https://<api_app_name>.azurewebsites.net/` zijn. Het lid **apiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.
    >
    > Zorg ervoor dat u deze aan uw gitignore-bestand toevoegt om te voorkomen dat deze geheimen worden doorgevoerd in broncodebeheer.

### <a name="implement-the-cross-platform-ui"></a>De platformoverschrijdende UI implementeren

1. Vervang in **main_page.dart** de **build**-functie door het volgende.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Voeg de vereiste imports bovenaan het bestand **main_page.dart** toe.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Voeg een veld aan de klasse **_MainPageState** toe om een verwijzing naar de **NotificationRegistrationService** op te slaan.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Bij de klasse **_MainPageState** implementeert u de gebeurtenis-handlers voor de **onPressed**-gebeurtenissen van de knoppen **Register** en **Deregister**. Roep de betreffende **Register**/**Deregister**-methoden aan en geef een waarschuwing weer om het resultaat aan te geven.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Controleer nu in **main.dart** of de volgende imports bovenaan het bestand worden weergegeven.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Declareer een variabele om een verwijzing naar een exemplaar van **NotificationActionService** op te slaan en initialiseer deze.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Voeg functies toe om de weergave van een waarschuwing te verwerken wanneer een actie wordt geactiveerd.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Werk de functie **main** bij om de stroom **NotificationActionService** **actionTriggered** te observeren en te controleren op acties die zijn vastgelegd tijdens het starten van de app.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Dit is om aan te geven dat acties voor pushmeldingen zijn ontvangen of worden doorgegeven. Normaal gesproken gebeurt dit op de achtergrond om bijvoorbeeld naar een specifieke weergave te navigeren of om gegevens te vernieuwen in plaats van een waarschuwing te geven in dit geval.
