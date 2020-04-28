---
title: Azure N-serie GPU-stuurprogramma-installatie voor Windows
description: Nvidia GPU-stuurprogramma's instellen voor VM's uit de N-serie met Windows Server of Windows in Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc11937410bf0307a00895e0ebd1f01a58bd1b1b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865783"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Nvidia GPU-stuurprogramma's installeren op VM's uit de N-serie met Windows 

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's met Windows, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd. De [NVIDIA GPU Driver Extension](../extensions/hpccompute-gpu-windows.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's zoals Azure PowerShell- of Azure Resource Manager-sjablonen. Bekijk de [NVIDIA GPU Driver Extension-documentatie](../extensions/hpccompute-gpu-windows.md) voor ondersteunde besturingssystemen en implementatiestappen.

Als u ervoor kiest GPU-stuurprogramma's handmatig te installeren, biedt dit artikel ondersteunde besturingssystemen, stuurprogramma's en installatie- en verificatiestappen. Handmatige driver setup informatie is ook beschikbaar voor [Linux VM's](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zie [GPU Windows VM-formaten](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor basisspecificaties, opslagcapaciteiten en schijfdetails. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installatie van stuurprogramma's

1. Maak verbinding via Extern bureaublad met elke VM uit de N-serie.

2. Download, haal en installeer het ondersteunde stuurprogramma voor uw Windows-besturingssysteem.

Na de installatie van GRID-stuurprogramma's op een VM is een herstart vereist. Na cuda-stuurprogramma-installatie is een herstart niet vereist.

## <a name="verify-driver-installation"></a>De installatie van het stuurprogramma controleren

Houd er rekening mee dat het Nvidia Control-paneel alleen toegankelijk is met de GRID-driverinstallatie. Als u CUDA-stuurprogramma's hebt geïnstalleerd, is het Nvidia-bedieningspaneel niet zichtbaar.

U de installatie van stuurprogramma's verifiëren in Apparaatbeheer. In het volgende voorbeeld wordt een succesvolle configuratie van de Tesla K80-kaart op een Azure NC VM weergegeven.

![GPU-stuurprogramma-eigenschappen](./media/n-series-driver-setup/GPU_driver_properties.png)

Als u de status van het GPU-apparaat wilt opvragen, voert u het hulpprogramma [voor de nvidia-smi-commandline](https://developer.nvidia.com/nvidia-system-management-interface) uit dat bij het stuurprogramma is geïnstalleerd.

1. Open een opdrachtprompt en wijzig de map **C:\Program Files\NVIDIA Corporation\NVSMI.**

2. Voer `nvidia-smi` uit. Als het stuurprogramma is geïnstalleerd, ziet u uitvoer vergelijkbaar met het volgende. De **GPU-Util** toont **0%** tenzij u momenteel een GPU-workload op de VM uitvoert. Uw stuurprogrammaversie en GPU-details kunnen afwijken van de weergegeven versie.

![NVIDIA-apparaatstatus](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-netwerkconnectiviteit

RDMA-netwerkconnectiviteit kan worden ingeschakeld op VM's die geschikt zijn voor RDMA-compatibele N-serie, zoals NC24r die is geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsingsgroep in een virtuele machineschaalset. De HpcVmDrivers-extensie moet worden toegevoegd om Windows-netwerkapparaatstuurprogramma's te installeren waarmee RDMA-connectiviteit kan worden ingeschakeld. Als u de VM-extensie wilt toevoegen aan een VM met RDMA-functie, gebruikt u [Azure PowerShell-cmdlets](/powershell/azure/overview) voor Azure Resource Manager.

Ga als bedoeld bij het installeren van de nieuwste versie 1.1 HpcVMDrivers-extensie op een bestaande VM die geschikt is voor RDMA met de naam myVM in de regio West-VS:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Zie [Virtuele machine-extensies en -functies voor Windows voor](extensions-features.md)meer informatie.

Het RDMA-netwerk ondersteunt MPI-verkeer (Message Passing Interface) voor toepassingen die worden uitgevoerd met [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) of Intel MPI 5.x. 


## <a name="next-steps"></a>Volgende stappen

* Ontwikkelaars die GPU-versnelde applicaties bouwen voor de NVIDIA Tesla GPU's kunnen ook de nieuwste [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)downloaden en installeren. Zie voor meer informatie de [CUDA Installation Guide](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


