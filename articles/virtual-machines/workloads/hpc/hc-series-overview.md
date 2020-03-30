---
title: HC-serie VM-overzicht - Azure Virtual Machines| Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906739"
---
# <a name="hc-series-virtual-machine-overview"></a>HC-serie virtuele machine overzicht

Het maximaliseren van de hpc-toepassingsprestaties op Intel Xeon Scalable Processors vereist een doordachte benadering van procesplaatsing op deze nieuwe architectuur. Hier schetsen we onze implementatie ervan op Azure HC-serie VM's voor HPC-toepassingen. We gebruiken de term "pNUMA" om te verwijzen naar een fysiek NUMA-domein en "vNUMA" om te verwijzen naar een gevirtualiseerd NUMA-domein. Op dezelfde manier zullen we de term "pCore" gebruiken om te verwijzen naar fysieke CPU-cores en "vCore" om te verwijzen naar gevirtualiseerde CPU-cores.

Fysiek is een HC-server 2 * 24-core Intel Xeon Platinum 8168-cpu's voor een totaal van 48 fysieke cores. Elke CPU is één pNUMA-domein en heeft uniforme toegang tot zes kanalen van DRAM. Intel Xeon Platinum-cpu's hebben een 4x grotere L2-cache dan in voorgaande generaties (256 KB/core -> 1 MB/core), terwijl ook de L3-cache wordt verminderd in vergelijking met eerdere Intel-cpu's (2,5 MB/core - > 1.375 MB/core).

De bovenstaande topologie draagt ook over naar de HC-serie hypervisor configuratie. Om ruimte te bieden voor de Azure-hypervisor om te werken zonder de VM te verstoren, reserveren we pCores 0-1 en 24-25 (dat wil zeggen de eerste 2 pCores op elke socket). Vervolgens wijzen we pNUMA-domeinen alle resterende kernen toe aan de VM. Zo zal de VM zien:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`kernen per VM

De VM heeft geen kennis dat pCores 0-1 en 24-25 niet werden gegeven aan het. Zo stelt het elke vNUMA bloot alsof het oorspronkelijk 22 kernen had.

Intel Xeon Platinum-, Gold- en Silver-cpu's introduceren ook een on-die 2D-mesh-netwerk voor communicatie binnen en buiten de CPU-socket. We raden procespinning aan voor optimale prestaties en consistentie. Procespinning werkt op VM's uit de HC-serie omdat het onderliggende silicium wordt blootgesteld aan de gastVM. Zie [Intel Xeon SP-architectuur](https://bit.ly/2RCYkiE)voor meer informatie.

Het volgende diagram toont de segregatie van kernen die zijn gereserveerd voor Azure Hypervisor en de VM uit de HC-serie.

![Scheiding van kernen gereserveerd voor Azure Hypervisor en HC-serie VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespecificaties

| Hardwarespecificaties          | HC-serie VM                     |
|----------------------------------|----------------------------------|
| Kernen                            | 44 (HT uitgeschakeld)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| CPU-frequentie (niet-AVX)          | 3,7 GHz (single core), 2,7-3,4 GHz (alle cores) |
| Geheugen                           | 8 GB/core (352 in totaal)            |
| Lokale schijf                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Netwerk                          | 50 Gb Ethernet (40 Gb bruikbaar) Azure tweede Gen SmartNIC*** |

## <a name="software-specifications"></a>Softwarespecificaties

| Softwarespecificaties     | HC-serie VM          |
|-----------------------------|-----------------------|
| Maximale MPI-taakgrootte            | 13200 cores (300 VM's in één VMSS met singlePlacementGroup=true) |
| MPI-ondersteuning                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Aanvullende kaders       | Unified Communication X, libfabric, PGAS |
| Azure Storage-ondersteuning       | Std + Premium (max 4 schijven) |
| OS-ondersteuning voor SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud-ondersteuning    | Ja                         |
| Azure-batchondersteuning         | Ja                         |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over HPC VM-formaten voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) en [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
