---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 78836ca4e51875be4237267b3bb9256cc4541fe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81791628"
---
Gebruik de Event Hubs uitvoer binding om gebeurtenissen naar een gebeurtenis stroom te schrijven. U moet een verzendmachtiging voor een Event Hub hebben om er gebeurtenissen naar te kunnen schrijven.

Zorg ervoor dat de vereiste pakket verwijzingen aanwezig zijn voordat u een uitvoer binding implementeert.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) die een bericht naar een event hub schrijft, met behulp van de retour waarde van de methode als uitvoer:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

In het volgende voor beeld ziet u hoe u de `IAsyncCollector` interface gebruikt om een batch berichten te verzenden. Dit scenario is gebruikelijk bij het verwerken van berichten die afkomstig zijn van een event hub en het verzenden van het resultaat naar een andere Event hub.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* bestand en een [C#-script functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand. Het eerste voor beeld is voor functions 2. x en hoger, en de tweede is voor functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de C#-script code waarmee een bericht wordt gemaakt:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Dit is de C#-script code waarmee meerdere berichten worden gemaakt:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* een bestand en een [Java script-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand. Het eerste voor beeld is voor functions 2. x en hoger, en de tweede is voor functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de Java script-code waarmee één bericht wordt verzonden:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Dit is de Java script-code waarmee meerdere berichten worden verzonden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* een bestand en een [python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Dit is de python-code voor het verzenden van één bericht:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die een bericht schrijft met de huidige tijd naar een event hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@EventHubOutput` annotatie voor para meters waarvan de waarde zou worden gepubliceerd in Event hub.  De para meter moet van het type zijn `OutputBinding<T>` , waarbij T een Pojo of een systeem eigen Java-type is.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik voor [C# class-bibliotheken](../articles/azure-functions/functions-dotnet-class-library.md)het kenmerk [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

De constructor van het kenmerk neemt de naam van de Event Hub en de naam van een app-instelling die de connection string bevat. Zie [output-Configuration (](#configuration)Engelstalig) voor meer informatie over deze instellingen. Hier volgt een `EventHub` voor beeld van een kenmerk:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie [output-C#-](#example)voor beeld voor een volledig voor beeld.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -aantekening voor para meters waarvan de waarde zou worden gepubliceerd in Event hub. De para meter moet van het type zijn `OutputBinding<T>` , waarbij `T` een Pojo of een systeem eigen Java-type is.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `EventHub` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Description|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' eventHub '. |
|**direction** | N.v.t. | Moet worden ingesteld op out. Deze para meter wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code waarmee de gebeurtenis wordt aangeduid. |
|**programmapad** |**EventHubName** | Functions 1. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2. x en hoger. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**Combi** |**Verbinding** | De naam van een app-instelling die de connection string aan de naam ruimte van de Event Hub bevat. Kopieer deze connection string door te klikken op de knop **verbindings gegevens** voor de *naam ruimte*, niet op de Event hub zelf. Deze connection string moet de machtiging verzenden hebben om het bericht naar de gebeurtenis stroom te verzenden.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Berichten verzenden met behulp van een methode parameter, zoals `out string paramName` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<string>` of gebruiken `IAsyncCollector<string>` in plaats van `out string` .

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Berichten verzenden met behulp van een methode parameter, zoals `out string paramName` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<string>` of gebruiken `IAsyncCollector<string>` in plaats van `out string` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

U krijgt toegang tot de uitvoer gebeurtenis door gebruik te maken `context.bindings.<name>` `<name>` van de waarde die is opgegeven in de `name` eigenschap van *function.jsop*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een event hub-bericht van een functie:

- **Retour waarde**: Stel de `name` eigenschap in *function.jsin op* aan `$return` . Met deze configuratie wordt de retour waarde van de functie persistent gemaakt als een event hub-bericht.

- Verplicht **: Geef**een waarde door aan de methode [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) van de para meter die is gedeclareerd als een [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) -type. De waarde die is door gegeven aan `set` , wordt persistent gemaakt als een event hub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een event hub-bericht van een functie met behulp van de [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -aantekening:

- **Retour waarde**: door de aantekening toe te passen op de functie zelf, wordt de geretourneerde waarde van de functie persistent gemaakt als een event hub-bericht.

- Verplicht **: als**u de bericht waarde expliciet wilt instellen, past u de aantekening toe op een specifieke para meter van het type [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding) , waarbij `T` een Pojo of een systeem eigen Java-type is. Met deze configuratie wordt door het door geven van een waarde aan de `setValue` methode de waarde persistent gemaakt als een event hub-bericht.

---

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Binding | Naslaginformatie |
|---|---|
| Event Hub | [Bedienings handleiding](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |
