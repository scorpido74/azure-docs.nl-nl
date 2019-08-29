---
title: Setup van het GPU-stuur programma voor Azure N-Series voor Windows | Microsoft Docs
description: NVIDIA GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie met Windows Server of Windows in azure
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc6e37b088c6bcbb2de4693eb50be661db869ecd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089192"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>NVIDIA GPU-Stuur Programma's installeren op Vm's met N-serie waarop Windows wordt uitgevoerd 

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure N-serie die worden uitgevoerd op Windows, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd. Met de [uitbrei ding NVIDIA GPU-stuur programma](../extensions/hpccompute-gpu-windows.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](../extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen.

Als u ervoor kiest om GPU-Stuur Programma's hand matig te installeren, worden in dit artikel ondersteunde besturings systemen, stuur Programma's en installatie-en verificatie stappen beschreven. Informatie over hand matige installatie van Stuur Programma's is ook beschikbaar voor [Linux-vm's](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zie [GPU Windows VM](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-grootten voor basis specificaties, opslag capaciteit en schijf Details. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installatie van Stuur Programma's

1. Verbind door Extern bureaublad naar elke VM van de N-serie.

2. Down load, pak en installeer het ondersteunde stuur programma voor uw Windows-besturings systeem.

Nadat het raster stuur programma is geïnstalleerd op een VM, moet opnieuw worden opgestart. Na de installatie van het CUDA-stuur programma is het niet nodig om opnieuw op te starten.

## <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

U kunt de installatie van Stuur Programma's controleren in Apparaatbeheer. In het volgende voor beeld ziet u een geslaagde configuratie van de Tesla K80-kaart op een Azure NC-VM.

![Eigenschappen van GPU-stuur programma](./media/n-series-driver-setup/GPU_driver_properties.png)

Als u de status van het GPU-apparaat wilt opvragen, voert u het [NVIDIA-SMI-](https://developer.nvidia.com/nvidia-system-management-interface) opdracht regel programma uit dat is geïnstalleerd met het stuur programma.

1. Open een opdracht prompt en ga naar de map **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Voer `nvidia-smi` uit. Als het stuur programma is geïnstalleerd, ziet u uitvoer die vergelijkbaar is met de volgende. Met de **GPU-util** wordt **0%** weer gegeven, tenzij u momenteel een GPU-werk belasting op de VM uitvoert. De versie-en GPU-Details van uw stuur programma kunnen afwijken van de gegevens die worden weer gegeven.

![Status van NVIDIA-apparaten](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-netwerk verbinding

RDMA-netwerk connectiviteit kan worden ingeschakeld op met RDMA geschikte virtuele machines uit de N-serie, zoals NC24r geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsings groep in een virtuele-machine schaalset. De HpcVmDrivers-extensie moet worden toegevoegd om Windows-netwerk Stuur Programma's te installeren waarmee RDMA-connectiviteit mogelijk wordt gemaakt. Gebruik [Azure PowerShell](/powershell/azure/overview) -cmdlets voor Azure Resource Manager om de VM-extensie toe te voegen aan een RDMA-VM van de N-serie.

Als u de meest recente versie 1,1 HpcVMDrivers-extensie wilt installeren op een bestaande, met RDMA geschikte virtuele machine met de naam myVM in de regio vs-West:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Zie voor meer informatie [virtuele-machine uitbreidingen en functies voor Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Het RDMA-netwerk ondersteunt MPI-verkeer (Message Passing Interface) voor toepassingen die worden uitgevoerd met [micro soft mpi](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) of Intel mpi 5. x. 


## <a name="next-steps"></a>Volgende stappen

* Ontwikkel aars die GPU-versnelde toepassingen bouwen voor de NVIDIA Tesla-Gpu's, kunnen ook de meest recente [CUDA-Toolkit](https://developer.nvidia.com/cuda-downloads)downloaden en installeren. Zie de [installatie handleiding voor CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)voor meer informatie.


