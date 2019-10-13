---
title: Bekende problemen met Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de beperkingen en bekende problemen met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 300da59aa1a16bb2c4cfeaf8035cbe882ae83358
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300238"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekende problemen met Azure Data Lake Storage Gen2

Dit artikel bevat een overzicht van de functies en hulpprogram ma's die nog niet worden ondersteund of die slechts gedeeltelijk worden ondersteund met opslag accounts die een hiërarchische naam ruimte (Azure Data Lake Storage Gen2) hebben.

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob Storage-Api's

Blob Storage-Api's zijn uitgeschakeld om problemen met de functie operability te voor komen, omdat Blob Storage Api's nog niet compatibel zijn met Azure Data Lake Gen2-Api's.

> [!NOTE]
> Met de open bare preview van toegang via meerdere protocollen op Data Lake Storage, kunnen BLOB-Api's en Data Lake Storage Gen2-Api's op dezelfde gegevens worden gebruikt. Zie [multi-protocol toegang op Data Lake Storage](data-lake-storage-multi-protocol-access.md)voor meer informatie.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Wat u kunt doen met bestaande hulpprogram ma's, toepassingen en services

Als een van deze BLOB-Api's gebruikt en u deze wilt gebruiken om te werken met alle inhoud in uw account, hebt u twee opties.

* **Optie 1**: Schakel geen hiërarchische naam ruimte in op uw Blob Storage-account totdat [toegang via meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) algemeen beschikbaar is en BLOB-api's volledig compatibel worden met Azure data Lake Gen2-api's. [Toegang tot meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) is momenteel beschikbaar als open bare preview.  Als u een opslag account **zonder** een hiërarchische naam ruimte gebruikt, hebt u geen toegang tot data Lake Storage Gen2 specifieke functies, zoals de toegangs beheer lijsten voor mappen en containers.

* **Optie 2**: hiërarchische naam ruimten inschakelen. Met de open bare preview [-versie van toegang tot meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md), hulpprogram ma's en toepassingen die BLOB-api's aanroepen, en Blob Storage functies, zoals Diagnostische logboeken, kunnen werken met accounts die een hiërarchische naam ruimte hebben. Lees dit artikel voor bekende problemen en beperkingen.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Wat te doen als u BLOB-Api's hebt gebruikt om gegevens te laden voordat BLOB-Api's werden uitgeschakeld

Als u deze Api's hebt gebruikt om gegevens te laden voordat ze werden uitgeschakeld en u een productie vereiste hebt voor toegang tot die gegevens, neemt u contact op met Microsoft Ondersteuning met de volgende gegevens:

> [!div class="checklist"]
> * De abonnements-ID (de GUID, niet de naam).
> * Naam van opslag account (s).
> * Of u nu actief van invloed hebt op de productie, en zo ja, voor welke opslag accounts?.
> * Zelfs als u niet actief van invloed is op de productie, moet u er rekening mee houden dat deze gegevens om een bepaalde reden moeten worden gekopieerd naar een ander opslag account. als dat het geval is, waarom?

In deze omstandigheden kunnen we de toegang tot de BLOB-API voor een beperkte periode herstellen, zodat u deze gegevens kunt kopiëren naar een opslag account waarvoor de functie hiërarchische naam ruimte niet is ingeschakeld.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problemen en beperkingen bij het gebruik van BLOB-Api's voor accounts met een hiërarchische naam ruimte

Met de open bare preview van toegang via meerdere protocollen op Data Lake Storage, kunnen BLOB-Api's en Data Lake Storage Gen2-Api's op dezelfde gegevens worden gebruikt.

In deze sectie worden de problemen en beperkingen beschreven met het gebruik van BLOB-Api's en Data Lake Storage Gen2 Api's om op dezelfde gegevens te werken.

* U kunt niet zowel BLOB-Api's als Data Lake Storage Api's gebruiken om naar hetzelfde exemplaar van een bestand te schrijven.

* Als u naar een bestand schrijft met behulp van Data Lake Storage Gen2 Api's, zijn de blokken van dat bestand niet zichtbaar voor aanroepen naar de BLOB-API voor [blok keren ophalen](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* U kunt een bestand overschrijven door gebruik te maken van Data Lake Storage Gen2 Api's of BLOB-Api's. Dit heeft geen invloed op de bestands eigenschappen.

