---
title: Een VM maken op basis van een gespecialiseerde schijf in azure | Microsoft Docs
description: Maak een nieuwe virtuele machine door een speciale niet-beheerde schijf te koppelen in het Resource Manager-implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cdbf55aae52cec9df1ba34cbeb34c67b8e5fc5d0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749201"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Een virtuele machine maken op basis van een gespecialiseerde VHD in een opslag account

Maak een nieuwe virtuele machine door een speciale niet-beheerde schijf te koppelen als de besturingssysteem schijf met behulp van Power shell. Een gespecialiseerde schijf is een kopie van de VHD van een bestaande virtuele machine die de gebruikers accounts, toepassingen en andere status gegevens van uw oorspronkelijke virtuele machine beheert. 

U hebt hiervoor twee opties:
* [Een VHD uploaden](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [De VHD van een bestaande Azure-VM kopiëren](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Optie 1: een speciale VHD uploaden

U kunt de VHD uploaden vanaf een gespecialiseerde virtuele machine die is gemaakt met een on-premises Virtualization tool, zoals Hyper-V, of een VM die is geëxporteerd uit een andere cloud.

### <a name="prepare-the-vm"></a>De virtuele machine voorbereiden
U kunt een speciale VHD uploaden die is gemaakt met behulp van een on-premises virtuele machine of een VHD die is geëxporteerd uit een andere cloud. Een gespecialiseerde VHD onderhoudt de gebruikers accounts, toepassingen en andere status gegevens van de oorspronkelijke VM. Als u van plan bent om de VHD te gebruiken om een nieuwe virtuele machine te maken, moet u ervoor zorgen dat de volgende stappen zijn uitgevoerd. 
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Generaliseer de virtuele machine **niet** met behulp van Sysprep.
  * Verwijder alle hulpprogram ma's voor gast-virtualisatie en de agents die zijn geïnstalleerd op de VM (bijvoorbeeld VMware-hulpprogram ma's).
  * Zorg ervoor dat de virtuele machine is geconfigureerd om het IP-adres en de DNS-instellingen via DHCP te halen. Dit zorgt ervoor dat de server een IP-adres binnen het VNet verkrijgt wanneer het wordt gestart. 


### <a name="get-the-storage-account"></a>Het opslag account ophalen
U hebt een opslag account in azure nodig om de geüploade VM-installatie kopie op te slaan. U kunt een bestaand opslag account gebruiken of een nieuwe maken. 

Als u de beschik bare opslag accounts wilt weer geven, typt u:

```powershell
Get-AzStorageAccount
```

Als u een bestaand opslag account wilt gebruiken, gaat u verder naar de sectie de VM-installatie kopie uploaden.

Als u een opslag account wilt maken, voert u de volgende stappen uit:

1. U hebt de naam nodig van de resource groep waar het opslag account moet worden gemaakt. Als u alle resource groepen in uw abonnement wilt weten, typt u:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Als u een resource groep met de naam **myResourceGroup** wilt maken in de regio **VS-West** , typt u:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Maak een opslag account met de naam **mystorageaccount** in deze resource groep met behulp van de cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>De VHD uploaden naar uw opslag account
Gebruik de cmdlet [add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) om de installatie kopie te uploaden naar een container in uw opslag account. In dit voor beeld wordt het bestand **myVHD. VHD** geüpload van `"C:\Users\Public\Documents\Virtual hard disks\"` naar een opslag account met de naam **mystorageaccount** in de resource groep **myResourceGroup** . Het bestand wordt in de container met de naam **mycontainer** geplaatst en de nieuwe bestands naam wordt **myUploadedVHD. VHD**.

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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Optie 2: de VHD kopiëren van een bestaande Azure VM

U kunt een VHD kopiëren naar een ander opslag account om te gebruiken bij het maken van een nieuwe, dubbele virtuele machine.

### <a name="before-you-begin"></a>Voordat u begint
Zorg ervoor dat:

* Informatie over de **bron-en doel opslag accounts**. Voor de bron-VM moet u het opslag account en de container namen hebben. Normaal gesp roken is de naam van de container de **vhd's**. U hebt ook een doel-opslag account nodig. Als u er nog geen hebt, kunt u er een maken met behulp van de portal (**alle Services** > opslag accounts > toevoegen) of met de cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) . 
* Het [AzCopy-hulp programma](../../storage/common/storage-use-azcopy.md)hebt gedownload en geïnstalleerd. 

### <a name="deallocate-the-vm"></a>De toewijzing van de virtuele machine ongedaan maken
Maak de toewijzing van de virtuele machine ongedaan, waardoor de VHD wordt gekopieerd. 

* **Portal**: Klik op **virtual machines** > **myVM** > Stop
* **Power shell**: gebruik [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) om de virtuele machine met de naam **myVM** in de **myResourceGroup**van de resource groep te stoppen (de toewijzing ongedaan te maken).

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

De **status** van de virtuele machine in de Azure Portal verandert van **gestopt** in **gestopt (toewijzing opgeheven)** .

### <a name="get-the-storage-account-urls"></a>De Url's van het opslag account ophalen
U hebt de Url's van de bron-en doel opslag accounts nodig. De Url's zien er als volgt uit: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Als u al bekend bent met het opslag account en de naam van de container, kunt u alleen de informatie tussen de haakjes vervangen om uw URL te maken. 

U kunt de Azure Portal of Azure Power shell gebruiken om de URL op te halen:

* **Portal**: Klik op de **>** voor **alle services** > **opslag accounts** > *opslag account* > **blobs** en uw VHD-bron bestand bevindt zich waarschijnlijk in de **vhd's** -container. Klik op **Eigenschappen** voor de container en kopieer de tekst met de label **URL**. U hebt de Url's nodig van zowel de bron-als de doel container. 
* **Power shell**: gebruik [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) voor het ophalen van de informatie voor de virtuele machine met de naam **MyVM** in de resource groep **myResourceGroup**. Zoek in de resultaten in de sectie **opslag profiel** voor de **VHD-URI**. Het eerste deel van de URI is de URL naar de container en het laatste deel is de VHD-naam van het besturings systeem voor de virtuele machine.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>De toegangs sleutels voor opslag ophalen
Zoek de toegangs sleutels voor de bron-en doel opslag accounts. Zie [over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor meer informatie over toegangs sleutels.

* **Portal**: Klik op **alle services** > **opslag accounts** > *opslag account* > **toegangs sleutels**. Kopieer de sleutel met de naam **key1**.
* **Power shell**: gebruik [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) om de opslag sleutel op te halen voor de **mystorageaccount** van het opslag account in de resource groep **myResourceGroup**. Kopieer de sleutel met de label **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>De VHD kopiëren
U kunt bestanden kopiëren tussen opslag accounts met behulp van AzCopy. Als de opgegeven container niet bestaat, wordt deze voor u gemaakt in de doel container. 

Als u AzCopy wilt gebruiken, opent u een opdracht prompt op de lokale computer en navigeert u naar de map waarin AzCopy is geïnstalleerd. Dit is vergelijkbaar met *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Als u alle bestanden in een container wilt kopiëren, gebruikt u de schakel optie **/s** . Dit kan worden gebruikt voor het kopiëren van de VHD van het besturings systeem en alle gegevens schijven als deze zich in dezelfde container bevinden. In dit voor beeld ziet u hoe u alle bestanden in de container **mysourcecontainer** in het opslag account **mysourcestorageaccount** kopieert naar de container **mydestinationcontainer** in het **mydestinationstorageaccount** -opslag account . Vervang de namen van de opslag accounts en containers door uw eigen. Vervang `<sourceStorageAccountKey1>` en `<destinationStorageAccountKey1>` door uw eigen sleutels.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Als u alleen een specifieke VHD in een container met meerdere bestanden wilt kopiëren, kunt u ook de bestands naam opgeven met behulp van de/Pattern-switch. In dit voor beeld wordt alleen het bestand met de naam **myFileName. VHD** gekopieerd.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Wanneer dit is voltooid, wordt er een bericht weer gegeven dat er ongeveer als volgt uitziet:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Problemen oplossen
* Wanneer u AZCopy gebruikt en de fout ' server kan de aanvraag niet verifiëren ' ziet, moet u ervoor zorgen dat de waarde van de autorisatie-header correct is opgemaakt, inclusief de hand tekening. Als u Key 2 of de secundaire opslag sleutel gebruikt, probeert u de primaire of 1e opslag sleutel te gebruiken.

## <a name="create-the-new-vm"></a>De nieuwe VM maken 

U moet netwerk-en andere VM-resources maken om te worden gebruikt door de nieuwe virtuele machine.

### <a name="create-the-subnet-and-vnet"></a>Het subNet en vNet maken

Maak het vNet en het subNet van het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md).

1. Maak het subNet. In dit voor beeld wordt een subnet met de naam **mySubNet**gemaakt in de resource groep **myResourceGroup**en wordt het adres voorvoegsel van het subnet ingesteld op **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Maak het vNet. In dit voor beeld wordt de naam van het virtuele netwerk ingesteld op **myVnetName**, de locatie van **VS-West**en het adres voorvoegsel voor het virtuele netwerk naar **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerk beveiligings groep en een RDP-regel maken
   Als u zich met RDP wilt aanmelden bij uw VM, moet u een beveiligings regel hebben waarmee RDP-toegang wordt toegestaan op poort 3389. Omdat de VHD voor de nieuwe virtuele machine is gemaakt op basis van een bestaande, gespecialiseerde VM, kunt u na het maken van de VM een bestaand account gebruiken van de bron-VM die is gemachtigd om zich aan te melden met RDP.
   Dit moet worden voltooid voordat de netwerk interface wordt gemaakt waaraan deze is gekoppeld.  
   In dit voor beeld wordt de naam van de NSG ingesteld op **mijnnbg** en de RDP-regel naam op **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Zie [poorten openen voor een virtuele machine in azure met behulp van Power shell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over eind punten en NSG regels.

### <a name="create-a-public-ip-address-and-nic"></a>Een openbaar IP-adres en een NIC maken
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Maak het open bare IP-adres. In dit voor beeld is de naam van het open bare IP-adres ingesteld op **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Maak de NIC. In dit voor beeld is de naam van de NIC ingesteld op **myNicName**. Met deze stap wordt ook de netwerk beveiligings groep gekoppeld die eerder met deze NIC is gemaakt.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>De naam en grootte van de virtuele machine instellen

In dit voor beeld wordt de naam van de virtuele machine ingesteld op ' myVM ' en de VM-grootte op ' Standard_A2 '.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>De NIC toevoegen
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>De besturingssysteem schijf configureren

1. Stel de URI in voor de VHD die u hebt geüpload of gekopieerd. In dit voor beeld wordt het VHD-bestand met de naam **myOsDisk. VHD** bewaard in een opslag account met de naam **myStorageAccount** in een container met de naam **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Voeg de besturingssysteem schijf toe. In dit voor beeld, wanneer de besturingssysteem schijf wordt gemaakt, wordt de term ' osDisk ' toegevoegd aan de naam van de virtuele machine om de naam van de besturingssysteem schijf te maken. In dit voor beeld wordt ook aangegeven dat deze op Windows gebaseerde VHD moet worden gekoppeld aan de virtuele machine als de besturingssysteem schijf.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Optioneel: als u gegevens schijven hebt die moeten worden gekoppeld aan de virtuele machine, voegt u de gegevens schijven toe met behulp van de Url's van de gegevens-Vhd's en het juiste LUN (Logical Unit Number).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Wanneer u een opslag account gebruikt, zien de schijf-Url's van de gegevens en het besturings systeem er ongeveer als volgt uit: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. U kunt dit vinden in de portal door te bladeren naar de doel opslag container, te klikken op het besturings systeem of de gegevens-VHD die is gekopieerd en vervolgens de inhoud van de URL te kopiëren.


### <a name="complete-the-vm"></a>De virtuele machine volt ooien 

Maak de virtuele machine met behulp van de configuraties die we zojuist hebben gemaakt.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Als deze opdracht is geslaagd, ziet u uitvoer als volgt:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Controleren of de virtuele machine is gemaakt
U ziet de zojuist gemaakte VM in de [Azure Portal](https://portal.azure.com), onder **alle services** > **virtuele machines**, of met behulp van de volgende Power shell-opdrachten:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Meld u aan bij de nieuwe virtuele machine. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](connect-logon.md)voor meer informatie.

