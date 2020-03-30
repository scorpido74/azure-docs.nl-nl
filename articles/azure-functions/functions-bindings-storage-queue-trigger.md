---
title: Azure Queue-opslagtrigger voor Azure-functies
description: Lees bijvoorbeeld een Azure-functie terwijl azure queue-opslaggegevens worden gewijzigd.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277373"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Queue-opslagtrigger voor Azure-functies

De trigger voor wachtrijopslag voert een functie uit omdat berichten worden toegevoegd aan azure queue-opslag.

## <a name="encoding"></a>Encoding

Functies verwachten een *base64* gecodeerde tekenreeks. Eventuele aanpassingen van het coderingstype (om gegevens voor te bereiden als een *basis64* gecodeerde tekenreeks) moeten worden geïmplementeerd in de aanroepende service.

## <a name="example"></a>Voorbeeld

Gebruik de wachtrijtrigger om een functie te starten wanneer een nieuw item in een wachtrij wordt ontvangen. Het wachtrijbericht wordt geleverd als invoer voor de functie.

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een `myqueue-items` [C#-functie](functions-dotnet-class-library.md) weergegeven die de wachtrij peilt en een logboek schrijft telkens wanneer een wachtrijitem wordt verwerkt.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een wachtrijtriggerbinding weergegeven in een *function.json-bestand* en [C#-scriptcode (.csx)](functions-reference-csharp.md) die de binding gebruikt. De functie `myqueue-items` peilt de wachtrij en schrijft een logboek telkens wanneer een wachtrijitem wordt verwerkt.

Hier is het *function.json* bestand:

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

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

De [sectie](#usage) `myQueueItem`gebruik legt uit `name` , die door de eigenschap in function.json wordt genoemd.  In de [sectie metagegevens](#message-metadata) van het bericht worden alle andere getoonde variabelen uitgelegd.

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een wachtrijtriggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie `myqueue-items` peilt de wachtrij en schrijft een logboek telkens wanneer een wachtrijitem wordt verwerkt.

Hier is het *function.json* bestand:

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

> [!NOTE]
> De parameter naam `context.bindings.<name>` weerspiegelt zoals in de JavaScript-code die de payload van het wachtrijitem bevat. Deze payload wordt ook doorgegeven als de tweede parameter naar de functie.

Hier is de JavaScript-code:

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

De [sectie](#usage) `myQueueItem`gebruik legt uit `name` , die door de eigenschap in function.json wordt genoemd.  In de [sectie metagegevens](#message-metadata) van het bericht worden alle andere getoonde variabelen uitgelegd.

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt uitgelegd hoe u een wachtrijbericht lezen dat via een trigger naar een functie wordt doorgegeven.

Een trigger voor de opslagwachtrij wordt gedefinieerd in `queueTrigger` *function.json* waar het *type* is ingesteld op .

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

De code * _ \_init_\_.py* `func.ServiceBusMessage`declareert een parameter als, waarmee u het wachtrijbericht in uw functie lezen.

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

In het volgende Java-voorbeeld wordt een triggerfunctie voor `myqueuename`opslagwachtrijen weergegeven, die het geactiveerde bericht registreert dat in de wachtrij is geplaatst.

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de volgende kenmerken om een wachtrijtrigger te configureren:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  De constructor van het kenmerk neemt de naam van de wachtrij om te controleren, zoals wordt weergegeven in het volgende voorbeeld:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  U `Connection` de eigenschap zo instellen dat de app-instelling die de tekenreeks voor opslagaccountverbinding bevat, wordt opgegeven, zoals in het volgende voorbeeld wordt weergegeven:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Zie [voorbeeld voor](#example)een volledig voorbeeld .

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het te gebruiken opslagaccount op te geven. De constructeur neemt de naam aan van een app-instelling die een tekenreeks voor opslagverbindingen bevat. Het kenmerk kan worden toegepast op parameter-, methode- of klassenniveau. In het volgende voorbeeld ziet u het niveau van de klasse en het methodeniveau:

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

De te gebruiken opslagrekening wordt in de volgende volgorde bepaald:

* De `QueueTrigger` eigenschap `Connection` van het attribuut.
* Het `StorageAccount` kenmerk dat is toegepast `QueueTrigger` op dezelfde parameter als het kenmerk.
* Het `StorageAccount` kenmerk dat op de functie wordt toegepast.
* Het `StorageAccount` kenmerk dat op de klasse is toegepast.
* De app-instelling AzureWebJobsStorage.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De `QueueTrigger` annotatie geeft u toegang tot de wachtrij die de functie activeert. In het volgende voorbeeld wordt het wachtrijbericht via de parameter beschikbaar gesteld voor de `message` functie.

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
|`name`       | Hiermee verklaart u de parameternaam in de functiehandtekening. Wanneer de functie wordt geactiveerd, heeft de waarde van deze parameter de inhoud van het wachtrijbericht. |
|`queueName`  | Hiermee verklaart u de naam van de wachtrij in het opslagaccount. |
|`connection` | Wijst naar de tekenreeks voor de verbinding met de opslagaccount. |

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `QueueTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t.| Moet ingesteld `queueTrigger`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction**| N.v.t. | Alleen in het *function.json-bestand.* Moet ingesteld `in`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. |De naam van de variabele die de payload van het wachtrijitem in de functiecode bevat.  |
|**queueName** | **QueueName**| De naam van de wachtrij om te peilen. |
|**verbinding** | **Verbinding** |De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'MyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

Toegang tot de berichtgegevens met `string paramName`behulp van een methodeparameter zoals . U zich binden aan een van de volgende typen:

* Object - De runtime functies deserialiseert een JSON-payload in een instantie van een willekeurige klasse die in uw code is gedefinieerd. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Als u probeert `CloudQueueMessage` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Toegang tot de berichtgegevens met `string paramName`behulp van een methodeparameter zoals . Het `paramName` is de waarde `name` die is opgegeven in de eigenschap *van function.json*. U zich binden aan een van de volgende typen:

* Object - De runtime functies deserialiseert een JSON-payload in een instantie van een willekeurige klasse die in uw code is gedefinieerd. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Als u probeert `CloudQueueMessage` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)

# <a name="javascript"></a>[Javascript](#tab/javascript)

De payload van het `context.bindings.<NAME>` `<NAME>` wachtrijitem is beschikbaar via de plaats waar de naam die is gedefinieerd in *function.json*. Als de payload JSON is, wordt de waarde gedeserialiseerd in een object.

# <a name="python"></a>[Python](#tab/python)

Toegang tot het wachtrijbericht via de parameter die is getypt als [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

De [annotatie QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) geeft u toegang tot het wachtrijbericht dat de functie heeft geactiveerd.

---

## <a name="message-metadata"></a>Metagegevens van berichten

De wachtrijtrigger biedt verschillende [metagegevenseigenschappen.](./functions-bindings-expressions-patterns.md#trigger-metadata) Deze eigenschappen kunnen worden gebruikt als onderdeel van bindende expressies in andere bindingen of als parameters in uw code. De eigenschappen zijn lid van de klasse [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`QueueTrigger`|`string`|Wachtrij payload (als een geldige string). Als de payload van het `QueueTrigger` wachtrijbericht een tekenreeks is, `name` heeft deze dezelfde waarde als de variabele die door de eigenschap in *function.json*wordt genoemd.|
|`DequeueCount`|`int`|Het aantal keren dat dit bericht in de wachtrij is geplaatst.|
|`ExpirationTime`|`DateTimeOffset`|Het tijdstip waarop het bericht verloopt.|
|`Id`|`string`|Id van wachtrijberichten.|
|`InsertionTime`|`DateTimeOffset`|Het tijdstip waarop het bericht aan de wachtrij is toegevoegd.|
|`NextVisibleTime`|`DateTimeOffset`|Het tijdstip waarop het bericht vervolgens zichtbaar is.|
|`PopReceipt`|`string`|Het bericht is pop-ontvangst.|

## <a name="poison-messages"></a>Gifberichten

Wanneer een wachtrijtriggerfunctie mislukt, probeert Azure Functions de functie tot vijf keer opnieuw voor een bepaald wachtrijbericht, inclusief de eerste poging. Als alle vijf pogingen mislukken, voegt de taakruntime een bericht toe aan een wachtrij met de naam * &lt;originalqueuename>-gif*. U een functie schrijven om berichten uit de gifwachtrij te verwerken door ze te loggen of een melding te sturen dat handmatige aandacht nodig is.

Als u gifberichten handmatig wilt verwerken, schakelt u de [wachtrijTelling](#message-metadata) van het wachtrijbericht in.

## <a name="polling-algorithm"></a>Polling-algoritme

De wachtrijtrigger implementeert een willekeurig exponentieel back-offalgoritme om het effect van idle-queue polling op de kosten van opslagtransacties te verminderen.

Het algoritme gebruikt de volgende logica:

- Wanneer een bericht wordt gevonden, wacht de runtime twee seconden en controleert u vervolgens op een ander bericht
- Wanneer er geen bericht wordt gevonden, wacht het ongeveer vier seconden voordat u het opnieuw probeert.
- Na daaropvolgende mislukte pogingen om een wachtrijbericht te krijgen, blijft de wachttijd toenemen totdat deze de maximale wachttijd bereikt, die standaard één minuut is.
- De maximale wachttijd is `maxPollingInterval` configureerbaar via de eigenschap in het [host.json-bestand](functions-host-json.md#queues).

Voor lokale ontwikkeling wordt het maximale polling-interval standaard ingesteld op twee seconden.

Met betrekking tot facturering, tijd besteed polling door de runtime is "gratis" en niet meegeteld voor uw account.

## <a name="concurrency"></a>Gelijktijdigheid

Wanneer er meerdere wachtrijberichten wachten, haalt de wachtrijtrigger een batch berichten op en roept functie-instanties gelijktijdig aan om deze te verwerken. Standaard is de batchgrootte 16. Wanneer het aantal dat wordt verwerkt tot 8 wordt, krijgt de runtime een andere batch en begint deze berichten te verwerken. Dus het maximum aantal gelijktijdige berichten wordt verwerkt per functie op een virtuele machine (VM) is 24. Deze limiet is afzonderlijk van toepassing op elke functie die in de wachtrij wordt geactiveerd op elke vm. Als uw functie-app wordt geschaald naar meerdere VM's, wacht elke VM op triggers en probeert deze functies uit te voeren. Als een functie-app bijvoorbeeld wordt geschaald naar 3 VM's, is het standaard maximumaantal gelijktijdige exemplaren van één functie die in de wachtrij wordt geactiveerd 72.

De batchgrootte en de drempelwaarde voor het verkrijgen van een nieuwe batch zijn configureerbaar in het [bestand host.json](functions-host-json.md#queues). Als u parallelle uitvoering voor functies die in de wachtrij worden geactiveerd in een functie-app wilt minimaliseren, u de batchgrootte instellen op 1. Deze instelling elimineert gelijktijdigheid alleen zolang uw functie-app op één virtuele machine (VM) wordt uitgevoerd. 

De wachtrijtrigger voorkomt automatisch dat een functie een wachtrijbericht meerdere keren verwerkt. functies hoeven niet te worden geschreven om idempotent te zijn.

## <a name="hostjson-properties"></a>host.json-eigenschappen

Het [bestand host.json](functions-host-json.md#queues) bevat instellingen die het triggergedrag van de wachtrij bepalen. Zie de sectie [host.json-instellingen](functions-bindings-storage-queue-output.md#hostjson-settings) voor meer informatie over de beschikbare instellingen.

## <a name="next-steps"></a>Volgende stappen

- [Opslagberichten voor wachtrijen schrijven (uitvoerbinding)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