* Wanneer u de bewerking [lijst-blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruikt zonder een scheidings teken op te geven, bevatten de resultaten zowel directory's als blobs.

  Als u ervoor kiest om een scheidings teken te gebruiken, gebruikt u alleen een slash (`/`). Dit is het enige ondersteunde scheidings teken.

* Als u de [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API gebruikt om een map te verwijderen, wordt die map alleen verwijderd als deze leeg is.

  Dit betekent dat u de BLOB-API niet recursief kunt gebruiken.

Deze BLOB REST Api's worden niet ondersteund:

* [BLOB plaatsen (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Pagina plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Paginabereiken ophalen](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB voor incrementele kopie](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Pagina van URL plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put-BLOB (toevoegen)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Blok toevoegen](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blok van URL toevoegen](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problemen met niet-beheerde virtuele machine schijven (VM)

Niet-beheerde VM-schijven worden niet ondersteund in accounts met een hiërarchische naam ruimte. Als u een hiërarchische naam ruimte wilt inschakelen op een opslag account, plaatst u onbeheerde VM-schijven in een opslag account waarvoor de functie hiërarchische naam ruimte niet is ingeschakeld.


## <a name="support-for-other-blob-storage-features"></a>Ondersteuning voor andere Blob Storage-functies

De volgende tabel bevat alle andere functies en hulpprogram ma's die nog niet worden ondersteund of die slechts gedeeltelijk worden ondersteund met opslag accounts die een hiërarchische naam ruimte (Azure Data Lake Storage Gen2) hebben.

| Functie/hulp programma    | Meer informatie    |
|--------|-----------|
| **Api's voor Data Lake Storage Gen2 Storage-accounts** | Gedeeltelijk ondersteund <br><br>Toegang tot meerdere protocollen op Data Lake Storage is momenteel beschikbaar als open bare preview. Met deze preview-versie kunt u BLOB-Api's gebruiken in de .NET-, Java-en python-Sdk's met accounts die een hiërarchische naam ruimte hebben.  De Sdk's bevatten nog geen Api's waarmee u met mappen kunt communiceren of toegangs beheer lijsten (Acl's) kunt instellen. Als u deze functies wilt uitvoeren, kunt u Data Lake Storage Gen2 **rest** -api's gebruiken. |
| **AzCopy** | Versie-specifieke ondersteuning <br><br>Gebruik alleen de meest recente versie van AzCopy ([AzCopy V10 toevoegen](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Eerdere versies van AzCopy, zoals AzCopy v 8.1, worden niet ondersteund.|
| **Beheer beleid voor Azure Blob Storage levenscyclus** | Ondersteund door de [toegang voor meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) preview. Koel-en archief toegangs lagen worden alleen ondersteund door de preview-versie. Het verwijderen van BLOB-moment opnamen wordt nog niet ondersteund. |
| **Azure-Content Delivery Network (CDN)** | Nog niet ondersteund|
| **Azure Search** |Ondersteund door de [toegang voor meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) preview.|
| **Azure-opslagverkenner** | Versie-specifieke ondersteuning <br><br>Gebruik alleen versie `1.6.0` of hoger. <br>Versie `1.6.0` is beschikbaar als [gratis down load](https://azure.microsoft.com/features/storage-explorer/).|
| **BLOB-container-Acl's** |Nog niet ondersteund|
| **Blobfuse** |Nog niet ondersteund|
| **Aangepaste domeinen** |Nog niet ondersteund|
| **Bestands systeem Verkenner** | Beperkte ondersteuning |
| **Registratie in diagnoselogboek** |Diagnostische logboeken worden ondersteund door de [toegang voor meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) preview. <br><br>Het inschakelen van Logboeken in het Azure Portal wordt momenteel niet ondersteund. Hier volgt een voor beeld van het inschakelen van de logboeken met behulp van Power shell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Zorg ervoor dat u `Blob` opgeeft als waarde voor de para meter `-ServiceType`, zoals wordt weer gegeven in dit voor beeld. <br><br>Momenteel kan Azure Storage Explorer niet worden gebruikt voor het weer geven van Diagnostische logboeken. Als u logboeken wilt weer geven, gebruikt u AzCopy of Sdk's.
| **Onveranderbare opslag** |Nog niet ondersteund <br><br>Onveranderbare opslag biedt de mogelijkheid om gegevens op te slaan in een [worm (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Lagen op object niveau** |De lagen cool en Archive worden ondersteund door de [toegang voor meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md) preview. <br><br> Alle andere toegangs lagen worden nog niet ondersteund.|
| **Ondersteuning voor Power shell en CLI** | Beperkte functionaliteit <br><br>Beheer bewerkingen, zoals het maken van een account, worden ondersteund. Gegevenslaag bewerkingen, zoals het uploaden en downloaden van bestanden, bevinden zich in de open bare preview als onderdeel van [toegang tot meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md). Werken met mappen en toegangs beheer lijsten (Acl's) instellen wordt nog niet ondersteund. |
| **Statische websites** |Nog niet ondersteund <br><br>Met name de mogelijkheid om bestanden te leveren aan [statische websites](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Toepassingen van derden** | Beperkte ondersteuning <br><br>Toepassingen van derden die gebruikmaken van REST-Api's voor werken, blijven werken als u ze gebruikt met Data Lake Storage Gen2. <br>Toepassingen die BLOB-Api's aanroepen, werken waarschijnlijk met de open bare preview van [toegangs rechten voor meerdere protocollen op Data Lake Storage](data-lake-storage-multi-protocol-access.md). 
| **Versie functies** |Nog niet ondersteund <br><br>Dit omvat [moment opnamen](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) en [tijdelijke verwijdering](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


