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
ms.openlocfilehash: be3ccfd0c562763d0968398ddb042dc5f07dbdcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101565"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Een gegeneraliseerde VHD uploaden en gebruiken om nieuwe virtuele machines te maken in azure

In dit artikel wordt stapsgewijs beschreven hoe u Power shell gebruikt om een VHD van een gegeneraliseerde VM naar Azure te uploaden, een installatie kopie van de VHD te maken en een nieuwe VM te maken op basis van die installatie kopie. U kunt een VHD uploaden die is geëxporteerd uit een on-premises virtualisatie hulpprogramma of vanuit een andere cloud. Het gebruik van [Managed disks](managed-disks-overview.md) voor de nieuwe virtuele machine vereenvoudigt het beheer van virtuele machines en biedt betere Beschik baarheid wanneer de virtuele machine wordt geplaatst in een beschikbaarheidsset. 

Zie [voorbeeld script voor het uploaden van een VHD naar Azure en het maken van een nieuwe virtuele machine](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)voor een voorbeeld script.

## <a name="before-you-begin"></a>Voordat u begint

- Voordat u een VHD naar Azure uploadt, moet u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Controleer [het plan voor de migratie naar Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) voordat u de migratie naar [Managed disks](managed-disks-overview.md)start.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>De bron-VM generaliseren met Sysprep

Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Zie het [overzicht van Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server functies](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)voor meer informatie.

> [!IMPORTANT]
> Als u van plan bent om Sysprep uit te voeren voordat u de VHD voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM hebt voor bereid](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Meld u aan bij de virtuele Windows-machine.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de Directory in%windir%\system32\sysprep en voer uit `sysprep.exe`.
3. Selecteer in het dialoog venster **hulp programma voor systeem voorbereiding** de optie **systeem out-of-Box Experience (OOBE) opgeven**en zorg ervoor dat het selectie vakje **generalize** is ingeschakeld.
4. Selecteer voor **afsluit opties**de optie **Afsluiten**.
5. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
6. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. Start de virtuele machine niet opnieuw op.


## <a name="get-a-storage-account"></a>Een opslag account ophalen

U hebt een opslag account in azure nodig om de geüploade VM-installatie kopie op te slaan. U kunt een bestaand opslag account gebruiken of een nieuwe maken. 

Als u de VHD gebruikt voor het maken van een beheerde schijf voor een virtuele machine, moet de locatie van het opslag account gelijk zijn aan de locatie waar u de virtuele machine gaat maken.

Voer het volgende in om de beschik bare opslag accounts weer te geven:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>De VHD uploaden naar uw opslag account

Gebruik de cmdlet [add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) om de VHD te uploaden naar een container in uw opslag account. In dit voor beeld wordt het bestand *myVHD. VHD* geüpload van *C:\Users\Public\Documents\Virtual harde\\ schijven* naar een opslag account met de naam *mystorageaccount* in de resource groep *myResourceGroup* . Het bestand wordt in de container met de naam *mycontainer* geplaatst en de nieuwe bestands naam wordt *myUploadedVHD. VHD*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Als dat lukt, krijgt u een antwoord dat er ongeveer als volgt uitziet:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Afhankelijk van uw netwerk verbinding en de grootte van het VHD-bestand kan het enige tijd duren voordat deze opdracht is voltooid.

### <a name="other-options-for-uploading-a-vhd"></a>Andere opties voor het uploaden van een VHD
 
U kunt ook een VHD uploaden naar uw opslag account met behulp van een van de volgende opties:

- [AzCopy](https://aka.ms/downloadazcopy)
- [BLOB-API Azure Storage kopiëren](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage Explorer uploaden van blobs](https://azurestorageexplorer.codeplex.com/)
- [Naslag informatie voor Storage import/export-service REST API](https://msdn.microsoft.com/library/dn529096.aspx)
-   U kunt het beste de import/export-service gebruiken als de geschatte upload tijd langer is dan zeven dagen. U kunt [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) gebruiken om de tijd te schatten van de gegevens grootte en de overdrachts eenheid. 
    Importeren/exporteren kan worden gebruikt om naar een Standard-opslag account te kopiëren. U moet de standaard opslag kopiëren naar een Premium Storage-account met behulp van een hulp programma zoals AzCopy.

> [!IMPORTANT]
> Als u AzCopy gebruikt om uw VHD te uploaden naar Azure, moet u ervoor zorgen dat u [ **/BlobType: pagina**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) hebt ingesteld voordat u het upload script uitvoert. Als de bestemming een blob is en deze optie niet is opgegeven, maakt AzCopy standaard een blok-blob.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Een beheerde installatie kopie maken op basis van de geüploade VHD 

Een beheerde installatie kopie maken op basis van de gegeneraliseerde VHD van het besturings systeem. Vervang de volgende waarden door uw eigen gegevens.


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

