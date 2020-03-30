---
title: Azure Event Grid blob storage event schema
description: Beschrijft de eigenschappen die zijn geleverd voor blobopslaggebeurtenissen met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265036"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid-gebeurtenisschema voor Blob-opslag

In dit artikel worden de eigenschappen en het schema voor blobopslaggebeurtenissen weergeven.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

Zie Bron van de gebeurtenis [Opslag](event-sources.md#storage)voor een lijst met voorbeeldscripts en -zelfstudies .

>[!NOTE]
> Alleen opslagaccounts van soort **StorageV2 (v2 voor algemeen gebruik)** en **BlobStorage** ondersteunen gebeurtenis-integratie. **Opslag (genral ei v1)** biedt *geen* ondersteuning voor integratie met Event Grid.

## <a name="list-of-events-for-blob-rest-apis"></a>Lijst met gebeurtenissen voor Blob REST API's

Deze gebeurtenissen worden geactiveerd wanneer een client een blob maakt, vervangt of verwijdert door Blob REST API's aan te roepen.

 |Gebeurtenisnaam |Beschrijving|
 |----------|-----------|
 |**Microsoft.Storage.BlobGemaakt** |Geactiveerd wanneer een blob wordt gemaakt of vervangen. <br>Deze gebeurtenis wordt in het `PutBlob`bijzonder `PutBlockList`geactiveerd `CopyBlob` wanneer clients de , of bewerkingen gebruiken die beschikbaar zijn in de Blob REST API.   |
 |**Microsoft.Storage.BlobVerwijderd** |Geactiveerd wanneer een blob wordt verwijderd. <br>Deze gebeurtenis wordt met name `DeleteBlob` geactiveerd wanneer clients de bewerking aanroepen die beschikbaar is in de Blob REST API. |

> [!NOTE]
> Als u ervoor wilt zorgen dat de gebeurtenis **Microsoft.Storage.BlobCreated** alleen wordt geactiveerd wanneer `CopyBlob` `PutBlob`een `PutBlockList` Block Blob volledig is vastgelegd, filtert u de gebeurtenis voor de API-aanroepen en REST. Deze API-aanroepen activeren de gebeurtenis **Microsoft.Storage.BlobCreated** pas nadat gegevens volledig zijn toegewezen aan een Block Blob. Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het maken van een filter.

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lijst met de gebeurtenissen voor Azure Data Lake Storage Gen 2 REST API's

Deze gebeurtenissen worden geactiveerd als u een hiërarchische naamruimte inschakelt op het opslagaccount en clients Azure Data Lake Storage Gen2 REST API's aanroepen.

|Gebeurtenisnaam|Beschrijving|
|----------|-----------|
|**Microsoft.Storage.BlobGemaakt** | Geactiveerd wanneer een blob wordt gemaakt of vervangen. <br>Deze gebeurtenis wordt met name `CreateFile` geactiveerd `FlushWithClose` wanneer clients de en bewerkingen gebruiken die beschikbaar zijn in de Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobVerwijderd** |Geactiveerd wanneer een blob wordt verwijderd. <br>Met name deze gebeurtenis wordt ook `DeleteFile` geactiveerd wanneer clients de bewerking aanroepen die beschikbaar is in de Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobNaam**|Geactiveerd wanneer een blob een nieuwe naam krijgt. <br>Deze gebeurtenis wordt met name `RenameFile` geactiveerd wanneer clients de bewerking gebruiken die beschikbaar is in de Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryGemaakt**|Geactiveerd wanneer een map wordt gemaakt. <br>Deze gebeurtenis wordt met name `CreateDirectory` geactiveerd wanneer clients de bewerking gebruiken die beschikbaar is in de Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryNieuwe naam**|Geactiveerd wanneer een map een nieuwe naam krijgt. <br>Deze gebeurtenis wordt met name `RenameDirectory` geactiveerd wanneer clients de bewerking gebruiken die beschikbaar is in de Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryVerwijderd**|Geactiveerd wanneer een map wordt verwijderd. <br>Deze gebeurtenis wordt met name `DeleteDirectory` geactiveerd wanneer clients de bewerking gebruiken die beschikbaar is in de Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Als u ervoor wilt zorgen dat de gebeurtenis **Microsoft.Storage.BlobCreated** alleen wordt geactiveerd wanneer `FlushWithClose` een Block Blob volledig is vastgelegd, filtert u de gebeurtenis voor de API-aanroep REST. Deze API-aanroep activeert de gebeurtenis **Microsoft.Storage.BlobCreated** pas nadat gegevens volledig zijn toegewezen aan een Block Blob. Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het maken van een filter.

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenisantwoord

Wanneer een gebeurtenis wordt geactiveerd, verzendt de gebeurtenisrasterservice gegevens over die gebeurtenis naar een endpoint van een abonnement.

Deze sectie bevat een voorbeeld van hoe die gegevens eruit zouden zien voor elke blob-opslaggebeurtenis.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobGemaakt-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Gebeurtenis Microsoft.Storage.BlobGemaakt (Data Lake Storage Gen2)

Als het blob-opslagaccount een hiërarchische naamruimte heeft, lijken de gegevens op het vorige voorbeeld, met uitzondering van de volgende wijzigingen:

* De `dataVersion` sleutel is ingesteld `2`op een waarde van .

* De `data.api` toets is ingesteld `CreateFile` `FlushWithClose`op de tekenreeks of .

* De `contentOffset` sleutel is opgenomen in de gegevensset.

> [!NOTE]
> Als toepassingen `PutBlockList` de bewerking gebruiken om een nieuwe blob naar het account te uploaden, bevatten de gegevens deze wijzigingen niet.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobVerwijderde gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted-gebeurtenis (Data Lake Storage Gen2)

Als het blob-opslagaccount een hiërarchische naamruimte heeft, lijken de gegevens op het vorige voorbeeld, met uitzondering van de volgende wijzigingen:

* De `dataVersion` sleutel is ingesteld `2`op een waarde van .

* De `data.api` toets is ingesteld `DeleteFile`op de tekenreeks.

* De `url` sleutel bevat `dfs.core.windows.net`het pad.

> [!NOTE]
> Als toepassingen `DeleteBlob` de bewerking gebruiken om een blob uit het account te verwijderen, bevatten de gegevens deze wijzigingen niet.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobNamed-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Gebeurtenis Microsoft.Storage.DirectoryGemaakt

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryNieuwe naam

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Gebeurtenis Microsoft.Storage.DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens voor blobopslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| api | tekenreeks | De bewerking die de gebeurtenis heeft geactiveerd. |
| clientRequestId | tekenreeks | een door de client opgegeven aanvraag-id voor de opslag-API-bewerking. Deze id kan worden gebruikt om te correleren met diagnostische logboeken voor Azure Storage met behulp van het veld 'client-request-id' in de logboeken en kan worden verstrekt in clientaanvragen met de koptekst 'x-ms-client-request-id'. Zie [Logboeknotatie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | tekenreeks | Door service gegenereerde aanvraag-id voor de opslag-API-bewerking. Kan worden gebruikt om te correleren met diagnostische logboeken voor Azure Storage met behulp van het veld 'request-id-header' in de logboeken en wordt geretourneerd door het starten van API-aanroep in de koptekst 'x-ms-request-id'. Zie [Logboeknotatie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | tekenreeks | De waarde die u gebruiken om bewerkingen voorwaardelijk uit te voeren. |
| Contenttype | tekenreeks | Het inhoudstype dat is opgegeven voor de blob. |
| inhoudLengte | geheel getal | De grootte van de blob in bytes. |
| blobtype | tekenreeks | Het type blob. Geldige waarden zijn "BlockBlob" of "PageBlob". |
| inhoudOffset | getal | De verschuiving in bytes van een schrijfbewerking die is uitgevoerd op het punt waarop de gebeurtenistriggeringstoepassing het schrijven naar het bestand heeft voltooid. <br>Wordt alleen weergegeven voor gebeurtenissen die zijn geactiveerd op blobopslagaccounts met een hiërarchische naamruimte.|
| destinationUrl |tekenreeks | De url van het bestand dat zal bestaan nadat de bewerking is voltooid. Als een bestand bijvoorbeeld een andere `destinationUrl` naam krijgt, bevat de eigenschap de url van de nieuwe bestandsnaam. <br>Wordt alleen weergegeven voor gebeurtenissen die zijn geactiveerd op blobopslagaccounts met een hiërarchische naamruimte.|
| sourceUrl |tekenreeks | De url van het bestand dat vóór de bewerking bestaat. Als een bestand bijvoorbeeld een andere `sourceUrl` naam krijgt, bevat het bestand de url van de oorspronkelijke bestandsnaam voorafgaand aan de bewerking hernoemen. <br>Wordt alleen weergegeven voor gebeurtenissen die zijn geactiveerd op blobopslagaccounts met een hiërarchische naamruimte. |
| url | tekenreeks | Het pad naar de blob. <br>Als de client een Blob REST API gebruikt, heeft de url deze structuur: * \<storage-account-name\>\<.blob.core.windows.net/ containernaam\>/\<bestandsnaam\>*. <br>Als de client een Data Lake Storage REST API gebruikt, heeft de url deze structuur: * \<storage-account-name\>\<.dfs.core.windows.net/ file-system-name\>/\<file-name file-name\>*. |
| Recursieve | tekenreeks | `True`de verrichting op alle kindermappen uit te voeren; anders `False`. <br>Wordt alleen weergegeven voor gebeurtenissen die zijn geactiveerd op blobopslagaccounts met een hiërarchische naamruimte. |
| Sequencer | tekenreeks | Een ondoorzichtige tekenreekswaarde die de logische volgorde van gebeurtenissen voor een bepaalde blobnaam vertegenwoordigt.  Gebruikers kunnen standaardtekenreeksvergelijking gebruiken om de relatieve volgorde van twee gebeurtenissen op dezelfde blobnaam te begrijpen. |
| storageDiagnostics | object | Diagnostische gegevens die af en toe zijn opgenomen door de Azure Storage-service. Wanneer aanwezig, moet worden genegeerd door event consumenten. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie [Route Blob-opslaggebeurtenissen - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)voor een inleiding tot het werken met blob-opslaggebeurtenissen. 
