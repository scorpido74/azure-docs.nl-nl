---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008627"
---
Voor nu, ultra schijven hebben extra beperkingen, ze zijn als volgt:

De enige redundantieopties voor de infrastructuur die momenteel beschikbaar zijn voor ultraschijven, zijn beschikbaarheidszones. VM's die andere redundantieopties gebruiken, kunnen geen ultraschijf bevestigen.

In de volgende tabel worden de ultra-schijven in de regio's beschreven en de bijbehorende beschikbaarheidsopties:

> [!NOTE]
> Sommige beschikbaarheidszone binnen deze regio's bieden geen ultraschijven.

|Regio's  |Geen redundantie van de infrastructuur  |Beschikbaarheidszones  |
|---------|---------|---------|
|VS - west     |Ja         |Nee         |
|VS - west 2    |Nee         |Ja         |
|VS - oost     |Nee         |Ja         |
|VS - oost 2     |Nee         |Ja         |
|Zuidoost-Azië     |Nee         |Ja         |
|Europa - noord     |Nee         |Ja         |
|Europa -west     |Nee         |Ja         |
|Verenigd Koninkrijk Zuid     |Nee         |Ja         |

- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3 (ESv3)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 (DSv3)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultraschijven
- Zijn alleen beschikbaar als dataschijven en ondersteunen alleen 4k fysieke sectorgrootte. Vanwege de 4K native sector grootte van Ultra Disk, zijn er een aantal toepassingen die niet compatibel zijn met ultra schijven. Een voorbeeld hiervan is Oracle Database, waarvoor release 12.2 of hoger nodig is om ultraschijven te ondersteunen.  
- Kan alleen worden gemaakt als lege schijven  
- Ondersteunt momenteel geen schijfmomentopnamen, VM-afbeeldingen, beschikbaarheidssets, Azure Dedicated Hosts of Azure-schijfversleuteling
- Ondersteunt momenteel geen integratie met Azure Backup of Azure Site Recovery
- De huidige maximumlimiet voor IOPS op GA VM's is 80.000.

Azure ultra disks bieden standaard maximaal 16 TiB per regio per abonnement, maar ultraschijven ondersteunen op verzoek een hogere capaciteit. Neem contact op met Azure Support om een capaciteitsverhoging aan te vragen.