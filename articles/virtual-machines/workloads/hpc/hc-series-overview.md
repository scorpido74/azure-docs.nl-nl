---
title: VM-overzicht van de HC-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de preview-ondersteuning voor de VM-grootte van de HC-serie in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906739"
---
# <a name="hc-series-virtual-machine-overview"></a>Overzicht van de virtuele machine van de HC-serie

Voor het optimaliseren van HPC-toepassings prestaties voor Intel Xeon-processoren is een doordachte benadering vereist voor het verwerken van de plaatsing van deze nieuwe architectuur. Hier geven we een overzicht van onze implementatie van IT op Vm's uit de Azure HC-serie voor HPC-toepassingen. We gebruiken de term ' pNUMA ' om te verwijzen naar een fysiek NUMA-domein en ' vNUMA ' om te verwijzen naar een gevirtualiseerde NUMA-domein. Op dezelfde manier gebruiken we de term ' pCore ' om te verwijzen naar fysieke CPU-kernen en ' vCore ' om te verwijzen naar gevirtualiseerde CPU-kernen.

Fysiek is een HC-Server 2 * 24-Core Intel Xeon Platinum 8168 Cpu's voor een totaal van 48 fysieke kernen. Elke CPU is een single pNUMA-domein en heeft eenvormige toegang tot zes kanalen van DRAM. Intel Xeon Platinum Cpu's heeft een 4x groter L2-cache dan in voor gaande generaties (256 KB/core-> 1 MB/kern geheugen), terwijl de L3-cache ook wordt beperkt vergeleken met eerdere Intel Cpu's (2,5 MB/core-> 1,375 MB/core).

De bovenstaande topologie wordt ook overgenomen door de configuratie van de HC-serie-Hyper Visor. Om ruimte te maken voor de werking van de Azure-Hyper Visor zonder dat de virtuele machine wordt verstoord, worden pCores 0-1 en 24-25 (dat wil zeggen, de eerste 2 pCores op elke socket) gereserveerd. Vervolgens wijst u pNUMA-domeinen alle resterende kernen toe aan de virtuele machine. Daarom ziet de VM er als volgt uit:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`kernen per VM

De VM heeft geen kennis die pCores 0-1 en 24-25 niet heeft gekregen. Daarom wordt elke vNUMA beschikbaar gemaakt alsof het systeem eigen 22 kernen heeft.

Intel Xeon Platinum-, Gold-en Silver Cpu's introduceren ook een on-to-Virtual Network-netwerk voor communicatie binnen en buiten de CPU-socket. Het wordt ten zeerste aanbevolen om proces vastmaken voor optimale prestaties en consistentie. Het vastmaken van processen werkt op Vm's uit de HC-serie, omdat het onderliggende silicium wordt blootgesteld aan de gast-VM. Zie [Intel Xeon SP-architectuur](https://bit.ly/2RCYkiE)voor meer informatie.

In het volgende diagram ziet u de schei ding van kernen die zijn gereserveerd voor Azure Hyper Visor en de VM van de HC-serie.

![Schei ding van kern geheugens gereserveerd voor Azure Hyper Visor en HC-serie VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespecificaties

| Hardwarespecificaties          | VM van HC-serie                     |
|----------------------------------|----------------------------------|
| Kernen                            | 44 (HT uitgeschakeld)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| CPU-frequentie (niet-AVX)          | 3,7 GHz (één kern), 2.7-3,4 GHz (alle kernen) |
| Geheugen                           | 8 GB/core (352 in totaal)            |
| Lokale schijf                       | NVMe van 700 GB                      |
| InfiniBand                       | 100 GB EDR Mellanox Connectx-5 * * |
| Netwerk                          | 50 GB Ethernet (40 GB bruikbaar) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Software specificaties

| Software specificaties     | VM van HC-serie          |
|-----------------------------|-----------------------|
| Maximale grootte van MPI-taak            | 13200 kernen (300 Vm's in één VMSS met singlePlacementGroup = True) |
| MPI-ondersteuning                 | MVAPICH2, OpenMPI, MPICH, platform MPI, Intel MPI  |
| Aanvullende Frameworks       | Unified Communication X, libfabric, PGAS |
| Ondersteuning voor Azure Storage       | Std + Premium (max. 4 schijven) |
| Ondersteuning van het besturings systeem voor SRIOV RDMA   | CentOS/RHEL 7,6 +, SLES 12 SP4 +, WinServer 2016 + |
| Ondersteuning voor Azure CycleCloud    | Yes                         |
| Ondersteuning voor Azure Batch         | Ja                         |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over HPC VM-grootten voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) en [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
