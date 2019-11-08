---
title: Een beheerde Azure-VM maken op basis van een gegeneraliseerde on-premises VHD | Microsoft Docs
description: Upload een gegeneraliseerde VHD naar Azure en gebruik deze om nieuwe virtuele machines te maken in het Resource Manager-implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ead44c321ffb7afb69295d1bf8c0e1acd26cb9ce
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749067"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Een gegeneraliseerde VHD uploaden en gebruiken om nieuwe virtuele machines te maken in azure

In dit artikel wordt stapsgewijs beschreven hoe u Power shell gebruikt om een VHD van een gegeneraliseerde VM naar Azure te uploaden, een installatie kopie van de VHD te maken en een nieuwe VM te maken op basis van die installatie kopie. U kunt een VHD uploaden die is geëxporteerd uit een on-premises virtualisatie hulpprogramma of vanuit een andere cloud. Het gebruik van [Managed disks](managed-disks-overview.md) voor de nieuwe virtuele machine vereenvoudigt het beheer van virtuele machines en biedt betere Beschik baarheid wanneer de virtuele machine wordt geplaatst in een beschikbaarheidsset. 

Zie [voorbeeld script voor het uploaden van een VHD naar Azure en het maken van een nieuwe virtuele machine](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)voor een voorbeeld script.

## <a name="before-you-begin"></a>Voordat u begint

- Voordat u een VHD naar Azure uploadt, moet u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Controleer [het plan voor de migratie naar Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) voordat u de migratie naar [Managed disks](managed-disks-overview.md)start.

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>De bron-VM generaliseren met Sysprep

Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Zie het [overzicht van Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server functies](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)voor meer informatie.

> [!IMPORTANT]
> Als u van plan bent om Sysprep uit te voeren voordat u de VHD voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM hebt voor bereid](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Meld u aan bij de virtuele Windows-machine.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de Directory in%windir%\system32\sysprep en voer `sysprep.exe`uit.
3. Selecteer in het dialoog venster **hulp programma voor systeem voorbereiding** de optie **systeem out-of-Box Experience (OOBE) opgeven**en zorg ervoor dat het selectie vakje **generalize** is ingeschakeld.
4. Selecteer voor **afsluit opties**de optie **Afsluiten**.
5. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
6. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. Start de virtuele machine niet opnieuw op.


## <a name="upload-the-vhd-to-your-storage-account"></a>De VHD uploaden naar uw opslag account

U kunt nu een VHD rechtstreeks uploaden naar een beheerde schijf. Zie [een VHD uploaden naar Azure met Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md)voor instructies.


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Een beheerde installatie kopie maken op basis van de geüploade VHD 

Maak een beheerde installatie kopie van de gegeneraliseerde door het besturings systeem beheerde schijf. Vervang de volgende waarden door uw eigen gegevens.


Stel eerst een aantal para meters in:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Maak de installatie kopie met de gegeneraliseerde VHD van het besturings systeem.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>De virtuele machine maken

Nu u een installatiekopie hebt gemaakt, kunt u een of meer nieuwe VM's van de installatiekopie maken met behulp. In dit voor beeld wordt een VM gemaakt met de naam *myVM* van *MyImage*, in *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Volgende stappen

Meld u aan bij de nieuwe virtuele machine. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie. 

