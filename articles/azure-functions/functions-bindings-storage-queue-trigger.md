---
title: Azure-wachtrij opslag trigger voor Azure Functions
description: Meer informatie over het uitvoeren van een Azure function als Azure Queue-opslag voor gegevens wijzigingen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277373"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure-wachtrij opslag trigger voor Azure Functions

De trigger voor wachtrij opslag voert een functie uit wanneer berichten worden toegevoegd aan de Azure-wachtrij opslag.

## <a name="encoding"></a>Encoding

Functions verwachten een *Base64* -gecodeerde teken reeks. Eventuele aanpassingen van het coderings type (om gegevens voor te bereiden als een *Base64* -gecodeerde teken reeks) moeten worden geïmplementeerd in de aanroepende service.

## <a name="example"></a>Voorbeeld

Gebruik de wachtrij trigger om een functie te starten wanneer er een nieuw item in een wachtrij wordt ontvangen. Het wachtrijbericht wordt geleverd als invoer voor de functie.

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#](functions-dotnet-class-library.md) -functie `myqueue-items` waarmee de wachtrij wordt gepeild en een logboek wordt geschreven telkens wanneer een wachtrij-item wordt verwerkt.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een wachtrij trigger in een *Function. json* -bestand en [C# script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie pollt de `myqueue-items` wachtrij en schrijft een logboek wanneer een wachtrij-item wordt verwerkt.

Hier is het bestand *Function. json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-script code:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

