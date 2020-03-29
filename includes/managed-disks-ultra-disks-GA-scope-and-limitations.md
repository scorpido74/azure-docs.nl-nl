---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935849"
---
Voor nu, ultra schijven hebben extra beperkingen, ze zijn als volgt:

- Worden ondersteund in de volgende regio's, met een wisselend aantal beschikbaarheidszones per regio:
    - VS - oost 2
    - VS - oost
    - VS - west 2
    - Zuidoost-Azië
    - Europa - noord
    - Europa -west
    - Verenigd Koninkrijk Zuid 
- Kan alleen worden gebruikt met beschikbaarheidszones (beschikbaarheidssets en afzonderlijke VM-implementaties buiten zones hebben niet de mogelijkheid om een ultraschijf te koppelen)
- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3 (ESv3)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 (DSv3)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultraschijven
- Zijn alleen beschikbaar als dataschijven en ondersteunen alleen 4k fysieke sectorgrootte. Vanwege de 4K native sector grootte van Ultra Disk, zijn er een aantal toepassingen die niet compatibel zijn met ultra schijven. Een voorbeeld hiervan is Oracle Database, waarvoor release 12.2 of hoger nodig is om ultraschijven te ondersteunen.  
- Kan alleen worden gemaakt als lege schijven  
- Geen ondersteuning voor schijfmomentopnamen, VM-afbeeldingen, beschikbaarheidssets en Azure-schijfversleuteling
- Ondersteuning voor integratie met Azure Backup of Azure Site Recovery
- De huidige maximumlimiet voor IOPS op GA VM's is 80.000.
- Als u wilt deelnemen aan een beperkte preview van een VM die 160.000 UltraDiskFeedback@microsoft IOPS met ultra schijven kan bereiken, u een e-mail sturen naar .com