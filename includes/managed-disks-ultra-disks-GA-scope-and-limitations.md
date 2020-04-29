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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008627"
---
Voor nu hebben Ultra disks de volgende beperkingen:

De enige opties voor de infra structuur-redundantie die momenteel beschikbaar zijn voor Ultra disks zijn beschikbaarheids zones. Vm's met andere redundantie opties kunnen geen Ultra schijf koppelen.

De volgende tabel geeft een overzicht van de regio's Ultra disks zijn beschikbaar in, evenals de bijbehorende beschikbaarheids opties:

> [!NOTE]
> Sommige beschikbaarheids zones in deze regio's bieden geen Ultra schijven.

|Regio's  |Geen infrastructuur redundantie  |Beschikbaarheidszones  |
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
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultra disks
- Zijn alleen beschikbaar als gegevens schijven en ondersteunen alleen de fysieke sector grootte van 4.000. Vanwege de standaard sector grootte van 4.000 kB van ultra disk zijn er sommige toepassingen die niet compatibel zijn met ultra disks. Een voor beeld hiervan is Oracle Database, waarvoor release 12,2 of hoger is vereist voor de ondersteuning van ultra disks.  
- Kan alleen worden gemaakt als lege schijven  
- Biedt momenteel geen ondersteuning voor schijf momentopnamen, VM-installatie kopieën, beschikbaarheids sets, voor Azure toegewezen hosts of Azure Disk Encryption
- Biedt momenteel geen ondersteuning voor integratie met Azure Backup of Azure Site Recovery
- De huidige maximum limiet voor IOPS op GA Vm's is 80.000.

Azure Ultra disks biedt standaard Maxi maal 16 TiB per regio per abonnement, maar Ultra schijven ondersteunen hogere capaciteit op aanvraag. Neem contact op met de ondersteuning van Azure om een verhoging van de capaciteit aan te vragen.