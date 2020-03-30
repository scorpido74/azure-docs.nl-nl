---
title: Pushmeldingen toevoegen aan uw UWP-app
description: Meer informatie over het gebruik van Azure App Service Mobile Apps en Azure Notification Hubs om pushmeldingen te verzenden naar uw UWP-app (Universal Windows Platform).
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6f200e9649a00bfe890d46f86e62404f1a7e844f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366287"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Pushmeldingen toevoegen aan uw Windows-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie voegt u pushmeldingen toe aan het [Windows-project voor snelstarten,](app-service-mobile-windows-store-dotnet-get-started.md) zodat er elke keer dat een record wordt ingevoegd een pushmelding naar het apparaat wordt verzonden.

Als u het gedownloade quick start-serverproject niet gebruikt, hebt u het uitbreidingspakket voor pushmeldingen nodig. Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) voor meer informatie.

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Een meldingshub configureren

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

U moet uw app indienen bij de Microsoft Store en vervolgens uw serverproject configureren om te integreren met [Windows Push Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) om push te verzenden.

1. Klik in Visual Studio Solution Explorer met de rechtermuisknop op het project UWP-app en klik op **App** > **voor inassociëren opslaan in de Store...**.

    ![App koppelen aan Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. Klik in de wizard op **Volgende,** meld u aan met uw Microsoft-account, typ een naam voor uw app in **Een nieuwe app-naam behouden**en klik vervolgens op **Reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de nieuwe app-naam, klikt u op **Volgende**en klikt u vervolgens op **Koppelen**. Hiermee worden de vereiste Registratiegegevens van de Microsoft Store toegevoegd aan het toepassingsmanifest.
4. Navigeer naar de [portal voor toepassingsregistratie](https://apps.dev.microsoft.com/) en meld u aan met uw Microsoft-account. Klik op de Windows Store-app die u in de vorige stap hebt gekoppeld.
5. Noteer op de registratiepagina de waarde onder **Toepassingsgeheimen** en het **pakket SID**, die u vervolgens gebruikt om uw mobiele app backend te configureren.

    ![App koppelen aan Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Het clientgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden met niemand en distribueer ze niet met uw app. De **toepassings-id** wordt gebruikt met het geheim om microsoft-accountverificatie te configureren.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) heeft ook instructies voor het configureren van UWP-apps voor pushmeldingen.

## <a name="configure-the-backend-to-send-push-notifications"></a>De backend configureren om pushmeldingen te verzenden

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="update-the-server-to-send-push-notifications"></a><a id="update-service"></a>De server bijwerken om pushmeldingen te verzenden

Gebruik de onderstaande procedure die&mdash;overeenkomt met uw backend-projecttype [.NET backend](#dotnet) of [Node.js backend](#nodejs).

### <a name="net-backend-project"></a><a name="dotnet"></a>.NET backend project

1. Klik in Visual Studio met de rechtermuisknop op het serverproject en klik op **NuGet-pakketten beheren**, zoek naar Microsoft.Azure.NotificationHubs en klik vervolgens op **Installeren**. Hiermee wordt de clientbibliotheek Van Notification Hubs geïnstalleerd.
2. Vouw **controllers**uit, open TodoItemController.cs en voeg de volgende instructies toe:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Voeg in de **methode PostTodoItem** de volgende code toe na de aanroep naar **InsertAsync:**

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

    Deze code vertelt de meldingshub om een pushmelding te verzenden nadat een nieuw item is ingebracht.

4. Publiceer het serverproject opnieuw.

### <a name="nodejs-backend-project"></a><a name="nodejs"></a>Node.js backend project
1. Stel je backend project in.
2. Vervang de bestaande code in het bestand todoitem.js als volgt:

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

    Hiermee wordt een WNS-pop-upmelding gestuurd die de item.text bevat wanneer een nieuw todo-item wordt ingevoegd.

3. Wanneer u het bestand op uw lokale computer bewerkt, publiceert u het serverproject opnieuw.

## <a name="add-push-notifications-to-your-app"></a><a id="update-app"></a>Pushmeldingen toevoegen aan uw app
Vervolgens moet uw app zich registreren voor pushmeldingen bij het opstarten. Als u verificatie al hebt ingeschakeld, moet u ervoor zorgen dat de gebruiker zich aanmeldt voordat u zich probeert te registreren voor pushmeldingen.

1. Open het **App.xaml.cs-projectbestand** `using` en voeg de volgende instructies toe:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Voeg in hetzelfde bestand de volgende **initNotificationsAsync-methodedefinitie** toe aan de klasse **App:**

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

    Met deze code haalt u de ChannelURI voor de app op van WNS en registreert deze ChannelURI vervolgens met uw mobiele app voor app-service.

3. Voeg boven aan de gebeurtenishandler **OnLaunched** in **App.xaml.cs**de **async-modifier** toe aan de methodedefinitie en voeg de volgende aanroep toe aan de nieuwe **InitNotificationsAsync-methode,** zoals in het volgende voorbeeld:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Dit garandeert dat de kortstondige ChannelURI wordt geregistreerd elke keer dat de aanvraag wordt gelanceerd.

4. Uw UWP-app-project opnieuw opbouwen. Uw app is nu gereed om pop-upmeldingen te ontvangen.

## <a name="test-push-notifications-in-your-app"></a><a id="test"></a>Pushmeldingen testen in uw app

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="next-steps"></a><a id="more"></a>Volgende stappen

Meer informatie over pushmeldingen:

* [De beheerde client gebruiken voor Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Sjablonen bieden u flexibiliteit om pushs en gelokaliseerde pushes te verzenden. Meer informatie over het registreren van sjablonen.
* [Problemen met pushmeldingen diagnosticeren](../notification-hubs/notification-hubs-push-notification-fixer.md) Er zijn verschillende redenen waarom meldingen kunnen worden verwijderd of niet op apparaten terechtkomen. In dit onderwerp ziet u hoe u de hoofdoorzaak van pushmeldingsfouten analyseren en achterhalen.

Overweeg verder te gaan naar een van de volgende tutorials:

* [Verificatie toevoegen aan uw app](app-service-mobile-windows-store-dotnet-get-started-users.md) Lees hoe u gebruikers van uw app verifieert met een id-provider.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Meer informatie over het toevoegen van offline ondersteuning voor uw app met behulp van een back-end van de mobiele app. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
