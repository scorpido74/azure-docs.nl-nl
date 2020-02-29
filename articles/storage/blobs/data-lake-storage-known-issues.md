---
title: Bekende problemen met Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over beperkingen en bekende problemen met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919608"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekende problemen met Azure Data Lake Storage Gen2

In dit artikel worden beperkingen en bekende problemen met Azure Data Lake Storage Gen2 beschreven.

## <a name="supported-blob-storage-features"></a>Ondersteunde functies van Blob-opslag

Een toenemend aantal functies voor Blob Storage werkt nu met accounts die een hiërarchische naam ruimte hebben. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een volledige lijst.

## <a name="supported-azure-service-integrations"></a>Ondersteunde Azure-service-integraties

Data Lake Storage Gen2 ondersteunt verschillende Azure-Services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. Zie [Azure-Services die ondersteuning bieden voor Azure data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)voor een lijst met ondersteunde Azure-Services.

Zie [Azure-Services die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Open source-platforms ondersteund

Data Lake Storage Gen2 ondersteuning voor verschillende open source-platforms. Zie voor een volledige lijst [open-source platforms die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md).

Zie [open-source platforms die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>BLOB storage-API 's

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

## <a name="file-system-support-in-sdks"></a>Ondersteuning voor bestands systemen in Sdk's

- [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) , [python](data-lake-storage-directory-file-acl-python.md)en [Java script](data-lake-storage-directory-file-acl-javascript.md) en ondersteuning zijn beschikbaar als open bare preview. Andere Sdk's worden momenteel niet ondersteund.
- Het ophalen en instellen van ACL-bewerkingen is momenteel niet recursief.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Ondersteuning voor bestands systemen in Power shell en Azure CLI

- [Power shell](data-lake-storage-directory-file-acl-powershell.md) en [Azure cli](data-lake-storage-directory-file-acl-cli.md) -ondersteuning zijn beschikbaar als open bare preview.
- Het ophalen en instellen van ACL-bewerkingen is momenteel niet recursief.

## <a name="lifecycle-management-policies"></a>Levenscyclus beheer beleid

* Het verwijderen van BLOB-moment opnamen wordt nog niet ondersteund.  

* Er zijn momenteel een aantal fouten die het levenscyclus beheer beleid en de Access-laag voor archieven beïnvloeden. 

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Azure Storage Explorer 1,10. x kan niet worden gebruikt voor het weer geven van Diagnostische logboeken. Als u logboeken wilt weer geven, gebruikt u AzCopy of Sdk's.

## <a name="blobfuse"></a>Blobfuse

Blobfuse wordt niet ondersteund.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Gebruik alleen de meest recente versie van AzCopy ([AzCopy V10 toevoegen](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Eerdere versies van AzCopy, zoals AzCopy v 8.1, worden niet ondersteund.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Opslagverkenner

Gebruik alleen versies `1.6.0` of hoger. Er is momenteel een opslag fout die van invloed is op de versie `1.11.0` die in bepaalde scenario's verificatie fouten kan veroorzaken. Er wordt een oplossing voor de opslag fout geïmplementeerd, maar als tijdelijke oplossing wordt u aangeraden versie `1.10.x` te gebruiken die beschikbaar is als [gratis down load](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` wordt niet beïnvloed door de opslag fout.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer in azure Portal

Acl's worden nog niet ondersteund.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Toepassingen van derden

Toepassingen van derden die gebruikmaken van REST-Api's voor werken, blijven werken als u ze gebruikt met Data Lake Storage Gen2 toepassingen die BLOB-Api's aanroepen, waarschijnlijk zullen werken.





