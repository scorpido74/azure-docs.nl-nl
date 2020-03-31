---
title: Azure Blob-opslagtrigger voor Azure-functies
description: Meer informatie over het uitvoeren van een Azure-functie als Azure Blob-opslaggegevens wijzigingen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 39e3521339947263161979033406fb39e397373f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348976"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Blob-opslagtrigger voor Azure-functies

De blob-opslagtrigger start een functie wanneer een nieuwe of bijgewerkte blob wordt gedetecteerd. De blob-inhoud wordt geleverd als [invoer voor de functie](./functions-bindings-storage-blob-input.md).

De Azure Blob-opslagtrigger vereist een opslagaccount voor algemene doeleinden. Als u een blob-only account wilt gebruiken of als uw toepassing gespecialiseerde behoeften heeft, bekijkt u de alternatieven voor het gebruik van deze trigger.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor informatie over de installatie en configuratiedetails.

## <a name="alternatives"></a>Alternatieven

### <a name="event-grid-trigger"></a>Trigger gebeurtenisraster

De [trigger van eventgrid](functions-bindings-event-grid.md) heeft ook ingebouwde ondersteuning voor [blobgebeurtenissen.](../storage/blobs/storage-blob-event-overview.md) Gebruik Gebeurtenisraster in plaats van de Blob-opslagtrigger voor de volgende scenario's:

