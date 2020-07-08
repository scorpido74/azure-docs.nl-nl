---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74279167"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>De grootte van tabeldefinities wijzigen

- De opslagcapaciteit wordt weergegeven in GiB-eenheden of 1024^3 bytes. Wanneer u schijven die zijn gemeten in GB (1000 ^ 3 bytes), vergelijkt met schijven gemeten in GiB (1024 ^ 3), weet u dat de capaciteits aantallen die in GiB zijn opgegeven, kleiner kunnen worden weer gegeven. Bijvoorbeeld 1023 GiB = 1098,4 GB.
- De schijfdoorvoer wordt gemeten in I/O-bewerkingen per seconde (IOPS) en MBps, waarbij MBps = 10^6 bytes per seconde.
- Gegevensschijven kunnen in de modus met of zonder caching werken. Voor schijfbewerkingen met gegevenscaching is de cachemodus van de host ingesteld op **ReadOnly** of **ReadWrite**.  Voor schijfbewerkingen zonder gegevenscaching is de cachemodus van de host ingesteld op **Geen**.
- Als u de beste prestaties voor uw Vm's wilt krijgen, moet u het aantal gegevens schijven per vCPU beperken tot twee schijven.
- **Verwachte netwerk bandbreedte** is het maximale aantal geaggregeerde band breedte dat per VM-type voor alle nic's voor alle doelen is toegewezen. Zie [netwerk bandbreedte van virtuele machines](../articles/virtual-network/virtual-machine-network-throughput.md)voor meer informatie.

  De hoogste limieten zijn niet gegarandeerd. Limieten bieden richt lijnen voor het selecteren van het juiste VM-type voor de gewenste toepassing. De werkelijke netwerk prestaties zijn afhankelijk van verschillende factoren, waaronder netwerk congestie, toepassings belasting en netwerk instellingen. Zie [netwerk doorvoer optimaliseren voor virtuele Azure-machines](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)voor meer informatie over het optimaliseren van netwerk doorvoer. Als u de verwachte netwerk prestaties op Linux of Windows wilt verzorgen, moet u mogelijk een specifieke versie selecteren of uw virtuele machine optimaliseren. Zie [band breedte/doorvoer testen (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md)voor meer informatie.



