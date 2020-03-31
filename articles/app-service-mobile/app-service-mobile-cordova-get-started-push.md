---
title: Pushmeldingen toevoegen aan een Apache Cordova-app
description: Meer informatie over het gebruik van mobiele apps om pushmeldingen naar uw Apache Cordova-app te verzenden.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461603"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Pushmeldingen toevoegen aan uw Apache Cordova-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [Quickstart-project van Apache Cordova,][5] zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quickstartserverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie [Werken met de .NET back-end server SDK voor mobiele apps voor][1]meer informatie.

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u een Apache Cordova-toepassing hebt die is ontwikkeld met Visual Studio 2015. Dit apparaat moet draaien op Google Android Emulator, een Android-apparaat, een Windows-apparaat of een iOS-apparaat.

Voor deze zelfstudie hebt u het volgende nodig:

* Een pc met [Visual Studio Community 2015][2] of hoger
* [Visual Studio Tools voor Apache Cordova][4]
* Een [actief Azure-account][3]
* Een voltooid [Apache Cordova quickstart][5] project
* (Android) Een [Google-account][6] met een geverifieerd e-mailadres
* (iOS) Een [Apple Developer Program-lidmaatschap][7] en een iOS-apparaat (iOS Simulator biedt geen ondersteuning voor pushmeldingen)
* (Windows) Een [Microsoft Store-ontwikkelaarsaccount][8] en een Windows 10-apparaat

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Een Notification Hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Bekijk een video met stappen in deze sectie][9].

## <a name="update-the-server-project"></a>Het serverproject bijwerken

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>Uw Cordova-app wijzigen

Om ervoor te zorgen dat uw Apache Cordova-app-project klaar is om pushmeldingen te verwerken, installeert u de Cordova-pushplug-in plus platformspecifieke pushservices.

#### <a name="update-the-cordova-version-in-your-project"></a>Werk de Cordova-versie in uw project bij.

Als uw project een versie van Apache Cordova gebruikt die eerder is dan versie 6.1.1, werkt u het clientproject bij. Ga als volgt te werk om het project bij te werken:

* Als u de configuratieontwerper `config.xml`wilt openen, klikt u met de rechtermuisknop op .
* Selecteer het tabblad **Platforms.**
* Selecteer **6.1.1**in het tekstvak **Cordova CLI** . 
* Als u het project wilt bijwerken, selecteert u **Bouwen**en selecteert u **Oplossing bouwen**.

#### <a name="install-the-push-plugin"></a>De pushplug-in installeren

Apache Cordova-toepassingen verwerken niet native apparaat- of netwerkmogelijkheden.  Deze mogelijkheden worden geleverd door plug-ins die worden gepubliceerd op [npm][10] of op GitHub. De `phonegap-plugin-push` plugin verwerkt pushmeldingen in het netwerk.

U de push-plugin op een van de volgende manieren installeren:

**Vanuit de opdrachtprompt:**

Voer de volgende opdracht uit:

    cordova plugin add phonegap-plugin-push

**Vanuit Visual Studio:**

1. Open het `config.xml` bestand in Solution Explorer. Selecteer vervolgens **Aangepast plug-ins** > **.** Selecteer **vervolgens Git** als installatiebron.

2. Voer `https://github.com/phonegap/phonegap-plugin-push` in als bron.

    ![Het bestand config.xml openen in Solution Explorer][img1]

3. Selecteer de pijl naast de installatiebron.

