---
title: Azure Blob-opslag bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Blob Storage-triggers en-bindingen in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenisverwerking, dynamische Computing, serverloze architectuur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 428334c1d1d28f53f3a4007b345bb2fb2249f26b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642212"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob-opslag bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Blob Storage-bindingen in Azure Functions. Azure Functions ondersteunt trigger-, invoer-en uitvoer bindingen voor blobs. Het artikel bevat een sectie voor elke binding:

* [BLOB-trigger](#trigger)
* [BLOB-invoer binding](#input)
* [BLOB-uitvoer binding](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Gebruik de trigger Event Grid in plaats van de Blob Storage-trigger voor BLOB-only Storage-accounts, voor hoge schaal of om latentie te verminderen. Zie de sectie [trigger](#trigger) voor meer informatie.

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De Blob Storage-bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet versie 2. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-opslagplaats.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De Blob Storage-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-opslagplaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Met de trigger voor Blob-opslag wordt een functie gestart wanneer er een nieuwe of bijgewerkte BLOB wordt gedetecteerd. De inhoud van de BLOB wordt opgegeven als invoer voor de functie.

De [trigger voor Event grid](functions-bindings-event-grid.md) heeft ingebouwde ondersteuning voor [BLOB-gebeurtenissen](../storage/blobs/storage-blob-event-overview.md) en kan ook worden gebruikt om een functie te starten wanneer er een nieuwe of bijgewerkte BLOB wordt gedetecteerd. Zie voor een voor beeld het [formaat van de afbeelding wijzigen met Event grid](../event-grid/resize-images-on-storage-blob-upload-event.md) zelf studie.

Gebruik Event Grid in plaats van de Blob Storage-trigger voor de volgende scenario's:

* Blob Storage-accounts
* Hoge schaal
* Latentie beperken

### <a name="blob-storage-accounts"></a>Blob Storage-accounts

[Blob Storage-accounts](../storage/common/storage-account-overview.md#types-of-storage-accounts) worden ondersteund voor BLOB-invoer-en uitvoer bindingen, maar niet voor BLOB-triggers. Blob-opslag triggers vereisen een opslag account voor algemeen gebruik.

### <a name="high-scale"></a>Hoge schaal

High Scale kan worden gedefinieerd als containers met meer dan 100.000 blobs in deze of opslag accounts met meer dan 100 BLOB-updates per seconde.

### <a name="latency-issues"></a>Latentie problemen

Als uw functie-app zich in het verbruiks abonnement bevindt, kan er een vertraging van 10 minuten zijn bij het verwerken van nieuwe blobs als een functie-app niet actief is. Om deze latentie te voor komen, kunt u overschakelen naar een App Service plan met Always ingeschakeld. U kunt ook een [Event grid trigger](functions-bindings-event-grid.md) gebruiken met uw Blob Storage-account. Zie de [Event grid zelf studie](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)voor een voor beeld. 

### <a name="queue-storage-trigger"></a>Opslag trigger voor wachtrij

Naast Event Grid is een andere alternatief voor verwerkings-blobs de wachtrij opslag trigger, maar heeft geen ingebouwde ondersteuning voor BLOB-gebeurtenissen. U moet wachtrij berichten maken bij het maken of bijwerken van blobs. Zie voor een voor beeld dat u dit hebt gedaan, het [voor beeld van BLOB-invoer binding verderop in dit artikel](#input---example).

## <a name="trigger---example"></a>Trigger - voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die een logboek schrijft wanneer een BLOB wordt toegevoegd of `samples-workitems` bijgewerkt in de container.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Met de `{name}` teken reeks in het pad `samples-workitems/{name}` van de BLOB-trigger maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#trigger---blob-name-patterns) verderop in dit artikel.

Zie `BlobTrigger` [trigger-Attributes](#trigger---attributes)(Engelstalig) voor meer informatie over het kenmerk.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *Function. json* -bestand en code die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Hier volgt de binding-gegevens de *function.json* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de `{name}` teken reeks in het pad `samples-workitems/{name}` van de BLOB-trigger maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#trigger---blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#trigger---configuration) bevat een uitleg van deze eigenschappen voor meer informatie over de bestands eigenschappen van de *functie. json* .

Dit is C# de script code die wordt gekoppeld aan `Stream`een:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Dit is C# de script code die wordt gekoppeld aan `CloudBlockBlob`een:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *Function. json* -bestand en [Java script-code](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` container.

Hier volgt de *function.json* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de `{name}` teken reeks in het pad `samples-workitems/{name}` van de BLOB-trigger maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#trigger---blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#trigger---configuration) bevat een uitleg van deze eigenschappen voor meer informatie over de bestands eigenschappen van de *functie. json* .

Dit is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *Function. json* -bestand en [python-code](functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Hier volgt de *function.json* bestand:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de `{name}` teken reeks in het pad `samples-workitems/{name}` van de BLOB-trigger maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#trigger---blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#trigger---configuration) bevat een uitleg van deze eigenschappen voor meer informatie over de bestands eigenschappen van de *functie. json* .

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een BLOB-trigger binding in een *Function. json* -bestand en [Java-code](functions-reference-java.md) die gebruikmaken van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `myblob` container.

Hier volgt de *function.json* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Dit is de Java-code:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="trigger---attributes"></a>Trigger - kenmerken

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken voor het configureren van een BLOB-trigger:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  De constructor van het kenmerk heeft een padtekenreeks die aangeeft dat de container moet worden weer gegeven en optioneel een [BLOB-naam patroon](#trigger---blob-name-patterns). Hier volgt een voorbeeld:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Zie [trigger voorbeeld](#trigger---example)voor een volledig voor beeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de parameter, klasseniveau of methode. Het volgende voorbeeld toont het klasseniveau en methode:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Het opslag account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De `BlobTrigger` van kenmerk `Connection` eigenschap.
* De `StorageAccount` kenmerk toegepast op de dezelfde parameter als de `BlobTrigger` kenmerk.
* De `StorageAccount` kenmerk toegepast op de functie.
* De `StorageAccount` kenmerk toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de BLOB waarmee de functie is geactiveerd. Raadpleeg het [voor beeld](#trigger---example) van de trigger voor meer informatie.

---

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `BlobTrigger` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blobTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. Uitzonde ringen worden vermeld in de sectie [gebruik](#trigger---usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt. |
|**Pad** | **BlobPath** |De [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) die moet worden bewaakt.  Dit kan een [patroon](#trigger---blob-name-patterns)voor een BLOB-naam zijn. |
|**verbinding** | **verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met `AzureWebJobsStorage`de naam.<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, geen [Blob Storage-account](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Toegang tot BLOB- `context.bindings.<name from function.json>`gegevens met behulp van.

# <a name="pythontabpython"></a>[Python](#tab/python)

Toegang tot BLOB-gegevens via de para meter getypeerd als [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Raadpleeg het [voor beeld](#trigger---example) van de trigger voor meer informatie.

# <a name="javatabjava"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de BLOB waarmee de functie is geactiveerd. Raadpleeg het [voor beeld](#trigger---example) van de trigger voor meer informatie.

---

## <a name="trigger---blob-name-patterns"></a>Trigger-BLOB-naam patronen

U kunt een patroon van een BLOB-naam `path` opgeven in de eigenschap in *Function. json* of in de `BlobTrigger` kenmerk-constructor. Het naam patroon kan een [filter of een bindings expressie](./functions-bindings-expressions-patterns.md)zijn. De volgende secties bevatten voor beelden.

### <a name="get-file-name-and-extension"></a>Bestands naam en-extensie ophalen

In het volgende voor beeld ziet u hoe u een afzonderlijke binding maakt met de naam en extensie van het BLOB-bestand:

```json
"path": "input/{blobname}.{blobextension}",
```

Als de BLOB de naam *Original-Blob1. txt*heeft, zijn de waarden `blobname` van `blobextension` de variabelen en in functie code *oorspronkelijk-Blob1* en *txt*.

### <a name="filter-on-blob-name"></a>Filteren op blobnaam

Het volgende voor beeld wordt alleen geactiveerd op blobs `input` in de container die beginnen met de teken reeks ' origineel-':

```json
"path": "input/original-{name}",
```

Als de naam van de BLOB *Original-Blob1. txt*is, is `name` `Blob1`de waarde van de variabele in functie code.

### <a name="filter-on-file-type"></a>Filteren op bestands type

In het volgende voor beeld worden alleen de *. png* -bestanden geactiveerd:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filteren op accolades in bestands namen

Als u wilt zoeken naar accolades in bestands namen, plaatst u de accolades met behulp van twee accolades. Het volgende voor beeld wordt gefilterd op blobs met accolades in de naam:

```json
"path": "images/{{20140101}}-{name}",
```

Als de BLOB de naam  *{20140101}-soundfile. mp3*heeft, `name` is de waarde van de variabele in de functie code *soundfile. mp3*.

## <a name="trigger---metadata"></a>Trigger-meta gegevens

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Meta gegevens zijn niet beschikbaar in python.

# <a name="javatabjava"></a>[Java](#tab/java)

Meta gegevens zijn niet beschikbaar in Java.

---

## <a name="trigger---blob-receipts"></a>Trigger-BLOB-ontvangst bewijzen

De Azure Functions runtime zorgt ervoor dat er geen blob-activering meer dan één keer wordt aangeroepen voor dezelfde nieuwe of bijgewerkte blob. Om te bepalen of een bepaalde BLOB-versie is verwerkt, worden de *BLOB-ontvangst*bevestigingen onderhouden.

Azure Functions worden BLOB-ontvangsten opgeslagen in een container met de naam *Azure-webjobs-hosts* in het Azure-opslag account voor uw functie-app ( `AzureWebJobsStorage`gedefinieerd door de app-instelling). Een BLOB-ontvangst heeft de volgende informatie:

* De geactiveerde functie (' *&lt;naam van de functie-app >* . Vervullen. functie naam >, bijvoorbeeld:  *&lt;* "MyFunctionApp. functions. CopyBlob")
* De container naam
* Het BLOB-type ("BlockBlob" of "PageBlob")
* De BLOB-naam
* De ETag (een BLOB-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

Als u het opnieuw verwerken van een BLOB wilt afdwingen, verwijdert u de BLOB-ontvangst voor die blob van de container *Azure-webjobs-hosts* hand matig. Tijdens het opnieuw verwerken wordt mogelijk niet onmiddellijk uitgevoerd. Dit is gegarandeerd op een later tijdstip.

## <a name="trigger---poison-blobs"></a>Trigger-verontreinigde blobs

Wanneer een BLOB-activerings functie mislukt voor een bepaalde blob, worden Azure Functions de nieuwe pogingen standaard een totaal van vijf keer uitgevoerd.

Als alle 5 pogingen mislukken, Azure Functions een bericht aan een opslag wachtrij met de naam *webjobs-sjabloon blobtrigger-Poison*toevoegen. Het wachtrij bericht voor verontreinigde blobs is een JSON-object dat de volgende eigenschappen bevat:

* FunctionId (in de indeling  *&lt;functie app name >* . Vervullen. functie naam >)  *&lt;*
* BlobType ("BlockBlob" of "PageBlob")
* ContainerName
* BlobName
* ETag (een BLOB-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger-gelijktijdigheid en geheugen gebruik

De BLOB-trigger maakt intern gebruik van een wachtrij, waardoor het maximum aantal gelijktijdige functie aanroepen wordt bepaald door de [wachtrij configuratie in host. json](functions-host-json.md#queues). De standaard instellingen beperken de gelijktijdigheid tot 24 aanroepen. Deze limiet geldt afzonderlijk voor elke functie die gebruikmaakt van een BLOB-trigger.

[Het verbruiks plan](functions-scale.md#how-the-consumption-and-premium-plans-work) beperkt een functie-app op één virtuele machine (VM) tot 1,5 GB aan geheugen. Het geheugen wordt gebruikt door elk gelijktijdig uitgevoerde functie-exemplaar en door de functions-runtime zelf. Als een door BLOB geactiveerde functie de volledige Blob in het geheugen laadt, is de maximale hoeveelheid geheugen die door de functie wordt gebruikt alleen voor blobs 24 * maximum grootte van BLOB. Een functie-app met drie door BLOB geactiveerde functies en de standaard instellingen hebben bijvoorbeeld een Maxi maal per VM gelijktijdigheid van 3 * 24 = 72 functie aanroepen.

Java script en Java-functies laden de volledige Blob in het C# geheugen, en functies doen dat als `string`u `Byte[]`een binding maakt met, of poco.

## <a name="trigger---polling"></a>Trigger - polling

Als de BLOB-container die wordt bewaakt meer dan 10.000 blobs (in alle containers) bevat, scant de functions runtime de logboek bestanden om te kijken of er nieuwe of gewijzigde blobs zijn. Dit proces kan leiden tot vertragingen. Een functie wordt mogelijk pas na een paar minuten of langer geactiveerd nadat de blob is gemaakt.

> [!WARNING]
> Daarnaast [worden opslag logboeken gemaakt op basis van ' Best effort '](/rest/api/storageservices/About-Storage-Analytics-Logging) . Er is geen garantie dat alle gebeurtenissen worden vastgelegd. Onder bepaalde omstandigheden kunnen Logboeken worden gemist.
> 
> Als u snellere of meer betrouw bare BLOB-verwerking nodig hebt, kunt u overwegen om een [wachtrij bericht](../storage/queues/storage-dotnet-how-to-use-queues.md) te maken wanneer u de BLOB maakt. Gebruik vervolgens een [wachtrij trigger](functions-bindings-storage-queue.md) in plaats van een BLOB-trigger om de BLOB te verwerken. Een andere optie is het gebruik van Event Grid. Raadpleeg de zelf studie voor het [automatiseren van het formaat van geüploade afbeeldingen met behulp van Event grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Invoer

Gebruik een invoer binding voor Blob-opslag om blobs te lezen.

## <a name="input---example"></a>Invoer-voor beeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [ C# functie](functions-dotnet-class-library.md) die gebruikmaakt van een wachtrij trigger en een invoer-BLOB-binding. Het wachtrij bericht bevat de naam van de BLOB en de functie registreert de grootte van de blob.

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

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* file [ C# -en script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een tekst-blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de C#-scriptcode:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

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

## <a name="input---attributes"></a>Invoer - kenmerken

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

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

U kunt het `StorageAccount` -kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie voor meer informatie, [Trigger - kenmerken](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Met `@BlobInput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [invoer voorbeeld](#input---example) voor meer informatie.

---

## <a name="input---configuration"></a>Invoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `Blob` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Uitzonde ringen worden vermeld in de sectie [gebruik](#input---usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.|
|**Pad** |**BlobPath** | Het pad naar de blob. |
|**verbinding** |**verbinding**| De naam van een app-instelling die de [opslag Connection String](../storage/common/storage-configure-connection-string.md) bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met `AzureWebJobsStorage`de naam.<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Toegang tot de BLOB- `context.bindings.<name from function.json>`gegevens met behulp van.

# <a name="pythontabpython"></a>[Python](#tab/python)

Toegang tot BLOB-gegevens via de para meter getypeerd als [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Raadpleeg het [invoer voorbeeld](#input---example) voor meer informatie.

# <a name="javatabjava"></a>[Java](#tab/java)

Met `@BlobInput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [invoer voorbeeld](#input---example) voor meer informatie.

---

## <a name="output"></a>Output

Gebruik Blob Storage-uitvoer bindingen om blobs te schrijven.

## <a name="output---example"></a>Uitvoer - voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [ C# functie](functions-dotnet-class-library.md) die gebruikmaakt van een BLOB-trigger en twee uitvoer BLOB-bindingen. De functie wordt geactiveerd door het maken van een afbeeldings-Blob in de container voor *beeld-installatie kopieën* . Er worden kleine en middel grote kopieën van de afbeeldings-BLOB gemaakt.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in een *Function. json* file [ C# -en script-code (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een tekst-blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de C#-scriptcode:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [HTTP-trigger, met behulp van OutputBinding](#http-trigger-using-outputbinding-java)
* [Wachtrij trigger, met behulp van functie retour waarde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger, met behulp van OutputBinding (Java)

 In het volgende voor beeld ziet u een Java- `HttpTrigger` functie die gebruikmaakt van de aantekening om een para meter te ontvangen met de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie `byte[]`als een. De `BlobOutput` annotatie koppelt aan `OutputBinding outputItem`, die vervolgens door de functie wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

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

 In het volgende voor beeld ziet u een Java- `QueueTrigger` functie die gebruikmaakt van de aantekening om een bericht te ontvangen met daarin de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie `byte[]`als een. De `BlobOutput` aantekening koppelt aan de functie retour waarde, die vervolgens door de runtime wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

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

 Gebruik in de [runtime-bibliotheek van Java](/java/api/overview/azure/functions/runtime)- `@BlobOutput` functies de annotatie voor functie parameters waarvan de waarde zou worden geschreven naar een object in Blob Storage.  Het parameter type moet zijn `OutputBinding<T>`, waarbij T een systeem eigen Java-type is of een POJO.

---

## <a name="output---attributes"></a>Uitvoer - kenmerken

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk gebruikt het pad naar de BLOB en een `FileAccess` para meter die aangeeft dat er een lees-of schrijf bewerking wordt uitgevoerd, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Met `@BlobOutput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [uitvoer voorbeeld](#output---example) voor meer informatie.

---

Zie [uitvoer voorbeeld](#output---example)voor een volledig voor beeld.

U kunt het `StorageAccount` -kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie voor meer informatie, [Trigger - kenmerken](#trigger---attributes).

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `Blob` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `out` voor een uitvoer binding. Uitzonde ringen worden vermeld in de sectie [gebruik](#output---usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.  Instellen op `$return` om te verwijzen naar de functie retour waarde.|
|**Pad** |**BlobPath** | Het pad naar de BLOB-container. |
|**verbinding** |**verbinding**| De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met `AzureWebJobsStorage`de naam.<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In Java script opent u de BLOB- `context.bindings.<name from function.json>`gegevens met behulp van.

# <a name="pythontabpython"></a>[Python](#tab/python)

U kunt functie parameters declareren als de volgende typen om naar Blob Storage te schrijven:

* Teken reeksen als`func.Out(str)`
* Stromen als`func.Out(func.InputStream)`

Raadpleeg het [uitvoer voorbeeld](#output---example) voor meer informatie.

# <a name="javatabjava"></a>[Java](#tab/java)

Met `@BlobOutput` het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, `dataType` stelt `binary`u in op. Raadpleeg het [uitvoer voorbeeld](#output---example) voor meer informatie.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Referentie |
|---|---|
| Blob | [BLOB-fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabel, wachtrij |  [Opslag fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij |  [Problemen oplossen](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
