---
title: Azure Functions bindt expressies en patronen
description: Leer verschillende bindingsexpressies van Azure-functies te maken op basis van algemene patronen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277646"
---
# <a name="azure-functions-binding-expression-patterns"></a>Bindingsexpressies van Azure-functies

Een van de meest krachtige kenmerken van [triggers en bindingen](./functions-triggers-bindings.md) is *bindende uitdrukkingen*. In het *bestand function.json* en in functieparameters en code u expressies gebruiken die worden opgelost in waarden uit verschillende bronnen.

De meeste expressies worden geïdentificeerd door ze tussen accolades te plaatsen. In een wachtrijtriggerfunctie `{queueTrigger}` wordt bijvoorbeeld de tekst van het wachtrijbericht opgelost. Als `path` de eigenschap voor een `container/{queueTrigger}` blobuitvoerbinding is en `HelloWorld`de functie `HelloWorld` wordt geactiveerd door een wachtrijbericht, wordt een blob met de naam gemaakt.

Typen bindingexpressies

* [Instellingen voor apps](#binding-expressions---app-settings)
* [Trigger-bestandsnaam](#trigger-file-name)
* [Trigger-metagegevens](#trigger-metadata)
* [JSON-nettoladingen](#json-payloads)
* [Nieuwe GUID](#create-guids)
* [Huidige datum en tijd](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressies binden - app-instellingen

Als aanbevolen praktijk moeten geheimen en verbindingstekenreeksen worden beheerd met app-instellingen in plaats van configuratiebestanden. Dit beperkt de toegang tot deze geheimen en maakt het veilig om bestanden zoals *function.json* op te slaan in openbare bronbeheerrepositories.

App-instellingen zijn ook handig wanneer u de configuratie wilt wijzigen op basis van de omgeving. In een testomgeving u bijvoorbeeld een andere wachtrij- of blobopslagcontainer controleren.

Bindende expressies voor app-instellingen worden anders geïdentificeerd dan andere bindende expressies: ze zijn verpakt in procentuele tekens in plaats van krullende accolades. Als het bindingspad voor `%Environment%/newblob.txt` de `Environment` blobuitvoer `Development`is en de waarde `Development` van de app-instelling is, wordt er bijvoorbeeld een blob in de container gemaakt.

Wanneer een functie lokaal wordt uitgevoerd, komen de waarden voor app-instelling uit het bestand *local.settings.json.*

Houd er `connection` rekening mee dat de eigenschap van triggers en bindingen een speciaal geval is en automatisch waarden als app-instellingen oplost, zonder dat het percentage wordt tekens. 

Het volgende voorbeeld is een Azure Queue `%input-queue-name%` Storage-trigger die een app-instelling gebruikt om de wachtrij te definiëren waarop u moet activeren.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

U dezelfde aanpak gebruiken in klasbibliotheken:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Trigger-bestandsnaam

De `path` trigger voor een Blob kan een patroon zijn waarmee u verwijzen naar de naam van de activerende blob in andere bindingen en functiecode. Het patroon kan ook filtercriteria bevatten die aangeven welke blobs een functieaanroep kunnen activeren.

In de volgende Blob-triggerbinding `path` is `sample-images/{filename}`het patroon bijvoorbeeld een `filename`bindende expressie met de naam :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

De `filename` expressie kan vervolgens worden gebruikt in een uitvoerbinding om de naam op te geven van de blob die wordt gemaakt:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Functiecode heeft toegang tot dezelfde `filename` waarde door te gebruiken als parameternaam:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Dezelfde mogelijkheid om bindende expressies en patronen te gebruiken, is van toepassing op kenmerken in klassenbibliotheken. In het volgende voorbeeld zijn de parameters `path` van de kenmerkconstructor dezelfde waarden als de voorgaande voorbeelden *van function.json:* 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

U ook expressies maken voor delen van de bestandsnaam. In het volgende voorbeeld wordt de functie alleen geactiveerd op bestandsnamen die overeenkomen met een patroon:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Zie de [bindingsverwijzing opslagblobvoor](functions-bindings-storage-blob.md)meer informatie over het gebruik van expressies en patronen in de blobpadreeks.

## <a name="trigger-metadata"></a>Trigger-metagegevens

Naast de gegevenspayload die door een trigger wordt geleverd (zoals de inhoud van het wachtrijbericht dat een functie heeft geactiveerd), bieden veel triggers extra metagegevenswaarden. Deze waarden kunnen worden gebruikt als invoerparameters in C# en F# of eigenschappen op het `context.bindings` object in JavaScript. 

Een Azure Queue-opslagtrigger ondersteunt bijvoorbeeld de volgende eigenschappen:

* QueueTrigger - berichtinhoud activeren als een geldige tekenreeks
* DequeueCount
* ExpirationTime
* Id
* Invoegtijd
* NextVisibleTime
* PopReceipt PopReceipt

Deze metagegevenswaarden zijn toegankelijk in de bestandseigenschappen *van function.json.* Stel dat u een wachtrijtrigger gebruikt en het wachtrijbericht de naam bevat van een blob die u wilt lezen. In het *bestand function.json* `queueTrigger` kunt u de `path` eigenschap metagegevens gebruiken in de eigenschap blob, zoals in het volgende voorbeeld wordt weergegeven:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Details van de metagegevenseigenschappen voor elke trigger worden beschreven in het bijbehorende referentieartikel. Zie bijvoorbeeld [metagegevens voor wachtrijtriggeren](functions-bindings-storage-queue-trigger.md#message-metadata). Documentatie is ook beschikbaar op het tabblad **Integreren** van de portal, in het gedeelte **Documentatie** onder het bindende configuratiegebied.  

## <a name="json-payloads"></a>JSON-nettoladingen

Wanneer een triggerpayload JSON is, u verwijzen naar de eigenschappen ervan in configuratie voor andere bindingen in dezelfde functie en in functiecode.

In het volgende voorbeeld wordt het *function.json-bestand* weergegeven voor een `{"BlobName":"HelloWorld.txt"}`webhookfunctie die een blobnaam in JSON ontvangt: . Een blob-invoerbinding leest de blob en de HTTP-uitvoerbinding retourneert de blob-inhoud in het HTTP-antwoord. Merk op dat de blob-invoerbinding de blobnaam krijgt door rechtstreeks naar de `BlobName` eigenschap te verwijzen (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Om dit te laten werken in C# en F#, hebt u een klasse nodig die de velden definieert die moeten worden gedeserialiseerd, zoals in het volgende voorbeeld:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

In JavaScript wordt JSON-deserialisatie automatisch uitgevoerd.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Dot notatie

Als sommige eigenschappen in uw JSON-payload objecten met eigenschappen zijn, u deze rechtstreeks verwijzen met behulp van puntnotatie. Stel dat uw JSON er als volgt uitziet:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

U direct `FileName` `BlobName.FileName`verwijzen naar als. Met deze JSON-indeling ziet `path` de eigenschap in het voorgaande voorbeeld er als volgt uit:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

In C#, zou je twee klassen nodig:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID's maken

Met `{rand-guid}` de bindingsexpressie wordt een GUID gemaakt. Met het volgende `function.json` blobpad in een bestand wordt een blob met een naam als *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Huidige tijd

De bindende `DateTime` expressie `DateTime.UtcNow`wordt opgelost tot . Met het volgende `function.json` blobpad in een bestand wordt een blob met een naam als *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Binding bij runtime

In C# en andere .NET-talen u een noodzakelijk bindend patroon gebruiken, in tegenstelling tot de declaratieve bindingen in *function.json* en kenmerken. Verplichte binding is handig wanneer bindende parameters moeten worden berekend tijdens runtime in plaats van ontwerptijd. Zie de verwijzing [naar C#-ontwikkelaars](functions-dotnet-class-library.md#binding-at-runtime) of de [verwijzing naar de C#-scriptontwikkelaars](functions-reference-csharp.md#binding-at-runtime)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [De retourwaarde azure-functie gebruiken](./functions-bindings-return-value.md)
