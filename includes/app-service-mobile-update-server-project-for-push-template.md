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
In deze sectie werkt u code bij in uw bestaande back-endproject voor mobiele apps om een pushmelding te verzenden telkens wanneer een nieuw item wordt toegevoegd. Dit proces wordt aangedreven door de [sjabloonfunctie](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) van Azure Notification Hubs, waarmee pushen tussen platforms mogelijk zijn. De verschillende clients worden geregistreerd voor pushmeldingen met behulp van sjablonen, en een enkele universele push kan naar alle clientplatforms.

Kies een van de volgende procedures die&mdash;overeenkomen met uw back-end projecttype [.NET back-end](#dotnet) of [Node.js back-end](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>.NET back-end project

1. Klik in Visual Studio met de rechtermuisknop op het serverproject. Selecteer vervolgens **NuGet-pakketten beheren**. Zoek `Microsoft.Azure.NotificationHubs`naar en selecteer **Installeren**. Dit proces installeert de Library Notification Hubs voor het verzenden van meldingen vanaf de back-end.
2. Open **controllers** > in het serverproject**TodoItemController.cs**. Voeg vervolgens het volgende toe met behulp van instructies:

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

    Met dit proces wordt een sjabloonmelding weergegeven die het item bevat. Tekst wanneer een nieuw item wordt ingevoegd.

4. Publiceer het serverproject opnieuw.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Node.js back-end project

1. Stel je backend project in.
2. Vervang de bestaande code in todoitem.js door de volgende code:

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

    Met dit proces wordt een sjabloonmelding gestuurd die de item.text bevat wanneer een nieuw item wordt ingevoegd.

3. Wanneer u het bestand op uw lokale computer bewerkt, publiceert u het serverproject opnieuw.
