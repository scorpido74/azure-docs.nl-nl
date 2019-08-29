---
title: Azure Windows VM-grootten-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor virtuele Windows High Performance Computing-machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 0c5398caf85566f78c7ebe9e8ba114a53d2f6271
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088526"
---
# <a name="high-performance-compute-vm-sizes"></a>High Performance Compute-VM-grootten

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Besturings systeem** : Windows Server 2016 op alle bovenstaande vm's van de HPC-serie. Windows Server 2012 R2, Windows Server 2012 wordt ook ondersteund op Vm's waarvoor geen SR-IOV is ingeschakeld (dus met uitzonde ring van HB en HC).

* **Mpi** : met de VM-grootten van SR-IOV op Azure (HB, HC) kunt u bijna elke mpi gebruiken met Mellanox OFED.
Op Vm's waarvoor geen SR-IOV is ingeschakeld, gebruiken ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface om te communiceren tussen instanties. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's.

* **INFINIBANDDRIVERWINDOWS VM extension** -op RDMA-compatibele vm's, voegt u de InfiniBandDriverWindows-extensie toe om InfiniBand in te scha kelen. Met deze Windows VM-extensie worden Windows-netwerk directe Stuur programma's (op niet-SR-IOV-Vm's) of Mellanox OFED-Stuur programma's (op SR-IOV-Vm's) geïnstalleerd voor RDMA-connectiviteit.
In bepaalde implementaties van A8-en A9-instanties wordt de uitbrei ding HpcVmDrivers automatisch toegevoegd. Houd er rekening mee dat de HpcVmDrivers VM-extensie wordt afgeschaft. het wordt niet bijgewerkt. U kunt [Azure PowerShell](/powershell/azure/overview) -cmdlets gebruiken om de VM-extensie toe te voegen aan een virtuele machine. 

  Met de volgende opdracht wordt de meest recente versie 1,0 InfiniBandDriverWindows-extensie geïnstalleerd op een bestaande virtuele RDMA-VM met de naam *myVM* die is geïmplementeerd in de resource groep met de naam *myResourceGroup* in de regio *VS-West* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  VM-extensies kunnen ook worden opgenomen in Azure Resource Manager sjablonen voor een eenvoudige implementatie, met het volgende JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Met de volgende opdracht wordt de meest recente versie 1,0 InfiniBandDriverWindows-extensie geïnstalleerd op alle virtuele machines met RDMA-functionaliteit in een bestaande VM-schaalset met de naam *myVMSS* geïmplementeerd in de resource groep met de naam *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Zie [extensies en functies van virtuele machines](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie. U kunt ook werken met uitbrei dingen voor virtuele machines die zijn geïmplementeerd in het [klassieke implementatie model](classic/manage-extensions.md).

* **RDMA-netwerk adres ruimte** : het RDMA-netwerk in azure behoudt de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.


### <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van Windows HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

* **Virtuele machines** : implementeer de met RDMA geschikte HPC-vm's in dezelfde beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service. 

* **Virtuele-machine schaal sets** : in een schaalset voor virtuele machines moet u ervoor zorgen dat u de implementatie beperkt tot één plaatsings groep. Stel bijvoorbeeld in een resource manager-sjabloon de `singlePlacementGroup` eigenschap in op. `true` 

* **Mpi tussen virtuele machines** : als er een mpi-communicatie nodig is tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde beschikbaarheidsset of op dezelfde schaalset van de virtuele machine bevinden.

* **Azure CycleCloud** : Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren op Windows-knoop punten.

* **Azure batch** : maak een [Azure batch](/azure/batch/) groep om mpi-workloads uit te voeren op Windows Server Compute-knoop punten. Zie voor meer informatie [gebruik van RDMA-compatibele of GPU-ingeschakelde instanties in batch-Pools](../../batch/batch-pool-compute-intensive-sizes.md). Zie ook het [batch Shipyard](https://github.com/Azure/batch-shipyard) -project, voor het uitvoeren van op containers gebaseerde workloads op batch.

* **Micro soft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-mpi die gebruikmaakt van het Azure RDMA-netwerk wanneer het is geïmplementeerd op RDMA-compatibele Windows-vm's. Zie voor beelden van implementaties [een Windows RDMA-cluster met HPC Pack instellen voor het uitvoeren van MPI-toepassingen](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Andere grootten
- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](../virtual-machines-windows-sizes-memory.md)
- [Geoptimaliseerde opslag](../virtual-machines-windows-sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [een Windows RDMA-cluster met HPC Pack instellen](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor het uitvoeren van MPI-toepassingen voor controle lijsten voor het gebruik van de compute-intensieve instanties met HPC Pack op Windows Server.

- Als u computerintensieve instanties wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch, raadpleegt u [taken met meerdere instanties gebruiken om mpi-toepassingen (Message Passing Interface) uit te voeren in azure batch](../../batch/batch-mpi.md).

- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
