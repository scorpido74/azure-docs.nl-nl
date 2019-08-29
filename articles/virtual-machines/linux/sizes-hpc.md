---
title: VM-grootten van Azure Linux-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor Linux High Performance Computing virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: ee99869c2b7a7b3ab38fdd9eae0687862ea53819
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100862"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>High Performance Computing-grootten van virtuele machines

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Met de VM-grootten van SR-IOV in azure kunt u bijna alle MPI gebruiken.
Op Vm's waarvoor geen SR-IOV is ingeschakeld, worden alleen Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure Linux RDMA-Stuur Programma's.


### <a name="supported-os-images"></a>Ondersteunde installatie kopieën van besturings systemen
 
Azure Marketplace heeft veel Linux-distributies die ondersteuning bieden voor RDMA-connectiviteit:
  
* **Op CentOS gebaseerde HPC** : voor virtuele machines die niet met SR-IOV kunnen worden gebruikt, zijn op CentOS gebaseerde versie 6,5 HPC of een hogere versie 7,5 geschikt. Voor virtuele machines met de H-serie wordt versie 7,1 tot 7,5 aanbevolen. RDMA-Stuur Programma's en Intel MPI 5,1 zijn geïnstalleerd op de virtuele machine.
  Voor virtuele machines met SR-IOV wordt CentOS-HPC 7,6 geoptimaliseerd en vooraf geladen met de RDMA-Stuur Programma's en verschillende MPI-pakketten die zijn geïnstalleerd.
  Voor andere VM-installatie kopieën van RHEL/CentOS voegt u de extensie InfiniBandLinux toe om InfiniBand in te scha kelen. Deze Linux VM-extensie installeert Mellanox OFED-Stuur programma's (op SR-IOV-Vm's) voor RDMA-connectiviteit. Met de volgende Power shell-cmdlet wordt de nieuwste versie (versie 1,0) van de InfiniBandDriverLinux-extensie geïnstalleerd op een bestaande, RDMA-compatibele VM. De RDMA-capable VM heet *myVM* en wordt als volgt geïmplementeerd in de resource groep met de naam *myResourceGroup* in de regio *VS West* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  VM-extensies kunnen ook worden opgenomen in Azure Resource Manager sjablonen voor een eenvoudige implementatie met het volgende JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Met de volgende opdracht wordt de meest recente versie 1,0 InfiniBandDriverLinux-extensie geïnstalleerd op alle virtuele machines met RDMA-functionaliteit in een bestaande VM-schaalset met de naam *myVMSS* geïmplementeerd in de resource groep met de naam *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Op de op CentOS gebaseerde HPC-installatie kopieën worden kernel-updates uitgeschakeld in het configuratie bestand **yum** . Dit komt doordat Linux RDMA-Stuur Programma's worden gedistribueerd als een RPM-pakket, en updates van Stuur Programma's kunnen mogelijk niet worden uitgevoerd als de kernel wordt bijgewerkt.
  >
  

* **SuSE Linux Enterprise Server** -SLES 12 SP3 voor HPC, SLES 12 SP3 voor HPC (Premium), SLES 12 SP1 voor HPC, SLES 12 SP1 voor HPC (Premium), SLES 12 SP4 en SLES 15. RDMA-Stuur Programma's worden geïnstalleerd en Intel MPI-pakketten worden gedistribueerd op de VM. Installeer MPI door de volgende opdracht uit te voeren:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16,04 LTS, 18,04 LTS. Stuur Programma's voor RDMA configureren op de VM en registreren bij Intel om Intel MPI te downloaden:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Zie [Enable InfiniBand](../workloads/hpc/enable-infiniband.md)voor meer informatie over het inschakelen van Infiniband, het instellen van MPI.


### <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van Linux HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

* **Virtuele machines** : implementeer de met RDMA geschikte HPC-vm's in dezelfde beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service. 

* **Virtuele-machine schaal sets** : in een schaalset voor virtuele machines moet u ervoor zorgen dat u de implementatie beperkt tot één plaatsings groep. Stel bijvoorbeeld in een resource manager-sjabloon de `singlePlacementGroup` eigenschap in op. `true` 

* **Mpi tussen virtuele machines** : als er een mpi-communicatie nodig is tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde beschikbaarheidsset of op dezelfde schaalset van de virtuele machine bevinden.

* **Azure CycleCloud** : Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren op Linux-knoop punten.

* **Azure batch** : maak een [Azure batch](/azure/batch/) groep voor het uitvoeren van MPI-workloads op Linux-reken knooppunten. Zie voor meer informatie [gebruik van RDMA-compatibele of GPU-ingeschakelde instanties in batch-Pools](../../batch/batch-pool-compute-intensive-sizes.md). Zie ook het [batch Shipyard](https://github.com/Azure/batch-shipyard) -project, voor het uitvoeren van op containers gebaseerde workloads op batch.

* **Micro soft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) ondersteunt diverse Linux-distributies die worden uitgevoerd op reken knooppunten die zijn geïmplementeerd in met RDMA geschikte Azure-vm's, die worden beheerd door een Windows Server-hoofd knooppunt. Zie voor een voorbeeld implementatie [HPC Pack Linux RDMA-cluster maken in azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Aandachtspunten voor netwerken
* Op niet-SR-IOV, met RDMA ingeschakelde Linux Vm's in azure, is eth1 gereserveerd voor RDMA-netwerk verkeer. Wijzig geen eth1-instellingen of informatie in het configuratie bestand dat verwijst naar dit netwerk.
* Op virtuele machines met SR-IOV (HB en HC-serie) wordt ib0 gereserveerd voor RDMA-netwerk verkeer.
* Het RDMA-netwerk in azure reserveert de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.
* Afhankelijk van uw keuze van het hulp programma voor cluster beheer is er mogelijk aanvullende systeem configuratie nodig om MPI-taken uit te voeren. Zo moet u in een cluster met Vm's mogelijk een vertrouwens relatie tussen de cluster knooppunten tot stand brengen door SSH-sleutels te genereren of door wacht woordloze SSH-aanmeldingen in te richten.


## <a name="other-sizes"></a>Andere grootten
- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het instellen, optimaliseren en schalen van [HPC-workloads](../workloads/hpc/configure.md) op Azure.
- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
