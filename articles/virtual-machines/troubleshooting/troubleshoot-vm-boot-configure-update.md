---
title: Het opstarten van de VM is vastgelopen op het ophalen van Windows Ready. Uw computer niet uitschakelen in azure | Microsoft Docs
description: Voer de stappen uit om het probleem op te lossen waarbij het opstarten van de VM vastloopt op ' Windows voorbereiden '. Schakel de computer niet uit.'
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2f3c18ea1887ea5b05bb89f85371139ac83dfe49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080166"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Het opstarten van de VM is vastgelopen op het ophalen van Windows Ready. Uw computer niet uitschakelen in azure

Dit artikel helpt u bij het oplossen van het probleem wanneer uw virtuele machine (VM) vastloopt op het Windows voorbereiden. De computer niet uitschakelen tijdens het opstarten.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Symptomen

Wanneer u **Diagnostische gegevens over opstarten** gebruikt om de scherm opname van een virtuele machine op te halen, wordt het besturings systeem niet volledig opgestart. Op de VM wordt het bericht ' Windows gereed ' opgehaald weer gegeven. Schakel de computer niet uit.'

![Voorbeeld bericht voor Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Voorbeeld bericht](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Oorzaak

Dit probleem treedt meestal op wanneer de server de laatste keer opnieuw wordt opgestart nadat de configuratie is gewijzigd. De configuratie wijziging kan worden geïnitialiseerd door Windows-updates of door de wijzigingen in de functies/functie van de server. Als de grootte van de updates groot is, heeft het besturings systeem meer tijd nodig om de wijzigingen opnieuw te configureren. Windows Update

## <a name="back-up-the-os-disk"></a>Back-up maken van de besturingssysteem schijf

Maak een back-up van de besturingssysteem schijf voordat u het probleem probeert op te lossen.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Voor Vm's met een versleutelde schijf moet u eerst de schijven ontgrendelen

Volg deze stappen om te bepalen of de virtuele machine een versleutelde virtuele machine is.

1. Open de virtuele machine op de Azure Portal en blader vervolgens naar de schijven.

2. Bekijk de versleutelings kolom om te controleren of versleuteling is ingeschakeld.

Als de schijf met het besturings systeem is versleuteld, ontgrendelt u de versleutelde schijf. Voer de volgende stappen uit om de schijf te ontgrendelen.

1. Maak een herstel-VM die zich in dezelfde resource groep, opslag account en locatie bevindt als de betrokken VM.

2. Verwijder de betreffende VM in het Azure Portal en behoud de schijf.

3. Voer Power shell uit als beheerder.

4. Voer de volgende cmdlet uit om de geheime naam op te halen.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Nadat u de geheime naam hebt, voert u de volgende opdrachten uit in Power shell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Converteer de base64-gecodeerde waarde naar bytes en schrijf de uitvoer naar een bestand. 

    > [!Note]
    > Als u de USB-ontgrendelings optie gebruikt, moet de bestands naam van het BEK overeenkomen met de oorspronkelijke BEK-GUID. Maak een map op uw C-station met de naam ' BEK ' voordat u deze stappen uitvoert.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Nadat het BEK-bestand op uw PC is gemaakt, kopieert u het bestand naar de herstel-VM waaraan de vergrendelde besturingssysteem schijf is gekoppeld. Voer de volgende opdrachten uit met behulp van de BEK-bestands locatie.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Optioneel**: In sommige gevallen kan het nodig zijn om de schijf te ontsleutelen met behulp van deze opdracht.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > De vorige opdracht gaat ervan uit dat de schijf die moet worden versleuteld, is op letter F.

8. Als u logboeken wilt verzamelen, gaat u naar het pad **stationsletter: \ Windows\System32\winevt\Logs**.

9. Ontkoppel het station van de herstel machine.

### <a name="create-a-snapshot"></a>Een momentopname maken

Als u een moment opname wilt maken, volgt u de stappen in [een moment opname van een schijf](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Een dump van het besturings systeem verzamelen

Volg de stappen in de sectie [OS-dump verzamelen](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) om een OS-dump te verzamelen wanneer de virtuele machine op de configuratie is vastgelopen.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Nadat u het dump bestand hebt verzameld, neemt u contact op met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om de hoofd oorzaak te analyseren.


## <a name="rebuild-the-vm-by-using-powershell"></a>De virtuele machine opnieuw samen stellen met behulp van Power shell

Nadat u het geheugen dump bestand hebt verzameld, volgt u deze stappen om de virtuele machine opnieuw op te bouwen.

**Voor niet-beheerde schijven**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Voor beheerde schijven**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
