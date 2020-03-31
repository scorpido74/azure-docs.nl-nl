---
title: Pushmeldingen toevoegen aan uw App Xamarin.Forms
description: Meer informatie over het gebruik van Azure-services om pushmeldingen met meerdere platforms naar uw Apps van Xamarin.Forms te verzenden.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f23ac2d693492695c398893c103d5a77a0e93129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461467"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Pushmeldingen toevoegen aan uw App Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan alle projecten die zijn voortgekomen uit de snel start van [Xamarin.Forms.](app-service-mobile-xamarin-forms-get-started.md) Dit betekent dat elke keer dat een record wordt ingevoegd een pushmelding naar alle cross-platform clients wordt verzonden.

Als u het gedownloade quick start-serverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie.

## <a name="prerequisites"></a>Vereisten

Voor iOS heb je een [Apple Developer Program-lidmaatschap](https://developer.apple.com/programs/ios/) en een fysiek iOS-apparaat nodig. De [iOS-simulator ondersteunt geen pushmeldingen.](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Een Notification Hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Het serverproject bijwerken om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Het Android-project configureren en uitvoeren (optioneel)

Vul deze sectie in om pushmeldingen in te schakelen voor het Xamarin.Forms Droid-project voor Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Firebase Cloud Messaging (FCM) inschakelen

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>De back-end van mobiele apps configureren om pushverzoeken te verzenden met FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Pushmeldingen toevoegen aan het Android-project

Met de back-end geconfigureerd met FCM, u componenten en codes toevoegen aan de client om te registreren bij FCM. U zich ook registreren voor pushmeldingen bij Azure Notification Hubs via de back-end van Mobiele apps en meldingen ontvangen.

1. Klik in het **Droid-project** met de rechtermuisknop **op Referenties > NuGet-pakketten beheren ...**.
1. Zoek in het venster NuGet Package Manager naar het **Xamarin.Firebase.Messaging-pakket** en voeg het toe aan het project.
1. Stel in de projecteigenschappen voor het **Droid-project** de app in om te compileren met Android-versie 7.0 of hoger.
1. Voeg het **bestand google-services.json,** gedownload van de Firebase-console, toe aan de root van het **Droid-project** en stel de buildactie in op **GoogleServicesJson.** Zie [Het JSON-bestand van Google Services toevoegen](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)voor meer informatie .

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

#### <a name="implementing-the-firebase-instance-id-service"></a>De Firebase Instance ID-service implementeren

1. Voeg een nieuwe klasse toe aan het **Droid-project** met de naam `FirebaseRegistrationService`, en zorg ervoor dat de volgende `using` instructies bovenaan het bestand aanwezig zijn:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Vervang de `FirebaseRegistrationService` lege klasse door de volgende code:

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

    De `FirebaseRegistrationService` klasse is verantwoordelijk voor het genereren van beveiligingstokens die de toepassing toestemming geven om toegang te krijgen tot FCM. De `OnTokenRefresh` methode wordt aangeroepen wanneer de toepassing een registratietoken van FCM ontvangt. De methode haalt het `FirebaseInstanceId.Instance.Token` token op uit de eigenschap, die asynchroon wordt bijgewerkt door FCM. De `OnTokenRefresh` methode wordt zelden aangeroepen, omdat het token alleen wordt bijgewerkt wanneer de toepassing is geïnstalleerd of verwijderd, wanneer de gebruiker toepassingsgegevens verwijdert, wanneer de toepassing de instantie-id wist of wanneer de beveiliging van het token is aangetast. Bovendien zal de FCM Instance ID-service vragen dat de toepassing het token periodiek vernieuwt, meestal om de 6 maanden.

    De `OnTokenRefresh` methode roept `SendRegistrationTokenToAzureNotificationHub` ook de methode op, die wordt gebruikt om het registratietoken van de gebruiker te koppelen aan de Azure Notification Hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Registreren bij de Azure Notification Hub

1. Voeg een nieuwe klasse toe aan het **Droid-project** met de naam `AzureNotificationHubService`, en zorg ervoor dat de volgende `using` instructies bovenaan het bestand aanwezig zijn:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Vervang de `AzureNotificationHubService` lege klasse door de volgende code:

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

    De `RegisterAsync` methode maakt een eenvoudige sjabloon voor meldingsberichten als JSON en registreert om sjabloonmeldingen van de meldingshub te ontvangen met behulp van het Firebase-registratietoken. Dit zorgt ervoor dat meldingen die vanuit de Azure Notification Hub worden verzonden, gericht zijn op het apparaat dat wordt vertegenwoordigd door het registratietoken.

#### <a name="displaying-the-contents-of-a-push-notification"></a>De inhoud van een pushmelding weergeven

1. Voeg een nieuwe klasse toe aan het **Droid-project** met de naam `FirebaseNotificationService`, en zorg ervoor dat de volgende `using` instructies bovenaan het bestand aanwezig zijn:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Vervang de `FirebaseNotificationService` lege klasse door de volgende code:

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
            var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);
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

    De `OnMessageReceived` methode, die wordt aangeroepen wanneer een toepassing een melding van FCM `SendNotification` ontvangt, haalt de inhoud van het bericht uit en roept de methode aan. Met deze methode wordt de berichtinhoud omgezet in een lokale melding die wordt gestart terwijl de toepassing wordt uitgevoerd, waarbij de melding wordt weergegeven in het meldingsgebied.

