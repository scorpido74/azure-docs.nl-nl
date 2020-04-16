---
title: Azure Blob Storage als gebeurtenisrasterbron
description: Beschrijft de eigenschappen die zijn geleverd voor blobopslaggebeurtenissen met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: cfc6e4790b67137b423cc90d93874d4914f81251
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393377"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob Storage als gebeurtenisrasterbron

In dit artikel worden de eigenschappen en het schema voor blobopslaggebeurtenissen weergeven.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md) Het geeft u ook een lijst met snelle starts en zelfstudies om Azure Blob Storage als gebeurtenisbron te gebruiken.


>[!NOTE]
> Alleen opslagaccounts van natura **StorageV2 (v2 voor algemeen gebruik),** **BlockBlobStorage**en **BlobStorage** ondersteunen gebeurtenis-integratie. **Opslag (genral ei v1)** biedt *geen* ondersteuning voor integratie met Event Grid.

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

### <a name="list-of-events-for-blob-rest-apis"></a>Lijst met gebeurtenissen voor Blob REST API's

Deze gebeurtenissen worden geactiveerd wanneer een client een blob maakt, vervangt of verwijdert door Blob REST API's aan te roepen.

 |Gebeurtenisnaam |Beschrijving|
 |----------|-----------|
 |**Microsoft.Storage.BlobGemaakt** |Geactiveerd wanneer een blob wordt gemaakt of vervangen. <br>Deze gebeurtenis wordt in het `PutBlob`bijzonder `PutBlockList`geactiveerd `CopyBlob` wanneer clients de , of bewerkingen gebruiken die beschikbaar zijn in de Blob REST API.   |
 |**Microsoft.Storage.BlobVerwijderd** |Geactiveerd wanneer een blob wordt verwijderd. <br>Deze gebeurtenis wordt met name `DeleteBlob` geactiveerd wanneer clients de bewerking aanroepen die beschikbaar is in de Blob REST API. |

> [!NOTE]
> Als u ervoor wilt zorgen dat de gebeurtenis **Microsoft.Storage.BlobCreated** alleen wordt geactiveerd wanneer `CopyBlob` `PutBlob`een `PutBlockList` Block Blob volledig is vastgelegd, filtert u de gebeurtenis voor de API-aanroepen en REST. Deze API-aanroepen activeren de gebeurtenis **Microsoft.Storage.BlobCreated** pas nadat gegevens volledig zijn toegewezen aan een Block Blob. Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het maken van een filter.

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lijst met de gebeurtenissen voor Azure Data Lake Storage Gen 2 REST API's

Deze gebeurtenissen worden geactiveerd als u een hiërarchische naamruimte inschakelt op het opslagaccount en clients Azure Data Lake Storage Gen2 REST API's aanroepen. Zie Inleiding tot Azure Data Lake Storage Gen2 voor meer informatie over Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md)

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

### <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenisantwoord

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

Als het blob-opslagaccount een hiërarchische naamruimte heeft, lijken de gegevens op het vorige voorbeeld, met uitzondering van deze wijzigingen:

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

Als het blob-opslagaccount een hiërarchische naamruimte heeft, lijken de gegevens op het vorige voorbeeld, met uitzondering van deze wijzigingen:

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

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

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

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: blob-opslaggebeurtenissen routeren naar een aangepast webeindpunt met Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure CLI gebruikt om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Snelstart: blob-opslaggebeurtenissen routeren naar een aangepast webeindpunt met PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Hier ziet u hoe u Azure PowerShell gebruiken om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Snelstart: Blob-opslaggebeurtenissen maken en routeren met de Azure-portal](blob-event-quickstart-portal.md) | Laat zien hoe u de portal gebruikt om blob-opslaggebeurtenissen naar een WebHook te verzenden. |
| [Azure CLI: u abonneren op gebeurtenissen voor een Blob-opslagaccount](./scripts/event-grid-cli-blob.md) | Voorbeeldscript dat zich abonneert op een gebeurtenis voor een Blob-opslagaccount. Het stuurt de gebeurtenis naar een WebHook. |
| [PowerShell: abonneer u op gebeurtenissen voor een Blob-opslagaccount](./scripts/event-grid-powershell-blob.md) | Voorbeeldscript dat zich abonneert op een gebeurtenis voor een Blob-opslagaccount. Het stuurt de gebeurtenis naar een WebHook. |
| [Resourcemanager-sjabloon: Blob-opslag en -abonnement maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Hiermee implementeert u een Azure Blob-opslagaccount en abonneert u zich op gebeurtenissen van die opslagaccount. Het stuurt gebeurtenissen naar een WebHook. |
| [Overzicht: reageren op blob-opslaggebeurtenissen](../storage/blobs/storage-blob-event-overview.md) | Overzicht van de integratie van Blob-opslag met gebeurtenisraster. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie [Route Blob-opslaggebeurtenissen - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)voor een inleiding tot het werken met blob-opslaggebeurtenissen. 
