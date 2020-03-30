---
title: VM maken vanaf een gespecialiseerde schijf in Azure
description: Maak een nieuwe vm door een gespecialiseerde niet-beheerde schijf toe te voegen in het implementatiemodel resourcebeheer.
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
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073353"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Een VM maken van een gespecialiseerde VHD in een opslagaccount

Maak een nieuwe vm door een gespecialiseerde onbeheerde schijf als besturingssysteemschijf te koppelen met Powershell. Een gespecialiseerde schijf is een kopie van VHD van een bestaande VM die de gebruikersaccounts, toepassingen en andere statusgegevens van uw oorspronkelijke VM onderhoudt. 

U hebt hiervoor twee opties:
* [Een VHD uploaden](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [De VHD van een bestaande Azure VM kopiëren](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Optie 1: Upload een gespecialiseerde VHD

U de VHD uploaden van een gespecialiseerde VM die is gemaakt met een on-premises virtualisatietool, zoals Hyper-V, of een VM die vanuit een andere cloud wordt geëxporteerd.

### <a name="prepare-the-vm"></a>De virtuele machine voorbereiden
U een gespecialiseerde VHD uploaden die is gemaakt met behulp van een on-premises VM of een VHD die vanuit een andere cloud wordt geëxporteerd. Een gespecialiseerde VHD onderhoudt de gebruikersaccounts, toepassingen en andere statusgegevens van uw oorspronkelijke VM. Als u van plan bent de VHD-as-is te gebruiken om een nieuwe virtuele machine te maken, moet u ervoor zorgen dat de volgende stappen zijn voltooid. 
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) **Generaliseer** de VM niet met Sysprep.
  * Verwijder alle hulpprogramma's en agents voor gastvirtualisatie die op de VM zijn geïnstalleerd (d.w.z. VMware-hulpprogramma's).
  * Controleer of de VM is geconfigureerd om zijn IP-adres en DNS-instellingen via DHCP op te halen. Dit zorgt ervoor dat de server een IP-adres binnen het VNet verkrijgt wanneer deze wordt opgestart. 


### <a name="get-the-storage-account"></a>Het opslagaccount aanschaffen
U hebt een opslagaccount in Azure nodig om de geüploade VM-afbeelding op te slaan. U een bestaand opslagaccount gebruiken of een nieuw account maken. 

Als u de beschikbare opslagaccounts wilt weergeven, typt u het:

```powershell
Get-AzStorageAccount
```

Als u een bestaand opslagaccount wilt gebruiken, gaat u door naar de sectie Vm-afbeelding uploaden.

Als u een opslagaccount wilt maken, voert u de volgende stappen uit:

