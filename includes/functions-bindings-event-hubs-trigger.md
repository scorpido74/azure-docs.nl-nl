---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 41e6352afb5eebc6ab09f43feac4e211232fd270
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85292180"
---
Gebruik de functie trigger om te reageren op een gebeurtenis die wordt verzonden naar een Event Hub gebeurtenis stroom. U moet lees toegang hebben tot de onderliggende Event Hub om de trigger in te stellen. Wanneer de functie wordt geactiveerd, wordt het bericht dat is door gegeven aan de functie als een teken reeks getypt.

## <a name="scaling"></a>Schalen

Elk exemplaar van een door een gebeurtenis geactiveerde functie wordt ondersteund door één [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -exemplaar. De trigger (aangedreven door Event Hubs) zorgt ervoor dat slechts één [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -exemplaar een lease kan verkrijgen op een bepaalde partitie.

Denk bijvoorbeeld aan een event hub als volgt:

* 10 partities
* 1.000 gebeurtenissen worden gelijkmatig verdeeld over alle partities, met 100 berichten per partitie

Wanneer de functie voor het eerst wordt ingeschakeld, is er slechts één exemplaar van de functie. We bellen het eerste functie-exemplaar `Function_0` . De `Function_0` functie heeft één exemplaar van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) dat een lease op alle tien partities bevat. Dit exemplaar leest gebeurtenissen van partities 0-9. Vanaf dit punt gebeurt een van de volgende handelingen:

* Er **zijn geen nieuwe functie-exemplaren nodig**: `Function_0` alle 1.000-gebeurtenissen kunnen worden verwerkt voordat de logica voor de schaal van functies van kracht worden. In dit geval worden alle 1.000-berichten verwerkt door `Function_0` .

* **Er wordt een extra functie-exemplaar toegevoegd**: als de logica voor het schalen van functies bepaalt dat `Function_0` er meer berichten zijn dan kan worden verwerkt, wordt er een nieuw exemplaar van de functie-app ( `Function_1` ) gemaakt. Deze nieuwe functie heeft ook een bijbehorend exemplaar van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Als de onderliggende Event Hubs detecteert dat een nieuw exemplaar van de host probeert berichten te lezen, worden de partities in de exemplaren van de host gebalanceerd. Partities 0-4 kunnen bijvoorbeeld worden toegewezen aan `Function_0` en partities van 5-9 tot `Function_1` .

* **N er worden meer functie exemplaren toegevoegd**: als de logica voor het schalen van functies bepaalt dat beide `Function_0` en `Function_1` meer berichten hebben dan ze kunnen verwerken, `Functions_N` worden nieuwe functie-app-exemplaren gemaakt.  Apps worden gemaakt op het punt waar `N` het groter is dan het aantal Event hub partities. In ons voor beeld verdeelt Event Hubs opnieuw taak verdeling van de partities, in dit geval over de instanties `Function_0` ... `Functions_9` .

Als er wordt geschaald, `N` is de instantie een getal dat groter is dan het aantal Event hub partities. Dit patroon wordt gebruikt om ervoor te zorgen dat er [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instanties beschikbaar zijn voor het verkrijgen van vergren delingen op partities, omdat deze beschikbaar zijn voor andere exemplaren. Er worden alleen kosten in rekening gebracht voor de resources die worden gebruikt wanneer het functie-exemplaar wordt uitgevoerd. Met andere woorden, er worden geen kosten in rekening gebracht voor deze overinrichting.

Wanneer de uitvoering van alle functies is voltooid (met of zonder fouten), worden controle punten toegevoegd aan het gekoppelde opslag account. Wanneer een controle slaagt, worden alle 1.000-berichten nooit meer opgehaald.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) waarmee de bericht tekst van de Event hub trigger wordt geregistreerd.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [meta gegevens van gebeurtenissen](#event-metadata) in functie code, moet u een binding maken met een [Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata) -object (hiervoor is een instructie using vereist voor `Microsoft.Azure.EventHubs` ). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindings expressies in de methode handtekening.  In het volgende voor beeld ziet u beide manieren om dezelfde gegevens op te halen:

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

Voor het ontvangen van gebeurtenissen in een batch, maakt `string` of `EventData` een matrix.  

> [!NOTE]
> Wanneer u in een batch ontvangt, kunt u niet binden aan methode parameters zoals in het bovenstaande voor beeld met `DateTime enqueuedTimeUtc` en moet u deze van elk `EventData` object ontvangen  

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

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* bestand en een [C#-script functie](../articles/azure-functions/functions-reference-csharp.md) die gebruikmaakt van de binding. De functie registreert de bericht tekst van de trigger van de Event Hub.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand.

### <a name="version-2x-and-higher"></a>Versie 2. x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Dit is de C#-script code:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [meta gegevens van gebeurtenissen](#event-metadata) in functie code, moet u een binding maken met een [Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata) -object (hiervoor is een instructie using vereist voor `Microsoft.Azure.EventHubs` ). U kunt ook toegang krijgen tot dezelfde eigenschappen met behulp van bindings expressies in de methode handtekening.  In het volgende voor beeld ziet u beide manieren om dezelfde gegevens op te halen:

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

Voor het ontvangen van gebeurtenissen in een batch, maakt `string` of `EventData` een matrix:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* een bestand en een [Java script-functie](../articles/azure-functions/functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van gebeurtenissen](#event-metadata) gelezen en wordt het bericht geregistreerd.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand.

### <a name="version-2x-and-higher"></a>Versie 2. x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1. x

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

Als u gebeurtenissen in een batch wilt ontvangen, `cardinality` moet u deze instellen `many` in de *function.jsop* bestand, zoals wordt weer gegeven in de volgende voor beelden.

### <a name="version-2x-and-higher"></a>Versie 2. x en hoger

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

### <a name="version-1x"></a>Versie 1. x

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

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een Event Hub trigger binding in een *function.jsin* een bestand en een [python-functie](../articles/azure-functions/functions-reference-python.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van gebeurtenissen](#event-metadata) gelezen en wordt het bericht geregistreerd.

In de volgende voor beelden ziet u Event Hubs bindings gegevens in de *function.jsvoor* het bestand.

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

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een event hub-trigger binding die de bericht tekst van de Event hub-trigger registreert.

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

 Gebruik in de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime)de `EventHubTrigger` annotatie voor para meters waarvan de waarde afkomstig is van Event hub. Door para meters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis binnenkomt.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](../articles/azure-functions/functions-dotnet-class-library.md)het kenmerk [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

De constructor van het kenmerk neemt de naam van de Event Hub, de naam van de Consumer groep en de naam van een app-instelling die de connection string bevat. Zie de [sectie trigger configuratie](#configuration)voor meer informatie over deze instellingen. Hier volgt een `EventHubTriggerAttribute` voor beeld van een kenmerk:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [trigger-C#-voor beeld](#example).

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)van Java-functies de [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) -aantekening voor para meters waarvan de waarde afkomstig is van Event hub. Door para meters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis binnenkomt. Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met `Optional<T>` .

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `EventHubTrigger` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `eventHubTrigger` . Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in` . Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die het gebeurtenis item in functie code vertegenwoordigt. |
|**programmapad** |**EventHubName** | Functions 1. x. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functions 2. x en hoger. De naam van de Event Hub. Wanneer de Event Hub naam ook aanwezig is in de connection string, overschrijft die waarde deze eigenschap tijdens runtime. Kan worden verwezen via [app-instellingen](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)`%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | Een optionele eigenschap waarmee de [Consumer groep](../articles/event-hubs/event-hubs-features.md#event-consumers) wordt ingesteld die wordt gebruikt om zich te abonneren op gebeurtenissen in de hub. Als u dit weglaat, `$Default` wordt de Consumer groep gebruikt. |
|**kardinaliteit** | N.v.t. | Voor Java script. Ingesteld op om `many` batch verwerking in te scha kelen.  Als u niets opgeeft of instelt op `one` , wordt één bericht door gegeven aan de functie. |
|**Combi** |**Verbinding** | De naam van een app-instelling die de connection string aan de naam ruimte van de Event Hub bevat. Kopieer deze connection string door te klikken op de knop **verbindings gegevens** voor de [naam ruimte](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), niet op de Event hub zelf. Deze connection string moet mini maal lees machtigingen hebben om de trigger te activeren.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Meta gegevens van gebeurtenis

De trigger Event Hubs biedt verschillende [Eigenschappen van meta gegevens](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Eigenschappen van meta gegevens kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. De eigenschappen zijn afkomstig van de [Event Data](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) -klasse.

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Het `PartitionContext` exemplaar.|
|`EnqueuedTimeUtc`|`DateTime`|De in wachtrij geplaatste tijd in UTC.|
|`Offset`|`string`|De offset van de gegevens ten opzichte van de Event hub-partitie stroom. De offset is een markering of id voor een gebeurtenis binnen de Event Hubs stroom. De id is uniek binnen een partitie van de Event Hubs stroom.|
|`PartitionKey`|`string`|De partitie waarnaar gebeurtenis gegevens moeten worden verzonden.|
|`Properties`|`IDictionary<String,Object>`|De gebruikers eigenschappen van de gebeurtenis gegevens.|
|`SequenceNumber`|`Int64`|Het logische Volg nummer van de gebeurtenis.|
|`SystemProperties`|`IDictionary<String,Object>`|De systeem eigenschappen, met inbegrip van de gebeurtenis gegevens.|

Zie [code voorbeelden](#example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="hostjson-properties"></a>host.jsop Eigenschappen
<a name="host-json"></a>

De [host.jsin](../articles/azure-functions/functions-host-json.md#eventhub) het bestand bevat instellingen die het gedrag van Event hubs trigger regelen. De configuratie verschilt, afhankelijk van de Azure Functions versie.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]