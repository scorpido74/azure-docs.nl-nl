---
title: Azure Blob Storage-uitvoer binding voor Azure Functions
description: Meer informatie over hoe u Azure Blob Storage-gegevens kunt leveren aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370829"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob Storage-uitvoer binding voor Azure Functions

Met de uitvoer binding kunt u Blob Storage-gegevens wijzigen en verwijderen in een Azure-functie.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [ C# functie](functions-dotnet-class-library.md) die gebruikmaakt van een BLOB-trigger en twee uitvoer BLOB-bindingen. De functie wordt geactiveerd door het maken van een afbeeldings-Blob in de container voor *beeld-installatie kopieën* . Er worden kleine en middel grote kopieën van de afbeeldings-BLOB gemaakt.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* file [ C# -en script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een tekst-blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In het bestand *Function. json* wordt de eigenschap `queueTrigger` meta gegevens gebruikt om de naam van de BLOB op te geven in de `path` eigenschappen:

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

Dit is de C#-scriptcode:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* -bestand en [Java script-code](functions-reference-node.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In het bestand *Function. json* wordt de eigenschap `queueTrigger` meta gegevens gebruikt om de naam van de BLOB op te geven in de `path` eigenschappen:

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

Dit is de JavaScript-code:

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

In het bestand *Function. json* wordt de eigenschap `queueTrigger` meta gegevens gebruikt om de naam van de BLOB op te geven in de `path` eigenschappen:

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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [HTTP-trigger, met behulp van OutputBinding](#http-trigger-using-outputbinding-java)
* [Wachtrij trigger, met behulp van functie retour waarde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger, met behulp van OutputBinding (Java)

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van de `HttpTrigger` aantekening om een para meter te ontvangen met de naam van een bestand in een BLOB storage-container. Met de `BlobInput` aantekening wordt het bestand vervolgens gelezen en wordt de inhoud door gegeven aan de functie als een `byte[]`. De `BlobOutput` aantekening wordt gekoppeld aan `OutputBinding outputItem`, die vervolgens door de functie wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Wachtrij trigger, met behulp van de functie retour waarde (Java)

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van de `QueueTrigger` aantekening om een bericht te ontvangen met daarin de naam van een bestand in een BLOB storage-container. Met de `BlobInput` aantekening wordt het bestand vervolgens gelezen en wordt de inhoud door gegeven aan de functie als een `byte[]`. De `BlobOutput` aantekening koppelt aan de retour waarde van de functie, die vervolgens door de runtime wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@BlobOutput` annotatie voor functie parameters waarvan de waarde zou worden geschreven naar een object in Blob Storage.  Het parameter type moet `OutputBinding<T>`zijn, waarbij T een systeem eigen Java-type is of een POJO.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk gebruikt het pad naar de BLOB en een `FileAccess`-para meter die een lees-of schrijf bewerking aangeeft, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

U kunt de eigenschap `Connection` instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Het kenmerk `@BlobOutput` geeft u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary`. Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

---

Zie [uitvoer voorbeeld](#example)voor een volledig voor beeld.

U kunt het kenmerk `StorageAccount` gebruiken om het opslag account op te geven bij klasse, methode of parameter niveau. Zie [trigger-Attributes](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `Blob`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `out` voor een uitvoer binding. Uitzonde ringen worden vermeld in de sectie [gebruik](#usage) . |
|**naam** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.  Ingesteld op `$return` om te verwijzen naar de retour waarde van de functie.|
|**path** |**BlobPath** | Het pad naar de BLOB-container. |
|**Combi** |**Verbinding**| De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In Java script opent u de BLOB-gegevens met behulp van `context.bindings.<name from function.json>`.

# <a name="python"></a>[Python](#tab/python)

U kunt functie parameters declareren als de volgende typen om naar Blob Storage te schrijven:

* Teken reeksen als `func.Out(str)`
* Stromen als `func.Out(func.InputStream)`

Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

Het kenmerk `@BlobOutput` geeft u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary`. Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Naslaginformatie |
|---|---|
| Blob | [BLOB-fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabel, wachtrij |  [Opslag fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij |  [Problemen oplossen](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
