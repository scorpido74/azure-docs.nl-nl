---
title: Azure VM-schijven uitsluiten van replicatie met Azure Site Recovery en Azure PowerShell
description: Meer informatie over het uitsluiten van schijven van virtuele Azure-machines tijdens Azure Site Recovery met Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973677"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Schijven uitsluiten van PowerShell-replicatie van Azure VM's

In dit artikel wordt beschreven hoe u schijven uitsluiten wanneer u Azure VM's nabootst. U schijven uitsluiten om de verbruikte replicatiebandbreedte of de doelbronnen die deze schijven gebruiken, te optimaliseren. Momenteel is deze mogelijkheid alleen beschikbaar via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

- Zorg ervoor dat u de [architectuur en componenten voor noodherstel](azure-to-azure-architecture.md)begrijpt.
- Raadpleeg de [ondersteuningsvereisten](azure-to-azure-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat u de AzureRm PowerShell-module "Az" hebt. Zie [PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u PowerShell wilt installeren of bijwerken.
- Zorg ervoor dat u ten minste één keer een kluis voor herstelservices hebt gemaakt en virtuele virtuele machines hebt beschermd. Als u deze dingen nog niet hebt gedaan, volgt u het proces bij [Het instellen van noodherstel voor virtuele Azure-machines met Azure PowerShell.](azure-to-azure-powershell.md)
- Als u op zoek bent naar informatie over het toevoegen van schijven aan een Azure VM die is ingeschakeld voor replicatie, [controleert u dit artikel](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Waarom schijven uitsluiten van replicatie
Mogelijk moet u schijven uitsluiten van replicatie omdat:

- Uw virtuele machine heeft [azure siteherstellimieten bereikt om gegevenswijzigingssnelheden te repliceren.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- De gegevens die op de uitgesloten schijf worden gechurn, zijn niet belangrijk of hoeven niet te worden gerepliceerd.

- U wilt opslag- en netwerkbronnen opslaan door de gegevens niet te repliceren.

## <a name="how-to-exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

In ons voorbeeld repliceren we een virtuele machine met één besturingssysteem en drie gegevensschijven in de regio Oost-VS naar de regio West US 2. De naam van de virtuele machine is *AzureDemoVM*. We sluiten schijf 1 uit en houden schijven 2 en 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Informatie over de virtuele machines om te repliceren

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Meer informatie over de schijven van de virtuele machine. Deze informatie wordt later gebruikt wanneer u de replicatie van de vm start.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Een virtuele Azure-machine repliceren

In het volgende voorbeeld gaan we ervan uit dat u al een cacheopslagaccount, replicatiebeleid en toewijzingen hebt. Als u deze dingen niet hebt, volgt u het proces bij [Noodherstel instellen voor virtuele Azure-machines met Azure PowerShell.](azure-to-azure-powershell.md)

Een virtuele Azure-machine repliceren met *beheerde schijven*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Wanneer de beginreplicatiebewerking slaagt, worden de VM-gegevens gerepliceerd naar het herstelgebied.

U naar de Azure-portal gaan en de gerepliceerde VM's bekijken onder 'gerepliceerde items'.

Het replicatieproces begint met het inzaaien van een kopie van de replicerende schijven van de virtuele machine in het herstelgebied. Deze fase wordt de eerste replicatiefase genoemd.

Nadat de eerste replicatie is voltooid, gaat replicatie door naar de differentiële synchronisatiefase. Op dit punt is de virtuele machine beschermd. Selecteer de beveiligde virtuele machine om te zien of schijven zijn uitgesloten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het uitvoeren van een testfailover](site-recovery-test-failover-to-azure.md).
