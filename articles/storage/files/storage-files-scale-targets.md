---
title: Azure bestanden schaalbaarheids- en prestatiedoelen
description: Meer informatie over de schaalbaarheids- en prestatiedoelen voor Azure Files, met inbegrip van de capaciteit, snelheid van aanvragen en bandbreedtelimieten voor binnenkomende en uitgaande.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a74ec3610367193b5eee53ea0e0818901433e96
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598568"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure bestanden schaalbaarheids- en prestatiedoelen

[Azure files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het industrie standaard SMB-protocol. Dit artikel worden de schaalbaarheids- en prestatiedoelen voor Azure Files en Azure File Sync.

De schaalbaarheids- en prestatiedoelen die hier worden vermeld geavanceerde doelen zijn, maar ook worden beïnvloed door andere variabelen in uw implementatie. Bijvoorbeeld, kan de doorvoer voor een bestand ook worden beperkt door de beschikbare netwerkbandbreedte, niet alleen de servers die als host fungeert voor de Azure Files-service. Het is raadzaam testen van uw gebruikspatroon om te bepalen of de schaalbaarheid en prestaties van Azure Files voldoen aan uw vereisten. We zijn ook toegewezen aan het verhogen van deze limieten na verloop van tijd. Aarzel niet om ons feedback te geven, hetzij in de opmerkingen hieronder of op het [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), over welke limieten u zou willen zien.

## <a name="azure-storage-account-scale-targets"></a>Schaal prestatiedoelen voor Azure storage-account

De bovenliggende resource voor een Azure-bestandsshare is een Azure storage-account. Een storage-account vertegenwoordigt een opslaggroep in Azure die kan worden gebruikt door meerdere opslagservices, met inbegrip van Azure Files, voor het opslaan van gegevens. Andere services die gegevens opslaan in storage-accounts zijn Azure Blob storage, Azure Queue storage en Azure-tabelopslag. De volgende doelen gelden voor alle opslagservices opslaan van gegevens in een storage-account:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Het gebruik van het opslag account voor algemene doel einden van andere opslag Services is van invloed op uw Azure-bestands shares in uw opslag account. Bijvoorbeeld, als de maximale opslagaccountcapaciteit met Azure Blob-opslag is bereikt, kunt u zich niet kunnen maken van nieuwe bestanden op uw Azure-bestandsshare, zelfs als uw Azure-bestandsshare dan de grootte van de maximale bestandsshare is.

## <a name="azure-files-scale-targets"></a>Azure bestanden schaal doelen

Er zijn drie categorieën beperkingen waarmee u rekening moet houden voor Azure Files: opslag accounts, shares en bestanden.

Bijvoorbeeld: met Premium-bestands shares kan een enkele share 100.000 IOPS behaalt en kan één bestand tot 5.000 IOPS worden geschaald. Als u dus drie bestanden in één share hebt, is het maximale aantal IOPS dat u van deze share kunt verkrijgen 15.000.

### <a name="standard-storage-account-limits"></a>Limieten voor standaard opslag accounts

Zie de sectie [bereik schaal doelen van Azure Storage](#azure-storage-account-scale-targets) voor deze limieten.

### <a name="premium-filestorage-account-limits"></a>Premium FileStorage-account limieten

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limieten voor opslag accounts zijn van toepassing op alle shares. Het omhoog schalen naar het maximum voor FileStorage-accounts wordt alleen behaald als er slechts één share per FileStorage-account is.

### <a name="file-share-and-file-scale-targets"></a>Bestands shares en bestands schaal doelen

> [!NOTE]
> Standaard bestands shares groter dan 5 TiB hebben bepaalde beperkingen en regionale beperkingen.
> Zie de sectie [onboarding to large shares](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) in de plannings handleiding voor een lijst met beperkingen, regionale informatie en instructies voor het inschakelen van deze grotere grootte van de bestands share.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync schaal doelen

Azure File Sync is ontworpen met het doel van oneindig gebruik, maar oneindig gebruik is niet altijd mogelijk. De volgende tabel geeft de grenzen van de test van micro soft aan en geeft ook aan welke doelen vaste limieten zijn:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync-maatstaven voor prestaties

Omdat de Azure File Sync-agent wordt uitgevoerd op een Windows Server-computer die verbinding met de Azure-bestandsshares maakt, de prestaties van de effectieve synchronisatie is afhankelijk van een aantal factoren in uw infrastructuur: Windows Server en de configuratie van de onderliggende schijven, netwerkbandbreedte tussen de server en de Azure-opslag, grootte, de grootte van de totale gegevensset en de activiteit op de gegevensset. Omdat Azure File Sync werkt op bestandsniveau, wordt de prestatiekenmerken van een oplossing op basis van Azure File Sync beter gemeten in het aantal objecten (bestanden en mappen) dat per seconde wordt verwerkt.

Voor Azure File Sync is prestaties van essentieel belang is in twee fasen:

1. **Eerste**eenmalige inrichting: als u de prestaties van de eerste inrichting wilt optimaliseren, raadpleegt u de voor bereiding op [Azure file sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) voor de optimale implementatie details.
2. **Voortdurende synchronisatie**: nadat de gegevens in eerste instantie zijn geseed in de Azure-bestands shares, Azure File Sync meerdere eind punten in de synchronisatie bewaard.

Voor hulp bij het plannen van de implementatie voor elk van de fasen, worden hieronder de resultaten waargenomen tijdens het interne testen op een systeem met een configuratie

| Systeemconfiguratie |  |
|-|-|
| CPU | 64 virtuele kernen met 64 MiB L3-cache |
| Geheugen | 128 GiB |
| Schijf | SAS-schijven met RAID 10 met accu back cache |
| Netwerk | 1 Gbps-netwerk |
| Workload | Bestandsserver voor algemeen gebruik|

| Eerste eenmalige inrichting  |  |
|-|-|
| Aantal objecten | 25.000.000-objecten |
| Grootte van de gegevensset| ~ 4,7 TiB |
| Gemiddelde grootte | ~ 200 KiB (grootste bestand: 100 GiB) |
| Uploaden van doorvoer | 20 objecten per seconde per synchronisatie groep |
| Namespace downloaden doorvoer * | 400 objecten per seconde |

\* Wanneer een nieuw servereindpunt wordt gemaakt, kan de Azure File Sync-agent een van de bestandsinhoud wordt niet gedownload. Het de volledige naamruimte voor het eerst synchroniseert en vervolgens triggers op de achtergrond intrekken om de bestanden in hun geheel te downloaden of, indien het cloud-opslaglagen is ingeschakeld, naar de cloud beleidsinstelling voor lagen ingesteld op het servereindpunt.

| Doorlopende synchronisatie  |   |
|-|--|
| Aantal objecten die zijn gesynchroniseerd| 125,000 objecten (~ 1% verloop) |
| Grootte van de gegevensset| 50 giB |
| Gemiddelde grootte | ~ 500 KiB |
| Uploaden van doorvoer | 20 objecten per seconde per synchronisatie groep |
| Volledige Download doorvoer * | 60 objecten per seconde |

\* Als cloud tiering is ingeschakeld, kunt u waarschijnlijk betere prestaties als slechts een deel van het bestand gegevens gedownload in acht nemen. De gegevens van de bestanden in de cache downloadt met Azure File Sync alleen wanneer ze worden gewijzigd op een van de eindpunten. Voor gelaagde of de zojuist gemaakte bestanden, de agent niet de gegevens uit een bestand wordt gedownload en synchroniseert de naamruimte op alle servereindpunten in plaats daarvan alleen. De agent biedt ook ondersteuning voor gedeeltelijke downloads van gelaagde bestanden, zoals ze worden gebruikt door de gebruiker. 

> [!Note]  
> De bovenstaande getallen zijn niet een indicatie van de prestaties die u ondervindt. De werkelijke prestaties wordt afhankelijk zijn van meerdere factoren, zoals wordt beschreven in het begin van deze sectie.

Als algemene richtlijn voor uw implementatie, moet u een aantal dingen waarmee u rekening moet houden:

- De doorvoer van het object is ongeveer wordt geschaald naar gelang het aantal synchronisatiegroepen op de server. Gegevens splitsen in meerdere synchronisatiegroepen op een server resulteert in betere doorvoer, wat ook wordt beperkt door de server en het netwerk.
- De doorvoer van het object is omgekeerd evenredig aan de MiB per seconde doorvoer. Voor kleinere bestanden worden er hogere doorvoer in termen van het aantal objecten dat per seconde, maar lagere MiB per seconde doorvoer wordt verwerkt. Daarentegen voor grotere bestanden krijgt u minder objecten dat per seconde, maar hoger MiB per seconde doorvoer wordt verwerkt. De MiB per seconde doorvoer wordt beperkt door de doelen van de schaal Azure Files.

## <a name="see-also"></a>Zie ook

- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
