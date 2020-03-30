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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279167"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>De grootte van tabeldefinities wijzigen

- De opslagcapaciteit wordt weergegeven in GiB-eenheden of 1024^3 bytes. Wanneer u schijven gemeten in GB (1000^3 bytes) vergelijkt met schijven gemeten in GiB (1024^3) bedenk dan dat capaciteitsaantallen in GiB kleiner kunnen lijken. Bijvoorbeeld 1023 GiB = 1098,4 GB.
- De schijfdoorvoer wordt gemeten in I/O-bewerkingen per seconde (IOPS) en MBps, waarbij MBps = 10^6 bytes per seconde.
- Gegevensschijven kunnen in de modus met of zonder caching werken. Voor schijfbewerkingen met gegevenscaching is de cachemodus van de host ingesteld op **ReadOnly** of **ReadWrite**.  Voor schijfbewerkingen zonder gegevenscaching is de cachemodus van de host ingesteld op **Geen**.
- Als u de beste prestaties voor uw VM's wilt krijgen, moet u het aantal gegevensschijven beperken tot twee schijven per vCPU.
- **Verwachte netwerkbandbreedte** is de maximale geaggregeerde bandbreedte die per VM-type wordt toegewezen voor alle NIC's, voor alle bestemmingen. Zie [Bandbreedte van het virtuele machinenetwerk](../articles/virtual-network/virtual-machine-network-throughput.md)voor meer informatie.

  Bovengrenzen zijn niet gegarandeerd. Limieten bieden richtlijnen voor het selecteren van het juiste VM-type voor de beoogde toepassing. De werkelijke netwerkprestaties zijn afhankelijk van verschillende factoren, waaronder netwerkcongestie, toepassingsbelasting en netwerkinstellingen. Zie [Netwerkdoorvoer optimaliseren voor virtuele Azure-machines voor](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)informatie over het optimaliseren van de netwerkdoorvoer. Om de verwachte netwerkprestaties op Linux of Windows te bereiken, moet u mogelijk een specifieke versie selecteren of uw VM optimaliseren. Zie [Bandwidth/Throughput testing (NTTTCP) voor](../articles/virtual-network/virtual-network-bandwidth-testing.md)meer informatie.



