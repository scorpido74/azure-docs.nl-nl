---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0f94c89a52de138b261796cbef25c0acb57622c4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799777"
---
## <a name="trigger"></a>Trigger

Gebruik de functie trigger om te reageren op een gebeurtenis die wordt verzonden naar een Event Hub gebeurtenis stroom. U moet lees toegang hebben tot de onderliggende Event Hub om de trigger in te stellen. Wanneer de functie wordt geactiveerd, wordt het bericht dat is door gegeven aan de functie als een teken reeks getypt.

## <a name="trigger---scaling"></a>Trigger-schalen

Elk exemplaar van een door een gebeurtenis geactiveerde functie wordt ondersteund door één [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -exemplaar. De trigger (aangedreven door Event Hubs) zorgt ervoor dat slechts één [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -exemplaar een lease kan verkrijgen op een bepaalde partitie.

Denk bijvoorbeeld aan een event hub als volgt:

* 10 partities
* 1\.000 gebeurtenissen worden gelijkmatig verdeeld over alle partities, met 100 berichten per partitie

Wanneer de functie voor het eerst wordt ingeschakeld, is er slechts één exemplaar van de functie. We bellen de eerste functie-exemplaar `Function_0`. De functie `Function_0` heeft één exemplaar van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) dat een lease op alle tien partities bevat. Dit exemplaar leest gebeurtenissen van partities 0-9. Vanaf dit punt gebeurt een van de volgende handelingen:

* **Er zijn geen nieuwe functie exemplaren nodig**: `Function_0` kunt alle 1.000 gebeurtenissen verwerken voordat de functies voor het schalen van de functie van kracht worden. In dit geval worden alle 1.000-berichten verwerkt door `Function_0`.

* **Er wordt een extra functie-exemplaar toegevoegd**: als de logica voor het schalen van functies bepaalt dat `Function_0` meer berichten heeft dan kan worden verwerkt, wordt een nieuw functie-app-exemplaar (`Function_1`) gemaakt. Deze nieuwe functie heeft ook een bijbehorend exemplaar van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Als de onderliggende Event Hubs detecteert dat een nieuw exemplaar van de host probeert berichten te lezen, worden de partities over de exemplaren van de host gebalanceerd. Partities 0-4 kunnen bijvoorbeeld worden toegewezen aan `Function_0` en partities 5-9 tot `Function_1`.

* **N er worden meer functie exemplaren toegevoegd**: als de logica voor het schalen van functies wordt vastgesteld dat zowel `Function_0` als `Function_1` meer berichten hebben dan ze kunnen verwerken, worden nieuwe apps voor de functie van `Functions_N`-functies gemaakt.  Apps worden gemaakt op het punt waar `N` groter is dan het aantal Event Hub partities. In ons voor beeld verdeelt Event Hubs opnieuw taak verdeling van de partities, in dit geval over de instanties `Function_0`...`Functions_9`.

Wanneer functies worden geschaald, is `N` exemplaren een getal dat groter is dan het aantal Event Hub partities. Dit wordt gedaan om ervoor te zorgen dat er [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instanties beschikbaar zijn voor het verkrijgen van vergren delingen op partities wanneer deze beschikbaar zijn voor andere instanties. Er worden alleen kosten in rekening gebracht voor de resources die worden gebruikt wanneer het functie-exemplaar wordt uitgevoerd. Met andere woorden, er worden geen kosten in rekening gebracht voor deze overinrichting.

Wanneer de uitvoering van alle functies is voltooid (met of zonder fouten), worden controle punten toegevoegd aan het gekoppelde opslag account. Wanneer een controle slaagt, worden alle 1.000-berichten nooit meer opgehaald.

## <a name="trigger---example"></a>Trigger-voor beeld

Zie het taalspecifieke voor beeld:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger- C# voor beeld

In het volgende voor beeld ziet u een [ C# functie](../articles/azure-functions/functions-dotnet-class-library.md) waarmee de bericht tekst van de Event hub trigger wordt geregistreerd.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [meta gegevens van gebeurtenissen](#trigger---event-metadata) in functie code, kunt u een binding maken met een [Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata) -object (hiervoor is een instructie using vereist voor `Microsoft.Azure.EventHubs`). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindings expressies in de methode handtekening.  In het volgende voor beeld ziet u beide manieren om dezelfde gegevens op te halen:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Als u gebeurtenissen in een batch wilt ontvangen, maakt u `string` of `EventData` een matrix.  

> [!NOTE]
> Wanneer u een batch ontvangt, kunt u niet binden aan methode parameters zoals in het bovenstaande voor beeld met `DateTime enqueuedTimeUtc` en moet u deze ontvangen van elk `EventData` object  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Voor beeld C# van trigger-script

In het volgende voor beeld ziet u een binding van een event hub trigger in een *Function. json* -bestand en een [ C# script functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie registreert de bericht tekst van de trigger van de Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* .

#### <a name="version-2x"></a>Versie 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versie 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de C# script code:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [meta gegevens van gebeurtenissen](#trigger---event-metadata) in functie code, kunt u een binding maken met een [Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata) -object (hiervoor is een instructie using vereist voor `Microsoft.Azure.EventHubs`). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindings expressies in de methode handtekening.  In het volgende voor beeld ziet u beide manieren om dezelfde gegevens op te halen:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Als u gebeurtenissen in een batch wilt ontvangen, moet u `string` of `EventData` een matrix:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Trigger- F# voor beeld

In het volgende voor beeld ziet u een binding van Event hub trigger in een *Function. json* -bestand en een [ F# functie](../articles/azure-functions/functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie registreert de bericht tekst van de trigger van de Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* . 

#### <a name="version-2x"></a>Versie 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versie 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier volgt de F# code:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger-java script-voor beeld

In het volgende voor beeld ziet u een Event Hub trigger binding in een *Function. json* -bestand en een [Java script-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van gebeurtenissen](#trigger---event-metadata) gelezen en wordt het bericht geregistreerd.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* .

#### <a name="version-2x"></a>Versie 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versie 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de Java script-code:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Als u gebeurtenissen in een batch wilt ontvangen, stelt u `cardinality` in op `many` in het bestand *Function. json* , zoals in de volgende voor beelden wordt weer gegeven.

#### <a name="version-2x"></a>Versie 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versie 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de Java script-code:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Voor beeld van een trigger-python

In het volgende voor beeld ziet u een binding van Event Hub trigger in een *Function. json* -bestand en een [python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van gebeurtenissen](#trigger---event-metadata) gelezen en wordt het bericht geregistreerd.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* .

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Trigger-Java-voor beeld

In het volgende voor beeld ziet u een event hub-trigger binding in een *Function. json* -bestand en een [Java-functie](../articles/azure-functions/functions-reference-java.md) die gebruikmaakt van de binding. De functie registreert de bericht tekst van de Event hub-trigger.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `EventHubTrigger` annotatie op para meters waarvan de waarde afkomstig is van Event hub. Door para meters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis binnenkomt.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van optionele\<T >.

## <a name="trigger---attributes"></a>Trigger-kenmerken

Gebruik in [ C# class bibliotheken](../articles/azure-functions/functions-dotnet-class-library.md)het kenmerk [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

De constructor van het kenmerk neemt de naam van de Event Hub, de naam van de Consumer groep en de naam van een app-instelling die de connection string bevat. Zie de [sectie trigger configuratie](#trigger---configuration)voor meer informatie over deze instellingen. Hier volgt een voor beeld van een `EventHubTriggerAttribute`-kenmerk:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [trigger- C# voor beeld](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `EventHubTrigger`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `eventHubTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die het gebeurtenis item in functie code vertegenwoordigt. |
|**path** |**EventHubName** | Functions 1. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**consumerGroup** |**ConsumerGroup** | Een optionele eigenschap waarmee de [Consumer groep](../articles/event-hubs/event-hubs-features.md#event-consumers) wordt ingesteld die wordt gebruikt om zich te abonneren op gebeurtenissen in de hub. Als u dit weglaat, wordt de `$Default` consumenten groep gebruikt. |
|**kardinaliteit** | N.v.t. | Voor Java script. Stel deze optie in op `many` om batch verwerking in te scha kelen.  Als u niets opgeeft of instelt op `one`, wordt één bericht door gegeven aan functie. |
|**Combi** |**Verbinding** | De naam van een app-instelling die de connection string aan de naam ruimte van de Event Hub bevat. Kopieer deze connection string door te klikken op de knop **verbindings gegevens** voor de [naam ruimte](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), niet op de Event hub zelf. Deze connection string moet mini maal lees machtigingen hebben om de trigger te activeren.|
|**path**|**EventHubName**|De naam van de Event Hub. Kan worden verwezen via de app-instellingen `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Trigger-gebeurtenis meta gegevens

De trigger Event Hubs biedt verschillende [Eigenschappen van meta gegevens](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Deze eigenschappen kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. Dit zijn eigenschappen van de klasse [Event Data](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Het `PartitionContext`-exemplaar.|
|`EnqueuedTimeUtc`|`DateTime`|De in wachtrij geplaatste tijd in UTC.|
|`Offset`|`string`|De offset van de gegevens ten opzichte van de Event hub-partitie stroom. De offset is een markering of id voor een gebeurtenis binnen de Event Hubs stroom. De id is uniek binnen een partitie van de Event Hubs stroom.|
|`PartitionKey`|`string`|De partitie waarnaar gebeurtenis gegevens moeten worden verzonden.|
|`Properties`|`IDictionary<String,Object>`|De gebruikers eigenschappen van de gebeurtenis gegevens.|
|`SequenceNumber`|`Int64`|Het logische Volg nummer van de gebeurtenis.|
|`SystemProperties`|`IDictionary<String,Object>`|De systeem eigenschappen, met inbegrip van de gebeurtenis gegevens.|

Zie [code voorbeelden](#trigger---example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="trigger---hostjson-properties"></a>Trigger-host. json-eigenschappen

Het bestand [host. json](../articles/azure-functions/functions-host-json.md#eventhub) bevat instellingen die het gedrag van Event hubs trigger regelen.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Uitvoer

Gebruik de Event Hubs uitvoer binding om gebeurtenissen naar een gebeurtenis stroom te schrijven. U moet een machtiging voor verzenden hebben voor een Event Hub om gebeurtenissen te kunnen schrijven.

Zorg ervoor dat de vereiste pakket verwijzingen aanwezig zijn: functions 1. x of functions 2. x

## <a name="output---example"></a>Uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Uitvoer- C# voor beeld

In het volgende voor beeld ziet u een [ C# functie](../articles/azure-functions/functions-dotnet-class-library.md) die een bericht naar een event hub schrijft, met behulp van de retour waarde van de methode als uitvoer:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

In het volgende voor beeld ziet u hoe u de `IAsyncCollector`-interface kunt gebruiken om een batch berichten te verzenden. Dit scenario is gebruikelijk bij het verwerken van berichten die afkomstig zijn van een event hub en het verzenden van het resultaat naar een andere Event hub.

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

### <a name="output---c-script-example"></a>Voor beeld C# van uitvoer script

In het volgende voor beeld ziet u een binding van een event hub trigger in een *Function. json* -bestand en een [ C# script functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* . Het eerste voor beeld is voor functions 2. x, en de tweede is voor functions 1. x. 

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

Dit is C# de script code voor het maken van één bericht:

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

Dit is C# de script code voor het maken van meerdere berichten:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Uitvoer- F# voor beeld

In het volgende voor beeld ziet u een binding van Event hub trigger in een *Function. json* -bestand en een [ F# functie](../articles/azure-functions/functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* . Het eerste voor beeld is voor functions 2. x, en de tweede is voor functions 1. x. 

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

Hier volgt de F# code:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Uitvoer-java script-voor beeld

In het volgende voor beeld ziet u een Event Hub trigger binding in een *Function. json* -bestand en een [Java script-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* . Het eerste voor beeld is voor functions 2. x, en de tweede is voor functions 1. x. 

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

### <a name="output---python-example"></a>Voor beeld van de uitvoer-python

In het volgende voor beeld ziet u een binding van Event Hub trigger in een *Function. json* -bestand en een [python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft een bericht naar een Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in het bestand *Function. json* .

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

### <a name="output---java-example"></a>Uitvoer-Java-voor beeld

In het volgende voor beeld ziet u een Java-functie die een bericht contianing de huidige tijd naar een event hub schrijft.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@EventHubOutput` annotatie voor para meters waarvan de waarde zou worden gepubliceerd in Event hub.  De para meter moet van het type `OutputBinding<T>` zijn, waarbij T een POJO of een systeem eigen Java-type is.

## <a name="output---attributes"></a>Uitvoer kenmerken

Gebruik voor [ C# class-bibliotheken](../articles/azure-functions/functions-dotnet-class-library.md)het kenmerk [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) .

De constructor van het kenmerk neemt de naam van de Event Hub en de naam van een app-instelling die de connection string bevat. Zie [output-Configuration (](#output---configuration)Engelstalig) voor meer informatie over deze instellingen. Hier volgt een voor beeld van een `EventHub`-kenmerk:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer- C# voor beeld](#output---c-example).

## <a name="output---configuration"></a>Uitvoer-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `EventHub`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' eventHub '. |
|**direction** | N.v.t. | Moet worden ingesteld op out. Deze para meter wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code waarmee de gebeurtenis wordt aangeduid. |
|**path** |**EventHubName** | Functions 1. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**Combi** |**Verbinding** | De naam van een app-instelling die de connection string aan de naam ruimte van de Event Hub bevat. Kopieer deze connection string door te klikken op de knop **verbindings gegevens** voor de *naam ruimte*, niet op de Event hub zelf. Deze connection string moet de machtiging verzenden hebben om het bericht naar de gebeurtenis stroom te verzenden.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

In C# en C# script verzendt u berichten met behulp van een methode parameter, zoals `out string paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<string>` of `IAsyncCollector<string>` gebruiken in plaats van `out string`.

In Java script kunt u de uitvoer gebeurtenis openen met behulp van `context.bindings.<name>`. `<name>` is de waarde die is opgegeven in de eigenschap `name` van *Function. json*.

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Dwingen | Naslaginformatie |
|---|---|
| Event Hub | [Bedienings handleiding](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor de host. json

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x. Het onderstaande voor beeld van host. JSON-bestand bevat alleen de instellingen van versie 2. x voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](../articles/azure-functions/functions-host-json-v1.md)voor een verwijzing naar de host. json in functions 1. x.

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
|maxBatchSize|64|Het maximum aantal gebeurtenissen dat per receive-lus is ontvangen.|
|prefetchCount|N.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende EventProcessorHost.|
|batchCheckpointFrequency|1|Het aantal gebeurtenis batches dat moet worden verwerkt voordat een EventHub-cursor controlepunt wordt gemaakt.|
