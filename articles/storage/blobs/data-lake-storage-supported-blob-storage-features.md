---
title: Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de functies van Blob Storage die u kunt gebruiken met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0e7cedaea89e2ed3d998df6ffe0ecaa06115e265
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513788"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2

Blob Storage functies als [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [Blob Storage levenscyclus beheer beleid](storage-lifecycle-management-concepts.md) werken nu met accounts die een hiërarchische naam ruimte hebben. U kunt dus hiërarchische naamruimten inschakelen op uw Blob Storage-accounts zonder toegang tot deze functies te verliezen.

Deze tabel geeft een lijst van de functies voor Blob-opslag die u kunt gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weer gegeven, worden in de loop van de tijd gewijzigd, omdat de ondersteuning blijft toenemen. Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor meer informatie over specifieke problemen met betrekking tot de preview-status van een functie.

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

> [!NOTE]
> Ondersteunings niveau verwijst alleen naar de manier waarop de functie wordt ondersteund met Data Lake Storage Gen2. 
>
> [Premium-performance BlockBlobStorage-accounts](storage-blob-create-account-block-blob.md) voor data Lake Storage Gen2 zijn momenteel beschikbaar als open bare preview. Ondersteunings niveaus voor deze typen accounts worden weer gegeven in de kolom **BlockBlobStorage (Premium)** .

|Blob Storage functie |Voor algemeen gebruik v2 |BlockBlobStorage (Premium) |Verwante artikelen: |
|---------------|-------------------|---|
|Hot Storage-toegangslaag|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Cool Storage-toegangslaag|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Gebeurtenissen|Algemeen beschikbaar|Preview|[Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)|
|Metrische gegevens (klassiek)|Algemeen beschikbaar|Niet ondersteund|[Azure Storage Analytics-metrische gegevens (klassiek)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrische gegevens in Azure Monitor|Algemeen beschikbaar|Preview|[Metrische gegevens van Azure Storage in Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Power shell-opdrachten voor Blob-opslag|Algemeen beschikbaar|Preview|[Quick Start: blobs uploaden, downloaden en vermelden met Power shell](storage-quickstart-blobs-powershell.md)|
|Azure CLI-opdrachten voor Blob Storage|Algemeen beschikbaar|Preview|[Quickstart: Blobs maken, downloaden, uploaden en weergeven met Azure CLI](storage-quickstart-blobs-cli.md)|
|Api's voor Blob-opslag|Algemeen beschikbaar|Preview|[Snelstart: De Azure Blob Storage-clientbibliotheek v12 voor .NET](storage-quickstart-blobs-dotnet.md)<br>[Quick Start: blobs beheren met Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Blobs beheren met Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Blobs beheren met JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnostische logboeken|Algemeen beschikbaar|Preview <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Logboekregistratie van Azure Opslaganalyse](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Access-laag archiveren|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Levenscyclus beheer beleid|Algemeen beschikbaar|Nog niet ondersteund|[De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)|
|Aanmelden Azure Monitor|Preview |Nog niet ondersteund|[Bewakings Azure Storage](../common/monitor-storage.md)|
|Momentopnamen|Preview<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Nog niet ondersteund|[BLOB-moment opnamen](snapshots-overview.md)|
|Statische websites|Preview<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Nog niet ondersteund|[Een statische website hosten in Azure Storage](storage-blob-static-website.md)|
|Onveranderbare opslag|Preview<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Nog niet ondersteund|[Bedrijfs kritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)|
|Blobfuse|Preview|Nog niet ondersteund|[Blob-opslag koppelen als een bestands systeem met blobfuse](storage-how-to-mount-container-linux.md)|
|Feed wijzigen|Nog niet ondersteund|Nog niet ondersteund|[Ondersteuning voor feed wijzigen in Azure Blob-opslag](storage-blob-change-feed.md)|
|Account-failover|Nog niet ondersteund|Nog niet ondersteund|[Herstel na nood gevallen en failover van accounts](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB-container-ACL|Nog niet ondersteund<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Nog niet ondersteund<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Container-ACL instellen](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Aangepaste domeinen|Nog niet ondersteund|Nog niet ondersteund|[Een aangepast domein toewijzen aan een Azure Blob Storage-eind punt](storage-custom-domain-name.md)|
|Voorlopig verwijderen|Nog niet ondersteund|Nog niet ondersteund|[Voorlopig verwijderen voor Azure Storage-blobs](storage-blob-soft-delete.md)|

<div id="diagnostic-logging"><sup>1</sup> voor Premium Block Blob Storage-accounts kunnen Diagnostische logboeken (klassiek) niet worden ingeschakeld met behulp van de Azure Portal. Schakel deze in met behulp van Power shell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> u kunt acl's instellen voor de hoofdmap van de container, maar niet de container zelf.</div><br>

<div id="preview-form"><sup>3</sup> Als u moment opnamen, onveranderlijke opslag of statische websites met Data Lake Storage Gen2 wilt gebruiken, moet u dit <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulier</a>invullen in de preview-versie.  </div>

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)