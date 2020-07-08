---
title: H-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de H-serie.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: f79dcb8886985d60a1ed82e1a77d231cf7d3ad24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678678"
---
# <a name="h-series"></a>H-serie

Virtuele machines uit de H-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3-processor kernen, tot 14 GB aan RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

ACU: 290-300

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Max. doorvoer schijf: IOPS | Maximum aantal Ethernet Nic's |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> voor mpi-toepassingen is het gereserveerde RDMA-back-end-netwerk ingeschakeld door het FDR InfiniBand-netwerk.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Ondersteunde installatie kopieën van besturings systemen (Linux)
 
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Zie [Enable InfiniBand](./workloads/hpc/enable-infiniband.md)voor meer informatie over het inschakelen van Infiniband, het instellen van MPI.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
