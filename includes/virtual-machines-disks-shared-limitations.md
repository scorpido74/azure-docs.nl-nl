---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008330"
---
In de preview-versie is het inschakelen van gedeelde schijven alleen beschikbaar voor een subset van schijf typen. Op dit moment kunnen alleen Ultra disks en Premium Ssd's gedeelde schijven inschakelen. Op elke beheerde schijf waarvoor gedeelde schijven zijn ingeschakeld, gelden de volgende beperkingen, geordend op schijf type:

### <a name="ultra-disks"></a>Ultraschijven

Ultra schijven hebben hun eigen afzonderlijke lijst met beperkingen, niet gerelateerd aan gedeelde schijven. Raadpleeg [using Azure Ultra disks](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)voor Ultra Disk-beperkingen.

Bij het delen van ultra schijven hebben ze de volgende extra beperkingen:

- Momenteel alleen ondersteund in VS-West.
- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium-SSD's

- Momenteel alleen ondersteund in de regio West-Centraal vs.
- Alle virtuele machines die een schijf delen, moeten worden geïmplementeerd in dezelfde [plaatsings groepen](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan alleen worden ingeschakeld op gegevens schijven, niet op OS-schijven.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Alleen-lezen host-caching is niet beschikbaar voor `maxShares>1`Premium-ssd's met.
- Beschikbaarheids sets en virtuele-machine schaal sets kunnen alleen worden gebruikt `FaultDomainCount` met ingesteld op 1.
- Azure Backup-en Azure Site Recovery-ondersteuning is nog niet beschikbaar.

Als u geïnteresseerd bent in het proberen van gedeelde schijven, [meldt u zich aan voor de preview-versie](https://aka.ms/AzureSharedDiskPreviewSignUp).
