---
title: Prestatielagen voor blobopslag blokkeren — Azure Storage
description: Bespreekt het verschil tussen premium- en standaardprestatielagen voor Azure block blob-opslag.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270227"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Prestatielagen voor blokblobopslag

Aangezien ondernemingen prestatiegevoelige cloud-native toepassingen implementeren, is het belangrijk om opties te hebben voor kosteneffectieve gegevensopslag op verschillende prestatieniveaus.

Azure block blob-opslag biedt twee verschillende prestatielagen:

- **Premium:** geoptimaliseerd voor hoge transactiesnelheden en consistente opslaglatentie met één cijfer
- **Standaard**: geoptimaliseerd voor hoge capaciteit en hoge doorvoer

De volgende overwegingen zijn van toepassing op de verschillende prestatieniveaus:

| Onderwerp |Standaardprestaties  |Premium prestaties  |
|---------|---------|---------|
|Beschikbaarheid in regio’s     |   Alle regio's      | In [bepaalde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Ondersteunde [opslagaccounttypen](../common/storage-account-overview.md#types-of-storage-accounts)     |     Algemene toepassing v2, BlobStorage, Algemeen doel v1    |    BlockBlobStorage     |
|Ondersteunt [blobs met hoge doorvoerblok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Ja, bij meer dan 4 MiB PutBlock- of PutBlob-formaten     |    Ja, bij meer dan 256 KiB PutBlock- of PutBlob-formaten    |
|Redundantie     |     Zie [Typen opslagaccounts](../common/storage-account-overview.md#types-of-storage-accounts)   |  Ondersteunt momenteel alleen lokaal redundante opslag (LRS) en zone-redudant opslag (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1.</sup> ZrS (Zoneredundant storage) is beschikbaar in bepaalde regio's voor premium performance block blob-opslagaccounts.</div>

Wat de kosten betreft, bieden premium prestaties geoptimaliseerde prijzen voor toepassingen met hoge transactietarieven om [de totale opslagkosten](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) voor deze workloads te verlagen.

## <a name="premium-performance"></a>Premium prestaties

Premium performance block blob-opslag maakt gegevens beschikbaar via krachtige hardware. Gegevens worden opgeslagen op solid-state drives (SSD's) die zijn geoptimaliseerd voor lage latentie. SSD's bieden een hogere doorvoer in vergelijking met traditionele harde schijven.

Premium performance storage is ideaal voor workloads die snelle en consistente responstijden vereisen. Het is het beste voor workloads die veel kleine transacties uitvoeren. Voorbeeldworkloads zijn:

- **Interactieve workloads**. Deze workloads vereisen onmiddellijke updates en feedback van gebruikers, zoals e-commerce en mapping-toepassingen. In een e-commercetoepassing worden bijvoorbeeld minder vaak bekeken items waarschijnlijk niet in de cache opgeslagen. Ze moeten echter direct op aanvraag aan de klant worden weergegeven.

- **Analytics**. In een IoT-scenario kunnen elke seconde veel kleinere schrijfbewerkingen naar de cloud worden gepusht. Grote hoeveelheden gegevens kunnen worden opgenomen, geaggregeerd voor analysedoeleinden en vervolgens vrijwel onmiddellijk worden verwijderd. De hoge opnamemogelijkheden van premium block blob-opslag maken het efficiënt voor dit type workload.

- **Kunstmatige intelligentie/machine learning (AI/ML)**. AI/ML houdt zich bezig met het verbruik en de verwerking van verschillende gegevenstypen zoals visuals, spraak en tekst. Dit krachtige computertype workload behandelt grote hoeveelheden gegevens die een snelle respons en efficiënte opnametijden voor gegevensanalyse vereisen.

- **Gegevenstransformatie**. Processen die voortdurend moeten worden bewerkt, gewijzigd en geconversie van gegevens vereisen onmiddellijke updates. Voor een nauwkeurige gegevensweergave moeten consumenten van deze gegevens deze wijzigingen onmiddellijk zien worden weergegeven.

## <a name="standard-performance"></a>Standaardprestaties

Standaardprestaties ondersteunen verschillende [toegangsniveaus](storage-blob-storage-tiers.md) om gegevens op de meest kosteneffectieve manier op te slaan. Het is geoptimaliseerd voor hoge capaciteit en hoge doorvoer op grote datasets.

- **Back-up- en noodherstelgegevenssets**. Standaardprestatieopslag biedt kostenefficiënte lagen, waardoor het een perfecte use case is voor zowel disaster recovery-gegevenssets op korte als lange termijn, secundaire back-ups en het archiveren van nalevingsgegevens.

- **Media-inhoud**. Afbeeldingen en video's worden vaak vaak geopend wanneer ze voor het eerst worden gemaakt en opgeslagen, maar dit inhoudstype wordt minder vaak gebruikt naarmate het ouder wordt. Standaard prestatieopslag biedt geschikte lagen voor media-inhoudsbehoeften. 

- **Verwerking van bulkgegevens**. Dit soort workloads zijn geschikt voor standaardopslag omdat ze kosteneffectieve opslag met hoge doorvoer vereisen in plaats van consistente lage latentie. Grote, ruwe datasets worden geënsceneerd voor verwerking en migreren uiteindelijk naar koelere lagen.

## <a name="migrate-from-standard-to-premium"></a>Migreren van standaard naar premium

U een bestaand standaardopslagaccount voor prestaties niet converteren naar een blokblobopslagaccount met premium prestaties. Als u wilt migreren naar een opslagaccount met premium prestaties, moet u een BlockBlobStorage-account maken en de gegevens migreren naar het nieuwe account. Zie [Een BlockBlobStorage-account maken](storage-blob-create-account-block-blob.md)voor meer informatie .

Als u blobs tussen opslagaccounts wilt kopiëren, [AzCopy](../common/storage-use-azcopy-blobs.md) u de nieuwste versie van het azcopy-opdrachtregelgereedschap gebruiken. Andere hulpprogramma's zoals Azure Data Factory zijn ook beschikbaar voor gegevensverplaatsing en -transformatie.

## <a name="blob-lifecycle-management"></a>Blob-levenscyclusbeheer

Het levenscyclusbeheer van blob-opslag biedt een uitgebreid, op regels gebaseerd beleid:

- **Premium:** Gegevens verlopen aan het einde van de levenscyclus.
- **Standaard:** Gegevens naar de beste toegangslaag overschakelen en gegevens aan het einde van de levenscyclus verlopen.

Zie [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)voor meer informatie.

U gegevens die zijn opgeslagen in een premium blokblobopslagaccount niet verplaatsen tussen hot-, cool- en archieflagen. U blobs echter kopiëren van een opslagaccount voor blokblobs naar de hot access-laag in een *ander* account. Als u gegevens wilt kopiëren naar een ander account, gebruikt u de [URL-API of](/rest/api/storageservices/put-block-from-url) [AzCopy v10.](../common/storage-use-azcopy-v10.md) De **API Voor het blokkeren van URL** kopieert gegevens op de server synchroon. Het gesprek wordt pas voltooid nadat alle gegevens van de oorspronkelijke serverlocatie naar de doellocatie zijn verplaatst.

## <a name="next-steps"></a>Volgende stappen

Evalueer hot, cool en archiveren in GPv2- en Blob-opslagaccounts.

- [Meer informatie over het opnieuw hydrateren van blobgegevens uit de archieflaag](storage-blob-rehydration.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](../common/storage-enable-and-view-metrics.md)
- [Controleer de prijzen voor hot, cool en archiveren in Blob-opslag- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
- [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
