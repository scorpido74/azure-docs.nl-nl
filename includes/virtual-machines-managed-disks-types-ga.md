---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e145cf481dd165144b188e6205e4b78cc61359fd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202529"
---
## <a name="premium-ssd"></a>Premium - SSD

Azure Premium Ssd's bieden ondersteuning voor hoge prestaties en lage latentie voor virtuele machines (Vm's) met invoer/uitvoer-intensieve workloads. Als u gebruik wilt maken van de snelheid en prestaties van Premium-opslag schijven, kunt u bestaande VM-schijven migreren naar Premium-Ssd's. Premium-Ssd's zijn geschikt voor essentiële productie toepassingen. Premium-Ssd's kan alleen worden gebruikt in combi natie met een VM-reeks die compatibel is met Premium Storage.

Zie [Windows VM-grootten](../articles/virtual-machines/windows/sizes.md)voor meer informatie over de verschillende VM-typen en-grootten in azure voor Windows, waaronder de grootte die compatibel is met Premium Storage. Zie [Linux VM-grootten](../articles/virtual-machines/linux/sizes.md)voor meer informatie over de verschillende VM-typen en-grootten in azure voor Linux, waaronder de grootte van Premium Storage-compatibel. Vanuit een van deze artikelen moet u elk artikel van de afzonderlijke VM-grootte controleren om te bepalen of het een Premium-opslag compatibel is.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Wanneer u een Premium-opslag schijf inricht, in tegens telling tot standaard opslag, bent u verzekerd van de capaciteit, IOPS en door Voer van de schijf. Als u bijvoorbeeld een P50-schijf maakt, wordt de opslag capaciteit van 4.095 GB door Azure ingericht, 7.500 IOPS en een door Voer van 250 MB/s voor die schijf. Uw toepassing kan alle of een deel van de capaciteit en prestaties gebruiken. Premium-SSD schijven zijn zodanig ontworpen dat er in de voor gaande tabel 99,9% van de tijd minder dan één milliseconde latentie en doel-IOPS en-door voer worden beschreven.

## <a name="bursting-preview"></a>Bursting (preview-versie)

Premium-SSD grootten die kleiner zijn dan P30 bieden nu schijf bursting (preview) en kunnen de IOPS per schijf tot 3.500 en de band breedte van Maxi maal 170 Mbps. Bursting wordt geautomatiseerd en werkt op basis van een tegoed systeem. Tegoeden worden automatisch opgeteld in een burst-Bucket wanneer het schijf verkeer lager is dan het ingerichte prestatie doel en de tegoeden automatisch worden gebruikt wanneer het verkeer groter wordt dan het doel, tot de maximale burst-limiet. De maximale burst-limiet bepaalt het maximum van schijf-IOPS & band breedte, zelfs als u burst-tegoeden hebt om te gebruiken. Schijf bursting biedt betere tolerantie voor onvoorspelbare wijzigingen van i/o-patronen. U kunt dit het beste gebruiken voor het opstarten van de besturingssysteem schijf en toepassingen met piekige-verkeer.    

