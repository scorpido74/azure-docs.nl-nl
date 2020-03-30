---
title: Bekende problemen met Azure Data Lake Storage Gen2 | Microsoft Documenten
description: Meer informatie over beperkingen en bekende problemen van Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061513"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekende problemen met Azure Data Lake Storage Gen2

In dit artikel worden beperkingen en bekende problemen van Azure Data Lake Storage Gen2 beschreven.

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob-opslagfuncties

Steeds meer Blob-opslagfuncties werken nu met accounts met een hiërarchische naamruimte. Zie [Blob Storage-functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een volledige lijst.

## <a name="supported-azure-service-integrations"></a>Ondersteunde Azure-service-integraties

Data Lake Storage gen2 ondersteunt verschillende Azure-services die u gebruiken om gegevens in te nemen, analyses uit te voeren en visuele weergaven te maken. Zie [Azure-services die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md)voor een lijst met ondersteunde Azure-services.

Bekijk [Azure-services die Azure Data Lake Storage Gen2 ondersteunen.](data-lake-storage-supported-azure-services.md)

## <a name="supported-open-source-platforms"></a>Ondersteunde open source platforms

Verschillende open source platforms ondersteunen Data Lake Storage Gen2. Zie [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md)voor een volledige lijst.

Zie [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen.](data-lake-storage-supported-open-source-platforms.md)

## <a name="blob-storage-apis"></a>Blob-opslag-API's

Blob API's en Data Lake Storage Gen2 API's kunnen op dezelfde gegevens werken.

In deze sectie worden problemen en beperkingen beschreven met het gebruik van blob-API's en API's voor Gegevensmeeropslag Gen2 om op dezelfde gegevens te werken.

* U niet beide Blob API's en API's voor gegevensopslag gebruiken om naar dezelfde instantie van een bestand te schrijven. Als u naar een bestand schrijft met behulp van API's van Data Lake Storage Gen2, zijn de blokken van dat bestand niet zichtbaar voor oproepen naar de BLOB-API [voor bloklijst downloaden.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) U een bestand overschrijven met api's van Data Lake Storage Gen2 of Blob API's. Dit heeft geen invloed op bestandseigenschappen.

* Wanneer u de [bewerking Lijstblobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruikt zonder een scheidingsteken op te geven, bevatten de resultaten zowel mappen als blobs. Als u ervoor kiest om een scheidingsteken te`/`gebruiken, gebruikt u alleen een voorwaartse slash ( ). Dit is de enige ondersteunde scheidingsteken.

* Als u de [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API gebruikt om een map te verwijderen, wordt die map alleen verwijderd als deze leeg is. Dit betekent dat u de blob-api-verwijdermappen niet recursief gebruiken.

Deze Blob REST API's worden niet ondersteund:

* [Blob plaatsen (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Pagina plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Paginabereiken ophalen](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob incrementele kopie](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Pagina van URL plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blob plaatsen (toevoegen)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Blok toevoegen](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blok toevoegen aan URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Niet-beheerde VM-schijven worden niet ondersteund in accounts met een hiërarchische naamruimte. Als u een hiërarchische naamruimte in een opslagaccount wilt inschakelen, plaatst u onbeheerde VM-schijven in een opslagaccount dat de functie hiërarchische naamruimte niet heeft ingeschakeld.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Ondersteuning voor bestandssystemen in SDKs

ACL-bewerkingen oppakken en instellen zijn momenteel niet recursief.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Ondersteuning voor bestandssystemen in PowerShell en Azure CLI

- [PowerShell-](data-lake-storage-directory-file-acl-powershell.md) en [Azure CLI-ondersteuning](data-lake-storage-directory-file-acl-cli.md) staan in een openbare preview.
- ACL-bewerkingen oppakken en instellen zijn momenteel niet recursief.

## <a name="lifecycle-management-policies"></a>Beleid voor levenscyclusbeheer

* Het verwijderen van blobsnapshots wordt nog niet ondersteund.  

## <a name="archive-tier"></a>Archieflaag

Er is momenteel een bug die van invloed is op de categorie archieftoegang.


## <a name="blobfuse"></a>Blobfuse

Blobfuse wordt niet ondersteund.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Gebruik alleen de nieuwste versie van AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Eerdere versies van AzCopy, zoals AzCopy v8.1, worden niet ondersteund.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Opslagverkenner

Gebruik alleen `1.6.0` versies of hoger.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer in de Azure-portal

ACL's worden nog niet ondersteund.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Toepassingen van derden

Toepassingen van derden die REST API's gebruiken om te werken, blijven werken als u ze gebruikt met Data Lake Storage Gen2-toepassingen die Blob-API's aanroepen, zullen waarschijnlijk werken.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Toegangscontrolelijsten (ACL) en anonieme leestoegang

Als [anonieme leestoegang](storage-manage-access-to-resources.md) is verleend aan een container, hebben ACL's geen effect op die container of de bestanden in die container.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Wasb-stuurprogramma (Windows Azure Storage Blob)

Momenteel zijn er verschillende problemen verbonden aan het gebruik van het WASB-stuurprogramma, samen met accounts met een hiërarchische naamruimte. We raden u aan het [ABFS-stuurprogramma (Azure Blob File System)](data-lake-storage-abfs-driver.md) te gebruiken in uw workloads. 





