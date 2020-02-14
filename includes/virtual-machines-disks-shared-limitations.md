---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202214"
---
In de preview-versie worden beheerde schijven waarvoor gedeelde schijven zijn ingeschakeld, onderhevig aan de volgende beperkingen:

- Momenteel alleen beschikbaar met Premium Ssd's.
- Momenteel alleen ondersteund in de regio West-Centraal vs.
- Alle virtuele machines die een schijf delen, moeten worden geïmplementeerd in dezelfde [plaatsings groepen](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan alleen worden ingeschakeld op gegevens schijven, niet op OS-schijven.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Alleen-lezen host-caching is niet beschikbaar voor Premium-Ssd's met `maxShares>1`.
- Beschikbaarheids sets en virtuele-machine schaal sets kunnen alleen worden gebruikt met `FaultDomainCount` ingesteld op 1.
- Azure Backup-en Azure Site Recovery-ondersteuning is nog niet beschikbaar.

Als u geïnteresseerd bent in het proberen van gedeelde schijven, [meldt u zich aan voor de preview-versie](https://aka.ms/shareddisksignup).
