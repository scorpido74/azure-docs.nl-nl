---
title: Azure Queue-opslag bindingen voor Azure Functions
description: Meer informatie over het gebruik van de Azure Queue Storage-trigger en uitvoer binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 3e72bd366cdbba1d73bc05f98d3848e2d4f0ca6c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925341"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue-opslag bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Queue Storage-bindingen in Azure Functions. Azure Functions ondersteunt trigger-en uitvoer bindingen voor wacht rijen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De bindingen voor de wachtrij opslag zijn opgenomen in het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet versie 2. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-opslagplaats.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De bindingen voor de wachtrij opslag zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-opslagplaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
Functions verwachten een *Base64* -gecodeerde teken reeks. Eventuele aanpassingen van het coderings type (om gegevens voor te bereiden als een *Base64* -gecodeerde teken reeks) moeten worden geïmplementeerd in de aanroepende service.

## <a name="trigger"></a>Trigger

Gebruik de wachtrij trigger om een functie te starten wanneer er een nieuw item in een wachtrij wordt ontvangen. Het wachtrijbericht wordt geleverd als invoer voor de functie.

## <a name="trigger---example"></a>Trigger - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee de `myqueue-items` wachtrij wordt gepeild en een logboek wordt geschreven telkens wanneer een wachtrij-item wordt verwerkt.

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

### <a name="trigger---c-script-example"></a>Trigger - voorbeeld van C#-script