Schijven die ondersteuning bieden voor bursting, worden standaard ingeschakeld voor nieuwe implementaties van toepasselijke schijf grootten in de [voorbeeld regio's](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) , zonder dat de gebruiker actie hoeft te ondernemen. Voor bestaande schijven van de toepasselijke grootten kunt u bursting met een van de volgende twee opties inschakelen: Ontkoppel en koppel de schijf opnieuw of stop de gekoppelde VM. Alle burst-toepasselijke schijf grootten beginnen met een volledige burst-credit Bucket wanneer de schijf is gekoppeld aan een virtuele machine die een maximale duur van Maxi maal 30 minuten ondersteunt. Zie [Premium-SSD bursting](../articles/virtual-machines/linux/disk-bursting.md)voor meer informatie over hoe bursting werkt op Azure-schijven. 

### <a name="transactions"></a>Transacties

Voor Premium-Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB.

## <a name="standard-ssd"></a>Standard - SSD

Azure Standard Ssd's is een voordelige opslag optie die is geoptimaliseerd voor workloads die consistente prestaties op lagere IOPS-niveaus nodig hebben. Standard-SSD biedt een goede ervaring op instap niveau voor degenen die willen overstappen op de Cloud, met name als u problemen ondervindt met de variantie van werk belastingen die op uw HDD-oplossingen op locatie worden uitgevoerd. In vergelijking met de standaard-Hdd's bieden de standaard-Ssd's betere Beschik baarheid, consistentie, betrouw baarheid en latentie. Standaard Ssd's zijn geschikt voor webservers, lage IOPS-toepassings servers, intensief gebruikte zakelijke toepassingen en werk belastingen voor ontwikkelen en testen. Net als standaard Hdd's zijn standaard Ssd's beschikbaar op alle Azure-Vm's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standaard Ssd's zijn ontworpen om latentie van één cijfer en de IOPS en door voer te bieden tot de limieten die zijn beschreven in de voor gaande tabel 99% van de tijd. De werkelijke IOPS en door Voer kunnen soms variëren, afhankelijk van de verkeers patronen. Standaard Ssd's bieden consistente prestaties dan de harde schijven met een lagere latentie.

### <a name="transactions"></a>Transacties

Voor standaard Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB. Deze trans acties hebben een facturerings impact.

## <a name="standard-hdd"></a>Standard - HDD

Azure Standard Hdd's levert betrouw bare, voordelige schijf ondersteuning voor virtuele machines met latentie-ongevoelig werk belastingen. Bij standaard opslag worden de gegevens opgeslagen op de harde schijven (Hdd's). Latentie, IOPS en door Voer van Standard-HDD schijven kunnen veel meer verschillen in vergelijking met schijven op basis van SSD. Standard-HDD schijven zijn ontworpen voor het leveren van schrijf latentie onder 10 MS en lees latentie onder 20ms voor de meeste i/o-bewerkingen, maar de werkelijke prestaties kunnen echter variëren, afhankelijk van de i/o-grootte en het werkbelasting patroon. Wanneer u werkt met Vm's, kunt u standaard HDD-schijven gebruiken voor dev/test-scenario's en minder kritieke workloads. Standaard Hdd's zijn beschikbaar in alle Azure-regio's en kunnen worden gebruikt met alle Azure-Vm's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transacties

Voor standaard Hdd's wordt elke IO-bewerking beschouwd als één trans actie, ongeacht de I/O-grootte. Deze trans acties hebben een facturerings impact.

## <a name="billing"></a>Facturering

Bij het gebruik van beheerde schijven gelden de volgende facturerings overwegingen:

- Schijftype
- Grootte van beheerde schijf
- Momentopnamen
- Uitgaande gegevens overdracht
- Aantal transacties

**Beheerde schijf grootte**: beheerde schijven worden gefactureerd op de ingerichte grootte. Azure wijst de ingerichte grootte (afgerond) toe aan de dichtstbijzijnde schijf grootte. Zie de vorige tabellen voor meer informatie over de geboden schijf groottes. Elke schijf wordt toegewezen aan een ondersteunde aanbieding van de ingerichte schijf en wordt dienovereenkomstig gefactureerd. Als u bijvoorbeeld een 200 GiB-Standard-SSD hebt ingericht, wordt deze toegewezen aan de aanbieding voor de schijf grootte van E15 (256 GiB). Facturering voor een ingerichte schijf wordt per uur naar beneden gefactureerd met behulp van de maandelijkse prijs voor de Premium Storage aanbieding. Als u bijvoorbeeld een E10-schijf hebt ingericht en deze na 20 uur hebt verwijderd, wordt u gefactureerd voor het E10-aanbod dat Maxi maal 20 uur in rekening wordt gebracht. Dit is ongeacht de hoeveelheid gegevens die naar de schijf wordt geschreven.

**Moment opnamen**: moment opnamen worden gefactureerd op basis van de gebruikte grootte. Als u bijvoorbeeld een moment opname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GiB en de daad werkelijke gebruikte gegevens grootte van 10 GiB, wordt de moment opname alleen gefactureerd voor de gebruikte gegevens grootte van 10 GiB.
