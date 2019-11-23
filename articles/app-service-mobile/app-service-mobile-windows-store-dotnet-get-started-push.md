---
title: Push meldingen toevoegen aan uw Universeel Windows-platform-app (UWP) | Microsoft Docs
description: Meer informatie over het gebruik van Azure App Service Mobile Apps en Azure Notification Hubs voor het verzenden van push meldingen naar uw Universeel Windows-platform-app (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 80723bc8e7685c66117e71d87cbf64710ab4507a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388630"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Push meldingen toevoegen aan uw Windows-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht

In deze zelf studie voegt u push meldingen toe aan het [Windows Quick Start](app-service-mobile-windows-store-dotnet-get-started.md) -project, zodat een push melding wordt verzonden naar het apparaat telkens wanneer een record wordt ingevoegd.

Als u het gedownloade Quick Start Server-project niet gebruikt, hebt u het uitbreidings pakket voor push meldingen nodig. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) voor meer informatie.

## <a name="configure-hub"></a>Een notification hub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

U moet uw app indienen bij de Microsoft Store en vervolgens uw server project configureren voor integratie met [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) voor het verzenden van push berichten.

1. Klik in Visual Studio Solution Explorer met de rechter muisknop op het UWP-app-project, klikt u op **Store** > **App koppelen aan de Store...** .

    ![App aan Microsoft Store koppelen](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. Klik in de wizard op **volgende**, Meld u aan met uw Microsoft-account, typ een naam voor uw app in **reserve een nieuwe app-naam**en klik vervolgens op **reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de naam van de nieuwe app, klikt u op **volgende**en klikt u vervolgens op **koppelen**. Hiermee voegt u de vereiste Microsoft Store registratie gegevens toe aan het toepassings manifest.
4. Ga naar de [Portal voor toepassings registratie](https://apps.dev.microsoft.com/) en meld u aan met uw Microsoft-account. Klik op de Windows Store-app die u in de vorige stap hebt gekoppeld.
5. Noteer op de pagina registratie de waarde onder **toepassings geheimen** en de **pakket-sid**, die u vervolgens kunt gebruiken om de back-end van uw mobiele app te configureren.

    ![App aan Microsoft Store koppelen](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Het clientgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden met niemand en distribueer ze niet met uw app. De **toepassings-id** wordt met het geheim gebruikt voor het configureren van micro soft-account authenticatie.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) heeft ook instructies voor het configureren van UWP-apps voor push meldingen.

## <a name="configure-the-backend-to-send-push-notifications"></a>De back-end configureren voor het verzenden van push meldingen

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>De server bijwerken om Push meldingen te verzenden

Gebruik de onderstaande procedure die overeenkomt met uw back-end-project type&mdash;[.net-back-end](#dotnet) of [node. js-back-end](#nodejs).

### <a name="dotnet"></a>.NET-back-upproject

1. Klik in Visual Studio met de rechter muisknop op het server project en klik op **NuGet-pakketten beheren**, zoek naar micro soft. Azure. notification hubs en klik vervolgens op **installeren**. Hiermee wordt de Notification Hubs-client bibliotheek geïnstalleerd.
2. Vouw **controllers**uit, open TodoItemController.cs en voeg de volgende using-instructies toe:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. In de methode **PostTodoItem** voegt u de volgende code toe na het aanroepen van **InsertAsync**:

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Met deze code wordt de notification hub geïnformeerd over het verzenden van een push melding nadat een nieuw item is ingevoegd.

4. Publiceer het server project opnieuw.

### <a name="nodejs"></a>Node. js-back-upproject
1. Stel uw back-end-project in.
2. Vervang de bestaande code in het bestand todoitem. js door het volgende:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Hiermee verzendt u een pop-upmelding WNS die het item. Text bevat wanneer een nieuw item wordt ingevoegd.

3. Wanneer u het bestand op uw lokale computer bewerkt, publiceert u het server project opnieuw.

## <a id="update-app"></a>Push meldingen toevoegen aan uw app
Vervolgens moet uw app registreren voor push meldingen bij het opstarten. Wanneer u verificatie al hebt ingeschakeld, moet u ervoor zorgen dat de gebruiker zich aanmeldt voordat u probeert te registreren voor push meldingen.

1. Open het project bestand **app.xaml.cs** en voeg de volgende `using`-instructies toe:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Voeg in hetzelfde bestand de volgende **InitNotificationsAsync** -methode definitie toe aan de **app** -klasse:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Met deze code wordt de kanaal voor de app opgehaald uit WNS en wordt vervolgens geregistreerd dat kanaal met uw mobiele App Service-app.

3. Voeg aan de bovenkant van de gebeurtenis-handler **OnLaunched** in **app.xaml.cs**de wijzigings functie **async** toe aan de methode definitie en voeg de volgende aanroep toe aan de nieuwe **InitNotificationsAsync** -methode, zoals in het volgende voor beeld:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Dit zorgt ervoor dat de tijdelijke kanaal wordt geregistreerd telkens wanneer de toepassing wordt gestart.

4. Bouw uw UWP-app-project opnieuw op. Uw app is nu gereed om pop-upmeldingen te ontvangen.

## <a id="test"></a>Push meldingen in uw app testen

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Volgende stappen

Meer informatie over push meldingen:

* [De beheerde client voor Azure-Mobile Apps gebruiken](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Sjablonen bieden u de mogelijkheid om platformoverschrijdende pushes en gelokaliseerde pushes te verzenden. Meer informatie over het registreren van sjablonen.
* [Problemen met push meldingen vaststellen](../notification-hubs/notification-hubs-push-notification-fixer.md) Er zijn verschillende redenen waarom meldingen verloren kunnen gaan of niet eindigen op apparaten. In dit onderwerp wordt uitgelegd hoe u de hoofd oorzaak van mislukte push meldingen kunt analyseren en berekenen.

Overweeg door te gaan met een van de volgende zelf studies:

* [Verificatie toevoegen aan uw app](app-service-mobile-windows-store-dotnet-get-started-users.md) Lees hoe u gebruikers van uw app verifieert met een id-provider.
* [Offline synchronisatie voor uw app inschakelen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Meer informatie over het toevoegen van offline ondersteuning aan uw app met een back-end voor een mobiele app. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
