---
title: Blob-opslagfuncties beschikbaar in Azure Data Lake Storage Gen2 | Microsoft Documenten
description: Meer informatie over welke Blob-opslagfuncties u gebruiken met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196005"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob-opslagfuncties beschikbaar in Azure Data Lake Storage Gen2

Blob Storage-functies zoals [diagnostische logboekregistratie,](../common/storage-analytics-logging.md) [toegangslagen](storage-blob-storage-tiers.md)en [blobopslagbeheerbeleid voor levenscycluswerken](storage-lifecycle-management-concepts.md) werken nu met accounts met een hiërarchische naamruimte. Daarom u hiërarchische naamruimten op uw Blob-opslagaccounts inschakelen zonder de toegang tot deze functies te verliezen.

In deze tabel worden de blob-opslagfuncties weergegeven die u gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weergegeven, veranderen na verloop van tijd naarmate de ondersteuning wordt uitgebreid.

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob-opslagfuncties

> [!NOTE]
> Ondersteuningsniveau verwijst alleen naar de manier waarop de functie wordt ondersteund met Data Lake Storage Gen2.

|Blob-opslagfunctie |Ondersteuningsniveau |Verwante artikelen: |
|---------------|-------------------|---|
|Hot Storage-toegangslaag|Algemeen verkrijgbaar|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Cool Storage-toegangslaag|Algemeen verkrijgbaar|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Gebeurtenissen|Algemeen verkrijgbaar|[Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)|
|Statistieken (klassiek)|Algemeen verkrijgbaar|[Azure Storage Analytics-statistieken (Klassiek)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrische gegevens in Azure Monitor|Algemeen verkrijgbaar|[Metrische gegevens van Azure Storage in Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell-opdrachten blobopslag|Algemeen verkrijgbaar|[Snelstart: blobs uploaden, downloaden en aanbieden met PowerShell](storage-quickstart-blobs-powershell.md)|
|Azure CLI-opdrachten voor Blob-opslag|Algemeen verkrijgbaar|[Snelstart: blobs maken, downloaden en weergeven met Azure CLI](storage-quickstart-blobs-cli.md)|
|Blob-opslag-API's|Algemeen verkrijgbaar|[Snelstart: Azure Blob-opslagclientbibliotheek v12 voor .NET](storage-quickstart-blobs-dotnet.md)<br>[Snelstart: blobs beheren met Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Snelstart: blobs beheren met Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Snelstart: blobs beheren met JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Laag voor archieftoegang|Preview|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Beleid voor levenscyclusbeheer|Preview|[De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)|
|Diagnostische logboeken|Preview|[Logboekregistratie van Azure Opslaganalyse](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed wijzigen|Nog niet ondersteund|[Feedondersteuning wijzigen in Azure Blob-opslag](storage-blob-change-feed.md)|
|Accountfailover|Nog niet ondersteund|[Disaster recovery en account failover](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blobcontainer ACL|Nog niet ondersteund|[Container ACL instellen](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Aangepaste domeinen|Nog niet ondersteund|[Een aangepast domein toewijzen aan een Azure Blob-opslageindpunt](storage-custom-domain-name.md)|
|Onveranderlijke opslag|Nog niet ondersteund|[Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)|
|Momentopnamen|Nog niet ondersteund|[Een blobmomentopname maken en beheren in .NET](storage-blob-snapshots.md)|
|Voorlopig verwijderen|Nog niet ondersteund|[Voorlopig verwijderen voor Azure Storage-blobs](storage-blob-soft-delete.md)|
|Statische websites|Nog niet ondersteund|[Een statische website hosten in Azure Storage](storage-blob-static-website.md)|
|Logboekregistratie in Azure-monitor|Nog niet ondersteund|Nog niet beschikbaar|
|Premium blokblobs|Nog niet ondersteund|[Een BlockBlobStorage-account maken](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-services die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md)
- [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)