- **Blob-only opslagaccounts:** [opslagaccounts met blobworden](../storage/common/storage-account-overview.md#types-of-storage-accounts) ondersteund voor blob-invoer- en uitvoerbindingen, maar niet voor blob-triggers.

- **Op grote schaal:** Op grote schaal kan losjes worden gedefinieerd als containers met meer dan 100.000 blobs of opslagaccounts met meer dan 100 blob-updates per seconde.

- **Latentie minimaliseren:** Als uw functie-app in het verbruiksplan staat, kan er tot 10 minuten vertraging optreden bij het verwerken van nieuwe blobs als een functie-app niet actief is. Om deze latentie te voorkomen, u overschakelen naar een App Service-abonnement met Always On ingeschakeld. U ook een [trigger voor gebeurtenisrastergebruiken](functions-bindings-event-grid.md) met uw Blob-opslagaccount. Zie bijvoorbeeld de [zelfstudie Gebeurtenisraster](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Zie het [formaat van het afbeeldingsformaat met de](../event-grid/resize-images-on-storage-blob-upload-event.md) zelfstudie Van gebeurtenisraster van een voorbeeld van gebeurtenisraster.

### <a name="queue-storage-trigger"></a>Queue Storage-trigger

Een andere benadering voor het verwerken van blobs is het schrijven van wachtrijberichten die overeenkomen met blobs die worden gemaakt of gewijzigd en vervolgens een [wachtrijopslagtrigger](./functions-bindings-storage-queue.md) gebruiken om te beginnen met verwerken.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die `samples-workitems` een logboek schrijft wanneer een blob in de container wordt toegevoegd of bijgewerkt.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

De `{name}` tekenreeks in het `samples-workitems/{name}` triggerpad van de blob maakt een [bindende expressie](./functions-bindings-expressions-patterns.md) die u in functiecode gebruiken om toegang te krijgen tot de bestandsnaam van de activerende blob. Zie [Blob-naampatronen](#blob-name-patterns) later in dit artikel voor meer informatie.

Zie kenmerken en `BlobTrigger` [annotaties](#attributes-and-annotations)voor meer informatie over het kenmerk.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een blobtriggerbinding weergegeven in een *function.json-bestand* en -code die de binding gebruikt. De functie schrijft een logboek wanneer een `samples-workitems` blob wordt toegevoegd of bijgewerkt in de [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

De `{name}` tekenreeks in het `samples-workitems/{name}` triggerpad van de blob maakt een [bindende expressie](./functions-bindings-expressions-patterns.md) die u in functiecode gebruiken om toegang te krijgen tot de bestandsnaam van de activerende blob. Zie [Blob-naampatronen](#blob-name-patterns) later in dit artikel voor meer informatie.

Zie [Deze](#configuration) eigenschappen voor meer informatie over de bestandseigenschappen *van function.json.*

Hier is C# scriptcode die `Stream`bindt aan een:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Hier is C# scriptcode die `CloudBlockBlob`bindt aan een:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een blobtriggerbinding weergegeven in een *function.json-bestand* en [JavaScript-code](functions-reference-node.md) die de binding gebruikt. De functie schrijft een logboek wanneer een `samples-workitems` blob wordt toegevoegd of bijgewerkt in de container.

Hier is het *function.json* bestand:

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

De `{name}` tekenreeks in het `samples-workitems/{name}` triggerpad van de blob maakt een [bindende expressie](./functions-bindings-expressions-patterns.md) die u in functiecode gebruiken om toegang te krijgen tot de bestandsnaam van de activerende blob. Zie [Blob-naampatronen](#blob-name-patterns) later in dit artikel voor meer informatie.

Zie [Deze](#configuration) eigenschappen voor meer informatie over de bestandseigenschappen *van function.json.*

Hier is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een blobtriggerbinding weergegeven in een *function.json-bestand* en [Python-code](functions-reference-python.md) die de binding gebruikt. De functie schrijft een logboek wanneer een `samples-workitems` blob wordt toegevoegd of bijgewerkt in de [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Hier is het *function.json* bestand:

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

De `{name}` tekenreeks in het `samples-workitems/{name}` triggerpad van de blob maakt een [bindende expressie](./functions-bindings-expressions-patterns.md) die u in functiecode gebruiken om toegang te krijgen tot de bestandsnaam van de activerende blob. Zie [Blob-naampatronen](#blob-name-patterns) later in dit artikel voor meer informatie.

Zie [Deze](#configuration) eigenschappen voor meer informatie over de bestandseigenschappen *van function.json.*

Hier is de Python-code:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Met deze functie wordt een logboek geschreven `myblob` wanneer een blob in de container wordt toegevoegd of bijgewerkt.

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de volgende kenmerken om een blobtrigger te configureren:

* [BlobTriggerAttribuut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  De constructor van het kenmerk neemt een padtekenreeks die aangeeft welke container u moet bekijken en eventueel een [blobnaampatroon](#blob-name-patterns). Hier volgt een voorbeeld:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Zie Voorbeeld van [Trigger](#example)voor een volledig voorbeeld .

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het te gebruiken opslagaccount op te geven. De constructeur neemt de naam aan van een app-instelling die een tekenreeks voor opslagverbindingen bevat. Het kenmerk kan worden toegepast op parameter-, methode- of klassenniveau. In het volgende voorbeeld ziet u het niveau van de klasse en het methodeniveau:

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

De te gebruiken opslagrekening wordt in de volgende volgorde bepaald:

* De `BlobTrigger` eigenschap `Connection` van het attribuut.
* Het `StorageAccount` kenmerk dat is toegepast `BlobTrigger` op dezelfde parameter als het kenmerk.
* Het `StorageAccount` kenmerk dat op de functie wordt toegepast.
* Het `StorageAccount` kenmerk dat op de klasse is toegepast.
* Het standaardopslagaccount voor de functie-app ('AzureWebJobsStorage'-appinstelling).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de blob die de functie heeft geactiveerd. Raadpleeg het [triggervoorbeeld](#example) voor meer informatie.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `BlobTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `blobTrigger`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. Uitzonderingen worden opgemerkt in de [sectie gebruik.](#usage) |
|**Naam** | N.v.t. | De naam van de variabele die de blob in functiecode vertegenwoordigt. |
|**Pad** | **BlobPath** |De [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) om in de gaten te houden.  Kan een [blob naampatroon](#blob-name-patterns). |
|**verbinding** | **Verbinding** | De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'AzureWebJobsMyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .<br><br>De verbindingstekenreeks moet voor een opslagaccount voor algemene doeleinden zijn, niet voor een [Blob-opslagaccount](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot `context.bindings.<NAME>` blobgegevens met behulp van de `<NAME>` waarde die is gedefinieerd in *function.json*.

# <a name="python"></a>[Python](#tab/python)

Toegang tot blobgegevens via de parameter die is getypt als [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Raadpleeg het [triggervoorbeeld](#example) voor meer informatie.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de blob die de functie heeft geactiveerd. Raadpleeg het [triggervoorbeeld](#example) voor meer informatie.

---

## <a name="blob-name-patterns"></a>Blobnaampatronen

U een blobnaampatroon `path` opgeven in de eigenschap `BlobTrigger` in *function.json* of in de kenmerkconstructor. Het naampatroon kan een [filter of bindende expressie](./functions-bindings-expressions-patterns.md)zijn. De volgende secties geven voorbeelden.

### <a name="get-file-name-and-extension"></a>Bestandsnaam en extensie downloaden

In het volgende voorbeeld ziet u hoe u afzonderlijk aan de naam en extensie van het blobbestand binden:

```json
"path": "input/{blobname}.{blobextension}",
```

Als de blob de naam *original-Blob1.txt*heeft, zijn de waarden van de `blobname` en `blobextension` variabelen in functiecode *origineel-Blob1* en *txt*.

### <a name="filter-on-blob-name"></a>Filteren op blobnaam

In het volgende voorbeeld wordt `input` alleen geactiveerd op blobs in de container die beginnen met de tekenreeks 'origineel':

```json
"path": "input/original-{name}",
```

Als de blobnaam *origineel-Blob1.txt*is, `name` is `Blob1`de waarde van de variabele in functiecode .

### <a name="filter-on-file-type"></a>Filteren op bestandstype

In het volgende voorbeeld wordt alleen worden geactiveerd op *png-bestanden:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filteren op krullende accolades in bestandsnamen

Om te zoeken naar krullende beugels in bestandsnamen, ontsnappen aan de beugels met behulp van twee beugels. In het volgende voorbeeld wordt gefiltert voor blobs met krullende accolades in de naam:

```json
"path": "images/{{20140101}}-{name}",
```

Als de blob de naam `name` * {20140101}-soundfile.mp3*heeft, is de variabele waarde in de functiecode *soundfile.mp3*.

## <a name="metadata"></a>Metagegevens

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Metagegevens zijn niet beschikbaar in Python.

# <a name="java"></a>[Java](#tab/java)

Metagegevens zijn niet beschikbaar in Java.

---

## <a name="blob-receipts"></a>Blob-ontvangstbewijzen

De runtime azure-functies zorgt ervoor dat er geen blobtriggerfunctie meer dan één keer wordt aangeroepen voor dezelfde nieuwe of bijgewerkte blob. Als u wilt bepalen of een bepaalde blobversie is verwerkt, worden *blob-ontvangstbewijzen bijgehouden.*

Azure Functions slaat blob-ontvangstbewijzen op in een container met de naam *azure-webjobs-hosts* `AzureWebJobsStorage`in het Azure-opslagaccount voor uw functie-app (gedefinieerd door de app-instelling). Een blob-ontvangstbewijs heeft de volgende informatie:

* De functie naam van de geactiveerde functie*&lt;(">*. Functies. functienaam>", bijvoorbeeld: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* De containernaam
* Het blobtype ('BlockBlob' of 'PageBlob')
* De blobnaam
* De ETag (een blob-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

Als u opwerking van een blob wilt forceren, verwijdert u het blob-ontvangstbewijs voor die blob handmatig uit de container *azure-webjobs-hosts.* Hoewel opwerking mogelijk niet onmiddellijk plaatsvindt, is het gegarandeerd op een later tijdstip mogelijk.

## <a name="poison-blobs"></a>Gif blobs

Wanneer een blobtriggerfunctie mislukt voor een bepaalde blob, worden azure-functies opnieuw geprobeerd en werken ze standaard 5 keer.

Als alle 5 pogingen mislukken, voegt Azure Functions een bericht toe aan een wachtrij met de naam *webjobs-blobtrigger-poison.* Het maximum aantal nieuwe pogingen is configureerbaar. Dezelfde MaxDequeueCount-instelling wordt gebruikt voor het verwerken van gifblobs en het verwerken van gifwachtrijberichten. Het wachtrijbericht voor gifblobs is een JSON-object dat de volgende eigenschappen bevat:

* FunctionId (in de naam van de * &lt;indelingsfunctie-app>*. Functies. functienaam>) * &lt; *
* BlobType ("BlockBlob" of "PageBlob")
* ContainerName
* BlobName
* ETag (een blob-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Gelijktijdigheid en geheugengebruik

De blobtrigger gebruikt een wachtrij intern, zodat het maximum aantal gelijktijdige functieaanroepen wordt beheerd door de [wachtrijconfiguratie in host.json](functions-host-json.md#queues). De standaardinstellingen beperken gelijktijdigheid tot 24 aanroepen. Deze limiet is afzonderlijk van toepassing op elke functie die een blobtrigger gebruikt.

[Het verbruiksplan](functions-scale.md#how-the-consumption-and-premium-plans-work) beperkt een functie-app op één virtuele machine (VM) tot 1,5 GB geheugen. Geheugen wordt gebruikt door elke gelijktijdig uitvoerende functie-instantie en door de runtime Functies zelf. Als een blob-geactiveerde functie de hele blob in het geheugen laadt, is het maximale geheugen dat door die functie wordt gebruikt alleen voor blobs 24 * maximale blobgrootte. Een functie-app met drie blob-geactiveerde functies en de standaardinstellingen hebben bijvoorbeeld een maximale gelijktijdigheid per VM van 3*24 = 72 functie-aanroepen.

JavaScript- en Java-functies laden de hele blob in het `string`geheugen `Byte[]`en C#-functies doen dat als u zich bindt aan , of POCO.

## <a name="polling"></a>Polling

Polling werkt als een hybride tussen het inspecteren van logs en het uitvoeren van periodieke containerscans. Blobs worden gescand in groepen van 10.000 tegelijk met een vervolgtoken dat tussen intervallen wordt gebruikt.

> [!WARNING]
> Daarnaast [worden opslaglogs gemaakt op basis van "best effort".](/rest/api/storageservices/About-Storage-Analytics-Logging) Er is geen garantie dat alle gebeurtenissen worden vastgelegd. Onder bepaalde omstandigheden kunnen logboeken worden gemist.
> 
> Als u snellere of betrouwbaardere blobverwerking nodig hebt, u overwegen een [wachtrijbericht](../storage/queues/storage-dotnet-how-to-use-queues.md) te maken wanneer u de blob maakt. Gebruik vervolgens een [wachtrijtrigger](functions-bindings-storage-queue.md) in plaats van een blobtrigger om de blob te verwerken. Een andere optie is het gebruik van Event Grid; zie de zelfstudie [Het formaat van geüploade afbeeldingen wijzigen met behulp van gebeurtenisraster](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Volgende stappen

- [Blob-opslaggegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
- [Blob-opslaggegevens schrijven vanuit een functie](./functions-bindings-storage-blob-output.md)
