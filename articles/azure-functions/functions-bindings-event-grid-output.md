---
title: Azure Event Grid output binding voor Azure Functions
description: Meer informatie over het verzenden van een gebeurtenisnet in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368946"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid output binding voor Azure Functions

Gebruik de uitvoerbinding Gebeurtenisraster om gebeurtenissen naar een aangepast onderwerp te schrijven. U moet een geldige [toegangssleutel hebben voor het aangepaste onderwerp.](../event-grid/security-authentication.md#custom-topic-publishing)

Zie het [overzicht](./functions-bindings-event-grid.md)voor informatie over de installatie en configuratiedetails.

> [!NOTE]
> De uitvoerbinding Event Grid biedt geen ondersteuning voor gedeelde toegangshandtekeningen (SAS-tokens). U moet de toegangssleutel van het onderwerp gebruiken.

> [!IMPORTANT]
> De uitvoerbinding Event Grid is alleen beschikbaar voor functies 2.x en hoger.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een bericht schrijft naar een aangepast gebeurtenisraster, met behulp van de retourwaarde van de methode als uitvoer:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

In het volgende voorbeeld `IAsyncCollector` ziet u hoe u de interface gebruiken om een batch berichten te verzenden.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld worden de bindingsbindingsgegevens van gebeurtenisrasters weergegeven in het bestand *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Hier is C#-scriptcode waarmee één gebeurtenis wordt gemaakt:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Hier is C#-scriptcode die meerdere gebeurtenissen maakt:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld worden de bindingsbindingsgegevens van gebeurtenisrasters weergegeven in het bestand *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Hier is JavaScript-code die één gebeurtenis maakt:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Hier is JavaScript-code die meerdere gebeurtenissen maakt:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

De uitvoerbinding Event Grid is niet beschikbaar voor Python.

# <a name="java"></a>[Java](#tab/java)

De uitvoerbinding eventgrid is niet beschikbaar voor Java.

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Voor [klassebibliotheken van C#](functions-dotnet-class-library.md)gebruikt u het kenmerk [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

De constructor van het kenmerk neemt de naam aan van een app-instelling die de naam van het aangepaste onderwerp bevat en de naam van een app-instelling die de onderwerpsleutel bevat. Zie [Uitvoer - configuratie](#configuration)voor meer informatie over deze instellingen. Hier is `EventGrid` een kenmerkvoorbeeld:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie [voorbeeld voor](#example)een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

De uitvoerbinding Event Grid is niet beschikbaar voor Python.

# <a name="java"></a>[Java](#tab/java)

De uitvoerbinding eventgrid is niet beschikbaar voor Java.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `EventGrid` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet worden ingesteld op "eventGrid". |
|**direction** | N.v.t. | Moet worden ingesteld op "out". Deze parameter wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**Naam** | N.v.t. | De variabele naam die wordt gebruikt in de functiecode die de gebeurtenis vertegenwoordigt. |
|**topicEndpointUri** |**TopicEndpointUri** | De naam van een app-instelling die de URI `MyTopicEndpointUri`bevat voor het aangepaste onderwerp, zoals . |
|**topicKeySetting** |**TopicKeySetting** | De naam van een app-instelling die een toegangssleutel voor het aangepaste onderwerp bevat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Controleer of u de `TopicEndpointUri` waarde van de eigenschap configuration instelt op de naam van een app-instelling die de URI van het aangepaste onderwerp bevat. Geef de URI van het aangepaste onderwerp niet rechtstreeks op in deze eigenschap.

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

Berichten verzenden met behulp van `out EventGridEvent paramName`een methodeparameter zoals . Als u meerdere berichten wilt `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` schrijven, `out EventGridEvent`kunt u .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Berichten verzenden met behulp van `out EventGridEvent paramName`een methodeparameter zoals . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. Als u meerdere berichten wilt `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` schrijven, `out EventGridEvent`kunt u .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de `context.bindings.<name>` uitvoergebeurtenis met behulp van waar `<name>` is de waarde opgegeven in de `name` eigenschap van *function.json*.

# <a name="python"></a>[Python](#tab/python)

De uitvoerbinding Event Grid is niet beschikbaar voor Python.

# <a name="java"></a>[Java](#tab/java)

De uitvoerbinding eventgrid is niet beschikbaar voor Java.

---

## <a name="next-steps"></a>Volgende stappen

* [Een gebeurtenis Event Grid verzenden](./functions-bindings-event-grid-trigger.md)
