---
title: DC-serie-Azure Virtual Machines
description: Specificaties voor de Vm's van de DC-serie.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493811"
---
# <a name="dc-series"></a>DC-serie

De [DC-serie](#dc-series) is een familie van virtuele machines in azure die helpt bij het beschermen van de vertrouwelijkheid en integriteit van uw gegevens en code tijdens de verwerking in de open bare Cloud. Deze machines worden ondersteund door de 3,7 GHz Intel XEON E-2176G-processor met SGX-technologie. Met de Intel Turbo Boost-technologie kunnen deze machines tot 4,7 GHz gaan. Met instanties van DC-reeksen kunnen klanten veilige enclave toepassingen bouwen om hun code en gegevens te beschermen terwijl ze worden gebruikt.

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000/32 (43) | 3200 /48 | 2 / 1500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400/96 | 2 / 3000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.