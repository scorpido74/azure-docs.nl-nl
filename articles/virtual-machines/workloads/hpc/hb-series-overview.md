---
title: Overzicht van de VM van de HB-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over de preview-ondersteuning voor de VM-grootte van de HB-serie in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707762"
---
# <a name="hb-series-virtual-machines-overview"></a>Overzicht van virtuele machines uit de HB-serie

Voor het maximaliseren van de prestaties van HPC-toepassingen (High Performance Compute) op AMD EPYC is een gerichte benadering van geheugen en proces plaatsing vereist. Hieronder wordt een overzicht gegeven van de AMD EPYC-architectuur en onze implementatie van IT op Azure voor HPC-toepassingen. We gebruiken de term ' pNUMA ' om te verwijzen naar een fysiek NUMA-domein en ' vNUMA ' om te verwijzen naar een gevirtualiseerde NUMA-domein.

Fysiek is een HB-serie 2 * 32-core EPYC 7551 Cpu's voor een totaal van 64 fysieke kernen. Deze 64 kernen zijn onderverdeeld in 16 pNUMA-domeinen (8 per socket), die elk vier kernen zijn en ook wel een ' CPU complex ' (of ' CCX ' genoemd). Elk CCX heeft zijn eigen L3-cache, waarmee wordt aangegeven dat een besturings systeem een pNUMA/vNUMA-grens ziet. Een paar aaneengesloten CCXs-shares hebben toegang tot twee kanalen van fysieke DRAM (32 GB DRAM in servers van de HB-serie).

Om ruimte te maken voor de werking van de Azure-Hyper Visor zonder dat de virtuele machine wordt verstoord, reserveren we fysiek pNUMA domein 0 (de eerste CCX). Vervolgens wijst u pNUMA domeinen 1-15 (de resterende CCX-eenheden) toe voor de virtuele machine. De VM ziet er als volgt uit:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`kernen per VM

De virtuele machine is zelf niet bekend dat pNUMA 0 niet aan het apparaat is toegewezen. De VM begrijpt pNUMA 1-15 als vNUMA 0-14, met 7 vNUMA op vSocket 0 en 8 vNUMA op vSocket 1. Hoewel dit asymmetrisch is, moet het besturings systeem worden opgestart en normaal functioneren. Verderop in deze hand leiding wordt u aangeraden om MPI-toepassingen uit te voeren op deze asymmetrische NUMA-indeling.

Het vastmaken van processen werkt op Vm's uit de HB-serie, omdat we het onderliggende silicium beschikbaar maken voor de gast-VM. Het wordt ten zeerste aanbevolen om proces vastmaken voor optimale prestaties en consistentie.

Meer informatie over de [AMD EPYC-architectuur](https://bit.ly/2Epv3kC) en [architecturen met meerdere chips](https://bit.ly/2GpQIMb) op LinkedIn. Zie de [HPC-afstemmings handleiding voor AMD EPYC-processors](https://bit.ly/2T3AWZ9)voor meer gedetailleerde informatie.

In het volgende diagram ziet u de schei ding van kernen die zijn gereserveerd voor Azure Hyper Visor en de HB-serie.

![Schei ding van kern geheugens gereserveerd voor Azure Hyper Visor en HB-serie VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespecificaties

| HW-specificaties                | VM van de HB-serie                     |
|----------------------------------|----------------------------------|
| Kernen                            | 60 (SMT uitgeschakeld)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU-frequentie (niet-AVX)          | ~ 2,55 GHz (enkele + alle kernen)   |
| Geheugen                           | 4 GB/core (240 in totaal)            |
| Lokale schijf                       | NVMe van 700 GB                      |
| InfiniBand                       | 100 GB EDR Mellanox Connectx-5 * * |
| Netwerk                          | 50 GB Ethernet (40 GB bruikbaar) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Software specificaties

| SW-specificaties           |VM van de HB-serie           |
|-----------------------------|-----------------------|
| Maximale grootte van MPI-taak            | 6000 kernen (100 virtuele-machine schaal sets) 12000 kernen (200 virtuele-machine schaal sets)  |
| MPI-ondersteuning                 | MVAPICH2, OpenMPI, MPICH, platform MPI, Intel MPI  |
| Aanvullende Frameworks       | Unified Communication X, libfabric, PGAS |
| Ondersteuning voor Azure Storage       | Std + Premium (max. 4 schijven) |
| Ondersteuning van het besturings systeem voor SRIOV RDMA   | CentOS/RHEL 7,6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Ondersteuning voor Azure CycleCloud    | Ja                         |
| Ondersteuning voor Azure Batch         | Ja                         |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over HPC VM-grootten voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) en [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
