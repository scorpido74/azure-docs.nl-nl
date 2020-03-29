---
title: Een VM maken op basis van een geüploade geügeneraliseerde VHD
description: Upload een gegeneraliseerde VHD naar Azure en gebruik deze om nieuwe VM's te maken in het implementatiemodel Resource Manager.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464948"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Een gegeneraliseerde VHD uploaden en deze gebruiken om nieuwe virtuele machines te maken in Azure

In dit artikel u powershell gebruiken om een VHD van een gegeneraliseerde VM naar Azure te uploaden, een afbeelding van de VHD te maken en een nieuwe VM van die afbeelding te maken. U een VHD uploaden die is geëxporteerd vanuit een on-premises virtualisatietool of vanuit een andere cloud. Het gebruik [van Beheerde schijven](managed-disks-overview.md) voor de nieuwe VM vereenvoudigt het VM-beheer en biedt een betere beschikbaarheid wanneer de VM in een beschikbaarheidsset wordt geplaatst. 

Zie Voorbeeldscript voor een [voorbeeldscript om een VHD naar Azure te uploaden en een nieuwe vm te maken.](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Voordat u begint

- Voordat u een VHD naar Azure uploadt, moet u [Een Windows VHD of VHDX voorbereiden volgen om te uploaden naar Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Controleer [Het plan voor de migratie naar beheerde schijven](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) voordat u uw migratie naar [beheerde schijven start.](managed-disks-overview.md)

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generaliseer de bron-VM met Behulp van Sysprep

Als u dit nog niet hebt gedaan, moet u de VM sysprepen voordat u de VHD uploadt naar Azure. Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Zie het [Sysprep-overzicht voor](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)meer informatie over Sysprep.

Controleer of de serverrollen die op de machine worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor serverrollen voor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)meer informatie.

> [!IMPORTANT]
> Als u van plan bent Sysprep uit te voeren voordat u uw VHD voor de eerste keer uploadt naar Azure, controleert u of u uw VM hebt [voorbereid.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Meld u aan bij de Windows-VM.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de map in %windir%\system32\sysprep `sysprep.exe`en voer deze uit.
3. Schakel in het dialoogvenster **Systeemvoorbereidingstool** de optie **Systeem-out-of-box experience (OOBE) in**en controleer of het selectievakje **Generaliseren** is ingeschakeld.
4. Selecteer **Afsluiten** **voor afsluitenopties**.
5. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
6. Wanneer Sysprep klaar is, schakelt het de virtuele machine uit. Start de VM niet opnieuw.


## <a name="upload-the-vhd"></a>De VHD uploaden 

U nu een VHD rechtstreeks uploaden naar een beheerde schijf. Zie Een [VHD uploaden naar Azure met Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md)voor instructies.



Zodra de VHD is geüpload naar de beheerde schijf, moet u [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) gebruiken om de beheerde schijf te krijgen.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>De installatiekopie maken
Maak een beheerde afbeelding van de gegeneraliseerde door het besturingssysteem beheerde schijf. Vervang de volgende waarden door uw eigen gegevens.

Stel eerst enkele variabelen in:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Maak de afbeelding met uw beheerde schijf.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Maak de installatiekopie.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>De virtuele machine maken

Nu u een installatiekopie hebt gemaakt, kunt u een of meer nieuwe VM's van de installatiekopie maken met behulp. In dit voorbeeld wordt een VM met de naam *myVM* gemaakt in *myImage*, in *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Volgende stappen

Meld u aan bij uw nieuwe virtuele machine. Zie [Verbinding maken en inloggen op een virtuele Azure-machine met Windows voor](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie. 

