---
title: Pushmeldingen verzenden naar Xamarin.iOS-apps met Azure Notification Hubs | Microsoft Docs
description: In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Xamarin.iOS-toepassing.
services: notification-hubs
keywords: ios-pushmeldingen,pushberichten,pushmeldingen,pushbericht
documentationcenter: xamarin
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 7427421719b44839e766234194640817ea686e3c
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213580"
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Zelfstudie: Pushmeldingen verzenden naar Xamarin.iOS-apps met Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs pushmeldingen verzendt naar een iOS-toepassing. U maakt een lege Xamarin.iOS-app die pushmeldingen ontvangt met de [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Als u klaar bent, kunt u de Notification Hub gebruiken om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd. De voltooide code is beschikbaar in het [Notification hubs-app][GitHub] -voor beeld.

In deze zelfstudie gaat u code maken of bijwerken om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Het bestand met de aanvraag voor certificaatondertekening genereren
> * Uw app voor pushmeldingen registreren
> * Een inrichtingsprofiel voor de app maken
> * De Notification Hub configureren voor iOS-pushmeldingen
> * Testpushmeldingen verzenden

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Meest recente versie van [Xcode][Install Xcode]
* Een apparaat dat compatibel is met iOS 10 (of een hogere versie)
* [Apple Developer Program](https://developer.apple.com/programs/)-lidmaatschap
* [Visual Studio voor Mac]
  
  > [!NOTE]
  > Vanwege configuratievereisten voor iOS-pushmeldingen moet u de voorbeeldtoepassing op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de simulator.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor Xamarin.iOS-apps.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Uw app verbinden met de Notification Hub

### <a name="create-a-new-project"></a>Een nieuw project maken

1. Maak in Visual Studio een nieuw iOS-project, selecteer de sjabloon **Single View App** (Toepassing met één weergave) en klik op **Volgende**.

     ![Visual Studio - Toepassingstype selecteren][31]

2. Voer de app-naam en organisatie-id in, klik op **Volgende** en vervolgens op **Maken**

3. Dubbelklik in de oplossingsweergave op *Into.plist* en zorg ervoor dat bij **Identiteit** de bundel-id overeenkomt met de id die werd gebruikt bij het maken van uw inrichtingsprofiel. Controleer onder **Ondertekening** of uw Developer-account is geselecteerd bij **Team**, of 'Ondertekening automatisch beheren' is geselecteerd en uw certificaat voor ondertekening en het profiel voor inrichting automatisch zijn geselecteerd.

    ![Visual Studio - iOS-app configureren][32]

4. Dubbel klik in de oplossings weergave op het `Entitlements.plist` en zorg ervoor dat **Push meldingen inschakelen** is ingeschakeld.

    ![Visual Studio - iOS-rechten configureren][33]

5. Voeg het Azure Messaging-pakket toe. Klik in de oplossingsweergave met de rechtermuisknop op het project en selecteer **Toevoegen** > **NuGet-pakketten toevoegen**. Zoek naar **Xamarin.Azure.NotificationHubs.iOS** en voeg het pakket toe aan uw project.

6. Voeg een nieuw bestand toe aan de klasse en geef deze de naam `Constants.cs`. Voeg de volgende variabelen toe en vervang de tijdelijke aanduidingen voor tekenreeksen door de `hubname` en `DefaultListenSharedAccessSignature` die u eerder hebt genoteerd.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. Voeg in `AppDelegate.cs` de volgende using-instructie toe:

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. Declareer een instantie van `SBNotificationHub`:

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. In `AppDelegate.cs` werkt u `FinishedLaunching()` zo bij dat deze overeenkomt met de volgende code:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound,
                                                                    (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
        } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                    new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        } else {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
        }

        return true;
    }
    ```

10. In `AppDelegate.cs` overschrijft u de methode `RegisteredForRemoteNotifications()`:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAllAsync (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. In `AppDelegate.cs` overschrijft u de methode `ReceivedRemoteNotification()`:

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. In `AppDelegate.cs` maakt u de methode `ProcessNotification()`:

    ```csharp
    void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
    {
        // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
        if (null != options && options.ContainsKey(new NSString("aps")))
        {
            //Get the aps dictionary
            NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;

            //Extract the alert text
            // NOTE: If you're using the simple alert by just specifying
            // "  aps:{alert:"alert msg here"}  ", this will work fine.
            // But if you're using a complex alert with Localization keys, etc.,
            // your "alert" object from the aps dictionary will be another NSDictionary.
            // Basically the JSON gets dumped right into a NSDictionary,
            // so keep that in mind.
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //If this came from the ReceivedRemoteNotification while the app was running,
            // we of course need to manually process things like the sound, badge, and alert.
            if (!fromFinishedLaunching)
            {
                //Manually show an alert
                if (!string.IsNullOrEmpty(alert))
                {
                    UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                    avAlert.Show();
                }
            }
        }
    }
    ```

    > [!NOTE]
    > U kunt ervoor kiezen `FailedToRegisterForRemoteNotifications()` te overschrijven om situaties zoals het ontbreken van een netwerkverbinding af te handelen. Dit is vooral belangrijk wanneer de gebruiker de toepassing in de offlinemodus start (bijvoorbeeld in een vliegtuig) en u scenario's voor het verzenden van pushberichten wilt afhandelen die specifiek zijn voor uw app.

13. Voer de app uit op uw apparaat.

## <a name="send-test-push-notifications"></a>Testpushmeldingen verzenden

U kunt ontvangst van meldingen in uw app testen met de optie *Test verzenden* in [Azure-portal]. Er wordt dan een pushmelding als test naar uw apparaat verzonden.

![Azure Portal - Test verzenden][30]

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Apps of ASP.NET verzonden met een compatibele bibliotheek. U kunt de REST API ook rechtstreeks gebruiken om meldingsberichten te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meldingen uitgezonden naar al uw iOS-apparaten die zijn geregistreerd bij de back-end. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke iOS-apparaten, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio voor Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure-portal]: https://portal.azure.com
