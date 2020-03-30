---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262069"
---
Azure managed disks biedt momenteel vier schijftypen, elk type is gericht op specifieke klantscenario's.

## <a name="disk-comparison"></a>Vergelijking van schijven

De volgende tabel biedt een vergelijking van ultraschijven, premium solid-state drives (SSD), standaard SSD en standaard harde schijven (HDD) voor beheerde schijven om u te helpen beslissen wat u wilt gebruiken.

|   | Ultraschijven   | Premium - SSD   | Standard - SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Schijftype   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |IO-intensieve workloads zoals [SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)top tier databases (bijvoorbeeld SQL, Oracle) en andere transaction-heavy workloads.   |Productie- en prestatiegevoelige workloads   |Webservers, bedrijfstoepassingen die weinig wordt gebruikt en dev/test   |Back-up, niet-kritiek, incidentele toegang   |
|Maximale schijfgrootte   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Maximale doorvoer   |2000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Max. IOPS   |160.000    |20.000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Ultraschijven

Azure-ultraschijven leveren een hoge doorvoer, hoge IOPS en een consistente schijfopslag met lage latentie voor Azure IaaS-VM's. Enkele extra voordelen van ultraschijven zijn de mogelijkheid om de prestaties van de schijf dynamisch te wijzigen, samen met uw workloads, zonder dat u uw virtuele machines (VM) opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties. Ultraschijven kunnen uitsluitend als gegevensschijven worden gebruikt. U wordt aangeraden Premium SSD-schijven als besturingssysteemschijven te gebruiken.

### <a name="performance"></a>Prestaties

Wanneer u een ultraschijf indient, u de capaciteit en de prestaties van de schijf onafhankelijk configureren. Ultra-schijven zijn er in verschillende vaste formaten, variërend van 4 GiB tot 64 TiB, en zijn voorzien van een flexibel prestatieconfiguratiemodel waarmee u IOPS en doorvoer onafhankelijk configureren.

Enkele belangrijke mogelijkheden van ultra schijven zijn:

- Schijfcapaciteit: de capaciteit van ultraschijven varieert van 4 GiB tot 64 TiB.
- Disk IOPS: Ultra disks support IOPS limits of 300 IOPS/GiB, up to a maximum of 160 K IOPS per disk. Als u de IOPS wilt bereiken die u hebt ingericht, moet u ervoor zorgen dat de geselecteerde Schijf-IOPS lager is dan de VM IOPS-limiet. De minimale gegarandeerde IOPS per schijf is 2 IOPS/GiB, met een totaal basislijnminimum van 100 IOPS. Als u bijvoorbeeld een 4 GiB ultra schijf had, heeft u een minimum van 100 IOPS, in plaats van acht IOPS.
- Schijfdoorvoer: Bij ultraschijven is de doorvoerlimiet van een enkele schijf 256 KiB/s voor elke ingerichte IOPS, tot een maximum van 2000 MBps per schijf (waarbij MBps = 10^6 Bytes per seconde). De minimale gegarandeerde doorvoer per schijf is 4KiB/s voor elke ingerichte IOPS, met een totaal basislijnminimum van 1 MBps.
- Ultraschijven ondersteunen het aanpassen van de schijfprestatiekenmerken (IOPS en doorvoer) tijdens runtime zonder de schijf los te koppelen van de virtuele machine. Zodra een werking van het formaat van de schijfprestaties op een schijf is uitgevoerd, kan het tot een uur duren voordat de wijziging daadwerkelijk van kracht wordt. Er is een limiet van vier prestatie-groottebewerkingen tijdens een venster van 24 uur. Het is mogelijk dat een prestatiewijzigingsbewerking mislukt vanwege een gebrek aan prestatiebandbreedtecapaciteit.

### <a name="disk-size"></a>Schijfgrootte

|Schijfgrootte (GiB)  |IOPS-dop  |Doorvoerkap (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2,400         |600         |
|16     |4800         |1200         |
|32     |9600         |2.000         |
|64     |19,200         |2.000         |
|128     |38,400         |2.000         |
|256     |76.800         |2.000         |
|512     |80,000         |2.000         |
|1.024-65.536 (maten in dit bereik nemen toe in stappen van 1 TiB)     |160.000         |2.000         |

### <a name="ga-scope-and-limitations"></a>GA-bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
