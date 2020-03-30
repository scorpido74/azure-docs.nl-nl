---
title: ND-serie - Azure Virtual Machines
description: Specificaties voor de ND-serie VM's.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 163dc0d6ecd4f91273a82b04ce186f7a8df868af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465053"
---
# <a name="nd-series"></a>ND-serie

De virtuele machines uit de ND-serie zijn een nieuwe toevoeging aan de GPU-familie die is ontworpen voor AI- en Deep Learning-workloads. Ze bieden uitstekende prestaties voor training en gevolgtrekking. ND-exemplaren worden aangedreven door [NVIDIA Tesla P40-GPU's](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) en Intel Xeon E5-2690 v4 (Broadwell) CPU's. Deze exemplaren bieden uitstekende prestaties voor floating point-bewerkingen met één precisie, voor AI-workloads die gebruikmaken van Microsoft Cognitive Toolkit, TensorFlow, Caffe en andere frameworks. De ND-serie biedt ook een veel grotere GPU-geheugengrootte (24 GB), waardoor veel grotere neurale netto-modellen kunnen worden aangepast. Net als de NC-serie biedt de ND-serie een configuratie met een secundair low-latency, high-throughput netwerk via RDMA en InfiniBand-connectiviteit, zodat u grootschalige trainingstaken uitvoeren met veel GPU's.

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

> [!IMPORTANT]
> Voor deze VM-serie wordt het vCPU-quotum (core) per regio in uw abonnement in eerste instantie ingesteld op 0. [Vraag een vCPU-quotumverhoging](../azure-supportability/resource-manager-core-quotas-request.md) aan voor deze serie in een [beschikbare regio](https://azure.microsoft.com/regions/services/).
>
| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = één P40-kaart.

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
