---
title: H-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de H-serie.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 656113df50936acf6f9a6b7e9cc8cb01a8aec2a8
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931747"
---
# <a name="h-series"></a>H-serie

Virtuele machines uit de H-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3-processor kernen, tot 14 GB aan RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie zijn momenteel niet SR-IOV ingeschakeld en ondersteunen Intel MPI 5. x en MS-MPI.

[ACU](acu.md): 290-300<br>
[Premium Storage](premium-storage-performance.md): niet ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
<br>

| Grootte | vCPU | Processor | Geheugen (GiB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GiB) | Max. aantal gegevensschijven | Max. doorvoer schijf: IOPS | Maximum aantal Ethernet-Vnic's |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3,3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3,3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3,3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3,3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3,3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3,3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> voor mpi-toepassingen is het gereserveerde RDMA-back-end-netwerk ingeschakeld door het FDR InfiniBand-netwerk.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> De H-serie van de [RDMA-compatibele vm's](sizes-hpc.md#rdma-capable-instances)zijn niet-SR-IOV ingeschakeld. Daarom zijn de ondersteunde [VM-installatie kopieën](./workloads/hpc/configure.md#vm-images), [InfiniBand-stuur programma](./workloads/hpc/enable-infiniband.md) vereisten en ondersteunde [mpi-bibliotheken](./workloads/hpc/setup-mpi.md) verschillend van de op SR-IOV ingeschakelde vm's.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het configureren van uw vm's, het](./workloads/hpc/configure.md) [inschakelen van Infiniband](./workloads/hpc/enable-infiniband.md), het [instellen van MPI](./workloads/hpc/setup-mpi.md) en het optimaliseren van HPC-toepassingen voor Azure bij [HPC-workloads](./workloads/hpc/overview.md).
- Lees over de laatste aankondigingen en enkele HPC-voorbeelden en -resultaten in de [Azure Compute Tech Community-blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
