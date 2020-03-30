---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589712"
---
Gebruik de functietrigger om te reageren op een gebeurtenis die naar een gebeurtenishubgebeurtenisstream is verzonden. U moet leestoegang hebben tot de onderliggende gebeurtenishub om de trigger in te stellen. Wanneer de functie wordt geactiveerd, wordt het bericht dat wordt doorgegeven aan de functie getypt als een tekenreeks.

## <a name="scaling"></a>Schalen

Elke instantie van een gebeurtenisgeactiveerde functie wordt ondersteund door één [EventProcessorHost-exemplaar.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) De trigger (aangedreven door Event Hubs) zorgt ervoor dat slechts één [EventProcessorHost-exemplaar](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) een lease-overeenkomst op een bepaalde partitie kan krijgen.

Overweeg bijvoorbeeld een Gebeurtenishub als volgt:

* 10 partities
* 1.000 gebeurtenissen gelijkmatig verdeeld over alle partities, met 100 berichten in elke partitie

Wanneer uw functie voor het eerst is ingeschakeld, is er slechts één exemplaar van de functie. Laten we de eerste `Function_0`functie-instantie aanroepen. De `Function_0` functie heeft één exemplaar van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) dat een lease heeft op alle tien partities. Dit exemplaar leest gebeurtenissen van partities 0-9. Vanaf dit punt gebeurt een van de volgende dingen:

* **Nieuwe functie-exemplaren zijn niet nodig:** `Function_0` kan alle 1000 gebeurtenissen verwerken voordat de logica voor het schalen van functies van kracht wordt. In dit geval worden alle 1.000 `Function_0`berichten verwerkt door .

