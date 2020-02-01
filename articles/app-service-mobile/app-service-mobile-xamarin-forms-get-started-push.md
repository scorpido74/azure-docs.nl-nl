---
title: Push meldingen toevoegen aan uw Xamarin. Forms-app
description: Meer informatie over hoe u Azure-Services gebruikt om multi-platform push meldingen te verzenden naar uw Xamarin. Forms-apps.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fc05763046da365e7770a9b208100e0366062f25
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898866"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Push meldingen toevoegen aan uw Xamarin. Forms-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan alle projecten die voortkomen uit de [Xamarin. formulieren snel starten](app-service-mobile-xamarin-forms-get-started.md). Dit betekent dat een push melding wordt verzonden naar alle platformoverschrijdende clients telkens wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start Server-project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voor iOS hebt u een [Apple Developer Program-lidmaatschap](https://developer.apple.com/programs/ios/) en een fysiek IOS-apparaat nodig. De [IOS-simulator biedt geen ondersteuning voor push meldingen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Een notification hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Het server project bijwerken voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Het Android-project configureren en uitvoeren (optioneel)

Voltooi deze sectie om Push meldingen in te scha kelen voor het Xamarin. Forms Droid-project voor Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messa ging (FCM) inschakelen

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>De Mobile Apps-back-end configureren voor het verzenden van push-aanvragen met behulp van FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Push meldingen toevoegen aan het Android-project

Als de back-end is geconfigureerd met FCM, kunt u onderdelen en codes aan de client toevoegen om te registreren bij FCM. U kunt zich ook registreren voor push meldingen met Azure Notification Hubs via de Mobile Apps back-end en meldingen ontvangen.

1. Klik in het project **Droid** met de rechter muisknop op **Naslag informatie > NuGet-pakketten beheren...** .
1. Zoek in het venster NuGet package manager naar het pakket **Xamarin. Firebase. Messa ging** en voeg dit toe aan het project.
1. In de project eigenschappen van het project **Droid** stelt u de app in op het compileren met Android versie 7,0 of hoger.
1. Voeg het bestand **Google-services. json** , dat is gedownload van de Firebase-console, toe aan de hoofdmap van het **Droid** -project en stel de build-actie in op **GoogleServicesJson**. Zie [het JSON-bestand van Google services toevoegen](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)voor meer informatie.

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging registreren