4. Als **u in SENDER_ID**al een numerieke project-id hebt voor het Google Developer Console-project, u deze hier toevoegen. Voer anders een tijdelijke aanduidingswaarde in, zoals 777777. Als u zich op Android richt, u deze waarde later bijwerken in het config.xml-bestand.

    >[!NOTE]
    >Vanaf versie 2.0.0 moet google-services.json worden geïnstalleerd in de hoofdmap van uw project om de afzender-id te configureren. Zie voor meer informatie de [installatiedocumentatie.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Selecteer **Toevoegen**.

De push plugin is nu geïnstalleerd.

#### <a name="install-the-device-plugin"></a>De apparaatplug-in installeren

Volg dezelfde procedure die u hebt gebruikt om de push-plugin te installeren. Voeg de apparaatplug-in toe in de lijst Core-plug-ins. (Selecteer **Plugins** > **Core**om deze te vinden.) Je hebt deze plugin nodig om de naam van het platform te verkrijgen.

#### <a name="register-your-device-when-the-application-starts"></a>Uw apparaat registreren wanneer de toepassing wordt gestart 

In eerste instantie bevatten we een minimale code voor Android. Later u de app wijzigen om te draaien op iOS of Windows 10.

1. Voeg een oproep toe om **ForPushNotifications** te registreren tijdens de callback voor het aanmeldingsproces. U het ook onder aan de **methode onDeviceReady** toevoegen:

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

    In dit voorbeeld wordt het **oproepregisterForPushNotifications** weergegeven nadat de verificatie is geslaagd. U kunt `registerForPushNotifications()` zo vaak bellen als nodig is.

2. Voeg als volgt de nieuwe **methode registerForPushNotifications** toe:

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
3. (Android) Vervang in de vorige `Your_Project_ID` code de numerieke project-id voor uw app vanuit de [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Optioneel) De app configureren en uitvoeren op Android

Vul deze sectie in om pushmeldingen voor Android in te schakelen.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>Firebase Cloud-berichten inschakelen

Aangezien u zich in eerste instantie op het Google Android-platform richt, moet u Firebase Cloud Messaging inschakelen.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>De back-end van de mobiele app configureren om pushverzoeken te verzenden met FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Uw Cordova-app configureren voor Android

Open **config.xml**in uw Cordova-app. Vervang `Your_Project_ID` vervolgens de numerieke project-id voor uw app vanuit de [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

**Index.js openen**. Werk vervolgens de code bij om uw numerieke project-ID te gebruiken.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Uw Android-apparaat configureren voor USB-foutopsporing

Voordat u uw toepassing implementeren op uw Android-apparaat, moet u USB-foutopsporing inschakelen. Volg de volgende stappen op je Android-telefoon:

1. Ga naar **Instellingen** > **Over telefoon**. Tik vervolgens op **het buildnummer** totdat de ontwikkelaarsmodus is ingeschakeld (ongeveer zeven keer).
2. Terug in **instellingen** > **ontwikkelaarsopties**, inschakelen **USB-foutopsporing**. Sluit vervolgens je Android-telefoon aan op je ontwikkel-pc met een USB-kabel.

We hebben dit getest met behulp van een Google Nexus 5X-apparaat met Android 6.0 (Marshmallow). Echter, de technieken zijn gebruikelijk in elke moderne Android-release.

#### <a name="install-google-play-services"></a>Google Play Services installeren

De pushplugin is afhankelijk van Android Google Play Services voor pushmeldingen.

1. Selecteer in Visual Studio De optie **Extra** > **Android** > **Android SDK Manager**. Vouw vervolgens de map **Extra's** uit. Schakel de juiste vakjes in om ervoor te zorgen dat elk van de volgende SDK's is geïnstalleerd:

   * Android 2.3 of hoger
   * Google Repository revisie 27 of hoger
   * Google Play Services 9.0.2 of hoger

2. Selecteer **Pakketten installeren**. Wacht dan tot de installatie is voltooid.

De huidige vereiste bibliotheken worden vermeld in de documentatie van de [installatievan phonegap-plugin-push.][19]

#### <a name="test-push-notifications-in-the-app-on-android"></a>Test pushmeldingen in de app op Android

U nu pushmeldingen testen door de app uit te voeren en items in de todoItem-tabel in te voegen. U testen vanaf hetzelfde apparaat of vanaf een tweede apparaat, zolang u dezelfde back-end gebruikt. Test je Cordova-app op het Android-platform op een van de volgende manieren:

* *Op een fysiek apparaat:* Bevestig je Android-apparaat met een USB-kabel op je ontwikkelcomputer.  In plaats van **Google Android Emulator,** selecteert u **Apparaat**. Visual Studio implementeert de toepassing op het apparaat en voert de toepassing uit. U vervolgens communiceren met de toepassing op het apparaat.

  Toepassingen voor het delen van schermen zoals [Mobizen][20] kunnen u helpen bij het ontwikkelen van Android-toepassingen. Mobizen projecteert uw Android-scherm naar een webbrowser op uw pc.

* *Op een Android emulator:* Er zijn extra configuratiestappen vereist wanneer u een emulator gebruikt.

    Zorg ervoor dat u implementeert op een virtueel apparaat waarop Google API's als doel zijn ingesteld, zoals wordt weergegeven in de AVD-manager (Android Virtual Device).

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Als u een snellere x86-emulator wilt gebruiken, [installeert u het HAXM-stuurprogramma][11]en configureert u de emulator om deze te gebruiken.

    Een Google-account toevoegen aan het Android-apparaat door > **Apps-instellingen toe** > **te voegen Voeg account toe.** **Apps** Volg dan de aanwijzingen.

    ![Een Google-account toevoegen aan het Android-apparaat](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Voer de todolist-app uit zoals voorheen en voeg een nieuw todo-item in. Deze keer wordt een meldingspictogram weergegeven in het meldingsgebied. U de meldingsla openen om de volledige tekst van de melding weer te geven.

    ![Melding weergeven](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Optioneel) Configureren en uitvoeren op iOS

Deze sectie is voor het uitvoeren van het Cordova-project op iOS-apparaten. Als u niet met iOS-apparaten werkt, u deze sectie overslaan.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>De iOS remote build-agent installeren en uitvoeren op een Mac- of cloudservice

Voordat u een Cordova-app op iOS uitvoeren met Visual Studio, gaat u de stappen in de [iOS-installatiehandleiding][12] doorlopen om de remote build-agent te installeren en uit te voeren.

Zorg ervoor dat je de app voor iOS bouwen. De stappen in de installatiehandleiding zijn vereist voor het bouwen van de app voor iOS vanuit Visual Studio. Als u geen Mac hebt, u bouwen voor iOS met behulp van de remote build-agent op een service als MacInCloud. Zie [Uw iOS-app uitvoeren in de cloud][21]voor meer informatie.

> [!NOTE]
> Xcode 7 of hoger is vereist om de push-plugin op iOS te gebruiken.

#### <a name="find-the-id-to-use-as-your-app-id"></a>De id zoeken die u als app-id wilt gebruiken

Voordat u uw app registreert voor pushmeldingen, opent u config.xml in uw Cordova-app, zoekt u de `id` kenmerkwaarde in het widgetelement en kopieert u deze voor later gebruik. In de volgende XML `io.cordova.myapp7777777`is de ID .

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Gebruik deze id later wanneer u een app-id maakt op de ontwikkelaarsportal van Apple. Als u een andere app-id maakt op de ontwikkelaarsportal, moet u later in deze zelfstudie een paar extra stappen nemen. De id in het widgetelement moet overeenkomen met de app-id op de ontwikkelaarsportal.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>De app registreren voor pushmeldingen op de ontwikkelaarsportal van Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Bekijk een video van vergelijkbare stappen](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Azure configureren om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Controleren of uw app-id overeenkomt met uw Cordova-app

Als de app-id die u in uw Apple Developer-account hebt gemaakt, al overeenkomt met de id van het widgetelement in het bestand config.xml, u deze stap overslaan. Als de i-adressen echter niet overeenkomen, neemt u de volgende stappen:

1. Verwijder de map platforms uit uw project.
2. Verwijder de map plug-ins uit uw project.
3. Verwijder de node_modules map uit uw project.
4. Werk het id-kenmerk van het widgetelement in het bestand config.xml bij om de app-id te gebruiken die u hebt gemaakt in uw Apple-ontwikkelaarsaccount.
5. Bouw uw project opnieuw op.

##### <a name="test-push-notifications-in-your-ios-app"></a>Pushmeldingen testen in je iOS-app

1. Controleer in Visual Studio of **iOS** is geselecteerd als implementatiedoel. Selecteer vervolgens **Apparaat** om de pushmeldingen uit te voeren op uw aangesloten iOS-apparaat.

    U de pushmeldingen uitvoeren op een iOS-apparaat dat is verbonden met uw pc met iTunes. De iOS Simulator ondersteunt geen pushmeldingen.

2. Selecteer de knop **Uitvoeren** of **F5** in Visual Studio om het project te bouwen en start de app op een iOS-apparaat. Selecteer vervolgens **OK** om pushmeldingen te accepteren.

   > [!NOTE]
   > De app vraagt bevestiging voor pushmeldingen tijdens de eerste run.

3. Typ in de app een taak en selecteer het pluspictogram **(+).**
4. Controleer of er een melding is ontvangen. Selecteer vervolgens **OK** om de melding te verwijderen.

## <a name="optional-configure-and-run-on-windows"></a>(Optioneel) Windows configureren en uitvoeren

In dit gedeelte wordt beschreven hoe u het Apache Cordova-app-project uitvoert op Windows 10-apparaten (de PhoneGap-pushplug-in wordt ondersteund op Windows 10). Als u niet met Windows-apparaten werkt, u deze sectie overslaan.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Uw Windows-app registreren voor pushmeldingen met WNS

Als u de Store-opties in Visual Studio wilt gebruiken, selecteert u een Windows-doel in de lijst Oplossingsplatforms, zoals **Windows-x64** of **Windows-x86.** (Vermijd **Windows-AnyCPU** voor pushmeldingen.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Bekijk een video van vergelijkbare stappen][13]

#### <a name="configure-the-notification-hub-for-wns"></a>De meldingshub voor WNS configureren

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Uw Cordova-app configureren om Windows-pushmeldingen te ondersteunen

Open de configuratieontwerper door met de rechtermuisknop op **config.xml**te klikken . Selecteer vervolgens **Ontwerper weergeven**. Selecteer vervolgens het tabblad **Windows** en selecteer **Vervolgens Windows 10** onder **Windows-doelversie**.

Als u pushmeldingen wilt ondersteunen in uw standaard -foutopsporingsbuilds, opent u het **bestand build.json.** Kopieer vervolgens de configuratie 'release' naar uw foutopsporingsconfiguratie.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Na de update moet het **build.json-bestand** de volgende code bevatten:

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

Bouw de app en controleer of er geen fouten zijn. Uw client-app moet zich nu registreren voor de meldingen van de back-end van Mobiele apps. Herhaal dit gedeelte voor elk Windows-project in uw oplossing.

#### <a name="test-push-notifications-in-your-windows-app"></a>Pushmeldingen testen in uw Windows-app

Controleer in Visual Studio of een Windows-platform is geselecteerd als implementatiedoel, zoals **Windows-x64** of **Windows-x86.** Als u de app wilt uitvoeren op een Windows 10-pc die Visual Studio host, kiest u **Local Machine**.

1. Selecteer de knop **Uitvoeren** om het project te bouwen en start de app.

2. Typ in de app een naam voor een nieuw todoitem en selecteer het **pluspictogram (+)** om het toe te voegen.

Controleer of er een melding wordt ontvangen wanneer het item wordt toegevoegd.

## <a name="next-steps"></a><a name="next-steps"></a>Volgende stappen

* Lees meer over [meldingenhubs][17] voor meer informatie over pushmeldingen.
* Als u dit nog niet hebt gedaan, gaat u verder met de zelfstudie door [verificatie toe][14] te voegen aan uw Apache Cordova-app.

Meer informatie over het gebruik van de volgende SDK's:

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
