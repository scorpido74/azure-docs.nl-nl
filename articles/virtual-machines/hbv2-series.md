---
title: HBv2-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de HBv2-serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: 730065333fde7431b7018ef0a6e614e12727d7ed
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673850"
---
# <a name="hbv2-series"></a>HBv2-serie

Vm's uit de HBv2-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een eindige element analyse en reservoir simulatie. HBv2 Vm's feature 120 AMD EPYC 7742-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Elke HBv2-VM biedt tot 340 GB/sec. geheugen bandbreedte en Maxi maal 4 teraFLOPS van FP64 compute.

Premium Storage: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Alles | 480 + 960 | 8 | 1 |


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