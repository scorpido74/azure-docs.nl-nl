---
title: Bekende problemen met Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de beperkingen en bekende problemen met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 3ea77eb5dd8a03f877164179e3accc3a6f6d0aef
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548322"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekende problemen met Azure Data Lake Storage Gen2

Dit artikel bevat een overzicht van de functies en hulpprogram ma's die nog niet worden ondersteund of die slechts gedeeltelijk worden ondersteund met opslag accounts die een hiërarchische naam ruimte (Azure Data Lake Storage Gen2) hebben.

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problemen en beperkingen bij het gebruik van BLOB-Api's

BLOB-Api's en Data Lake Storage Gen2-Api's kunnen op dezelfde gegevens worden gebruikt.

In deze sectie worden de problemen en beperkingen beschreven met het gebruik van BLOB-Api's en Data Lake Storage Gen2 Api's om op dezelfde gegevens te werken.

* U kunt niet zowel BLOB-Api's als Data Lake Storage Api's gebruiken om naar hetzelfde exemplaar van een bestand te schrijven. Als u naar een bestand schrijft met behulp van Data Lake Storage Gen2 Api's, zijn de blokken van dat bestand niet zichtbaar voor aanroepen naar de BLOB-API voor [blok keren ophalen](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . U kunt een bestand overschrijven door gebruik te maken van Data Lake Storage Gen2 Api's of BLOB-Api's. Dit heeft geen invloed op de bestands eigenschappen.

