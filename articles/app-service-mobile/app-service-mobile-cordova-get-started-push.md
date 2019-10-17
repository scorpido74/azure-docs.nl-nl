---
title: Push meldingen toevoegen aan een Apache Cordova-app met de functie Mobile Apps van Azure App Service | Microsoft Docs
description: Meer informatie over het gebruik van Mobile Apps voor het verzenden van push meldingen naar uw Apache Cordova-app.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a7e64b32a084075fe96cbd4ab7a0d28fc0f8e19
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388870"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Push meldingen toevoegen aan uw Apache Cordova-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het [Apache Cordova Quick][5] start-project, zodat een push melding wordt verzonden naar het apparaat wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start-Server project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end-server SDK voor Mobile apps][1]voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u beschikt over een Apache Cordova-toepassing die is ontwikkeld met Visual Studio 2015. Dit apparaat moet worden uitgevoerd op Google Android Emulator, een Android-apparaat, een Windows-apparaat of een iOS-apparaat.

Voor deze zelfstudie hebt u het volgende nodig:

* Een PC met [Visual Studio Community 2015][2] of hoger
* [Visual Studio Tools for Apache Cordova][4]
* Een [actief Azure-account][3].
* Een voltooid [Apache Cordova Quick][5] start-project
* Android Een [Google-account][6] met een geverifieerd e-mail adres
* O's Een [Apple Developer Program-lidmaatschap][7] en een IOS-apparaat (IOS-simulator ondersteunt geen push meldingen)
* Windows Een [Microsoft Store ontwikkelaars account][8] en een Windows 10-apparaat

## <a name="configure-hub"></a>Een notification hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Bekijk een video met de stappen in deze sectie][9].

## <a name="update-the-server-project"></a>Het server project bijwerken

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Uw Cordova-app wijzigen

Om ervoor te zorgen dat uw Apache Cordova-app-project gereed is voor het afhandelen van push meldingen, installeert u de Cordova push-invoeg toepassing plus alle platformspecifieke Push Services.

#### <a name="update-the-cordova-version-in-your-project"></a>Werk de Cordova-versie in uw project bij.

Als uw project gebruikmaakt van een versie van Apache Cordova die ouder is dan versie 6.1.1, werkt u het client project bij. Voer de volgende stappen uit om het project bij te werken:

* Als u de Configuration Designer wilt openen, klikt u met de rechter muisknop op `config.xml`.
* Selecteer het tabblad **platformen** .
* Selecteer in het tekstvak **CORDOVA cli** de optie **6.1.1**. 
* Als u het project wilt bijwerken, selecteert u **Build**en selecteert u **Build Solution**.

#### <a name="install-the-push-plugin"></a>De push-invoeg toepassing installeren

Apache Cordova-toepassingen verwerken niet systeem eigen apparaten of netwerk mogelijkheden.  Deze mogelijkheden worden geboden door invoeg toepassingen die zijn gepubliceerd op [NPM][10] of op github. De `phonegap-plugin-push`-invoeg toepassing verwerkt netwerk push meldingen.

U kunt de push-invoeg toepassing op een van de volgende manieren installeren:

**Vanaf de opdracht prompt:**

Voer de volgende opdracht uit:

    cordova plugin add phonegap-plugin-push

**Vanuit Visual Studio:**

1. Open het bestand `config.xml` in Solution Explorer. Selecteer vervolgens **invoeg toepassingen** > **aangepast**. Selecteer vervolgens **Git** als de installatie bron.

2. Geef `https://github.com/phonegap/phonegap-plugin-push` op als bron.

    ![Open het bestand config. XML in Solution Explorer][img1]

3. Selecteer de pijl naast de installatie bron.

4. Als u in **SENDER_ID**al een numerieke project-id hebt voor het Google developer console-project, kunt u deze hier toevoegen. Als dat niet het geval is, voert u een waarde voor de tijdelijke aanduiding in, zoals 777777. Als u Android als doel hebt, kunt u deze waarde later bijwerken in het bestand config. XML.

    >[!NOTE]
    >Vanaf versie 2.0.0 moet Google-services. json worden geïnstalleerd in de hoofdmap van uw project om de afzender-ID te configureren. Zie de [installatie documentatie](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md) voor meer informatie.