1. Open het bestand **AndroidManifest.xml** en voeg de volgende `<receiver>`-elementen toe aan het element `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>De Firebase instance ID-service implementeren

1. Voeg een nieuwe klasse toe aan het **Droid** -project met de naam `FirebaseRegistrationService`en zorg ervoor dat de volgende `using`-instructies boven aan het bestand worden weer gegeven:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Vervang de lege `FirebaseRegistrationService`-klasse door de volgende code:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    De klasse `FirebaseRegistrationService` is verantwoordelijk voor het genereren van beveiligings tokens die de toepassing machtigen om toegang te krijgen tot FCM. De `OnTokenRefresh` methode wordt aangeroepen wanneer de toepassing een registratie token van FCM ontvangt. Met de methode wordt het token opgehaald uit de eigenschap `FirebaseInstanceId.Instance.Token`, dat asynchroon wordt bijgewerkt door FCM. De `OnTokenRefresh` methode wordt niet regel matig aangeroepen, omdat het token alleen wordt bijgewerkt wanneer de toepassing wordt geïnstalleerd of verwijderd, wanneer de gebruiker toepassings gegevens verwijdert, wanneer de toepassing de exemplaar-ID wist of wanneer de beveiliging van het token is aangetast. Daarnaast wordt door de FCM instance ID-service gevraagd of de toepassing het token regel matig vernieuwt, meestal elke 6 maanden.

    De methode `OnTokenRefresh` roept ook de methode `SendRegistrationTokenToAzureNotificationHub` aan, die wordt gebruikt om het registratie token van de gebruiker te koppelen aan de Azure notification hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Registreren bij de Azure notification hub

1. Voeg een nieuwe klasse toe aan het **Droid** -project met de naam `AzureNotificationHubService`en zorg ervoor dat de volgende `using`-instructies boven aan het bestand worden weer gegeven:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Vervang de lege `AzureNotificationHubService`-klasse door de volgende code:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    Met de methode `RegisterAsync` maakt u een eenvoudige sjabloon voor een meldings bericht als JSON en registreert u voor het ontvangen van sjabloon meldingen van de notification hub met behulp van het Firebase-registratie token. Dit zorgt ervoor dat alle meldingen die worden verzonden vanuit de Azure notification hub, gericht zijn op het apparaat dat wordt weer gegeven door het registratie token.

#### <a name="displaying-the-contents-of-a-push-notification"></a>De inhoud van een push melding weer geven

1. Voeg een nieuwe klasse toe aan het **Droid** -project met de naam `FirebaseNotificationService`en zorg ervoor dat de volgende `using`-instructies boven aan het bestand worden weer gegeven:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Vervang de lege `FirebaseNotificationService`-klasse door de volgende code:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    De `OnMessageReceived` methode, die wordt aangeroepen wanneer een toepassing een melding van FCM ontvangt, extraheert de inhoud van het bericht en roept de `SendNotification`-methode aan. Met deze methode wordt de bericht inhoud geconverteerd naar een lokale melding die wordt gestart tijdens de uitvoering van de toepassing, met de melding die wordt weer gegeven in het systeemvak.

Nu bent u klaar om Push meldingen te testen in de app die wordt uitgevoerd op een Android-apparaat of de emulator.

### <a name="test-push-notifications-in-your-android-app"></a>Push meldingen testen in uw Android-app

De eerste twee stappen zijn alleen vereist bij het testen van een emulator.

1. Zorg ervoor dat u implementeert voor of fout opsporing op een apparaat of emulator die is geconfigureerd met Google Play Services. Dit kan worden gecontroleerd door te controleren of de **Play** -apps op het apparaat of in de emulator zijn geïnstalleerd.
2. Voeg een Google-account toe aan het Android-apparaat door te klikken op **Apps** > **instellingen** > **account toevoegen**. Volg vervolgens de aanwijzingen om een bestaand Google-account toe te voegen aan het apparaat of om een nieuwe te maken.
3. Klik in Visual Studio of Xamarin Studio met de rechter muisknop op het **Droid** -project en klik op **instellen als opstart project**.
4. Klik op **uitvoeren** om het project te bouwen en de app te starten op uw Android-apparaat of-emulator.
5. Typ een taak in de app en klik vervolgens op het plus teken ( **+** ).
6. Controleer of er een melding wordt ontvangen wanneer een item wordt toegevoegd.

## <a name="configure-and-run-the-ios-project-optional"></a>Het iOS-project configureren en uitvoeren (optioneel)

Deze sectie gaat over het uitvoeren van het Xamarin iOS-project voor iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overslaan.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>De notification hub configureren voor APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Vervolgens configureert u de iOS-project instelling in Xamarin Studio of Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Push meldingen toevoegen aan uw iOS-app

1. Open AppDelegate.cs in het **IOS** -project en voeg de volgende instructie toe aan het begin van het code bestand.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Voeg in de klasse **AppDelegate** een onderdrukking toe voor de gebeurtenis **RegisteredForRemoteNotifications** om te registreren voor meldingen:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. Voeg in **AppDelegate**ook de volgende onderdrukking toe voor de gebeurtenis-handler **DidReceiveRemoteNotification** :

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Deze methode verwerkt binnenkomende meldingen terwijl de app wordt uitgevoerd.

4. Voeg in de klasse **AppDelegate** de volgende code toe aan de methode **FinishedLaunching** :

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Hiermee wordt ondersteuning geboden voor externe meldingen en aanvragen voor een push registratie.

Uw app is nu bijgewerkt zodat push meldingen worden ondersteund.

#### <a name="test-push-notifications-in-your-ios-app"></a>Push meldingen in uw iOS-app testen

1. Klik met de rechter muisknop op het iOS-project en klik op **instellen als opstart project**.
2. Klik op de knop **uitvoeren** of druk op **F5** in Visual Studio om het project te bouwen en de app te starten in een IOS-apparaat. Klik vervolgens op **OK** om Push meldingen te accepteren.

   > [!NOTE]
   > U moet expliciet push meldingen van uw app accepteren. Deze aanvraag vindt alleen de eerste keer dat de app wordt uitgevoerd.

3. Typ een taak in de app en klik vervolgens op het plus teken ( **+** ).
4. Controleer of er een melding is ontvangen en klik vervolgens op **OK** om de melding te negeren.

## <a name="configure-and-run-windows-projects-optional"></a>Windows-projecten configureren en uitvoeren (optioneel)

Deze sectie is voor het uitvoeren van de Xamarin. Forms WinApp-en WinPhone81-projecten voor Windows-apparaten. Deze stappen bieden ook ondersteuning voor Universeel Windows-platform-projecten (UWP). Als u niet met Windows-apparaten werkt, kunt u deze sectie overslaan.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Uw Windows-app registreren voor push meldingen met Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>De notification hub configureren voor WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Push meldingen toevoegen aan uw Windows-app

1. Open in Visual Studio **app.xaml.cs** in een Windows-project en voeg de volgende-instructies toe.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Vervang `<your_TodoItemManager_portable_class_namespace>` door de naam ruimte van uw draag bare project dat de `TodoItemManager`-klasse bevat.

2. Voeg in App.xaml.cs de volgende **InitNotificationsAsync** -methode toe:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Met deze methode wordt het kanaal voor push meldingen opgehaald en wordt een sjabloon geregistreerd voor het ontvangen van sjabloon meldingen van uw notification hub. Een sjabloon melding die *messageParam* ondersteunt, wordt aan deze client geleverd.

3. Werk in App.xaml.cs de **OnLaunched** -gebeurtenis registratie methode definitie bij door de `async`-modificator toe te voegen. Voeg vervolgens de volgende regel code toe aan het einde van de methode:

    ```csharp
    await InitNotificationsAsync();
    ```

    Dit zorgt ervoor dat de registratie van push meldingen wordt gemaakt of vernieuwd telkens wanneer de app wordt gestart. Het is belang rijk dat u dit doet om te garanderen dat het push kanaal WNS altijd actief is.  

4. Open in Solution Explorer voor Visual Studio het bestand **package. appxmanifest** en stel de **pop-up** in op **Ja** onder **meldingen**.
5. Bouw de app en controleer of er geen fouten zijn. Uw client-app moet nu worden geregistreerd voor de sjabloon meldingen van de Mobile Apps back-end. Herhaal deze sectie voor elk Windows-project in uw oplossing.

#### <a name="test-push-notifications-in-your-windows-app"></a>Push meldingen in uw Windows-app testen

1. Klik in Visual Studio met de rechter muisknop op een Windows-project en klik op **instellen als opstart project**.
2. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten.
3. Typ in de app een naam voor een nieuwe todoitem en klik vervolgens op het plus teken ( **+** ) om deze toe te voegen.
4. Controleer of er een melding wordt ontvangen wanneer het item wordt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

U vindt meer informatie over push meldingen:

* [Push meldingen verzenden vanuit Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messa ging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Externe meldingen met Firebase Cloud Messa ging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Problemen met push meldingen vaststellen](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Er zijn verschillende redenen waarom meldingen verloren kunnen gaan of niet eindigen op apparaten. In dit onderwerp wordt uitgelegd hoe u de hoofd oorzaak van mislukte push meldingen kunt analyseren en berekenen.

U kunt ook door gaan met een van de volgende zelf studies:

* [Verificatie toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ontdek hoe u offlineondersteuning voor uw app toevoegt met behulp van een back-end voor Mobile Apps. Met offline synchronisatie kunnen gebruikers met een mobiele app communiceren&mdash;gegevens weer geven, toevoegen of wijzigen&mdash;zelfs wanneer er geen netwerk verbinding is.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
