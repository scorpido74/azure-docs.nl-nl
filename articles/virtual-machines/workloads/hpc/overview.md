---
title: High-Performance Computing op virtuele machines uit de H-serie-Azure Virtual Machines
description: Meer informatie over de functies en mogelijkheden van virtuele machines van de H-serie die zijn geoptimaliseerd voor HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76271020"
---
# <a name="high-performance-computing-on-h-series-vms"></a>High-Performance Computing op virtuele machines uit de H-serie

High Performance Computing (HPC) op Vm's uit de HB-serie en HC-serie bieden de meest geoptimaliseerde HPC-prestaties van alle Vm's op Azure. Met HPC geoptimaliseerde Vm's worden de meest moeilijke wiskundige problemen opgelost, zoals: vloeistof dynamiek, olie-en gasmotoren-simulaties en weer modellen.

Dit artikel heeft betrekking op enkele belang rijke functies van Vm's uit de HB-serie en HC-serie, waarom deze Vm's goed pres teren in HPC-scenario's en hoe u aan de slag kunt gaan.

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

Virtuele machines uit de HB-serie en HC-serie zijn ontworpen om de beste HPC-prestaties, een MPI-schaal baarheid (Message Passing Interface) en kosten efficiëntie te bieden voor HPC-workloads.

### <a name="message-passing-interface"></a>Berichten door geven interface

HB-Series en HC-serie ondersteunen bijna alle MPI-typen en-versies. Enkele van de meest voorkomende, ondersteunde MPI-typen zijn: OpenMPI, MVAPICH2, platform MPI, Intel MPI en alle RDMA-werk woorden (Remote Direct Memory Access). Zie voor meer informatie [instellen Message Passing Interface voor HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA en InfiniBand

De RDMA-interface is standaard op Vm's uit de HB-serie en HC-serie. RDMA-compatibele instanties communiceren via een InfiniBand-netwerk, op uitgebreide gegevens tarieven (EDR) voor virtuele machines uit de HB-serie en HC-serie. RDMA-compatibele instanties kunnen de schaal baarheid en prestaties van sommige MPI-toepassingen verhogen.

De InfiniBand-configuratie die virtuele machines uit de HB-serie en HC-serie ondersteunt, is niet-blokkerende Fat-structuren met een ontwerp met een lage diameter voor consistente RDMA-prestaties.

Zie [InfiniBand inschakelen](enable-infiniband.md) voor meer informatie over het instellen van Infiniband op uw virtuele machines uit de HB-of HC-serie.

## <a name="get-started"></a>Aan de slag

Bepaal eerst welke H-serie-VM u wilt gebruiken. Zie overzicht van de [HB-serie](hb-series-overview.md) en [het HC-serie-overzicht](hc-series-overview.md)voor meer informatie over geoptimaliseerde HPC-vm's. Zie [High Performance Compute VM sizes](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)voor specificaties.

Wanneer u een virtuele machine voor uw toepassing hebt geselecteerd en gemaakt, moet u deze configureren door middel van InfiniBand. Zie [Enable InfiniBand](enable-infiniband.md)voor meer informatie over het inschakelen van Infiniband op virtuele Windows-en Linux-machines.

Een essentieel onderdeel van HPC-workloads is MPI. HB-Series en HC-serie ondersteunen bijna alle MPI-typen en-versies. Zie voor meer informatie [instellen Message Passing Interface voor HPC](setup-mpi.md).

Nadat u uw VM-serie hebt gekozen, stelt u Infiniband en MPI in, u kunt nu beginnen met het bouwen van uw HPC-workloads.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [overzicht van de HB-serie](hb-series-overview.md) en het [HC-serie-overzicht](hc-series-overview.md) voor meer informatie over belang rijke verschillen en specificaties.

- Zie [High Performance Computing (HPC) in azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)voor een hoger niveau, de architectuur weergave voor het uitvoeren van HPC-workloads.
