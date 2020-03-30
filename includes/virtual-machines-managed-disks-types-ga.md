---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386086"
---
## <a name="premium-ssd"></a>Premium - SSD

Azure premium SSD's bieden krachtige en lage latentie schijfondersteuning voor virtuele machines (VM's) met input/output (IO)-intensieve workloads. Als u wilt profiteren van de snelheid en prestaties van premium opslagschijven, u bestaande VM-schijven migreren naar Premium SSD's. Premium SSD's zijn geschikt voor bedrijfskritische productietoepassingen. Premium SSD's kunnen alleen worden gebruikt met VM-series die compatibel zijn met premium opslag.

Zie [Windows VM-formaten](../articles/virtual-machines/windows/sizes.md)voor meer informatie over afzonderlijke VM-typen en -formaten in Azure voor Windows, inclusief welke formaten compatibel zijn met premium opslag. Zie [Linux VM-formaten](../articles/virtual-machines/linux/sizes.md)voor meer informatie over afzonderlijke VM-typen en -formaten in Azure voor Linux, inclusief welke formaten compatibel zijn met premium opslag. Uit een van deze artikelen moet u elk afzonderlijk artikel van vm-grootte controleren om te bepalen of het compatibel is met premium opslag.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Wanneer u een premium opslagschijf indient, in tegenstelling tot standaardopslag, bent u verzekerd van de capaciteit, IOPS en doorvoer van die schijf. Als u bijvoorbeeld een P50-schijf maakt, voorziet Azure in een opslagcapaciteit van 4095 GB, 7.500 IOPS en 250 MB/s-doorvoer voor die schijf. Uw toepassing kan de capaciteit en prestaties geheel of gedeeltelijk gebruiken. Premium SSD-schijven zijn ontworpen om lage enkelcijferige milliseconden te bieden en IOPS en doorvoer te targeten die in de vorige tabel 99,9% van de tijd zijn beschreven.

## <a name="bursting"></a>Barsten

Premium SSD-formaten kleiner dan P30 bieden nu schijfbarsten en kunnen hun IOPS per schijf tot 3.500 en hun bandbreedte tot 170 Mbps barsten. Bursting is geautomatiseerd en werkt op basis van een kredietsysteem. Credits worden automatisch verzameld in een burst bucket wanneer schijfverkeer lager is dan het ingerichte prestatiedoel en credits automatisch worden verbruikt wanneer het verkeer buiten het doel barst, tot de maximale burst-limiet. De maximale burst-limiet definieert het plafond van schijf IOPS & Bandbreedte, zelfs als je burst credits te consumeren van. Schijfbarsten zorgt voor een betere tolerantie voor onvoorspelbare veranderingen van IO-patronen. U het beste gebruiken voor OS schijf opstarten en toepassingen met stekek verkeer.    

Ondersteuning voor barstende schijven worden standaard ingeschakeld bij nieuwe implementaties van toepasselijke schijfformaten, zonder dat er actie van de gebruiker nodig is. Voor bestaande schijven van de toepasselijke grootte u het barsten met een van de twee opties inschakelen: de schijf loskoppelen en opnieuw bevestigen of de gekoppelde VM stoppen en opnieuw starten. Alle burst van toepassing schijfformaten zal beginnen met een volledige burst credit emmer wanneer de schijf is aangesloten op een virtuele machine die een maximale duur bij piek burst limiet van 30 minuten ondersteunt. Zie Premium SSD barsten voor meer informatie over hoe het werken op Azure Disks [werkt.](../articles/virtual-machines/linux/disk-bursting.md) 

### <a name="transactions"></a>Transacties

Voor premium SSD's wordt elke I/O-bewerking die minder dan of gelijk is aan 256 KiB-doorvoer beschouwd als één I/O-bewerking. I/O-bewerkingen groter dan 256 KiB-doorvoer worden beschouwd als meerdere I/O's van maat 256 KiB.

## <a name="standard-ssd"></a>Standard - SSD