* Wanneer u de bewerking [lijst-blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruikt zonder een scheidings teken op te geven, bevatten de resultaten zowel directory's als blobs. Als u een scheidings teken wilt gebruiken, moet u alleen een slash (`/`) gebruiken. Dit is het enige ondersteunde scheidings teken.

* Als u de [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API gebruikt om een map te verwijderen, wordt die map alleen verwijderd als deze leeg is. Dit betekent dat u de BLOB-API niet recursief kunt gebruiken.

Deze BLOB REST Api's worden niet ondersteund:

* [BLOB plaatsen (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Pagina plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Paginabereiken ophalen](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB voor incrementele kopie](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Pagina van URL plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put-BLOB (toevoegen)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Blok toevoegen](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blok van URL toevoegen](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Niet-beheerde VM-schijven worden niet ondersteund in accounts met een hiërarchische naam ruimte. Als u een hiërarchische naam ruimte wilt inschakelen op een opslag account, plaatst u onbeheerde VM-schijven in een opslag account waarvoor de functie hiërarchische naam ruimte niet is ingeschakeld.

<a id="api-scope-data-lake-client-library" />

## <a name="api-scope-for-data-lake-storage-client-library-for-sdk-powershell-and-cli"></a>API-bereik voor Data Lake Storage-client bibliotheek voor SDK, Power shell en CLI

### <a name="filesystem-support-in-sdks"></a>Bestandssysteem ondersteuning in Sdk's

- .NET, Java en python bevinden zich in de open bare preview. Andere Sdk's worden momenteel niet ondersteund.
- Het ophalen en instellen van Acl's-bewerkingen zijn momenteel niet recursief.

### <a name="filesystem-support-in-powershell-and-azure-cli"></a>Bestandssysteem ondersteuning in Power shell en Azure CLI

Het ophalen en instellen van Acl's-bewerkingen zijn momenteel niet recursief.

## <a name="support-for-other-blob-storage-features"></a>Ondersteuning voor andere Blob Storage-functies

De volgende tabel bevat alle andere functies en hulpprogram ma's die nog niet worden ondersteund of die slechts gedeeltelijk worden ondersteund met opslag accounts die een hiërarchische naam ruimte (Azure Data Lake Storage Gen2) hebben.

| Functie/hulp programma    | Meer informatie    |
|--------|-----------|
| **AzCopy** | Versie-specifieke ondersteuning <br><br>Gebruik alleen de meest recente versie van AzCopy ([AzCopy V10 toevoegen](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Eerdere versies van AzCopy, zoals AzCopy v 8.1, worden niet ondersteund.|
| **Beheer beleid voor Azure Blob Storage levenscyclus** | Levenscyclus beheer beleid wordt ondersteund (preview-versie).  Alle toegangs lagen worden ondersteund. De Access-laag voor het archief is momenteel beschikbaar als preview-versie. Het verwijderen van BLOB-moment opnamen wordt nog niet ondersteund. <br><br> Er zijn momenteel een aantal fouten die het levenscyclus beheer beleid en de Access-laag voor archieven beïnvloeden.  Meld u aan voor de preview van levenscyclus beheer-beleid en Archive Access [-laag.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)   |
| **Azure-Content Delivery Network (CDN)** | Nog niet ondersteund|
| **Azure Search** |Ondersteund (preview-versie)|
| **Azure-opslagverkenner** | Versie-specifieke ondersteuning. <br><br>Gebruik alleen versies `1.6.0` of hoger. <br> Er is momenteel een opslag fout die van invloed is op de versie `1.11.0` die kan leiden tot verificatie fouten in bepaalde scenario's. Er wordt een oplossing voor de opslag fout geïmplementeerd, maar als tijdelijke oplossing wordt u aangeraden versie `1.10.x` te gebruiken die beschikbaar is als [gratis down load](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` wordt niet beïnvloed door de opslag fout.|
| **BLOB-container-Acl's** |Nog niet ondersteund|
| **Blobfuse** |Nog niet ondersteund|
| **Aangepaste domeinen** |Nog niet ondersteund|
| **Storage Explorer in de Azure Portal** | Beperkte ondersteuning. Acl's worden nog niet ondersteund. |
| **Registratie in diagnoselogboek** |Diagnostische logboeken worden ondersteund (preview).<br><br>Het inschakelen van Logboeken in het Azure Portal wordt momenteel niet ondersteund. Hier volgt een voor beeld van het inschakelen van de logboeken met behulp van Power shell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Zorg ervoor dat u `Blob` opgeeft als de waarde van de para meter `-ServiceType`, zoals wordt weer gegeven in dit voor beeld. <br><br>Momenteel kan Azure Storage Explorer niet worden gebruikt voor het weer geven van Diagnostische logboeken. Als u logboeken wilt weer geven, gebruikt u AzCopy of Sdk's.
| **Onveranderbare opslag** |Nog niet ondersteund <br><br>Onveranderbare opslag biedt de mogelijkheid om gegevens op te slaan in een [worm (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Lagen op object niveau** |De lagen cool en Archive worden ondersteund. De Archive-laag is beschikbaar als preview-versie. Alle andere toegangs lagen worden nog niet ondersteund. <br><br> Er zijn momenteel enkele fouten die van invloed zijn op de toegangs laag voor het archief.  Meld u aan voor de preview-versie van de Access [-laag voor](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)het archief.|
| **Ondersteuning voor Power shell en CLI** | Beperkte functionaliteit <br><br>BLOB-bewerkingen worden ondersteund. Werken met mappen en toegangs beheer lijsten (Acl's) instellen wordt nog niet ondersteund. |
| **Statische websites** |Nog niet ondersteund <br><br>Met name de mogelijkheid om bestanden te leveren aan [statische websites](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Toepassingen van derden** | Beperkte ondersteuning <br><br>Toepassingen van derden die gebruikmaken van REST-Api's voor werken, blijven werken als u ze gebruikt met Data Lake Storage Gen2. <br>Toepassingen die BLOB-Api's aanroepen, werken waarschijnlijk.|
|**Voorlopig verwijderen** |Nog niet ondersteund|
| **Versie functies** |Nog niet ondersteund <br><br>Dit omvat het [voorlopig verwijderen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)en andere versies van functies, zoals [moment opnamen](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


