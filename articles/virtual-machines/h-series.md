---
title: H-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de H-serie.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/04/2020
ms.author: lahugh
ms.openlocfilehash: 6654506a1e53165ef0891ba0de32a7937c21c904
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164811"
---
# <a name="h-series"></a>H-serie

Virtuele machines uit de H-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3-processor kernen, tot 14 GB aan RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

ACU: 290-300

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> voor mpi-toepassingen is het gereserveerde RDMA-back-end-netwerk ingeschakeld door het FDR InfiniBand-netwerk.

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