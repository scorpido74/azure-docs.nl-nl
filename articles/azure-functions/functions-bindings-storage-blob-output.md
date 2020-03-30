---
title: Azure Blob-opslaguitvoerbinding voor Azure-functies
description: Meer informatie over het verstrekken van Azure Blob-opslaggegevens aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277243"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob-opslaguitvoerbinding voor Azure-functies

Met de uitvoerbinding u blobopslaggegevens wijzigen en verwijderen in een Azure-functie.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

Het volgende voorbeeld is een [C#-functie](functions-dotnet-class-library.md) die een blobtrigger en twee uitvoerblobbindingen gebruikt. De functie wordt geactiveerd door het maken van een afbeeldingblob in de *sample-images-container.* Hiermee worden kleine en middelgrote kopieën van de afbeeldingsblob gemaakt.

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

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

* [HTTP-trigger, met behulp van Uitvoerbinding](#http-trigger-using-outputbinding-java)
* [Wachtrijtrigger, met functieretourwaarde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger, met behulp van OutputBinding (Java)

 In het volgende voorbeeld wordt `HttpTrigger` een Java-functie weergegeven die de annotatie gebruikt om een parameter te ontvangen die de naam van een bestand in een blobopslagcontainer bevat. De `BlobInput` annotatie leest vervolgens het bestand en geeft `byte[]`de inhoud ervan door aan de functie als een . De `BlobOutput` annotatie bindt `OutputBinding outputItem`aan , die vervolgens wordt gebruikt door de functie om de inhoud van de invoerblob naar de geconfigureerde opslagcontainer te schrijven.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Wachtrijtrigger, met behulp van functieretourwaarde (Java)

 In het volgende voorbeeld wordt `QueueTrigger` een Java-functie weergegeven die de annotatie gebruikt om een bericht te ontvangen dat de naam van een bestand in een blobopslagcontainer bevat. De `BlobInput` annotatie leest vervolgens het bestand en geeft `byte[]`de inhoud ervan door aan de functie als een . De `BlobOutput` annotatie bindt aan de functieretourwaarde, die vervolgens door de runtime wordt gebruikt om de inhoud van de invoerblob naar de geconfigureerde opslagcontainer te schrijven.

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

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op functieparameters waarvan de `@BlobOutput` waarde zou worden geschreven naar een object in blobopslag.  Het parametertype `OutputBinding<T>`moet zijn, waar T een native Java-type of een POJO is.

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk neemt het `FileAccess` pad naar de blob en een parameter die lezen of schrijven aangeeft, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobOutput` kenmerk geeft u toegang tot de blob die de functie heeft geactiveerd. Als u een bytearray met `dataType` het `binary`kenmerk gebruikt, stelt u in op . Raadpleeg het [uitvoervoorbeeld](#example) voor meer informatie.

---

Zie Voorbeeld van [Uitvoer](#example)voor een volledig voorbeeld .

U `StorageAccount` het kenmerk gebruiken om het opslagaccount op klasse-, methode- of parameterniveau op te geven. Zie [Trigger - kenmerken voor](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)meer informatie.

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Blob` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `blob`zijn op. |
|**direction** | N.v.t. | Moet worden `out` ingesteld op voor een output binding. Uitzonderingen worden opgemerkt in de [sectie gebruik.](#usage) |
|**Naam** | N.v.t. | De naam van de variabele die de blob in functiecode vertegenwoordigt.  Ingesteld `$return` op de referentiewaarde voor het retourresultaat van de functie.|
|**Pad** |**BlobPath** | Het pad naar de blobcontainer. |
|**verbinding** |**Verbinding**| De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'AzureWebJobsMyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .<br><br>De verbindingstekenreeks moet voor een opslagaccount voor algemene doeleinden zijn, niet voor een [opslagaccount met blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Geeft aan of u gaat lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

In JavaScript toegang tot `context.bindings.<name from function.json>`de blobgegevens met behulp van .

# <a name="python"></a>[Python](#tab/python)

U functieparameters declareren als de volgende typen die u wilt uitschrijven naar blobopslag:

* Tekenreeksen als`func.Out(str)`
* Streams als`func.Out(func.InputStream)`

Raadpleeg het [uitvoervoorbeeld](#example) voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobOutput` kenmerk geeft u toegang tot de blob die de functie heeft geactiveerd. Als u een bytearray met `dataType` het `binary`kenmerk gebruikt, stelt u in op . Raadpleeg het [uitvoervoorbeeld](#example) voor meer informatie.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Naslaginformatie |
|---|---|
| Blob | [Blob-foutcodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tabel, Wachtrij |  [Opslagfoutcodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabel, Wachtrij |  [Probleemoplossing](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer blobopslaggegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob-opslaggegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
