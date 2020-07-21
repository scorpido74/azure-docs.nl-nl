---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dbca8798af83108ca5e8bdfe5918a25423fd934d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544316"
---
Azure Managed disks biedt momenteel vier schijf typen, elk type is gericht op specifieke klant scenario's.

## <a name="disk-comparison"></a>Vergelijking van schijven

De volgende tabel bevat een vergelijking van ultra disks, Premium-schijven (Solid-state drives), standaard SSD en standaard harde schijven (HDD) voor beheerde schijven om u te helpen bepalen wat u wilt gebruiken.

| Detail | Ultraschijven | Premium SSD | Standard SSD | Standard HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Schijftype   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensieve workloads, zoals [SAP Hana](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), data bases in de bovenste laag (bijvoorbeeld SQL, Oracle) en andere trans acties met een zware werk belasting.   |Productie- en prestatiegevoelige workloads   |Webservers, bedrijfstoepassingen die weinig wordt gebruikt en dev/test   |Back-up, niet-kritiek, incidentele toegang   |
|Maximale schijfgrootte   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Maximale doorvoer   |2.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Max. IOPS   |160.000    |20.000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Ultraschijven

Azure-ultraschijven leveren een hoge doorvoer, hoge IOPS en een consistente schijfopslag met lage latentie voor Azure IaaS-VM's. Enkele extra voor delen van ultra schijven zijn de mogelijkheid om de prestaties van de schijf en uw workloads dynamisch te wijzigen zonder dat u uw virtuele machines (VM) opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties. Ultraschijven kunnen uitsluitend als gegevensschijven worden gebruikt. U wordt aangeraden Premium SSD-schijven als besturingssysteemschijven te gebruiken.

### <a name="performance"></a>Prestaties

Wanneer u een ultra schijf inricht, kunt u onafhankelijk de capaciteit en de prestaties van de schijf configureren. Ultra disks zijn beschikbaar in verschillende vaste formaten, variërend van 4 GiB tot 64 TiB en bieden een flexibel prestatie configuratie model waarmee u IOPS en door Voer onafhankelijk kunt configureren.

Enkele belang rijke mogelijkheden van ultra disk zijn:

- Schijf capaciteit: schijven met een capaciteit van Maxi maal 4 GiB tot 64 TiB.
- Schijf-IOPS: Ultra schijven ondersteunen IOPS-limieten van 300 IOPS/GiB, Maxi maal 160 K IOPS per schijf. Zorg ervoor dat de geselecteerde schijf-IOPS kleiner is dan de limiet van de VM-IOPS om de IOPS die u hebt ingericht, te bewerkstelligen. De minimale gegarandeerde IOPS per schijf is 2 IOPS/GiB, met een totaal van de basis lijn mini maal 100 IOPS. Als u bijvoorbeeld een ultra schijf van 4 GiB hebt, hebt u Mini maal 100 IOPS, in plaats van acht IOPS.
- Schijf doorvoer: met ultra disks is de doorvoer limiet van één schijf 256 KiB/s voor elke ingerichte IOPS, tot een maximum van 2000 MBps per schijf (waarbij MBps = 10 ^ 6 bytes per seconde). De minimale gegarandeerde door Voer per schijf is 4KiB/s voor elke ingerichte IOPS, met een totaal van Mini maal 1 MBps voor de basis lijn.
- Ultra disks ondersteunen het aanpassen van de schijf prestatie kenmerken (IOPS en door Voer) tijdens runtime zonder dat de schijf wordt losgekoppeld van de virtuele machine. Zodra de bewerking voor het wijzigen van de schijf prestaties op een schijf is uitgevoerd, kan het tot een uur duren voordat de wijzigingen van kracht worden. Er geldt een limiet van vier bewerkingen voor de grootte van de prestaties tijdens een periode van 24 uur. Het is mogelijk dat de bewerking voor het wijzigen van de prestaties mislukt als gevolg van een gebrek aan prestatie bandbreedte capaciteit.

### <a name="disk-size"></a>Schijfgrootte

|Schijf grootte (GiB)  |IOPS Cap  |Doorvoer limiet (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2.400         |600         |
|16     |4800         |1200         |
|32     |9600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80,000         |2.000         |
|1024-65536 (grootten in dit bereik worden verhoogd in stappen van 1 TiB)     |160.000         |2.000         |

### <a name="ga-scope-and-limitations"></a>GA bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
