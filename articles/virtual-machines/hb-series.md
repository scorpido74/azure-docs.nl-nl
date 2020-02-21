---
title: HB-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de HB-serie.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 124bdecac900655ed8f6ad0aaaa51fadf342f2c6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492836"
---
# <a name="hb-series"></a>HB-serie

Vm's uit de HB-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-Dynamics, een expliciete, beperkte element analyse en weer modellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Een HB-VM biedt tot 260 GB/sec. geheugen bandbreedte.

ACU: 199-216

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alle | 700 | 4 | 1 |

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