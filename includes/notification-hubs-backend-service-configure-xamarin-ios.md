---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c13b7ee8c5c0a0d302e4822047ea60f9df120bf8
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112083"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Info.plist en Entitlements.plist configureren

1. Zorg ervoor dat u bent aangemeld bij uw **Apple Developer Account** in voorkeuren voor **Visual Studio** >  **...**  > **Publiceren** > **Apple Developer Accounts** en het juiste  *Certificaat* en *Inrichtingsprofiel* is gedownload. U moet deze assets hebben gemaakt als onderdeel van de vorige stappen.

1. Open in **PushDemo.iOS** **Info.plist** en zorg ervoor dat de **BundleIdentifier** overeenkomt met de waarde die is gebruikt voor het respectieve inrichtingsprofiel in de [Apple Developer Portal](https://developer.apple.com). De **BundleIdentifier** bevindt zich in de indeling ``com.<organization>.PushDemo``.

1. Stel in hetzelfde bestand **Minimale systeemversie** in op **13.0**.

    > [!NOTE]
    > Alleen apparaten met **iOS 13.0 en hoger** worden ondersteund voor deze zelfstudie, maar u kunt deze ook uitbreiden om apparaten te ondersteunen waarop oudere versies worden uitgevoerd.

1. Open de **Projectopties** voor **PushDemo. iOS** (dubbelklik op het project).

1. In **Projectopties**, onder **Bouwen > iOS-bundel ondertekenen**, controleert u of uw ontwikkelaarsaccount is geselecteerd onder **Team**. Zorg er vervolgens voor dat 'Ondertekening automatisch beheren' is geselecteerd en uw certificaat voor ondertekening en het profiel voor inrichting automatisch zijn geselecteerd.

    > [!NOTE]
    > Als uw *handtekeningscertificaat* en *inrichtingsprofiel* niet automatisch zijn geselecteerd, kiest u **Handmatige inrichting** en klikt u vervolgens op **Ondertekeningsopties voor bundel**. Zorg ervoor dat uw *Team* is geselecteerd voor **Handtekeningsidentiteit** en dat uw op *PushDemo* gerichte inrichtingsprofiel is geselecteerd voor **Inrichtingsprofiel** voor zowel de configuraties **Fouten opsporen** en **Release** die ervoor zorgen dat **iPhone** is geselecteerd voor het **Platform**  in beide gevallen.

1. Open in **PushDemo.iOS-** **Entitlements.plist** en zorg ervoor dat **Pushmeldingen inschakelen** is geselecteerd wanneer dit wordt weer gegeven op het tabblad **Rechten**. Zorg er vervolgens voor dat de instelling **APS-omgeving** is ingesteld op **ontwikkeling** wanneer deze wordt weergegeven op het tabblad **Bron**.

### <a name="handle-push-notifications-for-ios"></a>Pushmeldingen voor iOS verwerken

1. **Beheren** + **Klik** op het project **PushDemo.iOS**, kies **Nieuwe map** in het menu **Toevoegen** en klik vervolgens op **Toevoegen** met *Services* als de **Naam van de map**.

1. **Beheren** + **Klik** in de map **Services** en kies vervolgens **Nieuw bestand...** vanuit het menu **Toevoegen**.

1. Selecteer **Algemeen** > **Lege klasse**, voer *DeviceInstallationService.cs* in voor de **Naam** en klik vervolgens op **Nieuw** om de volgende implementatie toe te voegen.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Deze klasse biedt een unieke id (met behulp van de waarde [UIDevice. IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12)) en de Notification Hub-registratiepayload.

1. Voeg een nieuwe map toe aan het project **PushDemo. iOS** met de naam *Extensies*. Voeg vervolgens een **Lege klasse** toe aan die map met de naam *NSDataExtensions.cs* met de volgende implementatie.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. Zorg ervoor dat in **AppDelegate.cs** de volgende naamruimten aan de bovenkant van het bestand zijn toegevoegd.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Voeg een constante toe voor de token-cachesleutel van het apparaat.

    ```csharp
    const string CachedDeviceToken = "cached_device_token";
    ```

1. Voeg persoonlijke eigenschappen en hun respectieve back-upvelden toe om een verwijzing naar de implementaties **IPushDemoNotificationActionService**, **INotificationRegistrationService** en **IDeviceInstallationService** op te slaan.

    ```csharp
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
    ```

1. Voeg de methode **RegisterForRemoteNotifications** toe om instellingen voor gebruikersmeldingen te registreren en vervolgens voor externe meldingen met **APNS**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Voeg de methode **CompleteRegistrationAsync** toe om de waarde van de eigenschap `IDeviceInstallationService.Token` in te stellen. Vernieuw de registratie en sla het apparaat in de cache op als het is bijgewerkt sinds de laatste keer dat deze is opgeslagen.

    ```csharp
    async Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();

        var cachedToken = await SecureStorage.GetAsync(CachedDeviceToken)
            .ConfigureAwait(false);

        if (!string.IsNullOrWhiteSpace(cachedToken) &&
            cachedToken.Equals(DeviceInstallationService.Token))
            return;

        await NotificationRegistrationService.RefreshRegistrationAsync()
            .ConfigureAwait(false);

        await SecureStorage.SetAsync(CachedDeviceToken, DeviceInstallationService.Token)
            .ConfigureAwait(false);
    }
    ```

1. Voeg de methode **ProcessNotificationActions** voor het verwerken van de meldingsgegevens **NSDictionary** en het voorwaardelijk aanroepen van **NotificationActionService.TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Overschrijf de methode **RegisteredForRemoteNotifications** door het argument **deviceToken** door te geven aan de methode **CompleteRegistrationAsync**.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Overschrijf de methode **ReceivedRemoteNotification** door het argument **userInfo** door te geven aan de methode **ProcessNotificationActions**.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Overschrijf de methode **FailedToRegisterForRemoteNotifications** om de fout te registreren.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Dit is een tijdelijke aanduiding. U moet de juiste logboekregistratie en foutafhandeling implementeren voor productiescenario's.

1. Werk de methode **FinishedLaunching** bij om `Bootstrap.Begin` aan te roepen nadat de aanroep naar `Forms.Init` is door gegeven in de platformspecifieke implementatie van **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. In dezelfde methode vraagt u voorwaardelijke autorisatie aan en registreert u zich onmiddellijk na `Bootstrap.Begin` voor externe meldingen.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. In **FinishedLaunching** roept u **ProcessNotificationActions** onmiddellijk aan na het aanroepen van `LoadApplication` als het argument **opties** de **UIApplication.LaunchOptionsRemoteNotificationKey** heeft doorgegeven in het resulterende object **userInfo**.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