In [usage](#usage) het gedeelte gebruik `myQueueItem`wordt uitgelegd hoe dit wordt genoemd `name` door de eigenschap in function. json.  De [sectie meta gegevens](#message-metadata) van het bericht geeft een uitleg van alle andere variabelen die worden weer gegeven.

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voor beeld ziet u een binding van een wachtrij trigger in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie pollt de `myqueue-items` wachtrij en schrijft een logboek wanneer een wachtrij-item wordt verwerkt.

Hier is het bestand *Function. json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

> [!NOTE]
> De para meter name komt `context.bindings.<name>` overeen met de Java script-code die de nettolading van het wachtrij-item bevat. Deze nettolading wordt ook door gegeven als de tweede para meter voor de functie.

Dit is de Java script-code:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

In [usage](#usage) het gedeelte gebruik `myQueueItem`wordt uitgelegd hoe dit wordt genoemd `name` door de eigenschap in function. json.  De [sectie meta gegevens](#message-metadata) van het bericht geeft een uitleg van alle andere variabelen die worden weer gegeven.

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een wachtrij bericht leest dat is door gegeven aan een functie via een trigger.

Een opslag wachtrij trigger is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

De code * _ \_init_\_. py* declareert een para meter `func.ServiceBusMessage`zoals, waarmee u het wachtrij bericht in uw functie kunt lezen.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

In het volgende Java-voor beeld ziet u een functie voor het activeren van een opslag wachtrij, waarmee `myqueuename`het geactiveerde bericht in de wachtrij wordt geregistreerd.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)de volgende kenmerken om een wachtrij trigger te configureren:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  De constructor van het kenmerk maakt de naam van de wachtrij die moet worden bewaakt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  U kunt de `Connection` eigenschap instellen om de app-instelling op te geven die het opslag account bevat dat Connection String moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Zie voor een volledig [voor beeld.](#example)

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Het opslag account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De `QueueTrigger` eigenschap van `Connection` het kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op dezelfde para meter `QueueTrigger` als het kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op de functie.
* Het `StorageAccount` kenmerk dat wordt toegepast op de klasse.
* De instelling van de app ' AzureWebJobsStorage '.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Met `QueueTrigger` de aantekening krijgt u toegang tot de wachtrij die de functie activeert. In het volgende voor beeld wordt het wachtrij bericht beschikbaar voor de functie `message` via de para meter.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Eigenschap    | Beschrijving |
|-------------|-----------------------------|
|`name`       | Declareert de parameter naam in de functie handtekening. Wanneer de functie wordt geactiveerd, heeft de waarde van deze para meter de inhoud van het wachtrij bericht. |
|`queueName`  | Declareert de wachtrij naam in het opslag account. |
|`connection` | Verwijst naar het opslag account connection string. |

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het `QueueTrigger` -kenmerk.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**voert** | N.v.t.| Moet worden ingesteld op `queueTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction**| N.v.t. | Alleen in het bestand *Function. json* . Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. |De naam van de variabele die de nettolading van het wachtrij-item bevat in de functie code.  |
|**queueName** | **QueueName**| De naam van de wachtrij die moet worden gecontroleerd. |
|**verbinding** | **Combi** |De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' mijn opslag '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met `AzureWebJobsStorage`de naam.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Toegang krijgen tot de bericht gegevens met behulp van een `string paramName`methode parameter, zoals. U kunt verbinding maken met een van de volgende typen:

* Object-met de functions runtime wordt een JSON-nettolading gedeserialiseerd naar een exemplaar van een wille keurige klasse die in uw code is gedefinieerd. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Als u probeert verbinding te maken `CloudQueueMessage` met een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Toegang krijgen tot de bericht gegevens met behulp van een `string paramName`methode parameter, zoals. De `paramName` is de waarde die is opgegeven `name` in de eigenschap van *Function. json*. U kunt verbinding maken met een van de volgende typen:

* Object-met de functions runtime wordt een JSON-nettolading gedeserialiseerd naar een exemplaar van een wille keurige klasse die in uw code is gedefinieerd. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Als u probeert verbinding te maken `CloudQueueMessage` met een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De nettolading van het wachtrij-item `context.bindings.<NAME>` is `<NAME>` beschikbaar via de locatie die overeenkomt met de naam die is gedefinieerd in *Function. json*. Als de payload JSON is, wordt de waarde in een object gedeserialiseerd.

# <a name="python"></a>[Python](#tab/python)

Open het wachtrij bericht via de para meter getypeerd als [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

Met de [Queue trigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) -aantekening krijgt u toegang tot het wachtrij bericht waarmee de functie is geactiveerd.

---

## <a name="message-metadata"></a>Meta gegevens van bericht

De trigger van de wachtrij biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. De eigenschappen zijn leden van de klasse [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`QueueTrigger`|`string`|Nettolading van de wachtrij (als een geldige teken reeks). Als de nettolading van het wachtrij bericht een teken `QueueTrigger` reeks is, heeft dezelfde waarde als de variabele met `name` de naam van de eigenschap in *Function. json*.|
|`DequeueCount`|`int`|Het aantal keren dat dit bericht in de wachtrij is geplaatst.|
|`ExpirationTime`|`DateTimeOffset`|Het tijdstip waarop het bericht verloopt.|
|`Id`|`string`|Bericht-ID van de wachtrij.|
|`InsertionTime`|`DateTimeOffset`|De tijd waarop het bericht is toegevoegd aan de wachtrij.|
|`NextVisibleTime`|`DateTimeOffset`|De tijd waarop het bericht volgende wordt weer gegeven.|
|`PopReceipt`|`string`|De pop-ontvangst van het bericht.|

## <a name="poison-messages"></a>Verontreinigde berichten

Wanneer een functie voor het activeren van een wachtrij mislukt, Azure Functions de functie tot vijf keer opnieuw geprobeerd voor een gegeven wachtrij bericht, met inbegrip van de eerste try. Als alle vijf de pogingen mislukken, voegt de functions-runtime een bericht toe aan een wachtrij met de naam * &lt;originalqueuename>-Poison*. U kunt een functie schrijven om berichten uit de verontreinigde wachtrij te verwerken door ze te registreren of om een melding te verzenden dat er hand matige aandacht nodig is.

Als u verontreinigde berichten hand matig wilt verwerken, controleert u de [dequeueCount](#message-metadata) van het bericht in de wachtrij.

## <a name="polling-algorithm"></a>Polling-algoritme

Met de trigger voor de wachtrij wordt een wille keurig exponentiële uitstel algoritme geïmplementeerd om het effect van polling bij een niet-actieve wachtrij op kosten voor opslag transacties te verminderen.

De algoritme maakt gebruik van de volgende logica:

- Wanneer een bericht wordt gevonden, wacht de runtime twee seconden en vervolgens wordt een ander bericht gecontroleerd
- Wanneer er geen bericht wordt gevonden, wacht het ongeveer vier seconden voordat u het opnieuw probeert.
- Na volgende mislukte pogingen om een wachtrij bericht op te halen, blijft de wacht tijd toenemen totdat de maximale wacht tijd is bereikt. de standaard waarde is één minuut.
- De maximale wacht tijd kan worden geconfigureerd via de `maxPollingInterval` eigenschap in het [bestand host. json](functions-host-json.md#queues).

Voor lokale ontwikkeling wordt het maximale polling-interval standaard ingesteld op twee seconden.

In verband met de facturering is de tijd die nodig is voor het uitvoeren van een polling door de runtime "gratis" en niet voor uw account.

## <a name="concurrency"></a>Gelijktijdigheid

Wanneer er meerdere wachtrij berichten wachten, haalt de trigger van de wachtrij een batch berichten op en roept de functie-exemplaren gelijktijdig aan om ze te verwerken. De Batch grootte is standaard 16. Wanneer het aantal dat wordt verwerkt, wordt ingesteld op 8, haalt de runtime een nieuwe batch op en begint deze met de verwerking van die berichten. Het maximum aantal gelijktijdige berichten dat per functie op één virtuele machine (VM) wordt verwerkt, is dus 24. Deze limiet is van toepassing op elke virtuele machine die door de wachtrij wordt geactiveerd. Als uw functie-app wordt geschaald naar meerdere Vm's, wacht elke virtuele machine op Triggers en probeert de functies uit te voeren. Als een functie-app bijvoorbeeld uitschaalt naar 3 Vm's, is het maximum aantal gelijktijdige exemplaren van één door de wachtrij geactiveerde functie 72.

De Batch grootte en de drempel waarde voor het ophalen van een nieuwe batch kunnen worden geconfigureerd in het [bestand host. json](functions-host-json.md#queues). Als u parallelle uitvoering voor door de wachtrij geactiveerde functies in een functie-app wilt minimaliseren, kunt u de Batch grootte instellen op 1. Met deze instelling elimineert u alleen gelijktijdigheid, zolang uw functie-app op één virtuele machine (VM) wordt uitgevoerd. 

De wachtrij trigger voor komt automatisch dat een functie meerdere keren een wachtrij bericht verwerkt; functions hoeven niet te worden geschreven om te worden idempotent.

## <a name="hostjson-properties"></a>host. json-eigenschappen

Het bestand [host. json](functions-host-json.md#queues) bevat instellingen die het gedrag van de wachtrij activeren controleren. Zie de sectie [host. json-instellingen](functions-bindings-storage-queue-output.md#hostjson-settings) voor meer informatie over de beschik bare instellingen.

## <a name="next-steps"></a>Volgende stappen

- [Wachtrij opslag berichten schrijven (uitvoer binding)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
