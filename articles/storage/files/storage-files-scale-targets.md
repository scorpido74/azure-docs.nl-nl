---
title: Schaalbaarheids- en prestatiedoelen in Azure Files
description: Meer informatie over de schaalbaarheids- en prestatiedoelen voor Azure-bestanden, waaronder de capaciteit, de aanvraagsnelheid en de inkomende en uitgaande bandbreedtelimieten.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a74ec3610367193b5eee53ea0e0818901433e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255130"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Schaalbaarheids- en prestatiedoelen in Azure Files

[Azure Files](storage-files-introduction.md) biedt volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het industriestandaard SMB-protocol. In dit artikel worden de schaalbaarheids- en prestatiedoelen voor Azure Files en Azure File Sync besproken.

De schaalbaarheids- en prestatiedoelen die hier worden vermeld, zijn high-end doelen, maar kunnen worden beïnvloed door andere variabelen in uw implementatie. De doorvoer voor een bestand kan bijvoorbeeld ook worden beperkt door uw beschikbare netwerkbandbreedte, niet alleen de servers die de Azure Files-service hosten. We raden u ten zeerste aan uw gebruikspatroon te testen om te bepalen of de schaalbaarheid en prestaties van Azure-bestanden aan uw vereisten voldoen. We zijn ook vastbesloten om deze limieten in de loop van de tijd te verhogen. Aarzel niet om ons feedback te geven, hetzij in de reacties hieronder of op de [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), over welke limieten u ons wilt zien verhogen.

## <a name="azure-storage-account-scale-targets"></a>Azure-opslagaccountschaaldoelen

De bovenliggende bron voor een Azure-bestandsshare is een Azure-opslagaccount. Een opslagaccount vertegenwoordigt een opslaggroep in Azure die kan worden gebruikt door meerdere opslagservices, waaronder Azure Files, om gegevens op te slaan. Andere services die gegevens opslaan in opslagaccounts zijn Azure Blob-opslag, Azure Queue-opslag en Azure Table-opslag. De volgende doelen passen alle opslagservices toe die gegevens opslaan in een opslagaccount:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Het gebruik van opslagaccount voor algemene doeleinden van andere opslagservices is van invloed op uw Azure-bestandsshares in uw opslagaccount. Als u bijvoorbeeld de maximale opslagcapaciteit van het opslagaccount bereikt met Azure Blob-opslag, u geen nieuwe bestanden maken op uw Azure-bestandsshare, zelfs niet als uw Azure-bestandsshare lager is dan de maximale grootte van het aandeel.

## <a name="azure-files-scale-targets"></a>Azure Files schaaldoelen

Er zijn drie categorieën beperkingen die u moet overwegen voor Azure Files: opslagaccounts, shares en bestanden.

Bijvoorbeeld: met premium bestandsshares kan één aandeel 100.000 IOPS bereiken en kan één bestand worden opgeschaald tot 5.000 IOPS. Dus, als je drie bestanden in één aandeel hebt, is de maximale IOPS die je krijgen van dat aandeel 15.000.

### <a name="standard-storage-account-limits"></a>Standaardopslagaccountlimieten