* **Een extra functie-instantie wordt toegevoegd:** Als `Function_0` de logica voor het schalen van functies`Function_1`bepaalt dat er meer berichten zijn dan het kan verwerken, wordt een nieuwe functie-app-instantie ( ) gemaakt. Deze nieuwe functie heeft ook een bijbehorende instantie van [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Terwijl de onderliggende gebeurtenishubs detecteren dat een nieuw hostexemplaar berichten probeert te lezen, worden de partities over de host-instanties geladen. Partities 0-4 kunnen bijvoorbeeld worden `Function_0` toegewezen aan en `Function_1`partities 5-9 aan .

* **N meer functie-exemplaren worden toegevoegd:** Als `Function_0` de `Function_1` logica voor het schalen van `Functions_N` functies bepaalt dat beide en meer berichten hebben dan ze kunnen verwerken, worden nieuwe functie-app-exemplaren gemaakt.  Apps worden gemaakt tot `N` het punt waar groter is dan het aantal gebeurtenishubpartities. In ons voorbeeld, Event Hubs opnieuw laden saldi de `Function_0`partities, in dit geval over de instanties ... `Functions_9`.

Bij schaling `N` is het aantal instanties een getal dat groter is dan het aantal gebeurtenishubpartities. Dit patroon wordt gebruikt om ervoor te zorgen [EventProcessorHost-exemplaren](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) beschikbaar zijn voor het verkrijgen van vergrendelingen op partities zodra ze beschikbaar zijn in andere instanties. Er worden alleen kosten in rekening gebracht voor de resources die worden gebruikt wanneer de functie-instantie wordt uitgevoerd. Met andere woorden, u wordt niet in rekening gebracht voor deze over-provisioning.

Wanneer alle functieuitvoering is voltooid (met of zonder fouten), worden controlepunten toegevoegd aan het bijbehorende opslagaccount. Wanneer het controleren slaagt, worden alle 1.000 berichten nooit meer opgehaald.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](../articles/azure-functions/functions-dotnet-class-library.md) weergegeven die de berichttekst van de gebeurtenishubtrigger registreert.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [metagegevens](#event-metadata) van gebeurtenissen in `Microsoft.Azure.EventHubs`functiecode, bindt u aan een [GebeurtenisGegevensobject](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vereist een gebruiksinstructie voor ). U hebt ook toegang tot dezelfde eigenschappen door bindende expressies te gebruiken in de methodehandtekening.  In het volgende voorbeeld worden beide manieren weergegeven om dezelfde gegevens te krijgen:

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

Om gebeurtenissen in een `string` batch `EventData` te ontvangen, maak of een array.  

> [!NOTE]
> Wanneer u in een batch ontvangt, u `DateTime enqueuedTimeUtc` zich niet binden `EventData` aan methodeparameters zoals in het bovenstaande voorbeeld met en moet u deze van elk object ontvangen  

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een gebeurtenishubtriggerbinding in een *function.json-bestand* en een [C#-scriptfunctie](../articles/azure-functions/functions-reference-csharp.md) die de binding gebruikt. De functie registreert de berichttekst van de gebeurtenishubtrigger.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.*

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier is de C# scriptcode:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Als u toegang wilt krijgen tot [metagegevens](#event-metadata) van gebeurtenissen in `Microsoft.Azure.EventHubs`functiecode, bindt u aan een [GebeurtenisGegevensobject](/dotnet/api/microsoft.servicebus.messaging.eventdata) (vereist een gebruiksinstructie voor ). U hebt ook toegang tot dezelfde eigenschappen door bindende expressies te gebruiken in de methodehandtekening.  In het volgende voorbeeld worden beide manieren weergegeven om dezelfde gegevens te krijgen:

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

Als u gebeurtenissen in `string` een `EventData` batch wilt ontvangen, maakt u het volgende:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een gebeurtenishubtriggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](../articles/azure-functions/functions-reference-node.md) die de binding gebruikt. De functie leest [metagegevens van gebeurtenissen](#event-metadata) en registreert het bericht.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.*

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versie 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier is de JavaScript-code:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Als u gebeurtenissen in `cardinality` een `many` batch wilt ontvangen, stelt u in het *bestand function.json* in, zoals in de volgende voorbeelden wordt weergegeven.

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

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

### <a name="version-1x"></a>Versie 1.x

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

Hier is de JavaScript-code:

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

In het volgende voorbeeld ziet u een gebeurtenishubtriggerbinding in een *function.json-bestand* en een [Python-functie](../articles/azure-functions/functions-reference-python.md) die de binding gebruikt. De functie leest [metagegevens van gebeurtenissen](#event-metadata) en registreert het bericht.

In de volgende voorbeelden worden bindende gegevens van gebeurtenishubs weergegeven in het *bestand function.json.*

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Hier is de Python-code:

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

In het volgende voorbeeld ziet u een gebeurtenishubtriggerbinding die de berichttekst van de trigger van de gebeurtenishub registreert.

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

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `EventHubTrigger` waarde afkomstig zou zijn van Event Hub. Parameters met deze annotaties zorgen ervoor dat de functie wordt uitgevoerd wanneer een gebeurtenis aankomt.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

 ---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](../articles/azure-functions/functions-dotnet-class-library.md)het kenmerk [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

De constructor van het kenmerk neemt de naam van de gebeurtenishub, de naam van de consumentengroep en de naam van een app-instelling die de verbindingstekenreeks bevat. Zie de [sectie triggerconfiguratie](#configuration)voor meer informatie over deze instellingen. Hier is `EventHubTriggerAttribute` een kenmerkvoorbeeld:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Zie Voorbeeld trigger [- C#](#example)voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik de [functions runtime library](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) [GebeurtenisHubTrigger-annotatie](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) op parameters waarvan de waarde afkomstig zou zijn van Event Hub. Parameters met deze annotaties zorgen ervoor dat de functie wordt uitgevoerd wanneer een gebeurtenis aankomt. Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `EventHubTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `eventHubTrigger`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die het gebeurtenisitem in functiecode vertegenwoordigt. |
|**Pad** |**EventHubName** | Functies alleen 1.x. De naam van de gebeurtenishub. Wanneer de naam van de gebeurtenishub ook aanwezig is in de verbindingstekenreeks, overschrijft die waarde deze eigenschap tijdens runtime. |
|**eventHubName** |**EventHubName** | Functies 2.x en hoger. De naam van de gebeurtenishub. Wanneer de naam van de gebeurtenishub ook aanwezig is in de verbindingstekenreeks, overschrijft die waarde deze eigenschap tijdens runtime. Kan worden verwezen via app-instellingen %eventHubName% |
|**consumerGroup** |**Consumentengroep** | Een optionele eigenschap die de [consumentengroep](../articles/event-hubs/event-hubs-features.md#event-consumers) instelt waarmee u zich abonneert op gebeurtenissen in de hub. Indien weggelaten, `$Default` wordt de consumentengroep gebruikt. |
|**Kardinaliteit** | N.v.t. | Voor Javascript. Ingesteld `many` op om batching mogelijk te maken.  Als deze wordt `one`weggelaten of ingesteld op , wordt één bericht doorgegeven aan de functie. |
|**verbinding** |**Verbinding** | De naam van een app-instelling die de verbindingstekenreeks bevat met de naamruimte van de gebeurtenishub. Kopieer deze verbindingstekenreeks door op de knop **Verbindingsgegevens** voor de [naamruimte](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)te klikken, niet op de gebeurtenishub zelf. Deze verbindingstekenreeks moet ten minste leesmachtigingen hebben om de trigger te activeren.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metagegevens van gebeurtenissen

De trigger gebeurtenishubs biedt verschillende [metagegevenseigenschappen.](../articles/azure-functions/./functions-bindings-expressions-patterns.md) Metagegevenseigenschappen kunnen worden gebruikt als onderdeel van bindende expressies in andere bindingen of als parameters in uw code. De eigenschappen zijn afkomstig van de klasse [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|De `PartitionContext` instantie.|
|`EnqueuedTimeUtc`|`DateTime`|De in de wachtrij tijd in UTC.|
|`Offset`|`string`|De verschuiving van de gegevens ten opzichte van de partitiestroom van de gebeurtenishub. De verschuiving is een markering of id voor een gebeurtenis binnen de stream van gebeurtenishubs. De id is uniek binnen een partitie van de gebeurtenishubsstream.|
|`PartitionKey`|`string`|De partitie naar welke gebeurtenisgegevens moeten worden verzonden.|
|`Properties`|`IDictionary<String,Object>`|De gebruikerseigenschappen van de gebeurtenisgegevens.|
|`SequenceNumber`|`Int64`|Het logische volgnummer van de gebeurtenis.|
|`SystemProperties`|`IDictionary<String,Object>`|De systeemeigenschappen, inclusief de gebeurtenisgegevens.|

Zie [codevoorbeelden](#example) die deze eigenschappen eerder in dit artikel gebruiken.

## <a name="hostjson-properties"></a>host.json-eigenschappen

Het [bestand host.json](../articles/azure-functions/functions-host-json.md#eventhub) bevat instellingen die het triggergedrag van gebeurtenishubs beheren.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]