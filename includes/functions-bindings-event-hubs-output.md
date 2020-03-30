---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589738"
---
Gebruik de uitvoerbinding Gebeurtenishubs om gebeurtenissen naar een gebeurtenisstream te schrijven. U moet een verzendmachtiging voor een Event Hub hebben om er gebeurtenissen naar te kunnen schrijven.

Zorg ervoor dat de vereiste pakketreferenties aanwezig zijn voordat u een uitvoerbinding probeert te implementeren.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) weergegeven die een bericht naar een gebeurtenishub schrijft, met de retourwaarde van de methode als uitvoer:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

In het volgende voorbeeld `IAsyncCollector` ziet u hoe u de interface gebruiken om een batch berichten te verzenden. Dit scenario komt vaak voor wanneer u berichten verwerkt die afkomstig zijn van de ene gebeurtenishub en het resultaat naar een andere gebeurtenishub verzendt.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een gebeurtenishubtriggerbinding in een *function.json-bestand* en een [C#-scriptfunctie](../articles/azure-functions/functions-reference-csharp.md) die de binding gebruikt. De functie schrijft een bericht naar een gebeurtenishub.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.* Het eerste voorbeeld is voor functies 2.x en hoger, en de tweede is voor functies 1.x. 

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

Hier is C#-scriptcode die één bericht maakt:

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

Hier is C#-scriptcode die meerdere berichten maakt:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een gebeurtenishubtriggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](../articles/azure-functions/functions-reference-node.md) die de binding gebruikt. De functie schrijft een bericht naar een gebeurtenishub.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.* Het eerste voorbeeld is voor functies 2.x en hoger, en de tweede is voor functies 1.x. 

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

Hier is JavaScript-code die één bericht verzendt:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Hier is JavaScript-code die meerdere berichten verzendt:

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

In het volgende voorbeeld ziet u een gebeurtenishubtriggerbinding in een *function.json-bestand* en een [Python-functie](../articles/azure-functions/functions-reference-python.md) die de binding gebruikt. De functie schrijft een bericht naar een gebeurtenishub.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.*

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Hier is Python-code die één bericht verzendt:

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

In het volgende voorbeeld wordt een Java-functie weergegeven die een bericht met de huidige tijd naar een gebeurtenishub schrijft.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `@EventHubOutput` waarde zou worden gepubliceerd in Event Hub.  De parameter moet `OutputBinding<T>` van het type zijn, waarbij T een POJO of een native Java-type is.

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Voor [c#-klassebibliotheken](../articles/azure-functions/functions-dotnet-class-library.md)gebruikt u het kenmerk [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

De constructor van het kenmerk neemt de naam van de gebeurtenishub en de naam van een app-instelling die de verbindingstekenreeks bevat. Zie [Uitvoer - configuratie](#configuration)voor meer informatie over deze instellingen. Hier is `EventHub` een kenmerkvoorbeeld:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie Voorbeeld uitvoer [- C#](#example)voor een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de [EventHubOutput-annotatie](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) op parameters waarvan de waarde zou worden gepubliceerd in Gebeurtenishub. De parameter moet `OutputBinding<T>` van `T` het type zijn, waar is een POJO of een native Java-type.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `EventHub` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet worden ingesteld op "eventHub". |
|**direction** | N.v.t. | Moet worden ingesteld op "out". Deze parameter wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**Naam** | N.v.t. | De variabele naam die wordt gebruikt in de functiecode die de gebeurtenis vertegenwoordigt. |
|**Pad** |**EventHubName** | Functies alleen 1.x. De naam van de gebeurtenishub. Wanneer de naam van de gebeurtenishub ook aanwezig is in de verbindingstekenreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functies 2.x en hoger. De naam van de gebeurtenishub. Wanneer de naam van de gebeurtenishub ook aanwezig is in de verbindingstekenreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**verbinding** |**Verbinding** | De naam van een app-instelling die de verbindingstekenreeks bevat met de naamruimte van de gebeurtenishub. Kopieer deze verbindingstekenreeks door op de knop **Verbindingsgegevens** voor de *naamruimte*te klikken, niet op de gebeurtenishub zelf. Deze verbindingstekenreeks moet machtigingen hebben om het bericht naar de gebeurtenisstream te verzenden.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

Berichten verzenden met behulp van `out string paramName`een methodeparameter zoals . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. Als u meerdere berichten wilt `ICollector<string>` `IAsyncCollector<string>` schrijven, `out string`kunt u .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Berichten verzenden met behulp van `out string paramName`een methodeparameter zoals . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. Als u meerdere berichten wilt `ICollector<string>` `IAsyncCollector<string>` schrijven, `out string`kunt u .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de `context.bindings.<name>` uitvoergebeurtenis met behulp van waar `<name>` is de waarde opgegeven in de `name` eigenschap van *function.json*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitplaatsen van een Gebeurtenishub-bericht vanuit een functie:

- **Retourwaarde:** Stel `name` de eigenschap in `$return` *function.json* in op . Met deze configuratie blijft de retourwaarde van de functie bestaan als een gebeurtenishubbericht.

- **Noodzakelijk:** Geef een waarde door aan de [ingestelde](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) methode van de parameter die is opgegeven als [een uit-type.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) De waarde `set` die wordt doorgegeven aan wordt gehandhaafd als een Gebeurtenishub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitplaatsen van een Gebeurtenishub-bericht vanuit een functie met de [gebeurtenishub-annotatie:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Retourwaarde:** Door de annotatie toe te passen op de functie zelf, blijft de retourwaarde van de functie bestaan als een gebeurtenishubbericht.

- **Noodzakelijk:** Als u de berichtwaarde expliciet wilt instellen, past u [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)de `T` aantekening toe op een specifieke parameter van het type , waar een POJO of een native Java-type is. Met deze configuratie blijft het `setValue` doorgeven van een waarde aan de methode de waarde als een gebeurtenishubbericht behouden.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Event Hub | [Handleiding voor bewerkingen](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versies 2.x en hoger. Het voorbeeld host.json-bestand hieronder bevat alleen de instellingen van versie 2.x+ voor deze binding. Zie [host.json-verwijzing naar Azure-functies voor](../articles/azure-functions/functions-host-json.md)meer informatie over algemene configuratie-instellingen in versies 2.x en verder.

> [!NOTE]
> Zie [host.json-verwijzing naar Azure-functies 1.x voor](../articles/azure-functions/functions-host-json-v1.md)een verwijzing naar host.json in Functies 1.x .

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|`maxBatchSize`|10|Het maximale aantal gebeurtenissen per ontvangen lus.|
|`prefetchCount`|300|Het standaard aantal voorhaalde dat `EventProcessorHost`wordt gebruikt door de onderliggende .|
|`batchCheckpointFrequency`|1|Het aantal gebeurtenisbatches dat moet worden verwerkt voordat u een EventHub-cursorcontrolepunt maakt.|
