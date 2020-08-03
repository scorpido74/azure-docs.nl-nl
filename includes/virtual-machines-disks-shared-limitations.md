---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f6175a797b14077cafacaca1f2fd48f36e945d9e
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425212"
---
Het inschakelen van gedeelde schijven is alleen beschikbaar voor een subset van schijf typen. Op dit moment kunnen alleen Ultra disks en Premium Ssd's gedeelde schijven inschakelen. Op elke beheerde schijf waarvoor gedeelde schijven zijn ingeschakeld, gelden de volgende beperkingen, geordend op schijf type:

### <a name="ultra-disks"></a>Ultraschijven

Ultra schijven hebben hun eigen afzonderlijke lijst met beperkingen, niet gerelateerd aan gedeelde schijven. Raadpleeg [using Azure Ultra disks](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)voor Ultra Disk-beperkingen.

Bij het delen van ultra schijven hebben ze de volgende extra beperkingen:

- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Gedeelde Ultra schijven zijn beschikbaar in alle regio's die standaard ondersteuning bieden voor Ultra schijven, en u hoeft zich niet te registreren voor toegang om ze te gebruiken.

### <a name="premium-ssds"></a>Premium-SSD's

- Momenteel alleen ondersteund in de regio West-Centraal vs.
- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Kan alleen worden ingeschakeld op gegevens schijven, niet op OS-schijven.
- **Alleen-lezen** host-caching is niet beschikbaar voor Premium-ssd's met `maxShares>1` .
- Schijf bursting is niet beschikbaar voor Premium-Ssd's met `maxShares>1` .
- Wanneer u beschikbaarheids sets en virtuele-machine schaal sets gebruikt met gedeelde Azure-schijven, wordt de uitlijning van het [opslag fout domein](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) met het fout domein van de virtuele machine niet afgedwongen voor de gedeelde gegevens schijf.
- Wanneer u [proximity placement groups (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)gebruikt, moeten alle virtuele machines die een schijf delen deel uitmaken van dezelfde PPG.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Azure Backup-en Azure Site Recovery-ondersteuning is nog niet beschikbaar.

Als u geïnteresseerd bent in het proberen van gedeelde Premium Ssd's, [meldt u zich aan voor toegang](https://aka.ms/AzureSharedDiskGASignUp).