In het volgende voor beeld ziet u een binding van een wachtrij trigger in een *Function. json* file [ C# -en script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie pollt de `myqueue-items` wachtrij en schrijft een logboek wanneer een wachtrij-item wordt verwerkt.

Hier volgt de *function.json* bestand:

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

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de C#-scriptcode:

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

In het gedeelte [gebruik](#trigger---usage) wordt uitgelegd `myQueueItem`. dit wordt aangeduid met de eigenschap `name` in function. json.  De [sectie meta gegevens](#trigger---message-metadata) van het bericht geeft een uitleg van alle andere variabelen die worden weer gegeven.

### <a name="trigger---javascript-example"></a>Trigger - JavaScript-voorbeeld

In het volgende voor beeld ziet u een binding van een wachtrij trigger in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie pollt de `myqueue-items` wachtrij en schrijft een logboek wanneer een wachtrij-item wordt verwerkt.

Hier volgt de *function.json* bestand:

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

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

> [!NOTE]
> De para meter name geeft als `context.bindings.<name>` in de Java script-code die de nettolading van het wachtrij-item bevat. Deze nettolading wordt ook door gegeven als de tweede para meter voor de functie.

Dit is de JavaScript-code:

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

In het gedeelte [gebruik](#trigger---usage) wordt uitgelegd `myQueueItem`. dit wordt aangeduid met de eigenschap `name` in function. json.  De [sectie meta gegevens](#trigger---message-metadata) van het bericht geeft een uitleg van alle andere variabelen die worden weer gegeven.

### <a name="trigger---java-example"></a>Trigger - Java-voorbeeld

In het volgende Java-voor beeld ziet u een trigger voor opslag wachtrij waarmee het geactiveerde bericht in de wachtrij `myqueuename`wordt geregistreerd.

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

### <a name="trigger---python-example"></a>Voor beeld van een trigger-python

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

De code  *_\_init_\_. py* declareert een para meter als `func.ServiceBusMessage` waarmee u het wachtrij bericht in uw functie kunt lezen.

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

## <a name="trigger---attributes"></a>Trigger - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken voor het configureren van een wachtrij trigger:

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

  U kunt de eigenschap `Connection` instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Zie voor een compleet voorbeeld [Trigger - voorbeeld met C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de parameter, klasseniveau of methode. Het volgende voorbeeld toont het klasseniveau en methode:

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

* De `QueueTrigger` van kenmerk `Connection` eigenschap.
* De `StorageAccount` kenmerk toegepast op de dezelfde parameter als de `QueueTrigger` kenmerk.
* De `StorageAccount` kenmerk toegepast op de functie.
* De `StorageAccount` kenmerk toegepast op de klasse.
* De instelling van de app ' AzureWebJobsStorage '.

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `QueueTrigger` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t.| Moet worden ingesteld op `queueTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction**| n.v.t. | Alleen in het bestand *Function. json* . Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**De naam** | n.v.t. |De naam van de variabele die de nettolading van het wachtrij-item bevat in de functie code.  |
|**queueName** | **Wachtrijnaam**| De naam van de wachtrij die moet worden gecontroleerd. |
|**verbinding** | **Verbinding** |De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

In C# en C# script opent u de bericht gegevens met behulp van een methode parameter, zoals `string paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. U kunt verbinding maken met een van de volgende typen:

* Object-met de functions runtime wordt een JSON-nettolading gedeserialiseerd naar een exemplaar van een wille keurige klasse die in uw code is gedefinieerd. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Als u probeert verbinding te maken met `CloudQueueMessage` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

Gebruik in Java script `context.bindings.<name>` om toegang te krijgen tot de nettolading van het wachtrij-item. Als de payload JSON is, wordt deze gedeserialiseerd tot een object.

## <a name="trigger---message-metadata"></a>Trigger - bericht-metagegevens

De trigger van de wachtrij biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van de expressies in andere bindingen voor gegevensbinding of als parameters in uw code. Dit zijn eigenschappen van de klasse [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`QueueTrigger`|`string`|Nettolading van de wachtrij (als een geldige teken reeks). Als de nettolading van het wachtrij bericht als een teken reeks, `QueueTrigger` dezelfde waarde heeft als de variabele met de naam van de eigenschap `name` in *Function. json*.|
|`DequeueCount`|`int`|Het aantal keren dat dit bericht in de wachtrij is geplaatst.|
|`ExpirationTime`|`DateTimeOffset`|Het tijdstip waarop het bericht verloopt.|
|`Id`|`string`|Bericht-ID van de wachtrij.|
|`InsertionTime`|`DateTimeOffset`|De tijd waarop het bericht is toegevoegd aan de wachtrij.|
|`NextVisibleTime`|`DateTimeOffset`|De tijd waarop het bericht volgende wordt weer gegeven.|
|`PopReceipt`|`string`|De pop-ontvangst van het bericht.|

## <a name="trigger---poison-messages"></a>Trigger - Onverwerkbare berichten

Wanneer een functie voor het activeren van een wachtrij mislukt, Azure Functions de functie tot vijf keer opnieuw geprobeerd voor een gegeven wachtrij bericht, met inbegrip van de eerste try. Als alle vijf de pogingen mislukken, voegt de functions-runtime een bericht toe aan een wachtrij met de naam *&lt;originalqueuename >-Poison*. U kunt een functie schrijven om berichten uit de verontreinigde wachtrij te verwerken door ze te registreren of om een melding te verzenden dat er hand matige aandacht nodig is.

Als u verontreinigde berichten hand matig wilt verwerken, controleert u de [dequeueCount](#trigger---message-metadata) van het bericht in de wachtrij.

## <a name="trigger---polling-algorithm"></a>Trigger-polling-algoritme

Met de trigger voor de wachtrij wordt een wille keurig exponentiële uitstel algoritme geïmplementeerd om het effect van polling bij een niet-actieve wachtrij op kosten voor opslag transacties te verminderen.  Wanneer een bericht wordt gevonden, wacht de runtime twee seconden en vervolgens wordt een ander bericht gecontroleerd. Wanneer er geen bericht wordt gevonden, wacht het ongeveer vier seconden voordat u het opnieuw probeert. Na volgende mislukte pogingen om een wachtrij bericht op te halen, blijft de wacht tijd toenemen totdat de maximale wacht tijd is bereikt. de standaard waarde is één minuut. De maximale wacht tijd kan worden geconfigureerd via de eigenschap `maxPollingInterval` in het [bestand host. json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Trigger-gelijktijdigheid

Wanneer er meerdere wachtrij berichten wachten, haalt de trigger van de wachtrij een batch berichten op en roept de functie-exemplaren gelijktijdig aan om ze te verwerken. De Batch grootte is standaard 16. Wanneer het aantal dat wordt verwerkt, wordt ingesteld op 8, haalt de runtime een nieuwe batch op en begint deze met de verwerking van die berichten. Het maximum aantal gelijktijdige berichten dat per functie op één virtuele machine (VM) wordt verwerkt, is dus 24. Deze limiet is van toepassing op elke virtuele machine die door de wachtrij wordt geactiveerd. Als uw functie-app wordt geschaald naar meerdere Vm's, wacht elke virtuele machine op Triggers en probeert de functies uit te voeren. Als een functie-app bijvoorbeeld uitschaalt naar 3 Vm's, is het maximum aantal gelijktijdige exemplaren van één door de wachtrij geactiveerde functie 72.

De Batch grootte en de drempel waarde voor het ophalen van een nieuwe batch kunnen worden geconfigureerd in het [bestand host. json](functions-host-json.md#queues). Als u parallelle uitvoering voor door de wachtrij geactiveerde functies in een functie-app wilt minimaliseren, kunt u de Batch grootte instellen op 1. Met deze instelling elimineert u alleen gelijktijdigheid, zolang uw functie-app op één virtuele machine (VM) wordt uitgevoerd. 

De wachtrij trigger voor komt automatisch dat een functie meerdere keren een wachtrij bericht verwerkt; functions hoeven niet te worden geschreven om te worden idempotent.

## <a name="trigger---hostjson-properties"></a>Trigger - eigenschappen voor host.json

Het bestand [host. json](functions-host-json.md#queues) bevat instellingen die het gedrag van de wachtrij activeren controleren. Zie de sectie [host. json-instellingen](#hostjson-settings) voor meer informatie over de beschik bare instellingen.

## <a name="output"></a>Uitvoer

Gebruik de Azure Queue Storage-uitvoer binding om berichten te schrijven naar een wachtrij.

## <a name="output---example"></a>Uitvoer - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Uitvoer - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een wachtrij bericht wordt gemaakt voor elke ontvangen HTTP-aanvraag.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Uitvoer - voorbeeld van C#-script

In het volgende voor beeld ziet u een binding van een http-trigger in een *Function. json* file [ C# -en script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt een wachtrij-item met een **CustomQueueMessage** -object lading voor elke ontvangen HTTP-aanvraag.

Hier volgt de *function.json* bestand:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is C# de script code voor het maken van één wachtrij bericht:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

U kunt meerdere berichten tegelijk verzenden met behulp van een `ICollector`-of `IAsyncCollector`-para meter. Dit is C# de script code waarmee meerdere berichten worden verzonden, een met de HTTP-aanvraag gegevens en een met in code vastgelegde waarden:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Uitvoer - JavaScript-voorbeeld

In het volgende voor beeld ziet u een binding van de HTTP-trigger in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt een wachtrij-item voor elke ontvangen HTTP-aanvraag.

Hier volgt de *function.json* bestand:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

U kunt meerdere berichten tegelijk verzenden door een bericht matrix te definiëren voor de `myQueueItem` uitvoer binding. De volgende Java script-code verzendt twee wachtrij berichten met in code vastgelegde waarden voor elke ontvangen HTTP-aanvraag.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Uitvoer - Java-voorbeeld

 In het volgende voor beeld ziet u een Java-functie die een wachtrij bericht maakt voor wanneer door een HTTP-aanvraag wordt geactiveerd.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@QueueOutput` annotatie voor para meters waarvan de waarde wordt geschreven naar de wachtrij opslag.  Het parametertype moet `OutputBinding<T>`, waarbij T alle systeemeigen Java-type van een POJO.

### <a name="output---python-example"></a>Voor beeld van de uitvoer-python

In het volgende voor beeld ziet u hoe u één en meerdere waarden naar opslag wachtrijen kunt uitvoeren. De configuratie die nodig is voor *Function. json* is op dezelfde manier.

Een opslag wachtrij binding is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Als u een afzonderlijk bericht in de wachtrij wilt instellen, geeft u één waarde door aan de `set` methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Als u meerdere berichten in de wachtrij wilt maken, declareert u een para meter als het juiste lijst Type en geeft u een matrix met waarden (die overeenkomen met het lijst Type) door aan de `set` methode.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Het kenmerk is van toepassing op een `out` para meter of de retour waarde van de functie. De constructor van het kenmerk maakt de naam van de wachtrij, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

U kunt de eigenschap `Connection` instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Zie voor een compleet voorbeeld [uitvoer - voorbeeld met C#](#output---c-example).

U kunt het kenmerk `StorageAccount` gebruiken om het opslag account op te geven bij klasse, methode of parameter niveau. Zie trigger-Attributes (Engelstalig) voor meer informatie.

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `Queue` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op `queue`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | n.v.t. | Moet worden ingesteld op `out`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**De naam** | n.v.t. | De naam van de variabele die de wachtrij in functie code vertegenwoordigt. Ingesteld op `$return` om te verwijzen naar de retour waarde van de functie.|
|**queueName** |**Wachtrijnaam** | De naam van de wachtrij. |
|**verbinding** | **Verbinding** |De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

In C# en C# script, een enkel wachtrij bericht schrijven met behulp van een methode parameter, zoals `out T paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. U kunt het retour type van de methode gebruiken in plaats van een `out` para meter, en `T` kan een van de volgende typen zijn:

* Een object dat als JSON kan worden geserialiseerd
* `string`
* `byte[]`
* [CloudQueueMessage] 

Als u probeert verbinding te maken met `CloudQueueMessage` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

Schrijf C# in C# en script meerdere wachtrij berichten met behulp van een van de volgende typen: 

* `ICollector<T>` of `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

Gebruik in Java script-functies `context.bindings.<name>` om toegang te krijgen tot het bericht van de uitvoer wachtrij. U kunt een teken reeks of een JSON-serialiseerbaar object gebruiken voor de nettolading van het wachtrij-item.


## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Referentie |
|---|---|
| Wachtrij | [Fout codes voor de wachtrij](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij |  [Problemen oplossen](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het voor beeld van een host. JSON-bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x en hoger de [verwijzing host. json voor Azure functions](functions-host-json.md).

> [!NOTE]
> Voor een verwijzing van host.json in functies 1.x, Zie [naslaginformatie over host.json voor Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Het maximum interval tussen de polls van de wachtrij. Minimum is 00:00:00.100 (100 MS) en wordt verhoogd naar 00:01:00 (1 min.).  In 1. x is het gegevens type milliseconden en in 2. x en hoger het een time span.|
|visibilityTimeout|00:00:00|Het tijds interval tussen nieuwe pogingen wanneer het verwerken van een bericht mislukt. |
|batchSize|16|Het aantal wachtrij berichten dat door de functions-runtime gelijktijdig wordt opgehaald en processen parallel. Wanneer het nummer dat wordt verwerkt, naar de `newBatchThreshold`, haalt de runtime een nieuwe batch op en begint deze met de verwerking van die berichten. Het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, is `batchSize` plus `newBatchThreshold`. Deze limiet geldt afzonderlijk voor elke door de wachtrij geactiveerde functie. <br><br>Als u een parallelle uitvoering wilt voor komen voor berichten die worden ontvangen op één wachtrij, kunt u `batchSize` instellen op 1. Met deze instelling elimineert u echter alleen gelijktijdigheid, zolang uw functie-app wordt uitgevoerd op één virtuele machine (VM). Als de functie-app wordt geschaald naar meerdere Vm's, kan elke virtuele machine één exemplaar van elke door de wachtrij geactiveerde functie uitvoeren.<br><br>De maximale `batchSize` is 32. |
|maxDequeueCount|5|Het aantal keren dat een bericht moet worden verwerkt voordat het naar de verontreinigde wachtrij wordt verplaatst.|
|newBatchThreshold|batchSize/2|Telkens wanneer het aantal berichten dat gelijktijdig wordt verwerkt, naar dit nummer wordt opgehaald, haalt de runtime een andere batch op.|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Ga naar een zelf studie waarin een uitvoer binding voor de wachtrij opslag wordt gebruikt](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
