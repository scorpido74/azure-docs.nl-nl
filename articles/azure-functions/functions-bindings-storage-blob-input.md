---
title: Invoer binding voor Azure Blob Storage voor Azure Functions
description: Meer informatie over hoe u Azure Blob Storage-gegevens kunt leveren aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 33db9a8d86e02db2076cdb85170d466697930b96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633888"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Invoer binding voor Azure Blob Storage voor Azure Functions

Met de invoer binding kunt u Blob Storage-gegevens als invoer naar een Azure-functie lezen.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een wachtrij trigger en een invoer-BLOB-binding. Het wachtrij bericht bevat de naam van de BLOB en de functie registreert de grootte van de blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* -bestand en [C# script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een tekst-blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In het bestand *Function. json* wordt de `queueTrigger` eigenschap meta gegevens gebruikt om de naam van de BLOB op `path` te geven in de eigenschappen:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-script code:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* -bestand en [Java script-code](functions-reference-node.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In het bestand *Function. json* wordt de `queueTrigger` eigenschap meta gegevens gebruikt om de naam van de BLOB op `path` te geven in de eigenschappen:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de Java script-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* -bestand en [python-code](functions-reference-python.md) die gebruikmaken van de bindingen. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In het bestand *Function. json* wordt de `queueTrigger` eigenschap meta gegevens gebruikt om de naam van de BLOB op `path` te geven in de eigenschappen:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voor beelden:

* [HTTP-trigger, naam van de BLOB opzoeken in de query reeks](#http-trigger-look-up-blob-name-from-query-string)
* [Wachtrij trigger, naam van de BLOB ontvangen van wachtrij bericht](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-trigger, naam van de BLOB opzoeken in de query reeks

 In het volgende voor beeld ziet u een Java- `HttpTrigger` functie die gebruikmaakt van de aantekening om een para meter te ontvangen met de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie `byte[]`als een.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wachtrij trigger, naam van de BLOB ontvangen van wachtrij bericht

 In het volgende voor beeld ziet u een Java- `QueueTrigger` functie die gebruikmaakt van de aantekening om een bericht te ontvangen met daarin de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie `byte[]`als een.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Gebruik in de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime)de `@BlobInput` annotatie voor para meters waarvan de waarde afkomstig is uit een blob.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable `Optional<T>`-waarden met.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk gebruikt het pad naar de BLOB en een `FileAccess` para meter die aangeeft dat er een lees-of schrijf bewerking wordt uitgevoerd, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

U kunt het `StorageAccount` -kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie [trigger-Attributes en annotaties](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Met `@BlobInput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het `Blob` -kenmerk.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**voert** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Uitzonde ringen worden vermeld in de sectie [gebruik](#usage) . |
|**naam** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.|
|**programmapad** |**BlobPath** | Het pad naar de blob. |
|**verbinding** |**Combi**| De naam van een app-instelling die de [opslag Connection String](../storage/common/storage-configure-connection-string.md) bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met `AzureWebJobsStorage`de naam.<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang krijgen tot BLOB `context.bindings.<NAME>` - `<NAME>` gegevens die overeenkomen met de waarde die is gedefinieerd in *Function. json*.

# <a name="python"></a>[Python](#tab/python)

Toegang tot BLOB-gegevens via de para meter getypeerd als [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

Met `@BlobInput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens van een functie schrijven](./functions-bindings-storage-blob-output.md)
