---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471680"
---
In preview zijn beheerde schijven die gedeelde schijven hebben ingeschakeld, onderworpen aan de volgende beperkingen:

- Momenteel alleen beschikbaar met premium SSD's.
- Momenteel alleen ondersteund in de Regio West Central US.
- Alle virtuele machines die een schijf delen, moeten in dezelfde [plaatsingsgroepen](../articles/virtual-machines/windows/proximity-placement-groups.md)in de nabijheid worden geïmplementeerd.
- Kan alleen worden ingeschakeld op gegevensschijven, niet op os-schijven.
- Alleen basisschijven kunnen worden gebruikt met sommige versies van windows server failovercluster, voor meer informatie zie [Failover-hardwarevereisten en opslagopties](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadAlleen host caching is niet beschikbaar voor `maxShares>1`premium SSD's met .
- Beschikbaarheidssets en virtuele machineschaalsets kunnen `FaultDomainCount` alleen worden gebruikt met ingesteld op 1.
- Ondersteuning voor Azure Backup en Azure Site Recovery is nog niet beschikbaar.

Als u geïnteresseerd bent in het proberen gedeelde schijven dan [aanmelden voor onze preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
