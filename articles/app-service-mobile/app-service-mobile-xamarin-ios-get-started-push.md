---
title: Push meldingen toevoegen aan uw Xamarin. iOS-app met Azure App Service
description: Meer informatie over het gebruik van Azure App Service voor het verzenden van push meldingen naar uw Xamarin. iOS-app
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5f968de25226528cb7aa016a4c301d07e53f2695
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388395"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Push meldingen toevoegen aan uw Xamarin. iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het [Xamarin. IOS Quick Start](app-service-mobile-xamarin-ios-get-started.md) -project, zodat een push melding wordt verzonden naar het apparaat wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start Server-project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Snelstartgids voor Xamarin. IOS](app-service-mobile-xamarin-ios-get-started.md) .
* Een fysiek iOS-apparaat. Push meldingen worden niet ondersteund door de iOS-Simulator.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>De app voor push meldingen registreren op de ontwikkelaars portal van Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Uw mobiele app configureren voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Het server project bijwerken voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Uw Xamarin. iOS-project configureren

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Pushmeldingen toevoegen aan uw app

1. Voeg in **QSTodoService**de volgende eigenschap toe, zodat **AppDelegate** de mobiele client kan verkrijgen:

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

2. Voeg de volgende instructie `using` toe boven aan het **AppDelegate.cs** -bestand.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Vervang in **AppDelegate**de gebeurtenis **FinishedLaunching** :

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

4. Vervang in hetzelfde bestand de gebeurtenis `RegisteredForRemoteNotifications`. In deze code registreert u voor een eenvoudige sjabloon melding die door de server wordt verzonden over alle ondersteunde platforms.

    Zie voor meer informatie over sjablonen met Notification Hubs [sjablonen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Vervang vervolgens de gebeurtenis **DidReceivedRemoteNotification** :

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

Uw app is nu bijgewerkt zodat push meldingen worden ondersteund.

## <a name="test"></a>Push meldingen in uw app testen

1. Druk op de knop **uitvoeren** om het project te bouwen en de app te starten in een apparaat dat geschikt is voor IOS en klik vervolgens op **OK** om Push meldingen te accepteren.

   > [!NOTE]
   > U moet expliciet push meldingen van uw app accepteren. Deze aanvraag vindt alleen de eerste keer dat de app wordt uitgevoerd.

2. Typ een taak in de app en klik vervolgens op het plus teken ( **+** ).
3. Controleer of er een melding is ontvangen en klik vervolgens op **OK** om de melding te negeren.
4. Herhaal stap 2 en sluit de app onmiddellijk en controleer vervolgens of er een melding wordt weer gegeven.

U hebt deze zelf studie voltooid.
