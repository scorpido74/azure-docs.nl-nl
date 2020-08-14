---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225190"
---
Voor nu hebben Ultra disks de volgende beperkingen:

De enige opties voor de infra structuur-redundantie die momenteel beschikbaar zijn voor Ultra disks zijn beschikbaarheids zones. Vm's met andere redundantie opties kunnen geen Ultra schijf koppelen.

De volgende tabel geeft een overzicht van de regio's Ultra disks zijn beschikbaar in, evenals de bijbehorende beschikbaarheids opties:

> [!NOTE]
> Als een regio in de volgende lijst geen beschik bare beschikbaarheids zones met hoge schijven heeft, moeten de Vm's in die regio worden geïmplementeerd zonder enige infrastructuur redundantie opties om een ultra schijf te kunnen koppelen.

|Regio's  |Aantal beschikbaarheids zones dat ondersteuning biedt voor Ultra schijven  |
|---------|---------|
|VS (overheid) - Virginia     |Geen         |
|VS (overheid) - Arizona     |Geen         |
|South Central US     |Geen         |
|Central US     |Drie zones         |
|VS - west     |Geen         |
|West US 2    |Drie zones         |
|VS - oost     |Drie zones         |
|VS - oost 2     |Drie zones         |
|Zuidoost-Azië     |Drie zones         |
|Azië - oost     |Geen         |
|Europa - noord     |Drie zones          |
|Europa -west     |Drie zones          |
|Verenigd Koninkrijk Zuid     |Drie zones          |
|Japan East     |Drie zones         |
|Frankrijk - centraal    |Twee zones        |
|Brazil South    |Geen        |
|Australië - oost    |Drie zones        |
|Canada-centraal *    |Drie zones        |

\* Neem contact op met de ondersteuning van Azure om toegang te krijgen tot Beschikbaarheidszones voor deze regio.

- Worden alleen ondersteund in de volgende VM-reeks:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
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
- Ondersteunt alleen niet-in cache opgeslagen Lees bewerkingen en niet-opgeslagen schrijf bewerkingen
- De huidige maximum limiet voor IOPS op GA Vm's is 80.000.

Azure Ultra disks biedt standaard Maxi maal 16 TiB per regio per abonnement, maar Ultra schijven ondersteunen hogere capaciteit op aanvraag. Neem contact op met de ondersteuning van Azure om een verhoging van de capaciteit aan te vragen.