Zie de sectie [Azure-opslagaccountschaaldoelen](#azure-storage-account-scale-targets) voor deze limieten.

### <a name="premium-filestorage-account-limits"></a>Premium FileStorage-accountlimieten

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Opslagaccountlimieten zijn van toepassing op alle aandelen. Opschalen naar het maximum voor FileStorage-accounts is alleen haalbaar als er slechts één aandeel per FileStorage-account is.

### <a name="file-share-and-file-scale-targets"></a>Doelen voor bestandsshare- en bestandsschaal

> [!NOTE]
> Standaard bestandsaandelen groter dan 5 TiB hebben bepaalde beperkingen en regionale beperkingen.
> Zie het gedeelte [Onboard to grotere bestandsshares](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) van de planningshandleiding voor een lijst met beperkingen, regionale informatie en instructies om deze grotere bestandsgroottes in te schakelen.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync-schaaldoelen

Azure File Sync is ontworpen met als doel onbeperkt gebruik, maar onbeperkt gebruik is niet altijd mogelijk. In de volgende tabel worden de grenzen van het testen van Microsoft aangegeven en wordt ook aangegeven welke doelen harde limieten zijn:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metrische Azure File Sync-gegevens voor prestaties

Aangezien de Azure File Sync-agent wordt uitgevoerd op een Windows Server-machine die verbinding maakt met de Azure-bestandsshares, zijn de effectieve synchronisatieprestaties afhankelijk van een aantal factoren in uw infrastructuur: Windows Server en de onderliggende schijfconfiguratie, netwerkbandbreedte tussen de server en de Azure-opslag, bestandsgrootte, totale gegevenssetgrootte en de activiteit op de gegevensset. Aangezien Azure File Sync werkt op bestandsniveau, worden de prestatiekenmerken van een azure file sync-gebaseerde oplossing beter gemeten in het aantal objecten (bestanden en mappen) dat per seconde wordt verwerkt.

Voor Azure File Sync zijn de prestaties in twee fasen van cruciaal belang:

1. **Initiële eenmalige inrichting:** Raadpleeg [Onboarding met Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) voor de optimale implementatiegegevens om de prestaties bij de eerste inrichting te optimaliseren.
2. **Doorlopende synchronisatie**: Nadat de gegevens in eerste instantie zijn ingezaaid in de Azure-bestandsshares, houdt Azure File Sync meerdere eindpunten gesynchroniseerd.

Om u te helpen bij het plannen van uw implementatie voor elk van de fasen, hieronder zijn de resultaten waargenomen tijdens de interne tests op een systeem met een config

| Systeemconfiguratie |  |
|-|-|
| CPU | 64 virtuele kernen met 64 MiB L3-cache |
| Geheugen | 128 GiB |
| Schijf | SAS-schijven met RAID 10 met cache met batterij |
| Netwerk | 1 Gbps Netwerk |
| Workload | Bestandsserver voor algemeen gebruik|

| Initiële eenmalige inrichting  |  |
|-|-|
| Aantal objecten | 25 miljoen objecten |
| Gegevenssetgrootte| ~4,7 TiB |
| Gemiddelde bestandsgrootte | ~200 KiB (grootste bestand: 100 GiB) |
| Doorvoer uploaden | 20 objecten per seconde per synchronisatiegroep |
| Downloaddoorvoer van naamruimte* | 400 objecten per seconde |

*Wanneer een nieuw servereindpunt wordt gemaakt, downloadt de Azure File Sync-agent geen van de bestandsinhoud. Het synchroniseert eerst de volledige naamruimte en activeert vervolgens achtergrondherinnering om de bestanden te downloaden, hetzij in hun geheel, hetzij, als cloudtiering is ingeschakeld, naar het beleid voor cloudtiering dat is ingesteld op het servereindpunt.

| Doorlopende synchronisatie  |   |
|-|--|
| Aantal gesynchroniseerde objecten| 125.000 objecten (~1% churn) |
| Gegevenssetgrootte| 50 GiB |
| Gemiddelde bestandsgrootte | ~500 KiB |
| Doorvoer uploaden | 20 objecten per seconde per synchronisatiegroep |
| Volledige downloaddoorvoer* | 60 objecten per seconde |

*Als cloudtiering is ingeschakeld, zult u waarschijnlijk betere prestaties waarnemen omdat slechts een deel van de bestandsgegevens wordt gedownload. Azure File Sync downloadt alleen de gegevens van bestanden in de cache wanneer deze worden gewijzigd op een van de eindpunten. Voor gelaagde of nieuw gemaakte bestanden downloadt de agent de bestandsgegevens niet en synchroniseert hij alleen de naamruimte met alle servereindpunten. De agent ondersteunt ook gedeeltelijke downloads van gelaagde bestanden als ze worden geopend door de gebruiker. 

> [!Note]  
> De bovenstaande cijfers zijn geen indicatie van de prestaties die u zult ervaren. De werkelijke prestaties zijn afhankelijk van meerdere factoren, zoals beschreven in het begin van deze sectie.

Als algemene handleiding voor uw implementatie moet u een paar dingen in gedachten houden:

- De objectdoorvoer wordt ongeveer geschaald in verhouding tot het aantal synchronisatiegroepen op de server. Het splitsen van gegevens in meerdere synchronisatiegroepen op een server levert een betere doorvoer op, wat ook wordt beperkt door de server en het netwerk.
- De objectdoorvoer is omgekeerd evenredig met de MiB per seconde doorvoer. Voor kleinere bestanden ervaart u een hogere doorvoer in termen van het aantal objecten dat per seconde wordt verwerkt, maar een lagere MiB per seconde doorvoer. Omgekeerd, voor grotere bestanden, krijg je minder objecten verwerkt per seconde, maar hogere MiB per seconde doorvoer. De mib per seconde doorvoer wordt beperkt door de Azure Files schaal doelen.

## <a name="see-also"></a>Zie ook

- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