5. Selecteer **Toevoegen**.

De push-invoeg toepassing is nu geïnstalleerd.

#### <a name="install-the-device-plugin"></a>De invoeg toepassing voor apparaten installeren

Volg dezelfde procedure die u hebt gebruikt voor het installeren van de push-invoeg toepassing. Voeg de invoeg toepassing voor het apparaat toe vanuit de lijst core-invoeg toepassingen. (Als u het wilt zoeken, selecteert u **Plugins** > **kern**.) U hebt deze invoeg toepassing nodig om de platform naam op te halen.

#### <a name="register-your-device-when-the-application-starts"></a>Uw apparaat registreren wanneer de toepassing wordt gestart 

In eerste instantie bevatten we een minimale code voor Android. Later kunt u de App wijzigen zodat deze op iOS of Windows 10 wordt uitgevoerd.

1. Een aanroep aan **registerForPushNotifications** toevoegen tijdens het aanroepen van het aanmeldings proces. U kunt deze ook toevoegen aan de onderkant van de methode **onDeviceReady** :

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    In dit voor beeld ziet u het aanroepen van **registerForPushNotifications** nadat de verificatie is geslaagd. U kunt `registerForPushNotifications()` aanroepen zo vaak als nodig is.

2. Voeg de nieuwe methode **registerForPushNotifications** als volgt toe:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. Android Vervang in de voor gaande code `Your_Project_ID` door de numerieke project-ID voor uw app in de [Google-ontwikkelaars console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Beschrijving De app configureren en uitvoeren op Android

Voltooi deze sectie om Push meldingen in te scha kelen voor Android.

#### <a name="enable-gcm"></a>Firebase Cloud Messa ging inschakelen

Omdat u in eerste instantie het Google Android-platform doel hebt, moet u Firebase Cloud Messa ging inschakelen.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>De back-end van de mobiele app configureren voor het verzenden van push-aanvragen via FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Uw Cordova-app voor Android configureren

Open **config. XML**in uw Cordova-app. Vervang `Your_Project_ID` door de numerieke project-ID voor uw app in de [Google-ontwikkelaars console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Open **index. js**. Werk vervolgens de code bij om uw numerieke project-ID te gebruiken.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Uw Android-apparaat configureren voor USB-fout opsporing

Voordat u uw toepassing kunt implementeren op uw Android-apparaat, moet u USB-fout opsporing inschakelen. Voer de volgende stappen uit op uw Android-telefoon:

1. Ga naar **instellingen** > **over de telefoon**. Tik vervolgens op het **buildnummer** totdat de ontwikkelaars modus is ingeschakeld (ongeveer zeven keer).
2. Terug in **instellingen** > **Opties voor ontwikkel aars**, Schakel **USB-fout opsporing**in. Verbind vervolgens uw Android-telefoon met uw ontwikkel computer met een USB-kabel.

We hebben dit getest met behulp van een Google Nexus 5X-apparaat met Android 6,0 (Marshmallow). De technieken zijn echter gebruikelijk in alle moderne Android-versies.

#### <a name="install-google-play-services"></a>Google Play Services installeren

De push-invoeg toepassing is afhankelijk van Android Google Play Services voor push meldingen.

1. Selecteer in Visual Studio **extra** > **Android**- > **Android SDK Manager**. Vouw vervolgens de map **extra's** uit. Schakel de juiste selectie vakjes in om ervoor te zorgen dat elk van de volgende Sdk's is geïnstalleerd:

   * Android 2,3 of hoger
   * Google repository revisie 27 of hoger
   * Google Play Services 9.0.2 of hoger

2. Selecteer **pakketten installeren**. Wacht totdat de installatie is voltooid.

De huidige vereiste bibliotheken worden weer gegeven in de [PhoneGap-invoeg toepassing-push installatie documentatie][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Push meldingen testen in de app op Android

U kunt nu Push meldingen testen door de app uit te voeren en items in de tabel TodoItem in te voegen. U kunt testen vanaf hetzelfde apparaat of vanaf een tweede apparaat, op voor waarde dat u dezelfde back-end gebruikt. Test uw Cordova-app op het Android-platform op een van de volgende manieren:

* *Op een fysiek apparaat:* Koppel uw Android-apparaat aan uw ontwikkel computer met een USB-kabel.  Selecteer **apparaat**in plaats van **Google Android-Emulator**. Visual Studio implementeert de toepassing op het apparaat en voert de toepassing uit. U kunt vervolgens met de toepassing werken op het apparaat.

  Toepassingen voor scherm delen, zoals [Mobizen][20] , kunnen u helpen bij het ontwikkelen van Android-toepassingen. Mobizen projecten uw Android-scherm naar een webbrowser op uw PC.

* *Op een Android-Emulator:* Er zijn aanvullende configuratie stappen vereist wanneer u een emulator gebruikt.

    Zorg ervoor dat u implementeert op een virtueel apparaat waarvoor Google Api's als doel is ingesteld, zoals wordt weer gegeven in het Android Virtual Device (AVD) Manager.

    ![Virtuele Android-Apparaatbeheer](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Als u een snellere x86-emulator wilt gebruiken, [installeert u het HAXM-stuur programma][11]en configureert u de emulator om het te gebruiken.

    Voeg een Google-account toe aan het Android-apparaat door **apps**te selecteren  > **instellingen** > **account toevoegen**. Volg vervolgens de aanwijzingen.

    ![Een Google-account toevoegen aan het Android-apparaat](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Voer de ToDoList-app net als voorheen uit en voeg een nieuw TODO-item toe. Deze keer verschijnt een meldings pictogram in het systeemvak. U kunt de meldings lade openen om de volledige tekst van de melding weer te geven.

    ![Melding weer geven](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Beschrijving Configureren en uitvoeren op iOS

Deze sectie is voor het uitvoeren van het Cordova-project op iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overs Laan.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>De iOS-agent voor externe builds installeren en uitvoeren op een Mac-of Cloud service

Voordat u een Cordova-app in iOS kunt uitvoeren met behulp van Visual Studio, volgt u de stappen in de [IOS-installatie handleiding][12] om de agent voor extern bouwen te installeren en uit te voeren.

Zorg ervoor dat u de app voor iOS kunt bouwen. De stappen in de installatie handleiding zijn vereist voor het bouwen van de app voor iOS vanuit Visual Studio. Als u geen Mac hebt, kunt u voor iOS bouwen met behulp van de Remote build agent voor een service zoals MacInCloud. Zie [uw IOS-app uitvoeren in de Cloud][21]voor meer informatie.

> [!NOTE]
> Xcode 7 of hoger is vereist voor het gebruik van de push-invoeg toepassing op iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>De ID zoeken die moet worden gebruikt als uw app-ID

Voordat u uw app voor push meldingen registreert, opent u config. XML in uw Cordova-app, zoekt u de kenmerk waarde `id` in het widget-element en kopieert u deze voor later gebruik. De ID van de volgende XML-code is `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Gebruik deze id later wanneer u een app-ID maakt op de ontwikkelaars portal van Apple. Als u een andere app-ID in de ontwikkelaars Portal maakt, moet u verderop in deze zelf studie enkele extra stappen uitvoeren. De ID in het widget element moet overeenkomen met de App-ID in de ontwikkelaars Portal.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>De app voor push meldingen registreren op de ontwikkelaars portal van Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Bekijk een video van vergelijkbare stappen](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Azure configureren voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Controleer of de App-ID overeenkomt met uw Cordova-app

Als de App-ID die u hebt gemaakt in uw Apple Developer-account al overeenkomt met de ID van het widget-element in het bestand config. XML, kunt u deze stap overs Laan. Als de Id's echter niet overeenkomen, voert u de volgende stappen uit:

1. Verwijder de map platforms uit uw project.
2. Verwijder de map plugins uit uw project.
3. Verwijder de map node_modules uit uw project.
4. Werk het kenmerk id van het element widget in het bestand config. XML bij om de App-ID te gebruiken die u hebt gemaakt in uw Apple Developer-account.
5. Bouw het project opnieuw op.

##### <a name="test-push-notifications-in-your-ios-app"></a>Push meldingen in uw iOS-app testen

1. Zorg ervoor dat in Visual Studio **IOS** is geselecteerd als het implementatie doel. Selecteer vervolgens **apparaat** om de push meldingen uit te voeren op uw aangesloten IOS-apparaat.

    U kunt de push meldingen uitvoeren op een iOS-apparaat dat is verbonden met uw PC met iTunes. De iOS-simulator biedt geen ondersteuning voor push meldingen.

2. Selecteer de knop **uitvoeren** of **F5** in Visual Studio om het project te bouwen en de app te starten in een IOS-apparaat. Selecteer **OK** om Push meldingen te accepteren.

   > [!NOTE]
   > De app vraagt om bevestiging van push meldingen tijdens de eerste uitvoering.

3. Typ een taak in de app en selecteer vervolgens het plus teken **(+)** .
4. Controleer of er een melding is ontvangen. Selecteer **OK** om de melding te negeren.

## <a name="optional-configure-and-run-on-windows"></a>Beschrijving Configureren en uitvoeren in Windows

In deze sectie wordt beschreven hoe u het Apache Cordova-app-project uitvoert op Windows 10-apparaten (de push-invoeg toepassing PhoneGap wordt ondersteund op Windows 10). Als u niet met Windows-apparaten werkt, kunt u deze sectie overs Laan.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Uw Windows-app registreren voor push meldingen met WNS

Als u de archief opties in Visual Studio wilt gebruiken, selecteert u een Windows-doel in de lijst met oplossingen platforms, zoals **Windows-x64** of **Windows-x86**. (Vermijd **Windows-AnyCPU** voor push meldingen.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Bekijk een video van vergelijkbare stappen][13]

#### <a name="configure-the-notification-hub-for-wns"></a>De notification hub configureren voor WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Uw Cordova-app configureren voor ondersteuning van Windows-push meldingen

Open de Configuration Designer door met de rechter muisknop te klikken op **config. XML**. Selecteer vervolgens **Designer weer geven**. Selecteer vervolgens het tabblad **Windows** en selecteer vervolgens **Windows 10** onder Windows- **doel versie**.

Als u push meldingen in uw standaard builds (debug) wilt ondersteunen, opent u het bestand **Build. json** . Kopieer de ' release ' configuratie vervolgens naar de configuratie van de fout opsporing.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Na de update moet het bestand **Build. json** de volgende code bevatten:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Bouw de app en controleer of er geen fouten zijn. Uw client-app moet nu worden geregistreerd voor de meldingen van de Mobile Apps back-end. Herhaal deze sectie voor elk Windows-project in uw oplossing.

#### <a name="test-push-notifications-in-your-windows-app"></a>Push meldingen in uw Windows-app testen

Zorg ervoor dat in Visual Studio een Windows-platform is geselecteerd als het implementatie doel, zoals **Windows-x64** of **Windows-x86**. Als u de app wilt uitvoeren op een Windows 10-PC die als host fungeert voor Visual Studio, kiest u **lokale computer**.

1. Selecteer de knop **uitvoeren** om het project te bouwen en de app te starten.

2. Typ in de app een naam voor een nieuwe todoitem en selecteer vervolgens het plus teken **(+)** om deze toe te voegen.

Controleer of er een melding wordt ontvangen wanneer het item wordt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Notification hubs][17] voor push meldingen.
* Als u dit nog niet hebt gedaan, gaat u verder met de zelf studie door verificatie toe te [voegen][14] aan uw Apache Cordova-app.

Meer informatie over het gebruik van de volgende Sdk's:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
