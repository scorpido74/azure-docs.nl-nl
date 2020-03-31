---
title: NCv3-serie - Azure Virtual Machines
description: Specificaties voor de NCv3-serie VM's.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ae3604a9ea82e6e50ba4d639d36572f7b052e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302794"
---
# <a name="ncv3-series"></a>NCv3-serie

Vm's uit de NCv3-serie worden aangedreven door [NVIDIA Tesla V100](https://www.nvidia.com/data-center/tesla-v100/) GPU's. Deze GPU's kunnen 1,5x de rekenprestaties van de NCv2-serie leveren. Klanten kunnen profiteren van deze bijgewerkte GPU's voor traditionele HPC-workloads zoals reservoirmodellering, DNA-sequencing, eiwitanalyse, Monte Carlo-simulaties en anderen. De NC24rs v3-configuratie biedt een low latency, high-throughput netwerkinterface geoptimaliseerd voor nauw gekoppelde parallelle computing workloads. Naast de GPU's worden de NCv3-serie VM's ook aangedreven door Intel Xeon E5-2690 v4 (Broadwell) CPU's.

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

> [!IMPORTANT]
> Voor deze VM-serie wordt het vCPU-quotum (core) in uw abonnement in eerste instantie ingesteld op 0 in elke regio. [Vraag een vCPU-quotumverhoging](../azure-supportability/resource-manager-core-quotas-request.md) aan voor deze serie in een [beschikbare regio](https://azure.microsoft.com/regions/services/).
>
| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = één V100-kaart.

*RDMA-compatibel

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd.

De [NVIDIA GPU Driver Extension](./extensions/hpccompute-gpu-windows.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's zoals Azure PowerShell- of Azure Resource Manager-sjablonen. Bekijk de [NVIDIA GPU Driver Extension-documentatie](./extensions/hpccompute-gpu-windows.md) voor ondersteunde besturingssystemen en implementatiestappen. Zie Azure virtual machine [extensions and features](./extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest nvidia GPU-stuurprogramma's handmatig te installeren, raadpleegt u de opstelling van [GPU-stuurprogramma's uit de N-serie voor Windows](./windows/n-series-driver-setup.md) of [N-serie GPU-stuurprogramma's voor Linux](./linux/n-series-driver-setup.md) voor ondersteunde besturingssystemen, stuurprogramma's, installatie en verificatiestappen.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
