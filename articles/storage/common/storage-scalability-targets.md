---
title: Azure Storage schaalbaarheids-en prestatie doelen-opslag accounts
description: Meer informatie over de schaalbaarheids-en prestatie doelen, inclusief capaciteit, aanvraag snelheid en binnenkomende en uitgaande band breedte, voor Azure Storage-accounts.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 293d0de024ad815b4755d48833b7058c29ebcd5a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513299"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure Storage schaalbaarheids-en prestatie doelen voor opslag accounts

In dit artikel vindt u meer informatie over de schaalbaarheids-en prestatie doelen voor Azure Storage-accounts. De schaal baarheid en prestatie doelen die hier worden weer gegeven, zijn hoogwaardige doelen, maar kunnen worden behaald. In alle gevallen is de aanvraag snelheid en band breedte die door uw opslag account wordt behaald, afhankelijk van de grootte van de opgeslagen objecten, de toegangs patronen die worden gebruikt en het type werk belasting dat door uw toepassing wordt uitgevoerd.

Zorg ervoor dat u uw service test om te bepalen of de prestaties voldoen aan uw vereisten. Vermijd zo mogelijk plotselinge pieken in de frequentie van verkeer en zorg ervoor dat het verkeer goed wordt gedistribueerd over partities.

Wanneer uw toepassing de limiet bereikt van wat een partitie voor uw workload kan verwerken, begint Azure Storage met het retour neren van fout code 503 (server bezet) of fout code 500 (time-out van bewerking). Als er 503 fouten optreden, kunt u de toepassing aanpassen om een exponentieel uitstel-beleid te gebruiken voor nieuwe pogingen. Met de exponentiële uitstel kan de belasting van de partitie worden verkleind en kunnen pieken in het verkeer naar die partitie worden vergemakkelijkt.

## <a name="storage-account-scale-limits"></a>Schaal limieten voor opslag accounts

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Schaal limieten voor Premium-prestatie opslag accounts

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Schaal limieten van opslag Resource provider

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Schaal doelen voor Azure Blob-opslag

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files schaal doelen

Zie [Azure files schaal baarheid en prestatie doelen](../files/storage-files-scale-targets.md)voor meer informatie over de schaal-en prestatie doelen voor Azure Files en Azure file sync.

> [!IMPORTANT]
> Limieten voor opslag accounts zijn van toepassing op alle shares. Het omhoog schalen naar het maximum voor opslag accounts wordt alleen behaald als er slechts één share per opslag account is.
>
> Standaard bestands shares groter dan 5 TiB hebben bepaalde beperkingen en regionale beperkingen.
> Voor een lijst met beperkingen, regionale informatie en instructies voor het inschakelen van deze grotere grootte van bestands shares, zie de sectie [onboarding naar grotere bestands shares (Standard-laag)](../files/storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) van de Azure files plannings handleiding.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Schaal doelen voor Premium-bestanden

Er zijn drie categorieën beperkingen waarmee u rekening moet houden voor Premium-bestanden: opslag accounts, shares en bestanden.

Bijvoorbeeld: een enkele share kan 100.000 IOPS behaalt en één bestand kan tot 5.000 IOPS worden geschaald. Als u bijvoorbeeld drie bestanden in één share hebt, is het maximum aantal IOPs dat u van deze share kunt verkrijgen 15.000.

#### <a name="premium-file-share-limits"></a>Limieten voor Premium-bestands shares

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync schaal doelen

Azure File Sync is ontworpen met het doel van oneindig gebruik, maar oneindig gebruik is niet altijd mogelijk. De volgende tabel geeft de grenzen van de test van micro soft aan en geeft ook aan welke doelen vaste limieten zijn:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Schaal doelen Azure Queue-opslag

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Schaal doelen voor Azure Table Storage

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Zie ook

- [Prijs informatie voor opslag](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-abonnements-en service limieten, Quota's en beperkingen](../../azure-subscription-service-limits.md)
- [Replicatie Azure Storage](../storage-redundancy.md)
- [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../storage-performance-checklist.md)