1. U hebt de naam nodig van de resourcegroep waar het opslagaccount moet worden gemaakt. Als u alle brongroepen wilt vinden die zich in uw abonnement bevinden, typt u het:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Als u een resourcegroep met de naam **myResourceGroup** in de regio **West-VS** wilt maken, typt u het:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Maak een opslagaccount met de naam **mystorageaccount** in deze brongroep met de cmdlet [Nieuw-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>De VHD uploaden naar uw opslagaccount
Gebruik de [cmdlet Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) om de afbeelding te uploaden naar een container in uw opslagaccount. In dit voorbeeld wordt het bestand **myVHD.vhd** geüpload `"C:\Users\Public\Documents\Virtual hard disks\"` naar een opslagaccount met de naam **mystorageaccount** in de **brongroep myResourceGroup.** Het bestand zal worden geplaatst in de container met de naam **mycontainer** en de nieuwe bestandsnaam zal **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Als dit lukt, krijgt u een antwoord dat op dit lijkt:

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

Afhankelijk van uw netwerkverbinding en de grootte van uw VHD-bestand kan het even duren voordat deze opdracht is voltooid.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Optie 2: De VHD kopiëren vanaf een bestaande Azure VM

U een VHD kopiëren naar een ander opslagaccount dat u wilt gebruiken bij het maken van een nieuwe, dubbele VM.

### <a name="before-you-begin"></a>Voordat u begint
Zorg ervoor dat u:

* Heb informatie over de **bron- en bestemmingsopslagaccounts**. Voor de bron-VM moet u het opslagaccount en de containernamen hebben. Meestal zal de containernaam **vhds**zijn. Je hebt ook een bestemmingsopslagaccount nodig. Als u er nog geen hebt, u er een maken met behulp van de portal **(All Services** > Storage accounts > Add) of met de cmdlet [Nieuw-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Hebben gedownload en geïnstalleerd de [AzCopy tool](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>De VM deallocateeren
Deallocate de VM, die bevrijdt van de VHD te worden gekopieerd. 

* **Portal**: Klik op **virtuele machines** > **myVM** > Stop
* **Powershell**: Gebruik [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) om de VM met de naam **myVM** te stoppen (detoewijzen) in resourcegroep **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

De **status** voor de VM in de Azure-portal verandert **van Gestopt** naar Gestopt **(deallocated).**

### <a name="get-the-storage-account-urls"></a>Url's voor het opslagaccount opdoen
U hebt de URL's van de bron- en doelopslagaccounts nodig. De URL's `https://<storageaccount>.blob.core.windows.net/<containerName>/`zien eruit als: . Als u het opslagaccount en de naam van de container al kent, u de informatie tussen de haakjes vervangen om uw URL te maken. 

U de Azure-portal of Azure Powershell gebruiken om de URL op te halen:

* **Portal:** Klik **>** op het*opslagaccount* > voor**opslagaccounts** > voor **alle services** > **Blobs** en uw bron VHD-bestand bevindt zich waarschijnlijk in de **vhds-container.** Klik op **Eigenschappen** voor de container en kopieer de url met het **tekstlabel**. U hebt de URL's van zowel de bron- als de doelcontainers nodig. 
* **Powershell**: Gebruik [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) om de informatie voor VM met de naam **myVM** in de brongroep **myResourceGroup**te krijgen. Kijk in de resultaten in de sectie **Opslagprofiel** voor de **Vhd Uri.** Het eerste deel van de Uri is de URL naar de container en het laatste deel is de OS VHD naam voor de VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>De toegangssleutels voor opslag ophalen
Zoek de toegangssleutels voor de bron- en bestemmingsopslagaccounts. Zie [Over Azure-opslagaccounts](../../storage/common/storage-create-storage-account.md)voor meer informatie over toegangssleutels .

* **Portal:** Klik op **Alle services** > **Opslagaccounts** > *opslagaccount* > **Toegangssleutels**. Kopieer de sleutel met het label **toets1**.
* **Powershell**: Gebruik [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) om de opslagsleutel voor de opslagaccount **mystorageaccount** in de resourcegroep **myResourceGroup**te krijgen. Kopieer de toets met het **label 1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>De VHD kopiëren
U bestanden tussen opslagaccounts kopiëren met AzCopy. Als de opgegeven container niet bestaat, wordt deze voor u gemaakt als de opgegeven container niet bestaat. 

Als u AzCopy wilt gebruiken, opent u een opdrachtprompt op uw lokale machine en navigeert u naar de map waar AzCopy is geïnstalleerd. Het zal vergelijkbaar zijn met *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Als u alle bestanden in een container wilt kopiëren, gebruikt u de **/S-schakelaar.** Dit kan worden gebruikt om het OS VHD en alle gegevensschijven te kopiëren als ze zich in dezelfde container bevinden. In dit voorbeeld ziet u hoe u alle bestanden in de container **mysourcecontainer** in opslagaccount **mysourcestorageaccount kopieert** naar de container **mydestinationcontainer** in het opslagaccount **van mydestinationstorageaccount.** Vervang de namen van de opslagaccounts en containers door die van u. Vervang `<sourceStorageAccountKey1>` `<destinationStorageAccountKey1>` en met je eigen sleutels.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Als u alleen een specifieke VHD wilt kopiëren in een container met meerdere bestanden, u de bestandsnaam ook opgeven met behulp van de /Pattern-schakelaar. In dit voorbeeld wordt alleen het bestand met de naam **myFileName.vhd** gekopieerd.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Wanneer het klaar is, krijgt u een bericht dat er ongeveer uitziet als:

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
* Wanneer u AZCopy gebruikt, als u de fout 'Server kan de aanvraag niet verifiëren' ziet, controleert u of de waarde van de kopautorisatie correct wordt gevormd, inclusief de handtekening. Als u toets 2 of de secundaire opslagsleutel gebruikt, probeert u de primaire of 1e opslagsleutel te gebruiken.

## <a name="create-the-new-vm"></a>De nieuwe VM maken 

U moet netwerken en andere VM-bronnen maken die door de nieuwe VM kunnen worden gebruikt.

### <a name="create-the-subnet-and-vnet"></a>Het subnet en vNet maken

Maak het vNet en subNet van het [virtuele netwerk.](../../virtual-network/virtual-networks-overview.md)

1. Maak het subnet. In dit voorbeeld wordt een subnet met de naam **mySubNet**, in de brongroep **myResourceGroup,** en wordt het subnetadresvoorvoegsel ingesteld op **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Maak de vNet. In dit voorbeeld wordt de naam van het virtuele netwerk ingesteld als **myVnetName,** de locatie naar **West US**en het adresvoorvoegsel voor het virtuele netwerk op **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerkbeveiligingsgroep en een RDP-regel maken
   Als u inloggen op uw VM met RDP, moet u een beveiligingsregel hebben die RDP-toegang op poort 3389 toestaat. Omdat de VHD voor de nieuwe VM is gemaakt van een bestaande gespecialiseerde VM, u na het maken van de VM een bestaand account gebruiken van de bronvirtuele machine die toestemming had om u aan te melden met RDP.
   Dit moet worden voltooid voordat u de netwerkinterface maakt waaraan het wordt gekoppeld.  
   In dit voorbeeld wordt de NSG-naam ingesteld op **myNsg** en de naam van de RDP-regel op **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Zie Poorten openen voor een vm [in Azure met PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over eindpunten en NSG-regels.

### <a name="create-a-public-ip-address-and-nic"></a>Een openbaar IP-adres en NIC maken
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Maak het openbare IP.Create the public IP. In dit voorbeeld wordt de naam van het openbare IP-adres ingesteld op **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Maak de NIC. In dit voorbeeld wordt de NIC-naam ingesteld op **myNicName**. Deze stap associeert ook de netwerkbeveiligingsgroep die eerder met deze NIC is gemaakt.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>De naam en grootte van de VM instellen

In dit voorbeeld wordt de VM-naam ingesteld op 'myVM' en de VM-grootte op 'Standard_A2'.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Voeg de NIC toe
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>De OS-schijf configureren

1. Stel de URI in voor de VHD die u hebt geüpload of gekopieerd. In dit voorbeeld wordt het VHD-bestand met de naam **myOsDisk.vhd** bewaard in een opslagaccount met de naam **myStorageAccount** in een container met de naam **myContainer.**

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Voeg de OS-schijf toe. In dit voorbeeld wordt bij het maken van de OS-schijf de term 'osDisk' toegevoegd aan de VM-naam om de naam van de OS-schijf te maken. In dit voorbeeld wordt ook aangegeven dat deze VHD op Windows-gebaseerde VHD als de OS-schijf aan de VM moet worden gekoppeld.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Optioneel: Als u gegevensschijven hebt die aan de VM moeten worden gekoppeld, voegt u de gegevensschijven toe met behulp van de URL's van gegevens-VHD's en het juiste logische eenheidsnummer (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Bij het gebruik van een opslagaccount zien de URL's van de gegevens en de schijf van het besturingssysteem er ongeveer als volgt uit: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. U dit vinden op de portal door te bladeren naar de doelopslagcontainer, te klikken op het besturingssysteem of gegevens VHD die is gekopieerd en vervolgens de inhoud van de URL te kopiëren.


### <a name="complete-the-vm"></a>De VM voltooien 

Maak de VM met behulp van de configuraties die we zojuist hebben gemaakt.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Als deze opdracht is geslaagd, ziet u de uitvoer als volgt:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Controleren of de VM is gemaakt
U moet de nieuw gemaakte VM zien in de [Azure-portal,](https://portal.azure.com)onder**Virtuele machines** **voor alle services** > of met behulp van de volgende PowerShell-opdrachten:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Meld u aan bij uw nieuwe virtuele machine. Zie [Verbinding maken en inloggen op een virtuele Azure-machine met Windows voor](connect-logon.md)meer informatie.

