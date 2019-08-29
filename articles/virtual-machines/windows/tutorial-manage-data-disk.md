---
title: 'Zelfstudie: Azure-schijven beheren met Azure PowerShell | Microsoft Docs'
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt voor het maken en beheren van Azure-schijven voor virtuele machines
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: f44ea76ed22ab4feacc6ee37b165bbc33f7ddafc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101600"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Zelfstudie: Azure-schijven beheren met Azure PowerShell

Virtuele machines in Azure gebruiken schijven voor het opslaan van het besturingssysteem, toepassingen en gegevens van virtuele machines. Bij het maken van een VM is het van belang dat u een schijfgrootte en configuratie kiest die geschikt zijn voor de verwachte werkbelasting. Deze zelfstudie bevat informatie over het implementeren en beheren van VM-schijven. U krijgt informatie over:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="default-azure-disks"></a>Standaard Azure-schijven

Wanneer een virtuele Azure-machine wordt gemaakt, worden automatisch twee schijven aan de virtuele machine gekoppeld. 

**Besturingssysteemschijf**: besturingssysteemschijven kunnen tot 4 terabytes groot zijn, en huisvesten het besturingssysteem van de VM's.  De besturingssysteemschijf krijgt standaard stationsletter *C:* toegewezen. De schijfcacheconfiguratie van de besturingssysteemschijf is geoptimaliseerd voor besturingssysteemprestaties. De besturingssysteemschijf kan **beter geen** toepassingen of gegevens bevatten. Gebruik voor toepassingen en gegevens een gegevensschijf, zoals verderop in dit artikel wordt beschreven.

**Tijdelijke schijf**: tijdelijke schijven gebruiken een SSD-schijf die zich op dezelfde Azure-host bevindt als de virtuele machine. Tijdelijke schijven leveren zeer goede prestaties en kunnen worden gebruikt voor bewerkingen als tijdelijke gegevensverwerking. Als de virtuele machine wordt verplaatst naar een nieuwe host, worden gegevens die zijn opgeslagen op een tijdelijke schijf echter verwijderd. De grootte van de tijdelijke schijf wordt bepaald door de [VM-grootte](sizes.md). Tijdelijke schijven krijgen standaard de stationsletter *D:* toegewezen.

## <a name="azure-data-disks"></a>Azure-gegevensschijven

Er kunnen extra gegevensschijven worden toegevoegd voor het installeren van toepassingen en opslaan van gegevens. Gegevensschijven moeten worden gebruikt in situaties waarin duurzame en responsieve gegevensopslag nodig is. De grootte van de virtuele machine bepaalt hoeveel gegevensschijven aan een virtuele machine kunnen worden gekoppeld. Voor elke VM-vCPU kunnen vier schijven worden gekoppeld.

## <a name="vm-disk-types"></a>Typen VM-schijven

Azure biedt twee typen schijven.

**Standard-schijven** - ondersteund door HDD's en bieden voordelige en hoogwaardige opslag. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.

**Premium-schijven** - ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Ideaal voor virtuele machines met een productiewerkbelasting. Premium Storage ondersteunt virtuele machines uit de DS-serie, DSv2-serie GS-serie en FS-serie. Er zijn vijf typen Premium-schijven (P10, P20, P30, P40, P50); de grootte van de schijf bepaalt het schijftype. Wanneer u een selectie maakt, wordt de waarde van de schijfgrootte afgerond naar het volgende type. Als de grootte bijvoorbeeld kleiner is dan 128 GB is het schijftype P10, en tussen 129 en 512 GB is de schijf P20.

### <a name="premium-disk-performance"></a>Prestaties Premium-schijf
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

In de bovenstaande tabel wordt het max. IOP's per schijf aangegeven, maar er kan een hoger prestatieniveau worden bereikt door striping van meerdere gegevensschijven. Er kunnen bijvoorbeeld 64 gegevensschijven worden gekoppeld aan Standard_GS5 VM. Als voor elk van deze schijven een P30-grootte wordt gebruikt, kan een maximum van 80.000 IOP's worden behaald. Zie [VM-typen en -grootten](./sizes.md) voor gedetailleerde informatie over het maximum aantal IOP's per VM.

## <a name="create-and-attach-disks"></a>Schijven maken en koppelen

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Maak indien nodig een virtuele machine met de volgende opdrachten.

Stel met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) de gebruikersnaam en het wachtwoord in die nodig zijn voor de beheerdersaccount op de virtuele machine:


Maak de virtuele machine met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). U wordt gevraagd een gebruikersnaam en wachtwoord in te voeren voor het administrator-account voor de VM.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Maak de aanvankelijke configuratie met [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). In het volgende voorbeeld wordt een schijf van 128 gigabyte groot geconfigureerd.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Maak de gegevensschijf met de opdracht [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Haal de virtuele machine waaraan u de gegevensschijf wilt toevoegen op met de opdracht [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Voeg de gegevensschijf toe aan de configuratie van de virtuele machine met de opdracht [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Werk de virtuele machine bij met de opdracht [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Gegevensschijven voorbereiden

Wanneer een schijf is gekoppeld aan de virtuele machine, moet het besturingssysteem worden geconfigureerd voor gebruik van de schijf. Het volgende voorbeeld laat zien hoe u handmatig de eerste schijf configureert die aan de VM wordt toegevoegd. Dit proces kan ook worden geautomatiseerd met de [Custom Script Extension](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Handmatige configuratie

Maak een RDP-verbinding met de virtuele machine. Open PowerShell en voer dit script uit.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>De gegevensschijf controleren

Bekijk het `StorageProfile` voor de gekoppelde `DataDisks` om te controleren of de gegevensschijf is gekoppeld.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

De uitvoer zou er ongeveer uit moeten zien zoals in dit voorbeeld:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over onderwerpen over VM-schijven, zoals:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven

In de volgende zelfstudie leert u hoe u de configuratie van virtuele machines automatiseert.

> [!div class="nextstepaction"]
> [VM-configuratie automatiseren](./tutorial-automate-vm-deployment.md)
