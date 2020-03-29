---
title: Azure VM-formaten - Opslag | Microsoft Documenten
description: Geeft een lijst van de verschillende geoptimaliseerde opslagformaten voor virtuele machines in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913333"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Geoptimaliseerde opslagvoor virtuele machineformaten

Opslaggeoptimaliseerde VM-formaten bieden een hoge schijfdoorvoer en IO en zijn ideaal voor Big Data, SQL, NoSQL-databases, gegevensopslag en grote transactionele databases.  Voorbeelden hiervan zijn Cassandra, MongoDB, Cloudera en Redis. In dit artikel vindt u informatie over het aantal vCPU's, gegevensschijven en NIC's, lokale opslagdoorvoer en netwerkbandbreedte voor elke geoptimaliseerde grootte.

De [Lsv2-serie](lsv2-series.md) beschikt over een hoge doorvoer, lage latentie, direct in kaart gebrachtlokale NVMe-opslag draait op de [AMD EPYC<sup>TM</sup> 7551 processor](https://www.amd.com/en/products/epyc-7000-series) met een all core boost van 2,55 GHz en een maximale boost van 3,0 GHz. De Lsv2-serie VM's zijn er in maten van 8 tot 80 vCPU in een gelijktijdige multi-threading configuratie.  Er is 8 GiB geheugen per vCPU, en een 1,92 TB NVMe SSD M.2-apparaat per 8 vCPU's, met maximaal 19,2 TB (10x1,92TB) beschikbaar op de L80s v2.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.

Meer informatie over het optimaliseren van de prestaties op de virtuele machines uit de Lsv2-serie voor [Windows](windows/storage-performance.md) of [Linux.](linux/storage-performance.md)
