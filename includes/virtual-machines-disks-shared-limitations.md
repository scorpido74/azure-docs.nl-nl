---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944471"
---
Het inschakelen van gedeelde schijven is alleen beschikbaar voor een subset van schijf typen. Op dit moment kunnen alleen Ultra disks en Premium Ssd's gedeelde schijven inschakelen. Op elke beheerde schijf waarvoor gedeelde schijven zijn ingeschakeld, gelden de volgende beperkingen, geordend op schijf type:

### <a name="ultra-disks"></a>Ultraschijven

Ultra schijven hebben hun eigen afzonderlijke lijst met beperkingen, niet gerelateerd aan gedeelde schijven. Raadpleeg [using Azure Ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md)voor Ultra Disk-beperkingen.

Bij het delen van ultra schijven hebben ze de volgende extra beperkingen:

- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Gedeelde Ultra schijven zijn beschikbaar in alle regio's die standaard ondersteuning bieden voor Ultra schijven, en u hoeft zich niet te registreren voor toegang om ze te gebruiken.

### <a name="premium-ssds"></a>Premium-SSD's

- Momenteel alleen ondersteund in [een subset van regio's](#regional-availability).
- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Kan alleen worden ingeschakeld op gegevens schijven, niet op OS-schijven.
- **Alleen-lezen** host-caching is niet beschikbaar voor Premium-ssd's met `maxShares>1` .
- Schijf bursting is niet beschikbaar voor Premium-Ssd's met `maxShares>1` .
- Wanneer u beschikbaarheids sets en virtuele-machine schaal sets gebruikt met gedeelde Azure-schijven, wordt de uitlijning van het [opslag fout domein](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) met het fout domein van de virtuele machine niet afgedwongen voor de gedeelde gegevens schijf.
- Wanneer u [proximity placement groups (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)gebruikt, moeten alle virtuele machines die een schijf delen deel uitmaken van dezelfde PPG.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Azure Backup-en Azure Site Recovery-ondersteuning is nog niet beschikbaar.

#### <a name="regional-availability"></a>Regionale beschikbaarheid

Gedeelde Premium-Ssd's worden alleen ondersteund in de volgende regio's:

- VS - oost
- VS - oost 2
- VS - west
- VS - west 2
- VS - west-centraal
- South Central US
- VS - noord-centraal
- US - centraal
- Europa - west
- Europa - noord
- Korea - centraal
- Canada - midden
- Canada - oost
- Japan - oost
- Japan - west
- VS (overheid) - Virginia
- VS (overheid) - Arizona

Als u geïnteresseerd bent in het proberen van gedeelde Premium Ssd's, [meldt u zich aan voor toegang](https://aka.ms/AzureSharedDiskGASignUp).