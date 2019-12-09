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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935849"
---
Voor nu hebben Ultra disks de volgende beperkingen:

- Worden ondersteund in de volgende regio's, met een verschillend aantal beschikbaarheids zones per regio:
    - VS - oost 2
    - VS - oost
    - VS - west 2
    - Zuidoost-Azië
    - Europa - noord
    - Europa - west
    - VK - zuid 
- Kan alleen worden gebruikt met beschikbaarheids zones (beschikbaarheids sets en implementaties met één VM buiten zones kan geen Ultra schijf koppelen)
- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultra disks
- Zijn alleen beschikbaar als gegevens schijven en ondersteunen alleen de fysieke sector grootte van 4.000. Vanwege de standaard sector grootte van 4.000 kB van ultra disk zijn er sommige toepassingen die niet compatibel zijn met ultra disks. Een voor beeld hiervan is Oracle Database, waarvoor release 12,2 of hoger is vereist voor de ondersteuning van ultra disks.  
- Kan alleen worden gemaakt als lege schijven  
- Nog geen ondersteuning bieden voor schijf momentopnamen, VM-installatie kopieën, beschikbaarheids sets en Azure Disk Encryption
- Nog geen ondersteuning voor integratie met Azure Backup of Azure Site Recovery
- De huidige maximum limiet voor IOPS op GA Vm's is 80.000.
- Als u wilt deel nemen aan een beperkte preview van een virtuele machine die 160.000 IOPS kan bereiken met ultra disks, kunt u een e-mail UltraDiskFeedback@microsoft. com