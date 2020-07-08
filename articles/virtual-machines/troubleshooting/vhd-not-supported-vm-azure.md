---
title: VHD wordt niet ondersteund bij het maken van een virtuele machine in azure | Microsoft Docs
description: Dit artikel helpt bij het corrigeren van VHD-fouten bij het uitvoeren van een virtuele machine in Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937530"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>VHD wordt niet ondersteund bij het maken van een virtuele machine in azure

Dit artikel helpt bij het corrigeren van VHD-fouten bij het uitvoeren van een virtuele machine in Windows of Linux.

## <a name="symptoms"></a>Symptomen

Wanneer u een virtuele machine in Microsoft Azure maakt met behulp van een geüploade VHD, mislukt de implementatie en wordt het volgende fout bericht weer gegeven: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Oorzaak

Dit probleem treedt op om een van de volgende redenen:

- De VHD voldoet niet aan de uitlijning van 1 MB (offset). De ondersteunde schijfgrootte moet 1 MB * N zijn. De schijf moet bijvoorbeeld 102,401 MB zijn.
- De VHD is beschadigd of wordt niet ondersteund. 

## <a name="resolution"></a>Oplossing

> [!NOTE]
> De klant moet deze stappen uitvoeren voordat de VHD naar Azure wordt geüpload om de volgende oplossing uit te voeren.

Om dit probleem op te lossen, wijzigt u het formaat van de schijf zodat deze voldoet aan de uitlijning van 1 MB:

- Gebruik de [Power shell-cmdlet resize-VHD](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd)om het probleem in Windows op te lossen. Houd er rekening mee dat het **wijzigen van de VHD** geen Azure PowerShell-cmdlet is.

  1. [Installeer de Hyper-V-rol op Windows Server](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [De virtuele schijf converteren naar een VHD met een vaste grootte](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Gebruik de [qemu-img opdracht](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)om het probleem in Linux op te lossen.

Raadpleeg de volgende artikelen voor meer informatie over het maken en uploaden van een VHD voor het maken van een Azure-VM:

- [Een Linux-VM uploaden en maken op basis van een aangepaste schijf kopie met behulp van de Azure CLI 1,0](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Een Windows Server-VHD maken en uploaden naar Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

Voortdurende problemen kunnen duiden op een beschadigde VHD. In dit geval wordt u aangeraden de VHD helemaal opnieuw te bouwen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Over Windows VHD](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [Over Linux-VHD](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)