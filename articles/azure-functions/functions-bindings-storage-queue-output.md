---
title: Azure Queue storage output binding voor Azure Functions
description: Informatie over het maken van Azure Queue-opslagberichten in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277334"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Uitvoerbindingen voor Azure-wachtrijopslag voor Azure-functies

Azure Functions kan nieuwe Azure Queue-opslagberichten maken door een uitvoerbinding in te stellen.

Zie het [overzicht](./functions-bindings-storage-queue.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een wachtrijbericht maakt voor elke ONTVANGEN HTTP-aanvraag.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een HTTP-triggerbinding weergegeven in een *function.json-bestand* en [C#-scriptcode (.csx)](functions-reference-csharp.md) die de binding gebruikt. De functie maakt een wachtrijitem met een **objectpayload van CustomQueueMessage** voor elke ONTVANGEN HTTP-aanvraag.

Hier is het *function.json* bestand:

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
      "name": "$return"
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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is C#-scriptcode waarmee één wachtrijbericht wordt gemaakt:

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

U meerdere berichten tegelijk verzenden `ICollector` `IAsyncCollector` met behulp van een of parameter. Hier is C#-scriptcode die meerdere berichten verzendt, één met de HTTP-aanvraaggegevens en een met hardgecodeerde waarden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een HTTP-triggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie maakt een wachtrijitem voor elke ONTVANGEN HTTP-aanvraag.

Hier is het *function.json* bestand:

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
      "name": "$return"
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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

U meerdere berichten tegelijk verzenden door een `myQueueItem` berichtarray voor de uitvoerbinding te definiëren. De volgende JavaScript-code verzendt twee wachtrijberichten met hardgecodeerde waarden voor elke ONTVANGEN HTTP-aanvraag.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt uitgelegd hoe u enkele en meerdere waarden uitvoeren naar opslagwachtrijen. De configuratie die nodig is voor *function.json* is hoe dan ook hetzelfde.

Een binding voor opslagwachtrijen wordt gedefinieerd in *function.json* waar *type* is ingesteld op `queue`.

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

Als u een afzonderlijk bericht in de wachtrij `set` wilt instellen, geeft u één waarde door aan de methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Als u meerdere berichten in de wachtrij wilt maken, declareert u een parameter als het `set` juiste lijsttype en geeft u een array met waarden (die overeenkomen met het lijsttype) door aan de methode.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 In het volgende voorbeeld wordt een Java-functie weergegeven die een wachtrijbericht maakt voor wanneer het wordt geactiveerd door een HTTP-verzoek.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `@QueueOutput` waarde naar wachtrijopslag zou worden geschreven.  Het parametertype `OutputBinding<T>`moet `T` zijn, waar is een native Java-type van een POJO.

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het [wachtrijkenmerk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Het kenmerk is `out` van toepassing op een parameter of de retourwaarde van de functie. De constructor van het kenmerk neemt de naam van de wachtrij aan, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Zie Voorbeeld van [Uitvoer](#example)voor een volledig voorbeeld .

U `StorageAccount` het kenmerk gebruiken om het opslagaccount op klasse-, methode- of parameterniveau op te geven. Zie Trigger - kenmerken voor meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Met `QueueOutput` de annotatie u een bericht schrijven als de uitvoer van een functie. In het volgende voorbeeld wordt een http-geactiveerde functie weergegeven die een wachtrijbericht maakt.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Eigenschap    | Beschrijving |
|-------------|-----------------------------|
|`name`       | Hiermee verklaart u de parameternaam in de functiehandtekening. Wanneer de functie wordt geactiveerd, heeft de waarde van deze parameter de inhoud van het wachtrijbericht. |
|`queueName`  | Hiermee verklaart u de naam van de wachtrij in het opslagaccount. |
|`connection` | Wijst naar de tekenreeks voor de verbinding met de opslagaccount. |

De parameter die `QueueOutput` aan de annotatie is gekoppeld, wordt getypt als een instantie [OutputBinding\<T.\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Queue` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `queue`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet ingesteld `out`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die de wachtrij in functiecode vertegenwoordigt. Ingesteld `$return` op de referentiewaarde voor het retourresultaat van de functie.|
|**queueName** |**QueueName** | De naam van de wachtrij. |
|**verbinding** | **Verbinding** |De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'MyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

Schrijf één wachtrijbericht met behulp van `out T paramName`een methodeparameter zoals . U het type methoderetour `out` gebruiken `T` in plaats van een parameter en een van de volgende typen zijn:

* Een object serializable als JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Als u probeert `CloudQueueMessage` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)

Schrijf in het script C# en C# meerdere wachtrijberichten met een van de volgende typen: 

* `ICollector<T>` of `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Schrijf één wachtrijbericht met behulp van `out T paramName`een methodeparameter zoals . Het `paramName` is de waarde `name` die is opgegeven in de eigenschap *van function.json*. U het type methoderetour `out` gebruiken `T` in plaats van een parameter en een van de volgende typen zijn:

* Een object serializable als JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Als u probeert `CloudQueueMessage` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)

Schrijf in het script C# en C# meerdere wachtrijberichten met een van de volgende typen: 

* `ICollector<T>` of `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Het item uitvoerwachtrij `context.bindings.<NAME>` is `<NAME>` beschikbaar via de plaats waar de naam die is gedefinieerd in *function.json*. U een tekenreeks of een JSON-serializable object gebruiken voor de payload van wachtrijitems.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitplaatsen van een Gebeurtenishub-bericht vanuit een functie:

- **Retourwaarde:** Stel `name` de eigenschap in `$return` *function.json* in op . Met deze configuratie blijft de retourwaarde van de functie bestaan als een wachtrijopslagbericht.

- **Noodzakelijk:** Geef een waarde door aan de [ingestelde](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) methode van de parameter die is opgegeven als [een uit-type.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) De waarde `set` die wordt doorgegeven aan wordt gehandhaafd als een wachtrijopslagbericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitplaatsen van een gebeurtenishubbericht vanuit een functie met de annotatie [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Retourwaarde:** Door de annotatie toe te passen op de functie zelf, blijft de retourwaarde van de functie bestaan als een gebeurtenishubbericht.

- **Noodzakelijk:** Als u de berichtwaarde expliciet wilt instellen, past u [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)de `T` aantekening toe op een specifieke parameter van het type , waar een POJO of een native Java-type is. Met deze configuratie blijft het `setValue` doorgeven van een waarde aan de methode de waarde als een gebeurtenishubbericht behouden.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Naslaginformatie |
|---|---|
| Wachtrij | [Foutcodes voor wachtrijen](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabel, Wachtrij | [Opslagfoutcodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabel, Wachtrij |  [Probleemoplossing](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versies 2.x en hoger. Het voorbeeld host.json-bestand hieronder bevat alleen de instellingen van versie 2.x+ voor deze binding. Zie [host.json-verwijzing naar Azure-functies voor](functions-host-json.md)meer informatie over algemene configuratie-instellingen in versies 2.x en verder.

> [!NOTE]
> Zie [host.json-verwijzing naar Azure-functies 1.x voor](functions-host-json-v1.md)een verwijzing naar host.json in Functies 1.x .

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
|maxPollingInterval|00:00:01|Het maximale interval tussen wachtrijpeilingen. Minimum is 00:00:00.100 (100 ms) en stappen tot 00:01:00 (1 min).  In 1.x is het gegevenstype milliseconden, en in 2.x en hoger is het een TimeSpan.|
|zichtbaarheidTime-out|00:00:00|Het tijdsinterval tussen nieuwe pogingen bij verwerking van een bericht mislukt. |
|batchSize|16|Het aantal wachtrijberichten dat de runtime van de functies gelijktijdig ophaalt en parallel verwerkt. Wanneer het aantal dat wordt `newBatchThreshold`verwerkt wordt neerop de , de runtime krijgt een andere batch en begint de verwerking van deze berichten. Dus het maximum aantal gelijktijdige berichten dat `batchSize` `newBatchThreshold`per functie wordt verwerkt is plus . Deze limiet is afzonderlijk van toepassing op elke functie die in de wachtrij wordt geactiveerd. <br><br>Als u parallelle uitvoering wilt voorkomen voor berichten die `batchSize` in één wachtrij zijn ontvangen, u instellen op 1. Deze instelling elimineert echter alleen gelijktijdigheid zolang uw functie-app op één virtuele machine (VM) wordt uitgevoerd. Als de functie-app wordt geschaald naar meerdere VM's, kan elke VM één instantie van elke functie die in de wachtrij wordt geactiveerd, uitvoeren.<br><br>Het `batchSize` maximum is 32. |
|maxDequeueCount|5|Het aantal keren dat u een bericht wilt verwerken voordat u het naar de gifwachtrij verplaatst.|
|newBatchThreshold|batchGrootte/2|Wanneer het aantal berichten dat gelijktijdig wordt verwerkt, aan dit aantal wordt afgenomen, wordt een andere batch opgehaald.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren als gegevens over wachtrijopslaggegevens worden gewijzigd (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
