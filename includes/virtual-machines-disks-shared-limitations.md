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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008330"
---
In preview is het inschakelen van gedeelde schijven alleen beschikbaar voor een subset van schijftypen. Momenteel kunnen alleen ultraschijven en premium SSD's gedeelde schijven inschakelen. Voor elke beheerde schijf die gedeelde schijven heeft ingeschakeld, gelden de volgende beperkingen, georganiseerd op schijftype:

### <a name="ultra-disks"></a>Ultraschijven

Ultra schijven hebben hun eigen aparte lijst van beperkingen, los van gedeelde schijven. Raadpleeg Azure [ultradisks](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)gebruiken voor ultraschijfbeperkingen.

Bij het delen van ultraschijven hebben ze de volgende extra beperkingen:

- Momenteel alleen ondersteund in West US.
- Momenteel beperkt tot Azure Resource Manager- of SDK-ondersteuning.
- Alleen basisschijven kunnen worden gebruikt met sommige versies van windows server failovercluster, voor meer informatie zie [Failover-hardwarevereisten en opslagopties](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium-SSD's

- Momenteel alleen ondersteund in de Regio West Central US.
- Alle virtuele machines die een schijf delen, moeten in dezelfde [plaatsingsgroepen](../articles/virtual-machines/windows/proximity-placement-groups.md)in de nabijheid worden geïmplementeerd.
- Kan alleen worden ingeschakeld op gegevensschijven, niet op os-schijven.
- Alleen basisschijven kunnen worden gebruikt met sommige versies van windows server failovercluster, voor meer informatie zie [Failover-hardwarevereisten en opslagopties](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadAlleen host caching is niet beschikbaar voor `maxShares>1`premium SSD's met .
- Beschikbaarheidssets en virtuele machineschaalsets kunnen `FaultDomainCount` alleen worden gebruikt met ingesteld op 1.
- Ondersteuning voor Azure Backup en Azure Site Recovery is nog niet beschikbaar.

Als u geïnteresseerd bent in het proberen gedeelde schijven dan [aanmelden voor onze preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
