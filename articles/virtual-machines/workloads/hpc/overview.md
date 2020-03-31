---
title: High-performance computing op H-serie VM's - Azure Virtual Machines
description: Meer informatie over de functies en mogelijkheden van Vm's uit de H-serie die zijn geoptimaliseerd voor HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271020"
---
# <a name="high-performance-computing-on-h-series-vms"></a>High-performance computing op H-serie VM's

High-performance computing (HPC) op HB-serie en HC-serie VM's maken de meest geoptimaliseerde HPC-prestaties van alle VM's op Azure mogelijk. HPC geoptimaliseerde VM's worden gebruikt om enkele van de moeilijkste wiskundige problemen op te lossen, zoals: vloeistofdynamica, olie- en gassimulaties en weermodellering.

Dit artikel behandelt een aantal belangrijke kenmerken van HB-serie en HC-serie VM's, waarom deze VM's goed presteren in HPC-scenario's en hoe aan de slag te gaan.

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

HB-serie en HC-serie VM's zijn ontworpen om de beste HPC-prestaties, mpi-schaalbaarheid (message passing interface) en kostenefficiëntie voor HPC-workloads te bieden.

### <a name="message-passing-interface"></a>Interface voor het doorgeven van berichten

HB-series en HC-series ondersteunen bijna alle MPI-typen en -versies. Enkele van de meest voorkomende, ondersteunde MPI-typen zijn: OpenMPI, MVAPICH2, Platform MPI, Intel MPI en alle RDMA-werkwoorden (Remote Direct Memory Access). Zie [Interface voor het doorgeven van berichten instellen voor HPC voor](setup-mpi.md)meer informatie.

### <a name="rdma-and-infiniband"></a>RDMA en InfiniBand

De RDMA-interface is standaard op HB-serie en HC-serie VM's. RDMA-geschikte exemplaren communiceren via een InfiniBand-netwerk en werken tegen verbeterde datasnelheden (EDR) voor virtuele machines uit de HB-serie en HC-serie. Rdma-geschikte exemplaren kunnen de schaalbaarheid en prestaties van sommige MPI-toepassingen verbeteren.

De InfiniBand-configuratie die HB-serie en HC-serie VM's ondersteunt, blokkeren vetbomen met een ontwerp met een lage diameter voor consistente RDMA-prestaties.

Zie [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie over het instellen van InfiniBand op uw HB-serie of HC-serie VM's.

## <a name="get-started"></a>Aan de slag

Bepaal eerst welke H-serie VM je gaat gebruiken. Zie [hb-serie overzicht](hb-series-overview.md) en [HC-serie overzicht](hc-series-overview.md)voor meer informatie over hpc-geoptimaliseerde VM's. Zie [VM-formaten met hoge prestaties](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)voor specificaties .

Nadat u een VM voor uw toepassing hebt geselecteerd en gemaakt, moet u deze configureren door InfiniBand in te schakelen. Zie [InfiniBand inschakelen](enable-infiniband.md)voor meer informatie over het inschakelen van InfiniBand op zowel Windows- als Linux-VM's.

Een essentieel onderdeel van HPC-workloads is MPI. HB-series en HC-series ondersteunen bijna alle MPI-typen en -versies. Zie [Interface voor het doorgeven van berichten instellen voor HPC voor](setup-mpi.md)meer informatie.

Zodra u uw VM-serie hebt gekozen, Infiniband en MPI hebt ingesteld, bent u klaar om te beginnen met het bouwen van uw HPC-workloads.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [hb-serie overzicht](hb-series-overview.md) en [HC-serie overzicht](hc-series-overview.md) om meer te weten te komen over de belangrijkste verschillen en specificaties.

- Zie [High Performance Computing (HPC) op Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)voor een hoger niveau, een architecturale weergave van het uitvoeren van HPC-workloads.
