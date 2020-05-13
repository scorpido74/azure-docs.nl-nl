---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5f14a269478541eaa5852697a917afb3d771841a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196941"
---
Voor nu hebben Ultra disks de volgende beperkingen:

De enige opties voor de infra structuur-redundantie die momenteel beschikbaar zijn voor Ultra disks zijn beschikbaarheids zones. Vm's met andere redundantie opties kunnen geen Ultra schijf koppelen.

De volgende tabel geeft een overzicht van de regio's Ultra disks zijn beschikbaar in, evenals de bijbehorende beschikbaarheids opties:

> [!NOTE]
> Als een regio in de volgende lijst geen beschik bare beschikbaarheids zones met hoge schijven heeft, moeten de Vm's in die regio worden geïmplementeerd zonder enige infrastructuur redundantie opties om een ultra schijf te kunnen koppelen.

|Regio's  |Aantal beschikbaarheids zones dat ondersteuning biedt voor Ultra schijven  |
|---------|---------|
|VS (overheid) - Virginia     |Geen         |
|VS - zuid-centraal     |Geen         |
|VS - centraal     |Drie zones         |
|VS - west     |Geen         |
|VS - west 2    |Drie zones         |
|VS - oost     |Drie zones         |
|VS - oost 2     |Twee zones         |
|Zuidoost-Azië     |Drie zones         |
|Europa - noord     |Drie zones          |
|Europa -west     |Drie zones          |
|Verenigd Koninkrijk Zuid     |Drie zones          |
|Japan - oost     |Twee zones         |



- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Niet elke VM-grootte is beschikbaar in elke ondersteunde regio met ultra disks
- Zijn alleen beschikbaar als gegevens schijven en ondersteunen alleen de fysieke sector grootte van 4.000. Vanwege de standaard sector grootte van 4.000 kB van ultra disk zijn er sommige toepassingen die niet compatibel zijn met ultra disks. Een voor beeld hiervan is Oracle Database, waarvoor release 12,2 of hoger is vereist voor de ondersteuning van ultra disks.  
- Kan alleen worden gemaakt als lege schijven  
- Biedt momenteel geen ondersteuning voor schijf momentopnamen, VM-installatie kopieën, beschikbaarheids sets, voor Azure toegewezen hosts of Azure Disk Encryption
- Biedt momenteel geen ondersteuning voor integratie met Azure Backup of Azure Site Recovery
- De huidige maximum limiet voor IOPS op GA Vm's is 80.000.

Azure Ultra disks biedt standaard Maxi maal 16 TiB per regio per abonnement, maar Ultra schijven ondersteunen hogere capaciteit op aanvraag. Neem contact op met de ondersteuning van Azure om een verhoging van de capaciteit aan te vragen.