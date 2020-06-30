---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85111999"
---
### <a name="validate-package-name-and-permissions"></a>Pakketnaam en machtigingen valideren

1. Open in **PushDemo.Android** **Projectopties** en vervolgens **Android-toepassing** in het gedeelte **Build**.

1. Controleer of de **pakketnaam** overeenkomt met de waarde die u hebt gebruikt in het project **PushDemo** in de [Firebase-console](https://console.firebase.google.com). De **pakketnaam** heeft de indeling ``com.<organization>.pushdemo``.

1. Stel **Minimumversie van Android** in op **Android 8.0 (API-niveau 26)** en de **Doelversie van Android** op het meest recente **API-niveau**.

    > [!NOTE]
    > Alleen apparaten met **API-niveau 26 en hoger** worden ondersteund voor deze zelfstudie, maar u kunt deze ook uitbreiden om apparaten te ondersteunen waarop oudere versies worden uitgevoerd.

1. Zorg ervoor dat de machtigingen **INTERNET** en **READ_PHONE_STATE** zijn ingeschakeld onder **Vereiste machtigingen**.

1. Klik op **OK**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Voeg de Xamarin Google Play Services Base- en Xamarin.Firebase.Messaging-pakketten toe

1. Klik in **PushDemo.Android**, **Beheren** + **op** de map **Pakketten** en kies vervolgens **NuGet-pakketten beheren...** .

1. Zoek naar **Xamarin.GooglePlayServices.Base** (niet **Basement**) en controleer of deze is ingeschakeld.

1. Zoek naar **Xamarin.Firebase.Messaging** en controleer of deze is ingeschakeld.

1. Klik op **Pakketten toevoegen** en klik vervolgens op **Accepteren** wanneer u wordt gevraagd om de **licentievoorwaarden** te accepteren.

### <a name="add-the-google-services-json-file"></a>Voeg het JSON-bestand van Google Services toe

1. **Beheren** + **Klik** op het `PushDemo.Android`-project en kies vervolgens **Bestaand bestand...** in het menu **Toevoegen**.

1. Kies het bestand *google-services.json* dat u eerder hebt gedownload toen u het **PushDemo**-project in de [Firebase-console](https://console.firebase.google.com) instelde en klik op **Openen**.

1. Kies **Het bestand kopiëren naar de map** als hierom wordt gevraagd.

1. **Beheren** + **Klik** in het `PushDemo.Android`-project op het bestand *google-services.json* en controleer vervolgens of **GoogleServicesJson** is ingesteld als de **Build-actie**.

### <a name="handle-push-notifications-for-android"></a>Pushmeldingen voor Android verwerken

1. **Beheren** + **Klik** op het project `PushDemo.Android`, kies **Nieuwe map** in het menu **Toevoegen** en klik vervolgens op **Toevoegen**. Gebruik *Services* als de **Naam van de map**.

1. **Beheren** + **Klik** op de map **Services** en kies vervolgens **Nieuw bestand...** vanuit het menu **Toevoegen**.

1. Selecteer **Algemeen** > **Lege klasse**, voer *DeviceInstallationService.cs* in voor de **Naam** en klik vervolgens op **Nieuw** om de volgende implementatie toe te voegen.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Deze klasse biedt een unieke id (met [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) als onderdeel van de registratie-payload voor de meldingshub.

1. Voeg nog een **Lege klasse** toe aan de map **Services** met de naam *PushNotificationFirebaseMessagingService.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. Zorg ervoor dat in **MainActivity.cs** de volgende naamruimten bovenaan het bestand zijn toegevoegd.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. Stel in **MainActivity.cs** de **LaunchMode** in op **SingleTop** zodat **MainActivity** niet opnieuw wordt gemaakt als deze wordt geopend.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Voeg persoonlijke eigenschappen en hun bijbehorende back-upvelden toe om een verwijzing naar de implementaties **IPushNotificationActionService** en **IDeviceInstallationService** op te slaan.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implementeer de **IOnSuccessListener**-interface om het **Firebase**-token op te halen en op te slaan.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Voeg een nieuwe methode toe met de naam **ProcessNotificationActions**. Hiermee wordt gecontroleerd of een bepaalde **Intentie** een extra waarde heeft met de naam *actie*. Activeer deze actie voorwaardelijk met behulp van de implementatie van **IPushDemoNotificationActionService**.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Overschrijf de methode **OnNewIntent** om de methode **CheckIntentForNotificationActions** aan te roepen.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Omdat de **LaunchMode** voor de **activiteit** op **SingleTop** is ingesteld, wordt er een **intentie** verzonden naar het bestaande **activiteit**-exemplaar via de methode **OnNewIntent**, in plaats van via de methode **OnCreate**. U moet dus een binnenkomende intentie afhandelen in zowel **OnCreate** als **OnNewIntent**.

1. Werk de methode **OnCreate** bij om `Bootstrap.Begin` aan te roepen nadat de aanroep naar `base.OnCreate` is doorgegeven in de platformspecifieke implementatie van **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Roep in dezelfde methode **GetInstanceId** voorwaardelijk aan in het **FirebaseApp**-exemplaar, direct na het aanroepen van `Bootstrap.Begin`. Voeg **MainActivity** daarbij toe als de **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Blijf in **OnCreate** en roep **ProcessNotificationActions** aan zodra de aanroep van `LoadApplication` aan de huidige **intentie** is doorgegeven.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> U moet de app steeds opnieuw registreren wanneer u deze uitvoert en foutopsporingssessies stoppen om pushmeldingen te blijven ontvangen.
