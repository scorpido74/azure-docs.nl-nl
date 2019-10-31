---
title: Azure Block Blob Storage-prestatie lagen-Azure Storage
description: Prestatie lagen voor Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063263"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Prestatie lagen van Azure Block Blob Storage

Als ondernemingen prestaties gevoelige Cloud toepassingen implementeren, is het belang rijk dat u beschikt over opties voor rendabele gegevens opslag op verschillende prestatie niveaus.

Azure Block Blob Storage biedt twee verschillende prestatie lagen:

- Premium: geoptimaliseerd voor hoge transactie tarieven en een consistente opslag latentie van één cijfer
- Standaard: geoptimaliseerd voor hoge capaciteit en hoge door Voer

De volgende overwegingen zijn van toepassing op de verschillende prestatie lagen:

- Standaard prestaties zijn beschikbaar in alle [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Premium-prestaties zijn beschikbaar in [regio's selecteren](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Premium performance biedt geoptimaliseerde prijzen voor toepassingen met een hoge transactie snelheid om de [totale opslag kosten](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) voor deze werk belastingen te verlagen.
- Als u Premium-prestaties voor blok-blobs wilt ophalen, moet u het BlockBlobStorage-account type gebruiken.
- Standaard prestaties zijn beschikbaar met Algemeen v1-, Algemeen v2-en Blob Storage-accounts.
- Premium en standaard prestaties ondersteunen [blok-blobs met hoge door Voer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Blok-blobs met hoge door Voer zijn beschikbaar voor Premium-prestaties van meer dan 256 KiB. Blok-blobs met hoge door Voer zijn beschikbaar voor standaard prestaties in meer dan 4 MiB-put-blok of put-grootte van de blob.
- Premium-prestaties zijn momenteel alleen beschikbaar met lokaal redundante opslag (LRS).

## <a name="premium-performance"></a>Premium-prestaties

Met de Premium-opslag voor blok-blobs kunt u gegevens beschikbaar maken via high performance-hardware. Gegevens worden opgeslagen op Ssd's (Solid-state drives) die zijn geoptimaliseerd voor een lage latentie. Ssd's bieden een hogere door Voer vergeleken met traditionele harde schijven.

De Premium-Blob-opslag voor het blok keren van prestaties is ideaal voor werk belastingen waarvoor snelle en consistente reactie tijden zijn vereist. Het is het beste voor werk belastingen die veel kleine trans acties uitvoeren.

## <a name="standard-performance"></a>Standaard prestaties

Standaard prestaties bieden ondersteuning voor verschillende [toegangs lagen](storage-blob-storage-tiers.md) om gegevens op de voordeligste manier op te slaan. Het is geoptimaliseerd voor hoge capaciteit en hoge door Voer voor grote gegevens sets.

## <a name="blob-lifecycle-management"></a>Beheer van de BLOB levenscyclus

Blob Storage levenscyclus beheer biedt een uitgebreid beleid op basis van regels:

- Premium-verloopt gegevens aan het einde van de levens cyclus
- Standaard: overgangs gegevens naar de beste Access-laag en verlopen gegevens aan het einde van de levens cyclus

Zie [de Azure Blob-opslag levenscyclus beheren](storage-lifecycle-management-concepts.md)voor meer informatie.

Gegevens die zijn opgeslagen in een Premium Block Blob Storage-account kunnen niet worden verplaatst tussen de lagen hot, cool en Archive. U kunt echter blobs kopiëren van een blok-Blob-opslag account naar de laag Hot Access in een *ander* account. Gebruik de [put-blok versie van URL](/rest/api/storageservices/put-block-from-url) API of [AzCopy V10 toevoegen](../common/storage-use-azcopy-v10.md) om gegevens te kopiëren naar een ander account. **Met de put-blok kering van URL** -API worden gegevens synchroon gekopieerd op de server. De oproep wordt pas voltooid nadat alle gegevens zijn verplaatst van de oorspronkelijke server locatie naar de doel locatie.

## <a name="next-steps"></a>Volgende stappen

Evalueren van warme, koude en archief in GPv2-en Blob Storage-accounts

- [De beschikbaarheid controleren van de dynamische, statische en archieflaag per regio](https://azure.microsoft.com/regions/#services)
- [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
- [Meer informatie over reactiveren BLOB-gegevens uit de laag archief](storage-blob-rehydration.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](../common/storage-enable-and-view-metrics.md)
- [Prijzen voor de dynamische, statische en archieflaag controleren in Blob Storage- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
- [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
