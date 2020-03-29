---
title: B-serie burstable - Azure Virtual Machines
description: Beschrijft de B-serie burstable Azure VM-formaten.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161077"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B-serie burstable virtuele machine maten

De VM's uit de B-serie zijn ideaal voor workloads die niet continu de volledige prestaties van de CPU nodig hebben, zoals webservers, proof of concepts, kleine databases en ontwikkelbuildomgevingen. Deze workloads hebben doorgaans burstable prestatievereisten. De B-serie biedt u de mogelijkheid om een VM-grootte te kopen met basislijnprestaties en de VM-instantie bouwt credits op wanneer deze minder dan de basislijn gebruikt. Wanneer de VM krediet heeft verzameld, kan de VM boven de basislijn barsten met maximaal 100% van de vCPU wanneer uw toepassing hogere CPU-prestaties vereist.

De B-serie is verkrijgbaar in de volgende VM-formaten:

Premium opslag: ondersteund

Premium Storage caching: niet ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Cpu-perf van VM baseren | Max CPU Perf van VM | Initiële kredieten | Kredieten banked/uur | Max Banked Credits | Max. aantal gegevensschijven | Doorvoer van maximale opslag in de cache en temp: IOPS/MBps | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1.</sup> | 1  | 0.5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls wordt alleen ondersteund op Linux

## <a name="workload-example"></a>Voorbeeld van werkbelasting

Overweeg een office check-in/out applicatie. De applicatie moet CPU uitbarstingen tijdens kantooruren, maar niet veel rekenkracht tijdens off-uren. In dit voorbeeld vereist de werkbelasting een virtuele 16vCPU-machine met 64GiB RAM om efficiënt te werken.

De tabel toont de verkeersgegevens per uur en de grafiek is een visuele weergave van dat verkeer.

B16-kenmerken:

Max CPU perf: 16vCPU * 100% = 1600%

Basislijn: 270%

![Grafiek van verkeersgegevens per uur](./media/b-series-burstable/office-workload.png)

| Scenario | Time | CPU-gebruik (%) | Credits verzameld<sup>1</sup> | Credits beschikbaar |
| --- | --- | --- | --- | --- |
| B16ms-implementatie | Implementatie | Implementatie  | 480 (eerste credits) | 480 |
| Geen verkeer | 0:00 | 0 | 162 | 642 |
| Geen verkeer | 1:00 | 0 | 162 | 804 |
| Geen verkeer | 2:00 | 0 | 162 | 966 |
| Geen verkeer | 3:00 | 0 | 162 | 1128 |
| Geen verkeer | 4:00 | 0 | 162 | 1290 |
| Geen verkeer | 5:00 | 0 | 162 | 1452 |
| Weinig verkeer | 6:00 | 270 | 0 | 1452 |
| Medewerkers komen naar kantoor (app heeft 80% vCPU nodig) | 7:00 | 1280 | -606 | 846 |
| Medewerkers blijven naar kantoor (app heeft 80% vCPU nodig) | 8:00 | 1280 | -606 | 240 |
| Weinig verkeer | 9:00 | 270 | 0 | 240 |
| Weinig verkeer | 10:00 | 100 | 102 | 342 |
| Weinig verkeer | 11:00 | 50 | 132 | 474 |
| Weinig verkeer | 12:00 | 100 | 102 | 576 |
| Weinig verkeer | 13:00 | 100 | 102 | 678 |
| Weinig verkeer | 14:00 | 50 | 132 | 810 |
| Weinig verkeer | 15:00 | 100 | 102 | 912 |
| Weinig verkeer | 16:00 | 100 | 102 | 1014 |
| Medewerkers uitchecken (app heeft 100% vCPU nodig) | 17:00 | 1600 | -798 | 216 |
| Weinig verkeer | 18:00 | 270 | 0 | 216 |
| Weinig verkeer | 19:00 | 270 | 0 | 216 |
| Weinig verkeer | 20:00 | 50 | 132 | 348 |
| Weinig verkeer | 21:00 | 50 | 132 | 480 |
| Geen verkeer | 22:00 | 0 | 162 | 642 |
| Geen verkeer | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Credits verzameld / credits gebruikt in `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`een uur is gelijk aan: .  

Voor een D16s_v3 die 16 vCPU's en 64 GiB van geheugen heeft is het uurtarief $0.936 per uur (maandelijks $673.92) en voor B16ms met 16 vCPU's en 64 Geheugen GiB is het tarief $0.794 per uur (maandelijks $547.86). <b>Dit resulteert in 15% besparing!</b>

## <a name="q--a"></a>Vragen en antwoorden

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>V: Hoe krijg je 135% basisprestaties van een VM?