Azure-standaard-SSD's zijn een kosteneffectieve opslagoptie die is geoptimaliseerd voor workloads die consistente prestaties op lagere IOPS-niveaus nodig hebben. Standaard SSD biedt een goede instapervaring voor diegenen die naar de cloud willen verhuizen, vooral als u problemen ondervindt met de variantie van workloads die op uw HDD-oplossingen op locatie worden uitgevoerd. In vergelijking met standaard HDD's bieden standaard SD's een betere beschikbaarheid, consistentie, betrouwbaarheid en latentie. Standaard SSD's zijn geschikt voor webservers, lage IOPS-toepassingsservers, licht gebruikte bedrijfstoepassingen en Dev/Test-workloads. Net als standaard HDD's zijn er standaard SD's beschikbaar op alle Azure VM's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standaard SSD's zijn ontworpen om enkelcijferige millisecondelaten en de IOPS en doorvoer tot de grenzen beschreven in de vorige tabel 99% van de tijd. De werkelijke IOPS en doorvoer kunnen soms variëren, afhankelijk van de verkeerspatronen. Standaard SSD's bieden consistentere prestaties dan de HDD-schijven met de lagere latentie.

### <a name="transactions"></a>Transacties

Voor standaard SSD's wordt elke I/O-bewerking die minder dan of gelijk is aan 256 KiB-doorvoer beschouwd als één I/O-bewerking. I/O-bewerkingen groter dan 256 KiB-doorvoer worden beschouwd als meerdere I/O's van maat 256 KiB. Deze transacties hebben een factureringseffect.

## <a name="standard-hdd"></a>Standard HDD

Azure-standaard HDD's bieden betrouwbare, goedkope schijfondersteuning voor VM's met latentie-ongevoelige workloads. Met standaardopslag worden de gegevens opgeslagen op harde schijven (HDD's). Latentie, IOPS en doorvoer van standaard HDD-schijven kunnen groter variëren in vergelijking met SSD-schijven. Standaard HDD-schijven zijn ontworpen om schrijflatencies te leveren onder 10ms en leeslatencies onder 20ms voor de meeste IO-bewerkingen, maar de werkelijke prestaties kunnen variëren afhankelijk van de IO-grootte en het werkbelastingspatroon. Wanneer u met VM's werkt, u standaard HDD-schijven gebruiken voor dev/testscenario's en minder kritieke workloads. Standaard HDD's zijn beschikbaar in alle Azure-regio's en kunnen worden gebruikt met alle Azure VM's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transacties

Voor standaard HDD's wordt elke IO-bewerking beschouwd als één transactie, ongeacht de I/O-grootte. Deze transacties hebben een factureringseffect.

## <a name="billing"></a>Billing

Bij het gebruik van beheerde schijven zijn de volgende factureringsoverwegingen van toepassing:

- Schijftype
- beheerde schijfgrootte
- Momentopnamen
- Uitgaande gegevensoverdrachten
- Aantal transacties

**Beheerde schijfgrootte:** beheerde schijven worden gefactureerd op de ingerichte grootte. Azure brengt de ingerichte grootte (naar boven afgerond) in kaart met de dichtstbijzijnde aangeboden schijfgrootte. Zie de vorige tabellen voor meer informatie over de aangeboden schijfformaten. Elke schijf wordt toegewezen aan een gesteund ingerichte schijfgrootteaanbieden en wordt dienovereenkomstig gefactureerd. Als u bijvoorbeeld een 200 GiB Standard SSD hebt ingericht, wordt deze toegewezen aan het schijfformaataanbod van E15 (256 GiB). Facturering voor elke ingerichte schijf wordt naar rato per uur uitgevoerd met behulp van de maandelijkse prijs voor de Premium Storage-aanbieding. Als u bijvoorbeeld een E10-schijf hebt ingericht en deze na 20 uur hebt verwijderd, wordt er een factuur in rekening gebracht voor het E10-aanbod naar rato van 20 uur. Dit is ongeacht de hoeveelheid werkelijke gegevens die naar de schijf zijn geschreven.

**Momentopnamen:** Momentopnamen worden gefactureerd op basis van de gebruikte grootte. Als u bijvoorbeeld een momentopname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GiB en de werkelijke gebruikte gegevensgrootte van 10 GiB, wordt de momentopname alleen gefactureerd voor de gebruikte gegevensgrootte van 10 GiB.
