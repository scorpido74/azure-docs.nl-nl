---
title: Een beheerde afbeelding maken in Azure
description: Maak een beheerde afbeelding van een gegeneraliseerde VM of VHD in Azure. Afbeeldingen kunnen worden gebruikt om meerdere VM's te maken die beheerde schijven gebruiken.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 01619027ddc79530dc9541584efa9a3e518f5136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74842055"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Een beheerde installatiekopie van een gegeneraliseerde VM maken in Azure

Een beheerde installatiekopie kan worden gemaakt op basis van een gegeneraliseerde virtuele machine (VM) die als een beheerde schijf of een onbeheerde schijf is opgeslagen in een opslagaccount. De installatiekopie kan vervolgens worden gebruikt om meerdere VM's te maken. Zie [Prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor informatie over de manier waarop beheerde afbeeldingen worden gefactureerd. 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>De Windows VM generaliseren met behulp van Sysprep

Sysprep verwijdert al uw persoonlijke account- en beveiligingsgegevens en bereidt vervolgens de machine voor om als afbeelding te worden gebruikt. Zie [Sysprep-overzicht](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor informatie over Sysprep.

Controleer of de serverrollen die op de machine worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor serverrollen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) en [niet-ondersteunde scenario's](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)voor meer informatie.

> [!IMPORTANT]
> Nadat u Sysprep op een VM hebt uitgevoerd, wordt die VM als *algemeen* beschouwd en kan deze niet opnieuw worden gestart. Het generaliseringsproces van een VM is onomkeerbaar. Als u de oorspronkelijke VM moet laten functioneren, moet u een [kopie van de VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) maken en de kopie ervan generaliseren. 
>
> Als u van plan bent Sysprep uit te voeren voordat u uw virtuele harde schijf (VHD) voor de eerste keer uploadt naar Azure, controleert u of u uw VM hebt [voorbereid.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  
> 
> 

Voer de volgende stappen uit om uw Windows-VM te generaliseren:

1. Meld u aan bij uw Windows-VM.
   
2. Open een opdrachtpromptvenster als beheerder. Wijzig de map in %windir%\system32\sysprep `sysprep.exe`en voer deze uit.
   
3. Schakel in het dialoogvenster **Systeemvoorbereidingstool** de optie **Systeem-out-of-box experience (OOBE) in** en schakel het selectievakje **Generaliseren** in.
   
4. Selecteer **Afsluiten** **voor afsluitenopties**.
   
5. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)

6. Wanneer Sysprep is voltooid, wordt de VM uitgeschakeld. Start de VM niet opnieuw.

> [!TIP]
> **Optioneel** Gebruik [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) om uw afbeelding te optimaliseren en de eerste opstarttijd van uw VM te verkorten.
>
> Als u uw afbeelding wilt optimaliseren, monteert u uw VHD door erop te `/optimize-image` dubbelklikken in Windows Explorer en voert u DISM uit met de parameter.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Waar D: is het bereden Pad van VHD.
>
> Hardlopen `DISM /optimize-image` moet de laatste wijziging die u aan uw VHD. Als u voorafgaand aan de implementatie wijzigingen aanbrengt in `DISM /optimize-image` uw VHD, moet u opnieuw worden uitgevoerd.

## <a name="create-a-managed-image-in-the-portal"></a>Een beheerde afbeelding maken in de portal 

1. Ga naar de [Azure-portal](https://portal.azure.com) om de VM-afbeelding te beheren. Zoeken naar virtuele machines en selecteer **deze**.

2. Selecteer uw VM in de lijst.

3. Selecteer op de pagina **Virtuele machine** voor de virtuele machine voor de virtuele machine in het bovenste menu De optie **Vastleggen**.

   De pagina **Afbeelding maken** wordt weergegeven.

4. Accepteer voor **Naam**de vooraf ingevulde naam of voer een naam in die u voor de afbeelding wilt gebruiken.

5. Selecteer **voor resourcegroep** **Nieuw maken** en voer een naam in of selecteer een resourcegroep die u wilt gebruiken in de vervolgkeuzelijst.

6. Als u de bron-VM wilt verwijderen nadat de afbeelding is gemaakt, selecteert u **Deze virtuele machine automatisch verwijderen nadat deze afbeelding is gemaakt.**

7. Als u de mogelijkheid wilt hebben om de afbeelding in een [beschikbaarheidszone](../../availability-zones/az-overview.md)te gebruiken, selecteert u **Aan** voor **zonetolerantie**.

8. Selecteer **Maken** om de afbeelding te maken.

Nadat de afbeelding is gemaakt, u deze vinden als **een afbeeldingsbron** in de lijst met bronnen in de resourcegroep.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Een afbeelding van een virtuele machine maken met Powershell

 

Als u rechtstreeks vanuit de virtuele machine een afbeelding maakt, zorgt u ervoor dat de afbeelding alle schijven bevat die aan de VM zijn gekoppeld, inclusief de osschijf en eventuele gegevensschijven. In dit voorbeeld ziet u hoe u een beheerde afbeelding maakt van een VM die beheerde schijven gebruikt.

Controleer voordat u begint of u de nieuwste versie van de Azure PowerShell-module hebt. Voer de versie `Get-Module -ListAvailable Az` uit in PowerShell om de versie te vinden. Zie [Azure PowerShell installeren op Windows met PowerShellGet](/powershell/azure/install-az-ps)als u wilt upgraden. Als u PowerShell lokaal uitvoert, voert u een `Connect-AzAccount` verbinding met Azure uit.


> [!NOTE]
> Als u uw afbeelding wilt opslaan in zoneredundante opslag, moet u deze maken in `-ZoneResilient` een regio die`New-AzImageConfig` [beschikbaarheidszones](../../availability-zones/az-overview.md) ondersteunt en de parameter opnemen in de afbeeldingsconfiguratie (opdracht).

Voer de volgende stappen uit om een VM-afbeelding te maken:

1. Maak een aantal variabelen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Zorg ervoor dat de VM is deallocated.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Stel de status van de virtuele machine in **op Algemeen.** 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Haal de virtuele machine op. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Een afbeelding maken vanaf een beheerde schijf met PowerShell

Als u een afbeelding wilt maken van alleen de osschijf, geeft u de beheerde schijf-id op als de osschijf:

    
1. Maak een aantal variabelen. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Haal de VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Haal de id van de beheerde schijf.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Een afbeelding maken op basis van een momentopname met Powershell

U een beheerde afbeelding maken op basis van een momentopname van een gegeneraliseerde vm door de volgende stappen te volgen:

    
1. Maak een aantal variabelen. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Neem de foto.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Een afbeelding maken van een vm die een opslagaccount gebruikt

Als u een beheerde afbeelding wilt maken van een VM die geen beheerde schijven gebruikt, hebt u de URI van het OS VHD in het opslagaccount nodig in de volgende indeling: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. In dit voorbeeld bevindt de VHD zich in *mystorageaccount*, in een container met de naam *vhdcontainer*, en de VHD-bestandsnaam is *vhdfilename.vhd*.


1.  Maak een aantal variabelen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. De VM stoppen/detoewijzen.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markeer de VM als algemeen.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Maak de afbeelding met behulp van het algemene besturingssysteem VHD.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Volgende stappen
- [Maak een VM op basis van een beheerde afbeelding](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

