---
title: H-serie - Azure Virtual Machines
description: Specificaties voor de H-serie VM's.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470500"
---
# <a name="h-series"></a>H-serie

Vm's uit de H-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen-eisen per kern. Vm's uit de H-serie zijn voorzien van 8 of 16 Intel Xeon E5 2667 v3-processorkernen, tot 14 GB RAM per CPU-kern en geen hyperthreading. H-serie beschikt over 56 Gb/sec Mellanox FDR InfiniBand in een niet-blokkerende vetboom configuratie voor consistente RDMA prestaties. Vm's uit de H-serie ondersteunen Intel MPI 5.x en MS-MPI.

ACU: 290-300

Premium opslag: niet ondersteund

Premium Storage Caching: niet ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugenbandbreedte GB/s | Cpu-frequentie baseren (GHz) | All-cores frequentie (GHz, piek) | Single-core frequentie (GHz, piek) | RDMA-prestaties (Gb/s) | ONDERSTEUNING VOOR MPI | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Max Ethernet-NIC's |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1.</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1.</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Voor MPI-toepassingen wordt het speciale RDMA-backend-netwerk ingeschakeld door het FDR InfiniBand-netwerk.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Ondersteunde OS-afbeeldingen (Linux)
 
De Azure Marketplace heeft veel Linux-distributies die RDMA-connectiviteit ondersteunen:
  
* **CentOS-gebaseerde HPC** - Voor niet-SR-IOV-geschikte VM's, CentOS-gebaseerde versie 6.5 HPC of een latere versie, tot 7,5 zijn geschikt. Voor VM's uit de H-serie worden versies 7.1 tot en met 7.5 aanbevolen. RDMA drivers en Intel MPI 5.1 zijn geïnstalleerd op de VM.
  Voor SR-IOV VM's wordt CentOS-HPC 7.6 geoptimaliseerd en vooraf geladen met de RDMA-stuurprogramma's en verschillende MPI-pakketten geïnstalleerd.
  Voor andere RHEL/CentOS VM-afbeeldingen voegt u de InfiniBandLinux-extensie toe om InfiniBand in te schakelen. Deze Linux VM-extensie installeert Mellanox OFED-drivers (op SR-IOV VM's) voor RDMA-connectiviteit. De volgende PowerShell-cmdlet installeert de nieuwste versie (versie 1.0) van de InfiniBandDriverLinux-extensie op een bestaande VM die geschikt is voor RDMA. De VM die geschikt is voor RDMA heet *myVM* en wordt als volgt geïmplementeerd in de resourcegroep *myResourceGroup* in de regio *West-VS:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Vm-extensies kunnen ook worden opgenomen in Azure Resource Manager-sjablonen voor eenvoudige implementatie met het volgende JSON-element:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Met de volgende opdracht wordt de nieuwste versie 1.0 InfiniBandDriverLinux-extensie geïnstalleerd op alle VM's die geschikt zijn voor RDMA in een bestaande virtuele machineschaalset met de naam *myVMSS* geïmplementeerd in de brongroep *myResourceGroup:*
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Op de CentOS-gebaseerde HPC-afbeeldingen worden kernelupdates uitgeschakeld in het **configuratiebestand van Yum.** Dit komt omdat Linux RDMA-stuurprogramma's worden gedistribueerd als een RPM-pakket en stuurprogramma-updates mogelijk niet werken als de kernel wordt bijgewerkt.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 voor HPC, SLES 12 SP3 voor HPC (Premium), SLES 12 SP1 voor HPC, SLES 12 SP1 voor HPC (Premium), SLES 12 SP4 en SLES 15. RDMA-stuurprogramma's zijn geïnstalleerd en Intel MPI-pakketten worden gedistribueerd op de VM. Installeer MPI door de volgende opdracht uit te voeren:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Rdma-stuurprogramma's configureren op de VM en registreer u bij Intel om Intel MPI te downloaden:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Zie [InfiniBand](./workloads/hpc/enable-infiniband.md)inschakelen voor meer informatie over het inschakelen van InfiniBand.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
