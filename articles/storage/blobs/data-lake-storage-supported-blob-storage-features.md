---
title: Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de functies van Blob Storage die u kunt gebruiken met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: debc674184e6846128b0b8cff857c75a805e365a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925694"
---
# <a name="blobstoragefeaturesavailableinazuredatalakestoragegen2"></a>Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2

Blob Storage functies als [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [Blob Storage levenscyclus beheer beleid](storage-lifecycle-management-concepts.md) werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze functies te verliezen.

Deze tabel geeft een lijst van de functies voor Blob-opslag die u kunt gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weer gegeven, worden in de loop van de tijd gewijzigd, omdat de ondersteuning blijft toenemen.

## <a name="supportedblobstoragefeatures"></a>Ondersteunde functies van Blob-opslag

> [!NOTE]
> Ondersteunings niveau verwijst alleen naar de manier waarop de functie wordt ondersteund met Data Lake Storage Gen2.

|Blob Storage functie |Ondersteunings niveau |Verwante artikelen |
|---------------|-------------------|---|
|Hot Storage-toegangslaag |Algemeen beschikbaar|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Cool Storage-toegangslaag |Algemeen beschikbaar|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Gebeurtenissen|Algemeen beschikbaar|[Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)|
|Metrische gegevens (klassiek)|Algemeen beschikbaar|[Azure Storage Analytics-metrische gegevens (klassiek)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrische gegevens in Azure Monitor|Algemeen beschikbaar|[Azure Storage metrics in Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Power shell-opdrachten voor Blob-opslag|Algemeen beschikbaar|[Quick Start: blobs uploaden, downloaden en vermelden met Power shell](storage-quickstart-blobs-powershell.md)|
|Azure CLI-opdrachten voor Blob Storage|Algemeen beschikbaar|[Quick Start: blobs maken, downloaden en vermelden met Azure CLI](storage-quickstart-blobs-cli.md)|
|BLOB storage-API 's|Algemeen beschikbaar|[Snelstartgids: Azure Blob Storage-client bibliotheek V12 voor .NET](storage-quickstart-blobs-dotnet.md)<br>[Quick Start: blobs beheren met Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Quick Start: blobs beheren met python V12 SDK](storage-quickstart-blobs-python.md)<br>[Quick Start: blobs beheren met Java script V12 SDK in node. js](storage-quickstart-blobs-nodejs.md)|
|Access-laag archiveren|Preview|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Levenscyclus beheer beleid|Preview|[De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)|
|Diagnostische logboeken|Preview|[Azure Storage Analytics-logboek registratie](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed wijzigen|Nog niet ondersteund|[Ondersteuning voor feed wijzigen in Azure Blob-opslag](storage-blob-change-feed.md)|
|Account-failover|Nog niet ondersteund|[Herstel na nood gevallen en failover van accounts](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB-container-ACL|Nog niet ondersteund|[Container-ACL instellen](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Aangepaste domeinen|Nog niet ondersteund|[Een aangepast domein toewijzen aan een Azure Blob Storage-eind punt](storage-custom-domain-name.md)|
|Onveranderbare opslag|Nog niet ondersteund|[Bedrijfs kritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)|
|Momentopnamen|Nog niet ondersteund|[Een BLOB-moment opname maken en beheren in .NET](storage-blob-snapshots.md)|
|Voorlopig verwijderen|Nog niet ondersteund|[Voorlopig verwijderen voor Azure Storage-blobs](storage-blob-soft-delete.md)|
|Statische websites|Nog niet ondersteund|[Een statische website hosten in Azure Storage](storage-blob-static-website.md)|
|Aanmelden Azure Monitor|Nog niet ondersteund|Nog niet beschikbaar|
|Premium blok-blobs|Nog niet ondersteund|[Een BlockBlobStorage-account maken](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Toegang tot meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)