Nu bent u klaar test pushmeldingen in de app die draait op een Android-apparaat of de emulator.

### <a name="test-push-notifications-in-your-android-app"></a>Pushmeldingen testen in je Android-app

De eerste twee stappen zijn alleen vereist wanneer u test op een emulator.

1. Zorg ervoor dat u een apparaat of emulator implementeert of debugt dat is geconfigureerd met Google Play Services. Dit kan worden geverifieerd door te controleren of de **Play-apps** op het apparaat of de emulator zijn geïnstalleerd.
2. Een Google-account toevoegen aan het Android-apparaat door op**Instellingen** >  **apps** > **te klikken Voeg account toe.** Volg vervolgens de aanwijzingen om een bestaand Google-account aan het apparaat toe te voegen of een nieuw account te maken.
3. Klik in Visual Studio of Xamarin Studio met de rechtermuisknop op het **Droid-project** en klik op **Instellen als opstartproject**.
4. Klik **op Uitvoeren** om het project te bouwen en start de app op uw Android-apparaat of emulator.
5. Typ in de app een taak en**+** klik op het pluspictogram ( )
6. Controleer of er een melding wordt ontvangen wanneer een item wordt toegevoegd.

## <a name="configure-and-run-the-ios-project-optional"></a>Het iOS-project configureren en uitvoeren (optioneel)

Deze sectie gaat over het uitvoeren van het Xamarin iOS-project voor iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overslaan.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>De meldingshub voor APNS configureren

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Vervolgens configureer je de iOS-projectinstelling in Xamarin Studio of Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Pushmeldingen toevoegen aan je iOS-app

1. Open in het **iOS-project** AppDelegate.cs en voeg de volgende instructie toe aan de bovenkant van het codebestand.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Voeg in de klasse **AppDelegate** een overschrijving toe voor de gebeurtenis **RegisteredForRemoteNotifications** om u te registreren voor meldingen:

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

3. Voeg in **AppDelegate**ook de volgende overschrijving toe voor de **gebeurtenishandler DidReceiveRemoteNotification:**

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

    Met deze methode worden binnenkomende meldingen verwerkt terwijl de app wordt uitgevoerd.

4. Voeg in de klasse **AppDelegate** de volgende code toe aan de methode **FinishedLaunching:**

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

    Dit maakt ondersteuning voor externe meldingen en verzoeken push registratie.

Uw app is nu bijgewerkt om pushmeldingen te ondersteunen.

#### <a name="test-push-notifications-in-your-ios-app"></a>Pushmeldingen testen in je iOS-app

