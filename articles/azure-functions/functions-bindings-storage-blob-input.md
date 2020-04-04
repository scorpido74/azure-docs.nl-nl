---
title: Azure Blob-opslaginvoerbinding voor Azure-functies
description: Meer informatie over het verstrekken van Azure Blob-opslaggegevens aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 33db9a8d86e02db2076cdb85170d466697930b96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633888"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Blob-opslaginvoerbinding voor Azure-functies

Met de invoerbinding u blobopslaggegevens lezen als invoer voor een Azure-functie.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Het volgende voorbeeld is een [C#-functie](functions-dotnet-class-library.md) die een wachtrijtrigger en een invoerblobbinding gebruikt. Het wachtrijbericht bevat de naam van de blob en de functie registreert de grootte van de blob.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voorbeeld worden blobinvoer- en uitvoerbindingen weergegeven in een *function.json-bestand* en [C#-scriptcode (.csx)](functions-reference-csharp.md) die de bindingen gebruikt. De functie maakt een kopie van een tekstblob. De functie wordt geactiveerd door een wachtrijbericht met de naam van de blob die u wilt kopiëren. De nieuwe blob heeft de naam *{originalblobname}-Copy*.

In het *bestand function.json* wordt de `queueTrigger` eigenschap metagegevens `path` gebruikt om de blobnaam in de eigenschappen op te geven:

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

In het volgende voorbeeld worden blobinvoer- en uitvoerbindingen weergegeven in een *function.json-bestand* en [JavaScript-code](functions-reference-node.md) die de bindingen gebruikt. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrijbericht met de naam van de blob die u wilt kopiëren. De nieuwe blob heeft de naam *{originalblobname}-Copy*.

In het *bestand function.json* wordt de `queueTrigger` eigenschap metagegevens `path` gebruikt om de blobnaam in de eigenschappen op te geven:

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

In het volgende voorbeeld worden blobinvoer- en uitvoerbindingen weergegeven in een *function.json-bestand* en [Python-code](functions-reference-python.md) die de bindingen gebruikt. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrijbericht met de naam van de blob die u wilt kopiëren. De nieuwe blob heeft de naam *{originalblobname}-Copy*.

In het *bestand function.json* wordt de `queueTrigger` eigenschap metagegevens `path` gebruikt om de blobnaam in de eigenschappen op te geven:

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [HTTP-trigger, blobnaam opzoeken vanuit querytekenreeks](#http-trigger-look-up-blob-name-from-query-string)
* [Wachtrijtrigger, blobnaam ontvangen uit wachtrijbericht](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-trigger, blobnaam opzoeken vanuit querytekenreeks

 In het volgende voorbeeld wordt `HttpTrigger` een Java-functie weergegeven die de annotatie gebruikt om een parameter te ontvangen die de naam van een bestand in een blobopslagcontainer bevat. De `BlobInput` annotatie leest vervolgens het bestand en geeft `byte[]`de inhoud ervan door aan de functie als een .

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wachtrijtrigger, blobnaam ontvangen uit wachtrijbericht

 In het volgende voorbeeld wordt `QueueTrigger` een Java-functie weergegeven die de annotatie gebruikt om een bericht te ontvangen dat de naam van een bestand in een blobopslagcontainer bevat. De `BlobInput` annotatie leest vervolgens het bestand en geeft `byte[]`de inhoud ervan door aan de functie als een .

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

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `@BlobInput` waarde afkomstig zou zijn van een blob.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk neemt het `FileAccess` pad naar de blob en een parameter die lezen of schrijven aangeeft, zoals in het volgende voorbeeld wordt weergegeven:

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

U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

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

U `StorageAccount` het kenmerk gebruiken om het opslagaccount op klasse-, methode- of parameterniveau op te geven. Zie [Trigger - kenmerken en annotaties](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)voor meer informatie .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobInput` kenmerk geeft u toegang tot de blob die de functie heeft geactiveerd. Als u een bytearray met `dataType` het `binary`kenmerk gebruikt, stelt u in op . Raadpleeg het [invoervoorbeeld](#example) voor meer informatie.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Blob` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `blob`zijn op. |
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Uitzonderingen worden opgemerkt in de [sectie gebruik.](#usage) |
|**Naam** | N.v.t. | De naam van de variabele die de blob in functiecode vertegenwoordigt.|
|**Pad** |**BlobPath** | Het pad naar de blob. |
|**verbinding** |**Verbinding**| De naam van een app-instelling die de [tekenreeks Opslagverbinding](../storage/common/storage-configure-connection-string.md) bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'AzureWebJobsMyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .<br><br>De verbindingstekenreeks moet voor een opslagaccount voor algemene doeleinden zijn, niet voor een [opslagaccount met blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Geeft aan of u gaat lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot `context.bindings.<NAME>` blobgegevens met behulp van de `<NAME>` waarde die is gedefinieerd in *function.json*.

# <a name="python"></a>[Python](#tab/python)

Toegang tot blobgegevens via de parameter die is getypt als [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Raadpleeg het [invoervoorbeeld](#example) voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobInput` kenmerk geeft u toegang tot de blob die de functie heeft geactiveerd. Als u een bytearray met `dataType` het `binary`kenmerk gebruikt, stelt u in op . Raadpleeg het [invoervoorbeeld](#example) voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer blobopslaggegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob-opslaggegevens schrijven vanuit een functie](./functions-bindings-storage-blob-output.md)
