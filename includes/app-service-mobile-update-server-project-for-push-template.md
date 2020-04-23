---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857366"
---
In deze sectie werkt u de code in uw bestaande Mobile Apps back-end-project bij om een push melding te verzenden wanneer een nieuw item wordt toegevoegd. Dit proces wordt ingeschakeld door de [sjabloon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) functie van Azure notification hubs, waarmee platformoverschrijdende pushes mogelijk zijn. De verschillende clients zijn geregistreerd voor push meldingen met behulp van sjablonen en één universele push kan alle client platforms ophalen.

Kies een van de volgende procedures die overeenkomen met uw back-end&mdash;-project, de [.net-back-end](#dotnet) of de [node. js-back-end](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>.NET-back-end-project

1. Klik in Visual Studio met de rechter muisknop op het server project. Selecteer vervolgens **NuGet-pakketten beheren**. Zoek naar `Microsoft.Azure.NotificationHubs`en selecteer **installeren**. Met dit proces wordt de Notification Hubs-bibliotheek geïnstalleerd voor het verzenden van meldingen van de back-end.
2. Open in het server project **controllers** > **TodoItemController.cs**. Voeg vervolgens de volgende using-instructies toe:

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

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

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

    Dit proces verzendt een sjabloon melding die het item bevat. Tekst wanneer een nieuw item wordt ingevoegd.

4. Publiceer het server project opnieuw.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Node. js-back-end-project

1. Stel uw back-end-project in.
2. Vervang de bestaande code in todoitem. js door de volgende code:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Dit proces verzendt een sjabloon melding die het item. tekst bevat wanneer een nieuw item wordt ingevoegd.

3. Wanneer u het bestand op uw lokale computer bewerkt, publiceert u het server project opnieuw.
