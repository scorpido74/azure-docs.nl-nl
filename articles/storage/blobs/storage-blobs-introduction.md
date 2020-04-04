---
title: Inleiding tot Blob-opslag (object) - Azure Storage
description: Azure Blob-opslag is bedoeld voor het opslaan van grote hoeveelheden ongestructureerde objectgegevens, zoals tekst of binaire gegevens. Azure Blob-opslag is zeer schaalbaar en beschikbaar. Clients hebben toegang tot gegevensobjecten in Blob-opslag vanuit PowerShell of via Azure CLI, vanuit code via Azure Storage-clientbibliotheken of via REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f7d1967c8a9585fbf5131da986595761030f0e5f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632000"
---
# <a name="introduction-to-azure-blob-storage"></a>Inleiding tot Azure Blob-opslag

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Resources voor blobopslag

Er zijn drie typen resources voor blobopslag:

- Het **opslagaccount**
- Een **container** in het opslagaccount
- Een **blob** in een container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram met de relatie tussen een opslagaccount, containers en blobs](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Opslagaccounts

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het blob-eindpunt voor Azure Storage vormen het basisadres voor de objecten in uw opslagaccount.

Als de naam van uw opslagaccount bijvoorbeeld *mystorageaccount* is, is het standaardeindpunt voor de blobopslag:

```
http://mystorageaccount.blob.core.windows.net
```

Raadpleeg [Een opslagaccount maken](../common/storage-account-create.md) als u een opslagaccount wilt maken. Zie [Overzicht voor Azure-opslagaccounts](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over opslagaccounts.

### <a name="containers"></a>Containers

Een container kan een of meer blobs bevatten, net zoals een map een of meer bestanden kan bevatten in een bestandssysteem. Een opslagaccount kan een onbeperkt aantal containers bevatten en een container kan een onbeperkt aantal blobs bevatten.

> [!NOTE]
> De naam van een container mag alleen kleine letters bevatten. Zie [Naamgeving voor en verwijzingen naar containers, blobs en metagegevens](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) voor meer informatie over de naamgeving voor containers.

### <a name="blobs"></a>Blobs

Azure Storage ondersteunt drie soorten blobs:

- **Blok-blobs** worden gebruikt voor het opslaan van tekst en binaire gegevens, tot ongeveer 4,7 TB. Blok-blobs bestaan uit blokken met gegevens die afzonderlijk kunnen worden beheerd.
- **Toevoeg-blobs** bestaan uit blokken zoals blok-blobs, maar zijn geoptimaliseerd voor toevoegbewerkingen. Toevoeg-blobs zijn ideaal voor scenario's zoals het registreren van logboekgegevens van virtuele machines.
- **Pagina-blobs** worden gebruikt voor het opslaan van bestanden voor willekeurige toegang tot maximaal 8 TB in grootte. Paginablobs slaan VHD-bestanden (virtual hard drive) op en dienen als schijven voor virtuele Azure-machines. Zie [Overzicht van Azure-pagina-blobs](storage-blob-pageblob-overview.md) voor meer informatie over pagina-blobs

Zie [Blok-blobs, toevoeg-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) voor meer informatie over de verschillende soorten blobs.

## <a name="move-data-to-blob-storage"></a>Gegevens verplaatsen naar de blobopslag

Er zijn een aantal oplossingen waarmee u bestaande gegevens naar de blobopslag kunt migreren:

- **AzCopy** is een gebruiksvriendelijk opdrachtregelprogramma voor Windows en Linux waarmee u in containers of opslagaccounts gegevens kunt kopiëren naar en vanuit de blobopslag. Zie [Gegevensoverdracht met AzCopy v10 (preview-versie)](../common/storage-use-azcopy-v10.md) voor meer informatie over AzCopy.
- De **bibliotheek voor gegevensverplaatsing van Azure Storage** is een .NET-bibliotheek voor het verplaatsen van gegevens tussen Azure Storage-services. Het hulpprogramma AzCopy is samengesteld uit de bibliotheek voor gegevensverplaatsing. Zie de [naslagdocumentatie](/dotnet/api/microsoft.azure.storage.datamovement) voor de bibliotheek voor gegevensverplaatsing voor meer informatie.
- **Azure Data Factory** ondersteunt het kopiëren van gegevens van en naar Blob-opslag met behulp van de accountsleutel, een handtekening voor gedeelde toegang, een serviceprincipal of beheerde identiteiten voor Azure-bronnen. Zie [Gegevens naar en van Azure Blob Storage kopiëren met Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie.
- **Blobfuse** is een stuurprogramma voor het virtuele bestandssysteem voor Azure Blob Storage. U kunt Blobfuse gebruiken om via het Linux-bestandssysteem toegang te krijgen tot uw bestaande blok-blobgegevens in uw Azure Storage-account. Zie [Blob-opslag koppelen als een bestandssysteem met Blobfuse](storage-how-to-mount-container-linux.md) voor meer informatie.
- **Azure Data Box-service** is beschikbaar om on-premises gegevens over te zetten naar Blob-opslag wanneer grote gegevenssets of netwerkbeperkingen het uploaden van gegevens via de draad onrealistisch maken. Afhankelijk van de grootte van uw gegevens u [Azure Data Box Disk,](../../databox/data-box-disk-overview.md) [Azure Data Box](../../databox/data-box-overview.md)of Azure Data [Box Heavy-apparaten](../../databox/data-box-heavy-overview.md) van Microsoft aanvragen. U uw gegevens vervolgens naar die apparaten kopiëren en deze terugsturen naar Microsoft om te worden geüpload naar Blob-opslag.
- De **Azure Import/Export-service** biedt een manier om grote hoeveelheden gegevens te importeren of exporteren van en naar uw opslagaccount met behulp van harde schijven die u levert. Zie [De Microsoft Azure Import/Export-service gebruiken om gegevens over te zetten naar de blobopslag](../common/storage-import-export-service.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Schaalbaarheid en prestatiedoelen voor Blob-opslag](scalability-targets.md)
