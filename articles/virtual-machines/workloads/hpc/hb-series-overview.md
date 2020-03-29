---
title: VM-serie-serie - Azure Virtual Machines | Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707762"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-serie virtuele machines overzicht

Het maximaliseren van high performance compute (HPC) applicatie prestaties op AMD EPYC vereist een doordachte aanpak geheugenlokalisatie en procesplaatsing. Hieronder schetsen we de AMD EPYC-architectuur en onze implementatie ervan op Azure voor HPC-toepassingen. We gebruiken de term "pNUMA" om te verwijzen naar een fysiek NUMA-domein en "vNUMA" om te verwijzen naar een gevirtualiseerd NUMA-domein.

Fysiek is een HB-serie 2 * 32-core EPYC 7551 CPU's voor een totaal van 64 fysieke kernen. Deze 64 cores zijn onderverdeeld in 16 pNUMA-domeinen (8 per socket), die elk vier cores zijn en bekend staan als een "CPU Complex" (of "CCX"). Elke CCX heeft zijn eigen L3-cache, dat is hoe een OS een pNUMA/vNUMA-grens ziet. Een paar aangrenzende CCXs deelt toegang tot twee kanalen van fysieke DRAM (32 GB DRAM in HB-serie servers).

Om ruimte te bieden voor de Azure-hypervisor om te werken zonder de VM te verstoren, reserveren we fysieke pNUMA-domein 0 (de eerste CCX). Vervolgens wijzen we pNUMA-domeinen 1-15 (de overige CCX-eenheden) toe voor de VM. De VM ziet:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`kernen per VM

De VM, zelf, weet niet dat pNUMA 0 niet werd gegeven aan het. De VM begrijpt pNUMA 1-15 als vNUMA 0-14, met 7 vNUMA op vSocket 0 en 8 vNUMA op vSocket 1. Hoewel dit asymmetrisch is, moet uw besturingssysteem normaal opstarten en werken. Later in deze handleiding instrueren we hoe we mpi-toepassingen het beste kunnen uitvoeren op deze asymmetrische NUMA-lay-out.

Process pinning zal werken op HB-serie VM's, omdat we de onderliggende silicium as-is bloot stellen aan de gast VM. We raden procespinning aan voor optimale prestaties en consistentie.

Bekijk meer over [AMD EPYC-architectuur](https://bit.ly/2Epv3kC) en [multi-chip architecturen](https://bit.ly/2GpQIMb) op LinkedIn. Zie voor meer gedetailleerde informatie de [HPC Tuning Guide for AMD EPYC-processors.](https://bit.ly/2T3AWZ9)

Het volgende diagram toont de scheiding van kernen die zijn gereserveerd voor Azure Hypervisor en de VM uit de HB-serie.

![Scheiding van kernen gereserveerd voor Azure Hypervisor en HB-serie VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespecificaties

| HW Specificaties                | HB-serie VM                     |
|----------------------------------|----------------------------------|
| Kernen                            | 60 (SMT uitgeschakeld)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU-frequentie (niet-AVX)          | ~2,55 GHz (enkel + alle cores)   |
| Geheugen                           | 4 GB/core (240 in totaal)            |
| Lokale schijf                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Netwerk                          | 50 Gb Ethernet (40 Gb bruikbaar) Azure tweede Gen SmartNIC*** |

## <a name="software-specifications"></a>Softwarespecificaties

| SW Specificaties           |HB-serie VM           |
|-----------------------------|-----------------------|
| Maximale MPI-taakgrootte            | 6000 cores (100 virtuele machineschaalsets) 12000 cores (200 virtuele machineschaalsets)  |
| MPI-ondersteuning                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Aanvullende kaders       | Unified Communication X, libfabric, PGAS |
| Azure Storage-ondersteuning       | Std + Premium (max 4 schijven) |
| OS-ondersteuning voor SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud-ondersteuning    | Ja                         |
| Azure-batchondersteuning         | Ja                         |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over HPC VM-formaten voor [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) en [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
