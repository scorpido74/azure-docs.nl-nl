---
title: Bindingen van meldingenhubs voor Azure-functies
description: Meer informatie over het gebruik van Azure Notification Hub-binding in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277282"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Binding van de uitvoer van meldingenhubs voor Azure-functies

In dit artikel wordt uitgelegd hoe u pushmeldingen verzendt met [Azure Notification Hubs-bindingen](../notification-hubs/notification-hubs-push-notification-overview.md) in Azure-functies. Azure Functions ondersteunt uitvoerbindingen voor Meldingshubs.

Azure Notification Hubs moeten zijn geconfigureerd voor de Platform Notifications Service (PNS) die u wilt gebruiken. Zie [Aan de slag met Meldingenhubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) en selecteer uw doelclientplatform in de vervolgkeuzelijst boven aan de pagina voor meer informatie over het ontvangen van pushmeldingen in uw client-app in uw client-app.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google heeft [Google Cloud Messaging (GCM) afgeschaft ten gunste van Firebase Cloud Messaging (FCM).](https://developers.google.com/cloud-messaging/faq) Deze uitvoerbinding ondersteunt FCM niet. Als u meldingen wilt verzenden met FCM, gebruikt u de [Firebase-API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) rechtstreeks in uw functie of gebruikt u [sjabloonmeldingen.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De bindingen van meldingenhubs worden geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet, versie 1.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

Deze binding is niet beschikbaar in functies 2.x en hoger.

## <a name="example---template"></a>Voorbeeld - sjabloon

De meldingen die u verzendt, kunnen native meldingen of [sjabloonmeldingen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)zijn. Native meldingen richten zich op een `platform` specifiek clientplatform zoals geconfigureerd in de eigenschap van de uitvoerbinding. Een sjabloonmelding kan worden gebruikt om meerdere platforms te targeten.   

Zie het taalspecifieke voorbeeld:

* [C#-script - parameter out](#c-script-template-example---out-parameter)
* [C# script - asynchroon](#c-script-template-example---asynchronous)
* [C# script - JSON](#c-script-template-example---json)
* [C#-script - bibliotheektypen](#c-script-template-example---library-types)
* [F #](#f-template-example)
* [Javascript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Voorbeeld van C#-scriptsjabloon - parameter out

In dit voorbeeld wordt een melding `message` verstuurt voor een [sjabloonregistratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die een tijdelijke aanduiding in de sjabloon bevat.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Voorbeeld van c#-scriptsjabloon - asynchroon

Als u asynchrone code gebruikt, zijn uit-parameters niet toegestaan. In dit `IAsyncCollector` geval gebruikt om uw sjabloonmelding terug te sturen. De volgende code is een asynchrone voorbeeld van de bovenstaande code. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C# scriptsjabloon voorbeeld - JSON

In dit voorbeeld wordt een melding `message` verstuurt voor een [sjabloonregistratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die een tijdelijke aanduiding in de sjabloon bevat met behulp van een geldige JSON-tekenreeks.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Voorbeeld van c#-scriptsjabloon - bibliotheektypen

In dit voorbeeld ziet u hoe u typen gebruikt die zijn gedefinieerd in de [Microsoft Azure Notification Hubs-bibliotheek.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Voorbeeld van f#-sjabloon

In dit voorbeeld wordt een melding `location` `message`verstuurt voor een [sjabloonregistratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die en .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Voorbeeld van JavaScript-sjabloon

In dit voorbeeld wordt een melding `location` `message`verstuurt voor een [sjabloonregistratie](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) die en .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Voorbeeld - APNS-native

In dit c#-scriptvoorbeeld ziet u hoe u een native APNS-melding verzendt. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Voorbeeld - WNS-native

In dit c#-scriptvoorbeeld ziet u hoe u typen gebruikt die zijn gedefinieerd in de [Microsoft Azure Notification Hubs-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) om een native WNS-pop-upmelding te verzenden. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

De constructorparameters en -eigenschappen van het kenmerk worden beschreven in de [configuratiesectie.](#configuration)

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `NotificationHub` die u instelt in het *bestand function.json* en het kenmerk:

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** |N.v.t.| Moet ingesteld `notificationHub`zijn op. |
|**direction** |N.v.t.| Moet ingesteld `out`zijn op. | 
|**Naam** |N.v.t.| Variabele naam die wordt gebruikt in functiecode voor het bericht van de meldingshub. |
|**tagExpressie** |**Tagexpression** | Met tagexpressies u opgeven dat meldingen worden geleverd aan een reeks apparaten die zich hebben geregistreerd om meldingen te ontvangen die overeenkomen met de tagexpressie.  Zie [Expressies routeren en taggen](../notification-hubs/notification-hubs-tags-segment-push-message.md)voor meer informatie . |
|**hubNaam** | **HubName** | Naam van de hubbron voor meldingen in de Azure-portal. |
|**verbinding** | **Instelling voor verbindingstekenreeks** | De naam van een app-instelling die een verbindingstekenreeks voor meldingenhubs bevat.  De verbindingstekenreeks moet zijn ingesteld op de waarde *DefaultFullSharedAccessSignature* voor uw meldingshub. Zie [Instelling van verbindingstekenreeksen](#connection-string-setup) later in dit artikel.|
|**Platform** | **Platform** | De eigenschap platform geeft het clientplatform aan dat uw meldingsdoelen target. Als de eigenschap platform standaard wordt weggelaten uit de uitvoerbinding, kunnen sjabloonmeldingen worden gebruikt om elk platform te targeten dat is geconfigureerd op de Azure Notification Hub. Zie [Sjablonen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)voor meer informatie over het gebruik van sjablonen in het algemeen voor het verzenden van cross-platformmeldingen met een Azure Notification Hub. Wanneer het **platform** is ingesteld, moet het platform een van de volgende waarden zijn: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Zie [Pushmeldingen naar iOS verzenden met Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)voor meer informatie over het configureren van de meldingshub voor APNS en het ontvangen van de melding in een client-app.</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Zie Aan de [slag met meldinghubs voor Kindle-apps](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)voor meer informatie over het configureren van de meldingshub voor ADM en het ontvangen van de melding in een Kindle-app.</li><li><code>wns</code>&mdash;[Windows Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) is gericht op Windows-platforms. Windows Phone 8.1 en hoger wordt ook ondersteund door WNS. Zie [Aan de slag met Notification Hubs voor Windows Universal Platform Apps voor](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)meer informatie.</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](/previous-versions/windows/apps/ff402558(v=vs.105)). Dit platform ondersteunt Windows Phone 8 en eerdere Windows Phone-platforms. Zie [Pushmeldingen verzenden met Azure Notification Hubs op Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)voor meer informatie.</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>voorbeeld van function.json-bestand

Hier is een voorbeeld van een binding met meldingshubs in een *function.json-bestand.*

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Instelling van verbindingstekenreeks

Als u een binding voor het uitvoeren van een meldingshub wilt gebruiken, moet u de verbindingstekenreeks voor de hub configureren. U een bestaande meldingshub selecteren of een nieuwe hub maken direct op het tabblad *Integreren* in de Azure-portal. U de verbindingstekenreeks ook handmatig configureren. 

Ga als u de verbindingstekenreeks configureren naar een bestaande meldingshub:

1. Navigeer naar uw meldingshub in de [Azure-portal,](https://portal.azure.com)kies **Toegangsbeleid**en selecteer de knop Kopiëren naast het beleid **DefaultFullSharedAccessSignature.** Hiermee kopieert u de verbindingstekenreeks voor het beleid *DefaultFullSharedAccessSignature* naar uw meldingshub. Met deze verbindingstekenreeks kan uw functie meldingsberichten naar de hub verzenden.
    ![De tekenreeks voor de meldingshub-verbinding kopiëren](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigeer naar uw functie-app in de Azure-portal, kies **Toepassingsinstellingen,** voeg een sleutel toe zoals **MyHubConnectionString,** plak de gekopieerde *DefaultFullSharedAccessSignature* voor uw meldingshub als waarde en klik op **Opslaan**.

De naam van deze toepassingsinstelling is wat er in de instelling voor uitvoerbindingsbinding in *function.json* of het kenmerk .NET wordt weergegeven. Zie de [sectie Configuratie](#configuration) eerder in dit artikel.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Meldingshub | [Handleiding voor bewerkingen](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)