1. Klik met de rechtermuisknop op het iOS-project en klik op **Instellen als StartUp-project**.
2. Druk op de knop **Uitvoeren** of **F5** in Visual Studio om het project te bouwen en start de app op een iOS-apparaat. Klik vervolgens op **OK** om pushmeldingen te accepteren.

   > [!NOTE]
   > U moet expliciet pushmeldingen van uw app accepteren. Deze aanvraag vindt alleen plaats wanneer de app voor het eerst wordt uitgevoerd.

3. Typ in de app een taak en**+** klik op het pluspictogram ( )
4. Controleer of er een melding is ontvangen en klik op **OK** om de melding te verwijderen.

## <a name="configure-and-run-windows-projects-optional"></a>Windows-projecten configureren en uitvoeren (optioneel)

Deze sectie is voor het uitvoeren van de Xamarin.Forms WinApp en WinPhone81 projecten voor Windows-apparaten. Deze stappen ondersteunen ook UwP-projecten (Universal Windows Platform). Als u niet met Windows-apparaten werkt, kunt u deze sectie overslaan.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Uw Windows-app registreren voor pushmeldingen bij Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>De meldingshub voor WNS configureren

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Pushmeldingen toevoegen aan uw Windows-app

1. Open in Visual Studio **App.xaml.cs** in een Windows-project en voeg de volgende instructies toe.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Vervang `<your_TodoItemManager_portable_class_namespace>` de naamruimte van uw draagbare `TodoItemManager` project dat de klasse bevat.

2. Voeg in App.xaml.cs de volgende **InitNotificationsAsync-methode** toe:

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

    Met deze methode wordt het pushmeldingskanaal opgehaald en wordt een sjabloon geregistreerd om sjabloonmeldingen van uw meldingshub te ontvangen. Een sjabloonmelding die *messageParam* ondersteunt, wordt aan deze client geleverd.

3. Werk in App.xaml.cs de definitie van de `async` gebeurtenishandler van **OnLaunched** bij door de modifier toe te voegen. Voeg vervolgens de volgende coderegel toe aan het einde van de methode:

    ```csharp
    await InitNotificationsAsync();
    ```

    Dit zorgt ervoor dat de pushmeldingsregistratie wordt gemaakt of vernieuwd telkens wanneer de app wordt gestart. Het is belangrijk om dit te doen om te garanderen dat het WNS-pushkanaal altijd actief is.  

4. Open in Solution Explorer voor Visual Studio het bestand **Package.appxmanifest** en stel **Toast geschikt in op** **Ja** onder **Meldingen**.
5. Bouw de app en controleer of er geen fouten zijn. Uw client-app moet zich nu registreren voor de sjabloonmeldingen van de back-end van Mobiele apps. Herhaal dit gedeelte voor elk Windows-project in uw oplossing.

#### <a name="test-push-notifications-in-your-windows-app"></a>Pushmeldingen testen in uw Windows-app

1. Klik in Visual Studio met de rechtermuisknop op een Windows-project en klik op **Instellen als opstartproject**.
2. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten.
3. Typ in de app een naam voor een nieuw todoitem en klik op het pluspictogram (**+**) om het toe te voegen.
4. Controleer of er een melding wordt ontvangen wanneer het item wordt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over pushmeldingen:

* [Pushmeldingen verzenden vanuit Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Externe meldingen met Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Problemen met pushmeldingen diagnosticeren](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Er zijn verschillende redenen waarom meldingen kunnen worden verwijderd of niet op apparaten terechtkomen. In dit onderwerp ziet u hoe u de hoofdoorzaak van pushmeldingsfouten analyseren en achterhalen.

Je ook doorgaan naar een van de volgende tutorials:

* [Verificatie toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ontdek hoe u offlineondersteuning voor uw app toevoegt met behulp van een back-end voor Mobile Apps. Met offline synchronisatie kunnen gebruikers communiceren&mdash;met een mobiele app&mdash;die gegevens bekijkt, toevoegt of wijzigt, zelfs als er geen netwerkverbinding is.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
