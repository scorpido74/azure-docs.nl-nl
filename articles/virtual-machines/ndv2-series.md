---
title: NDv2-serie - Azure Virtual Machines
description: Specificaties voor de NDv2-serie VM's.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247279"
---
# <a name="updated-ndv2-series"></a>Bijgewerkte NDv2-serie

De virtuele machine uit de NDv2-serie is een nieuwe toevoeging aan de GPU-familie, ontworpen voor de behoeften van de meest veeleisende GPU-versnelde AI-, machine learning-, simulatie- en HPC-workloads.

NDv2 wordt aangedreven door 8 NVIDIA Tesla V100 NVLINK-aangesloten GPU's, elk met 32 GB GPU-geheugen. Elke NDv2 VM heeft ook 40 niet-HyperThreaded Intel Xeon Platinum 8168 (Skylake) cores en 672 GiB van systeemgeheugen.

NDv2-exemplaren bieden uitstekende prestaties voor HPC- en AI-workloads die gebruikmaken van CUDA GPU-geoptimaliseerde berekeningskernels en de vele AI-, ML- en analysetools die GPU-versnelling 'out-of-box' ondersteunen, zoals TensorFlow, Pytorch, Caffe, STROOMVERSNELLINGEn en andere Kaders.

Kritisch is dat de NDv2 is gebouwd voor zowel computerintensieve scale-up (waarbij 8 GPU's per VM worden gebruikt) als scale-out (waarbij meerdere VM's samenwerken) workloads. De NDv2-serie ondersteunt nu 100-Gigabit InfiniBand EDR backend-netwerken, vergelijkbaar met die op de HB-serie van HPC VM, om high-performance clustering mogelijk te maken voor parallelle scenario's, waaronder gedistribueerde training voor AI en ML. Dit backend-netwerk ondersteunt alle belangrijke InfiniBand-protocollen, inclusief die in dienst van NVIDIA's NCCL2-bibliotheken, waardoor gpu's naadloos kunnen worden geclusterd.

> Wanneer [u InfiniBand inschakelt](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) op de ND40rs_v2 VM, u de 4.7-1.0.0.1 Mellanox OFED driver gebruiken.
>
> Vanwege het toegenomen GPU-geheugen vereist de nieuwe ND40rs_v2 VM het gebruik van [Generatie 2 VM's](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) en marketplace-afbeeldingen. 
>
> Let op: de ND40s_v2 met 16 GB per GPU-geheugen is niet langer beschikbaar voor preview en is vervangen door de bijgewerkte ND40rs_v2.

<br>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

InfiniBand: Ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD): GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximale netwerkbandbreedte | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd.

De [NVIDIA GPU Driver Extension](./extensions/hpccompute-gpu-linux.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's zoals Azure PowerShell- of Azure Resource Manager-sjablonen. Zie Azure virtual machine [extensions and features](./extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest nvidia GPU-stuurprogramma's handmatig te installeren, raadpleegt u [de GPU-stuurprogramma-installatie van de N-serie voor Linux.](./linux/n-series-driver-setup.md)

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
