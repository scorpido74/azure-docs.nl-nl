---
title: Herstel na noodgevallen voor Azure VM's met Azure PowerShell en Azure Site Recovery
description: Meer informatie over het instellen van noodherstel voor virtuele Azure-machines met Azure Site Recovery met Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212287"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Noodherstel instellen voor Azure virtuele machines met Azure PowerShell

In dit artikel ziet u hoe u noodherstel voor azure-virtuele machines instelt en test met Azure PowerShell.

Procedures voor:

> [!div class="checklist"]
> - Maak een Recovery Services-kluis.
> - Stel de kluiscontext in voor de PowerShell-sessie.
> - Bereid de kluis voor om azure virtuele machines te repliceren.
> - Netwerktoewijzingen maken.
> - Maak opslagaccounts om virtuele machines naar te repliceren.
> - Repliceer virtuele Azure-machines naar een herstelregio voor herstel na noodgevallen.
> - Voer een failover van de test uit, valideer en ruim de failover op.
> - Faal naar het herstelgebied.

> [!NOTE]
> Mogelijk zijn niet alle scenariomogelijkheden die via de portal beschikbaar zijn, beschikbaar via Azure PowerShell. Enkele scenariomogelijkheden die momenteel niet via Azure PowerShell worden ondersteund, zijn:
> - De mogelijkheid om op te geven dat alle schijven in een virtuele machine moeten worden gerepliceerd zonder dat u elke schijf van de virtuele machine expliciet hoeft op te geven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Raadpleeg de [ondersteuningsvereisten](azure-to-azure-support-matrix.md) voor alle onderdelen.
- U beschikt over `Az` de Azure PowerShell-module. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [handleiding voor het installeren en configureren van Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Meld u aan bij `Connect-AzAccount` uw Azure-abonnement met de cmdlet.

```azurepowershell
Connect-AzAccount
```

Selecteer uw Azure-abonnement. Gebruik `Get-AzSubscription` de cmdlet om de lijst met Azure-abonnementen waartoe u toegang hebt te krijgen. Selecteer het Azure-abonnement waarmee `Set-AzContext` u wilt werken met de cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Informatie over de te repliceren virtuele machine

In dit artikel wordt een virtuele machine in de regio Oost-VS gerepliceerd naar en hersteld in de regio West US 2. De virtuele machine die wordt gerepliceerd heeft een OS-schijf en een enkele gegevensschijf. De naam van de virtuele machine `AzureDemoVM`die in het voorbeeld wordt gebruikt is .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Ontvang schijfgegevens voor de schijven van de virtuele machine. Schijfgegevens worden later gebruikt bij het starten van replicatie voor de virtuele machine.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak een resourcegroep waarin u de kluis Herstelservices maken.

> [!IMPORTANT]
> * De kluis Herstelservices en de virtuele machines die worden beschermd, moeten zich op verschillende Azure-locaties bevinden.
> * De brongroep van de kluis Herstelservices en de virtuele machines die worden beveiligd, moeten zich op verschillende Azure-locaties bevinden.
> * De kluis Herstelservices en de brongroep waartoe deze behoort, kunnen zich op dezelfde Azure-locatie bevinden.

In het voorbeeld in dit artikel, de virtuele machine wordt beschermd is in het Oosten van de VS regio. Het herstelgebied dat is geselecteerd voor noodherstel is de regio West US 2. De kluis van de herstelservices en de resourcegroep van de kluis bevinden zich beide in het herstelgebied, West US 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Maak een kluis van herstelservices. In dit voorbeeld wordt een `a2aDemoRecoveryVault` kluis van Recovery Services met de naam gemaakt in de regio West US 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>De context van de kluis instellen

Stel de kluiscontext in voor gebruik in de PowerShell-sessie. Nadat de vaultcontext is ingesteld, worden Azure Site Recovery-bewerkingen in de PowerShell-sessie uitgevoerd in de context van de geselecteerde kluis.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Voor een Azure-naar-Azure-migratie u de vault-context instellen op de nieuw gemaakte kluis:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>De kluis voorbereiden om azure virtuele machines te repliceren

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Een fabricobject Siteherstel maken om het primaire (bron)gebied weer te geven

Het stofobject in de kluis vertegenwoordigt een Azure-gebied. Het primaire weefselobject wordt gemaakt om het Azure-gebied weer te geven waartoe virtuele machines behoren tot de kluis. In het voorbeeld in dit artikel, de virtuele machine wordt beschermd is in het Oosten van de VS regio.

- Per regio kan slechts één weefselobject worden gemaakt.
- Als u eerder siteherstelreplicatie voor een vm in de Azure-portal hebt ingeschakeld, maakt Siterecovery automatisch een fabricobject. Als er een fabricobject bestaat voor een regio, u geen nieuw object maken.

Voordat u begint, begrijpt u dat siteherstelbewerkingen asynchroon worden uitgevoerd. Wanneer u een bewerking start, wordt een Azure Site Recovery-taak ingediend en wordt een taakvolgobject geretourneerd. Gebruik het object taakbijhouden om de`Get-AzRecoveryServicesAsrJob`laatste status voor de taak te krijgen ( ) en om de status van de bewerking te controleren.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Als virtuele machines uit meerdere Azure-regio's worden beschermd tot dezelfde kluis, maakt u één fabricobject voor elke bron Azure-regio.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Een object siteherstelmaken om het herstelgebied weer te geven

Het object herstelstructuur vertegenwoordigt de locatie van het herstel azure. Als er een failover is, worden virtuele machines gerepliceerd en hersteld naar het herstelgebied dat wordt vertegenwoordigd door de herstelstructuur. De azure-regio voor herstel die in dit voorbeeld wordt gebruikt, is West US 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Een siteherstelbeveiligingscontainer maken in de primaire stof

De beveiligingscontainer is een container die wordt gebruikt om gerepliceerde items in een stof te groeperen.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Een siteherstelbeveiligingscontainer maken in de terugwinningsstructuur

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Een beveiligingscontainertoewijzing maken tussen de primaire en terugwinningsbeschermingscontainer

Een beveiligingscontainertoewijzing brengt de primaire beveiligingscontainer in kaart met een terugwinningsbeschermingscontainer en een replicatiebeleid. Maak één toewijzing voor elk replicatiebeleid dat u gebruikt om virtuele machines te repliceren tussen een containerpaar voor beveiliging.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Een beveiligingscontainertoewijzing maken voor failback (reverse replication na een failover)

Wanneer u klaar bent om de mislukte virtuele machine terug te brengen naar de oorspronkelijke Azure-regio, doet u een failback wanneer u klaar bent om de mislukte virtuele machine terug te brengen naar de oorspronkelijke Azure-regio. Om terug te mislukken, wordt de mislukte virtuele machine omgekeerd gerepliceerd van de mislukte bovenregio naar de oorspronkelijke regio. Voor omgekeerde replicatie schakelen de rollen van het oorspronkelijke gebied en het herstelgebied over. De oorspronkelijke regio wordt nu de nieuwe herstelregio, en wat oorspronkelijk het herstelgebied was, wordt nu de primaire regio. De toewijzing van beveiligingscontainervoor omgekeerde replicatie vertegenwoordigt de geschakelde rollen van de oorspronkelijke en herstelgebieden.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Cacheopslagaccount en doelopslagaccount maken

Een cacheopslagaccount is een standaardopslagaccount in dezelfde Azure-regio als de virtuele machine die wordt gerepliceerd. Het cacheopslagaccount wordt gebruikt om replicatiewijzigingen tijdelijk vast te houden voordat de wijzigingen worden verplaatst naar het herstelgebied Azure. U ervoor kiezen om, maar het is niet nodig, om verschillende cache opslagaccounts voor de verschillende schijven van een virtuele machine op te geven.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Voor virtuele machines **die geen beheerde schijven gebruiken,** is het doelopslagaccount het opslagaccount in het herstelgebied waaraan schijven van de virtuele machine worden gerepliceerd. Het doelopslagaccount kan een standaardopslagaccount of een premium opslagaccount zijn. Selecteer het type opslagaccount dat vereist is op basis van de gegevenswijzigingssnelheid (IO-schrijfsnelheid) voor de schijven en de door Azure Site Recovery ondersteunde churn-limieten voor het opslagtype.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Netwerktoewijzingen maken

Een netwerktoewijzing brengt virtuele netwerken in de primaire regio in kaart aan virtuele netwerken in het herstelgebied. De netwerktoewijzing geeft het virtuele Azure-netwerk in het herstelgebied op, waar naar een virtuele machine in het primaire virtuele netwerk moet mislukken. Eén Virtueel Azure-netwerk kan worden toegewezen aan slechts één virtueel Azure-netwerk in een herstelgebied.

- Maak een virtueel Azure-netwerk in het herstelgebied om niet over te gaan:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Haal het primaire virtuele netwerk op. De VNet waarmee de virtuele machine is aangesloten:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Netwerktoewijzing maken tussen het primaire virtuele netwerk en het virtuele herstelnetwerk:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Netwerktoewijzing maken voor de omgekeerde richting (terugfalen):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure virtuele machine repliceren

Repliceer de virtuele Azure-machine met **beheerde schijven**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Repliceer de virtuele Azure-machine met **onbeheerde schijven.**

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Zodra de replicatiebewerking van de start is geslaagd, worden virtuele machinegegevens gerepliceerd naar het herstelgebied.

Het replicatieproces begint met het in eerste instantie zaaien van een kopie van de replicerende schijven van de virtuele machine in het herstelgebied. Deze fase wordt de eerste replicatiefase genoemd.

De eerste replicatie van AFter is voltooid, replicatie wordt verplaatst naar de differentiële synchronisatiefase. Op dit punt is de virtuele machine beschermd en kan er een testfailoverbewerking op worden uitgevoerd. De replicatiestatus van het gerepliceerde item dat de virtuele machine **vertegenwoordigt,** gaat naar de status Beveiligd nadat de eerste replicatie is voltooid.

Controleer de replicatiestatus en replicatiestatus voor de virtuele machine door details op te halen van het replicatiebeveiligde item dat ermee overeenkomt.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Een failovertest, valideren en opschonen van een failover doen

Nadat replicatie voor de virtuele machine een beveiligde status heeft bereikt, kan een testfailoverbewerking worden uitgevoerd op de virtuele machine (op het replicatiebeveiligde item van de virtuele machine).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Doe een test failover.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Wacht tot de failoverbewerking is voltooid.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Nadat de failovertaak van de test is voltooid, u verbinding maken met de test die is mislukt via de virtuele machine en de testfailover valideren.

Zodra het testen is voltooid op de test is mislukt over virtuele machine, het opschonen van de test kopie door te beginnen met de cleanup test failover operatie. Met deze bewerking wordt het testexemplaar verwijderd van de virtuele machine die is gemaakt door de testfailover.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Failover naar Azure

Fail over de virtuele machine naar een specifiek herstelpunt.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Wanneer de failovertaak succesvol is, u de failoverbewerking vastleggen.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Opnieuw beveiligen en terugnaar het brongebied

Als u na een failover klaar bent om terug te gaan naar het oorspronkelijke `Update-AzRecoveryServicesAsrProtectionDirection` gebied, begint u met reverse replicatie voor het replicatiebeveiligde item met de cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Nadat de herbescherming is voltooid, u mislukken in de omgekeerde richting, West-VS naar Oost-VS, en niet terug naar de bron regio.

## <a name="disable-replication"></a>Replicatie uitschakelen

U replicatie `Remove-AzRecoveryServicesAsrReplicationProtectedItem` uitschakelen met de cmdlet.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [PowerShell-verwijzing naar Azure Site Recovery](/powershell/module/az.RecoveryServices) voor meer informatie over hoe u andere taken uitvoeren, zoals het maken van herstelplannen en het testen van failover van herstelplannen met PowerShell.
