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
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260709"
---
Voor nu hebben Ultra disks de volgende beperkingen:

- Worden ondersteund in de volgende regio's, met een verschillend aantal beschikbaarheids zones per regio:
    - US - oost 2
    - US - oost
    - US - west 2
    - Zuidoost-Azië
    - Europa - noord
    - Europa -west
    - Verenigd Koninkrijk Zuid 
- Kan alleen worden gebruikt met beschikbaarheids zones (beschikbaarheids sets en implementaties met één VM buiten zones kan geen Ultra schijf koppelen)
- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultra disks.
- Zijn alleen beschikbaar als gegevens schijven en ondersteunen alleen de fysieke sector grootte van 4.000  
- Kan alleen worden gemaakt als lege schijven  
- Nog geen ondersteuning bieden voor schijf momentopnamen, VM-installatie kopieën, beschikbaarheids sets en Azure Disk Encryption
- Nog geen ondersteuning voor integratie met Azure Backup of Azure Site Recovery
- De huidige maximum limiet voor IOPS op GA Vm's is 80.000.
- Als u wilt deel nemen aan een beperkte preview van een virtuele machine die 160.000 IOPS kan bereiken met ultra disks, kunt u een e-mail UltraDiskFeedback@microsoft. com