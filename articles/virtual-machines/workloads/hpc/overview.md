---
title: High Performance Computing op VM's uit de H-serie - Azure Virtual Machines
description: Meer informatie over de functies en mogelijkheden van VM's uit de H-serie die zijn geoptimaliseerd voor HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: 1f88496a6c474194089f20e128b2b92ec61e8559
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083426"
---
# <a name="high-performance-computing-on-h-series-vms"></a>High Performance Computing op VM's uit de H-serie

High Performance Computing (HPC) op VM's uit de HB- en HC-serie met de meest geoptimaliseerde HPC-prestaties van alle VM's op Azure. Voor HPC geoptimaliseerde VM's worden gebruikt om de moeilijkste wiskundige problemen op te lossen, zoals: vloeistofdynamica, olie- en gassimulaties en weermodellen.

In dit artikel komen enkele belangrijke functies van VM's uit de HB- en HC-serie aan bod en wordt uitgelegd waarom deze VM's goed presteren in HPC-scenario's en hoe u aan de slag kunt gaan.

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

VM's uit de HB- en HC-serie leveren de beste HPC-prestaties en MPI-schaalbaarheid (Message Passing Interface) en bieden de beste kostenefficiëntie voor HPC-workloads.

### <a name="message-passing-interface"></a>Message Passing Interface

De HB- en HC-serie ondersteunen bijna alle MPI-typen en -versies. Enkele van de meest voorkomende ondersteunde MPI-typen zijn: OpenMPI, MVAPICH2, Platform MPI, Intel MPI en alle RDMA-bewerkingen (Remote Direct Memory Access). Zie [Message Passing Interface instellen voor HPC](setup-mpi.md) voor meer informatie.

### <a name="rdma-and-infiniband"></a>RDMA en InfiniBand

De RDMA-interface is de standaardinterface op VM's uit de HB-serie en HC-serie. Voor RDMA geschikte instanties communiceren via een InfiniBand-netwerk, met hoge datasnelheden (EDR) voor VM's uit de HB- en HC-serie. Voor RDMA geschikte instanties kunnen de schaalbaarheid en prestaties van sommige MPI-toepassingen verhogen.

De InfiniBand-configuratie die VM's uit de HB- en HC-serie ondersteunt, bestaat uit niet-blokkerende FAT-structuren met een ontwerp met een lage diameter voor consistente RDMA-prestaties.

Zie [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie over het instellen van InfiniBand op uw virtuele machines uit de HB- of HC-serie.

## <a name="get-started"></a>Aan de slag

Bepaal eerst welke H-serie-VM u wilt gebruiken. Zie [Overzicht van de HB-serie](hb-series-overview.md) en [Overzicht van de HC-serie](hc-series-overview.md) voor meer informatie over voor HPC geoptimaliseerde VM's. Zie [High Performance Compute VM-grootten](../../sizes-hpc.md).

Zodra u een VM voor uw toepassing hebt geselecteerd en gemaakt, moet u deze configureren door InfiniBand in te schakelen. Zie [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie over het inschakelen van InfiniBand op virtuele Windows- en Linux-machines.

Een essentieel onderdeel van HPC-workloads is MPI. De HB- en HC-serie ondersteunen bijna alle MPI-typen en -versies. Zie [Message Passing Interface instellen voor HPC](setup-mpi.md) voor meer informatie.

Zodra u uw VM-serie hebt gekozen en Infiniband en MPI hebt ingesteld, kunt u beginnen met het bouwen van uw HPC-workloads.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [Overzicht van de HB-serie](hb-series-overview.md) en [Overzicht van de HC-serie](hc-series-overview.md) voor meer informatie over belangrijke verschillen en specificaties.

- Zie [High Performance Computing (HPC) op Azure](/azure/architecture/topics/high-performance-computing/) voor een gedetailleerdere architectuurweergave van HPC-workloads die worden uitgevoerd.
