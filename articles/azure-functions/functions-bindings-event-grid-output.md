---
title: Azure Event Grid uitvoer binding voor Azure Functions
description: Meer informatie over het verzenden van een Event Grid gebeurtenis in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368946"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid uitvoer binding voor Azure Functions

Gebruik de Event Grid uitvoer binding om gebeurtenissen te schrijven naar een aangepast onderwerp. U moet een geldige [toegangs sleutel voor het aangepaste onderwerp](../event-grid/security-authentication.md#custom-topic-publishing)hebben.

Zie het [overzicht](./functions-bindings-event-grid.md)voor meer informatie over de installatie-en configuratie details.

> [!NOTE]
> De Event Grid uitvoer binding biedt geen ondersteuning voor hand tekeningen voor gedeelde toegang (SAS-tokens). U moet de toegangs sleutel van het onderwerp gebruiken.

> [!IMPORTANT]
> De Event Grid uitvoer binding is alleen beschikbaar voor functions 2. x en hoger.

## <a name="example"></a>Voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die een bericht naar een event grid aangepast onderwerp schrijft, met behulp van de retour waarde van de methode als uitvoer:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

In het volgende voor beeld ziet u hoe u de `IAsyncCollector`-interface gebruikt om een batch berichten te verzenden.

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

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in het bestand *Function. json* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is C# de script code voor het maken van één gebeurtenis:

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

Dit is C# de script code waarmee meerdere gebeurtenissen worden gemaakt:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in het bestand *Function. json* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is de Java script-code waarmee één gebeurtenis wordt gemaakt:

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

Dit is de Java script-code waarmee meerdere gebeurtenissen worden gemaakt:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik voor [ C# class-bibliotheken](functions-dotnet-class-library.md)het kenmerk [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

De constructor van het kenmerk maakt de naam van een app-instelling die de naam van het aangepaste onderwerp bevat en de naam van een app-instelling die de sleutel van het onderwerp bevat. Zie [output-Configuration (](#configuration)Engelstalig) voor meer informatie over deze instellingen. Hier volgt een voor beeld van een `EventGrid`-kenmerk:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie voor een volledig [voor beeld.](#example)

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `EventGrid`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' eventGrid '. |
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze para meter wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code waarmee de gebeurtenis wordt aangeduid. |
|**topicEndpointUri** |**TopicEndpointUri** | De naam van een app-instelling die de URI voor het aangepaste onderwerp bevat, zoals `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | De naam van een app-instelling die een toegangs sleutel voor het aangepaste onderwerp bevat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Zorg ervoor dat u de waarde van de eigenschap `TopicEndpointUri` Configuration instelt op de naam van een app-instelling die de URI van het aangepaste onderwerp bevat. Geef de URI van het aangepaste onderwerp niet rechtstreeks op in deze eigenschap.

## <a name="usage"></a>Gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Berichten verzenden met behulp van een methode parameter, zoals `out EventGridEvent paramName`. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Berichten verzenden met behulp van een methode parameter, zoals `out EventGridEvent paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Open de uitvoer gebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven in de eigenschap `name` van *Function. json*.

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="next-steps"></a>Volgende stappen

* [Een Event Grid gebeurtenis verzenden](./functions-bindings-event-grid-trigger.md)
