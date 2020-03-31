---
title: Pushmeldingen toevoegen aan je Xamarin.iOS-app
description: Meer informatie over het gebruik van Azure App Service om pushmeldingen naar uw Xamarin.iOS-app te verzenden.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249280"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Pushmeldingen toevoegen aan je Xamarin.iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [quick start-project Xamarin.iOS,](app-service-mobile-xamarin-ios-get-started.md) zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quick start-serverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Snelstarttutorial Xamarin.iOS.](app-service-mobile-xamarin-ios-get-started.md)
* Een fysiek iOS-apparaat. Pushmeldingen worden niet ondersteund door de iOS-simulator.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>De app registreren voor pushmeldingen op de ontwikkelaarsportal van Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Uw mobiele app configureren om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Het serverproject bijwerken om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Uw Xamarin.iOS-project configureren

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Pushmeldingen toevoegen aan uw app

1. Voeg in **QSTodoService**de volgende eigenschap toe zodat **AppDelegate** de mobiele client kan aanschaffen:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Voeg de `using` volgende instructie toe aan de bovenkant van het **AppDelegate.cs** bestand.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Overschrijven in **AppDelegate**de gebeurtenis **FinishedLaunching:**

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Overschrijven in hetzelfde `RegisteredForRemoteNotifications` bestand de gebeurtenis. In deze code registreert u zich voor een eenvoudige sjabloonmelding die door de server op alle ondersteunde platforms wordt verzonden.

    Zie Sjablonen voor meer informatie over sjablonen met [meldingshubs.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Overschrijven van de gebeurtenis **DidReceivedRemoteNotification:**

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Uw app is nu bijgewerkt om pushmeldingen te ondersteunen.

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Pushmeldingen testen in uw app

1. Druk op de knop **Uitvoeren** om het project te bouwen en start de app op een apparaat dat geschikt is voor iOS en klik vervolgens op **OK** om pushmeldingen te accepteren.

   > [!NOTE]
   > U moet expliciet pushmeldingen van uw app accepteren. Deze aanvraag vindt alleen plaats wanneer de app voor het eerst wordt uitgevoerd.

2. Typ in de app een taak en**+** klik op het pluspictogram ( )
3. Controleer of er een melding is ontvangen en klik op **OK** om de melding te verwijderen.
4. Herhaal stap 2 en sluit de app onmiddellijk en controleer of er een melding wordt weergegeven.

U hebt deze zelfstudie met succes voltooid.