**A**: De 135% wordt gedeeld onder de 8 vCPU's die deel uitmaken van de VM-grootte. Als uw toepassing bijvoorbeeld 4 van de 8 cores gebruikt die werken aan batchverwerking en elk van deze 4 vCPU's op 30% gebruik draait, zou de totale hoeveelheid VM-CPU-prestaties 120% bedragen.  Dit betekent dat uw VM krediettijd zou opbouwen op basis van de 15% delta van uw basislijnprestaties.  Maar het betekent ook dat wanneer u credits beschikbaar dat dezelfde VM kan gebruiken 100% van alle 8 vCPU's geven dat VM een Max CPU prestaties van 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>V: Hoe kan ik mijn kredietsaldo en verbruik controleren

**A:** We zullen de komende weken 2 nieuwe statistieken introduceren, met de **creditstatistiek** u zien hoeveel credits uw VM heeft gebanked en de **metriek ConsumedCredit** laat zien hoeveel CPU-credits uw VM van de bank heeft verbruikt.    U deze statistieken bekijken vanuit het deelvenster metrische gegevens in de portal of programmatisch via de Azure Monitor API's.

Zie [Overzicht van statistieken in Microsoft Azure](../azure-monitor/platform/data-platform.md)voor meer informatie over hoe u toegang krijgen tot de metrische gegevens voor Azure.

### <a name="q-how-are-credits-accumulated"></a>V: Hoe worden credits verzameld?

**A**: De VM-accumulatie- en verbruikspercentages zijn zodanig ingesteld dat een VM die precies op het basisprestatieniveau draait, noch een nettoaccumulatie of verbruik van barstende credits zal hebben.  Een VM zal een netto toename van credits wanneer het draait onder de basis prestatieniveau en zal een netto daling van de credits wanneer de VM is gebruik te maken van de CPU meer dan de basis prestatieniveau.

**Voorbeeld:** Ik implementeer een VM met de b1ms-grootte voor mijn kleine tijd- en aanwezigheidsdatabasetoepassing. Deze grootte maakt het mogelijk mijn applicatie te gebruiken tot 20% van een vCPU als mijn baseline, dat is 0,2 credits per minuut kan ik gebruiken of bank.

Mijn sollicitatie is druk aan het begin en einde van mijn werknemers werkdag, tussen 7:00-9:00 en 04:00 - 18:00 uur. Tijdens de andere 20 uur van de dag, mijn applicatie is meestal in actief, alleen met behulp van 10% van de vCPU. Voor de niet-piekuren verdien ik 0,2 credits per minuut, maar verbruik ik slechts 0,l credits per minuut, dus mijn VM zal 0,1 x 60 = 6 credits per uur bankieren.  Voor de 20 uur dat ik buiten de piek ben, zal ik 120 credits bankieren.  

Tijdens piekuren mijn applicatie gemiddeld60% vCPU gebruik, ik verdien nog steeds 0,2 credits per minuut, maar ik verbruiken 0,6 credits per minuut, voor een netto kosten van 0,4 credits per minuut of 0,4 x 60 = 24 credits per uur. Ik heb 4 uur per dag van piek gebruik, dus het kost 4 x 24 = 96 credits voor mijn piek gebruik.

Als ik de 120 credits die ik verdiende off-peak en trek de 96 credits die ik gebruikte voor mijn piektijden, ik bank een extra 24 credits per dag die ik kan gebruiken voor andere uitbarstingen van activiteit.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>V: Hoe kan ik geaccumuleerde en gebruikte credits berekenen?

**A:** U de volgende formule gebruiken:

(Base CPU perf vm - CPU-gebruik) / 100 = Credits bank of gebruik per minuut

bijvoorbeeld in bovenstaande instantie is uw basislijn 20% en als u 10% van de CPU gebruikt, verzamelt u zich (20%-10%)/100 = 0,1 krediet per minuut.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>V: Ondersteunt de B-serie Premium Storage dataschijven?

**A:** Ja, alle B-serie formaten ondersteunen Premium Storage dataschijven.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>V: Waarom is mijn resterende tegoed ingesteld op 0 na een redeploy of een stop/start?

**A:** Wanneer een VM "REDPLOYED" is en de VM naar een ander knooppunt wordt verplaatst, gaat het geaccumuleerde tegoed verloren. Als de vm wordt gestopt/gestart, maar op hetzelfde knooppunt blijft, behoudt de VM het geaccumuleerde tegoed. Wanneer de VM begint vers op een knooppunt, krijgt het een eerste krediet, voor Standard_B8ms het is 240 minuten.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>V: Wat gebeurt er als ik een niet-ondersteunde OS-afbeelding op B1ls implementeer?

**A** : B1ls ondersteunt alleen Linux-afbeeldingen en als u een andere OS-afbeelding implementeert, krijgt u mogelijk niet de beste klantervaring.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.