---
title: VMware-noodherstel instellen met PowerShell in Azure Site Revoery
description: Meer informatie over het instellen van replicatie en failover naar Azure voor noodherstel van VMware VM's met PowerShell in Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257197"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Noodherstel van VMware VM's instellen op Azure met PowerShell

In dit artikel ziet u hoe u virtuele Machines van VMware repliceren en failoveren naar Azure met Azure PowerShell.

Procedures voor:

> [!div class="checklist"]
> - Maak een vault van Recovery Services en stel de kluiscontext in.
> - Serverregistratie in de kluis valideren.
> - Replicatie instellen, inclusief een replicatiebeleid. Voeg uw vCenter-server toe en ontdek VM's.
> - Een vCenter-server toevoegen en ontdekken
> - Maak opslagaccounts om replicatielogboeken of -gegevens vast te houden en de VM's te repliceren.
> - Een failover uitvoeren. Configureer failover-instellingen en voer een instellingen uit voor het repliceren van virtuele machines.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](vmware-azure-architecture.md).
- Raadpleeg de [ondersteuningsvereisten](site-recovery-support-matrix-to-azure.md) voor alle onderdelen.
- U beschikt over `Az` de Azure PowerShell-module. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [handleiding voor het installeren en configureren van Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="log-into-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Selecteer het Azure-abonnement waaraan u uw Virtuele VMware-machines wilt repliceren. Gebruik de cmdlet Get-AzSubscription om de lijst met Azure-abonnementen waartoe u toegang hebt, te krijgen. Selecteer het Azure-abonnement waarmee u wilt werken met de cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Een Recovery Services-kluis instellen

1. Maak een resourcegroep waarin u de kluis Herstelservices maken. In het onderstaande voorbeeld wordt de resourcegroep VMwareDRtoAzurePS genoemd en wordt gemaakt in de regio Oost-Azië.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Maak een kluis van herstelservices. In het onderstaande voorbeeld wordt de kluis Herstelservices vmwaredrtoazureps genoemd en wordt gemaakt in de regio Oost-Azië en in de resourcegroep die in de vorige stap is gemaakt.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Download de kluisregistratiesleutel voor de kluis. De kluisregistratiesleutel wordt gebruikt om de on-premises configuratieserver te registreren bij deze kluis. Registratie maakt deel uit van het installatieproces van de Configuration Server-software.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Gebruik de gedownloade kluisregistratiesleutel en volg de stappen in de onderstaande artikelen om de installatie en registratie van de configuratieserver te voltooien.
   - [Kies uw beschermingsdoelen](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [De bronomgeving instellen](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>De context van de kluis instellen

Stel de kluiscontext in met de cmdlet Set-ASRVaultContext. Eenmaal ingesteld worden de volgende Azure Site Recovery-bewerkingen in de PowerShell-sessie uitgevoerd in de context van de geselecteerde kluis.

> [!TIP]
> De Azure Site Recovery PowerShell-module (Az.RecoveryServices-module) wordt geleverd met eenvoudig te gebruiken aliassen voor de meeste cmdlets. De cmdlets in de module nemen de vorm * \<Operation>-**AzRecoveryServicesAsr**\<Object>* en hebben gelijkwaardige aliassen die de vorm * \<Operatie>-**ASR**\<Object>*. U de cmdletaliassen vervangen voor gebruiksgemak.

In het onderstaande voorbeeld worden de kluisdetails van de $vault variabele gebruikt om de kluiscontext voor de PowerShell-sessie op te geven.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Als alternatief voor de map-ASRVaultContext-cmdlet kan men ook de cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile gebruiken om de vaultcontext in te stellen. Geef het pad op waarop het kluisregistratiesleutelbestand zich bevindt als de parameter -pad naar de cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Bijvoorbeeld:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
In de volgende secties van dit artikel wordt ervan uitgegaan dat de vault context voor Azure Site Recovery-bewerkingen is ingesteld.

## <a name="validate-vault-registration"></a>Vault-registratie valideren

In dit voorbeeld hebben we het volgende:

- Er is een configuratieserver **(ConfigurationServer)** geregistreerd bij deze kluis.
- Een extra processerver (**ScaleOut-ProcessServer**) is geregistreerd op *ConfigurationServer*
- Accounts (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) zijn ingesteld op de configuratieserver. Deze accounts worden gebruikt om de vCenter-server toe te voegen, om virtuele machines te ontdekken en om de mobiliteitsservicesoftware op Windows- en Linux-servers die moeten worden gerepliceerd, te pushen.

1. Geregistreerde configuratieservers worden weergegeven door een fabricobject in Site Recovery. Download de lijst met fabricobjecten in de kluis en identificeer de configuratieserver.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identificeer de processervers die kunnen worden gebruikt om machines te repliceren.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Uit de bovenstaande ***uitvoer komt $ProcessServers[0]*** overeen met *ScaleOut-ProcessServer* en ***$ProcessServers[1]*** overeenkomt met de rol Process Server op *ConfigurationServer*

3. Identificeer accounts die zijn ingesteld op de configuratieserver.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Van de bovenstaande uitvoer ***komt $AccountHandles[0]*** overeen met de rekening *vCenter_account*, ***$AccountHandles[1]*** om WindowsAccount te *accounteren*en ***$AccountHandles[2]*** om LinuxAccount te *accounteren*

## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

In deze stap worden twee replicatiebeleidsregels gemaakt. Het ene beleid om virtuele Machines van VMware te repliceren naar Azure en het andere om te repliceren is mislukt via virtuele machines die in Azure worden uitgevoerd, terug naar de on-premises VMware-site.

> [!NOTE]
> De meeste Azure Site Recovery-bewerkingen worden asynchroon uitgevoerd. Wanneer u een bewerking start, wordt een Azure Site Recovery-taak ingediend en wordt een taakvolgobject geretourneerd. Dit taakvolgobject kan worden gebruikt om de status van de bewerking te controleren.

1. Maak een replicatiebeleid met de naam *Replicatiebeleid* om virtuele Machines van VMware te repliceren naar Azure met de opgegeven eigenschappen.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Maak een replicatiebeleid dat u wilt gebruiken voor failback van Azure naar de on-premises VMware-site.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Gebruik de taakgegevens in *$Job_FailbackPolicyCreate* om de bewerking bij te houden tot de voltooiing.

   * Maak een beveiligingscontainertoewijzing om replicatiebeleid met de configuratieserver in kaart te brengen.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Een vCenter-server toevoegen en VM's ontdekken

Voeg een vCenterServer toe op IP-adres of hostnaam. De parameter **-port** geeft de poort op op de vCenter-server waarmee verbinding moet worden gemaakt, **-Parameter Naam** geeft een vriendelijke naam op die moet worden gebruikt voor de vCenter-server en de parameter **-Account** geeft de accountgreep op de configuratieserver op die moet worden gebruikt om virtuele machines te detecteren die door de vCenter-server worden beheerd.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Opslagaccounts maken voor replicatie

**Als u naar beheerde schijf wilt schrijven, gebruikt u [powershell Az.RecoveryServices module 2.0.0.](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)** Het vereist alleen het aanmaken van een logopslagaccount. Het wordt aanbevolen om een standaard accounttype en LRS-redundantie te gebruiken, omdat het wordt gebruikt om alleen tijdelijke logboeken op te slaan. Controleer of het opslagaccount is gemaakt in hetzelfde Azure-gebied als de kluis.

Als u een versie van de Az.RecoveryServices-module gebruikt die ouder is dan 2.0.0, gebruikt u de volgende stappen om opslagaccounts te maken. Deze opslagaccounts worden later gebruikt om virtuele machines te repliceren. Controleer of de opslagaccounts zijn gemaakt in hetzelfde Azure-gebied als de kluis. U deze stap overslaan als u van plan bent een bestaand opslagaccount te gebruiken voor replicatie.

> [!NOTE]
> Terwijl u on-premises virtuele machines repliceert naar een premium opslagaccount, moet u een extra standaardopslagaccount opgeven (logboekopslagaccount). De logboekopslagaccount bevat replicatielogboeken als tussenopslag totdat de logboeken kunnen worden toegepast op het premium opslagdoel.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware VM's repliceren

Het duurt ongeveer 15-20 minuten voor virtuele machines worden ontdekt vanaf de vCenter-server. Eenmaal ontdekt, wordt een beschermbaar itemobject gemaakt in Azure Site Recovery voor elke ontdekte virtuele machine. In deze stap worden drie van de gedetecteerde virtuele machines gerepliceerd naar de Azure Storage-accounts die in de vorige stap zijn gemaakt.

U hebt de volgende gegevens nodig om een ontdekte virtuele machine te beschermen:

* Het te repliceren object.
* Het opslagaccount om de virtuele machine te repliceren naar (alleen als u re-replica's naar opslagaccount). 
* Een logboekopslag is nodig om virtuele machines te beschermen tegen een premium opslagaccount of een beheerde schijf.
* De processerver die moet worden gebruikt voor replicatie. De lijst met beschikbare processervers is opgehaald en opgeslagen in de ***variabelen $ProcessServers[0]****(ScaleOut-ProcessServer)* en ***$ProcessServers[1]*** *(ConfigurationServer).*  
* Het account dat moet worden gebruikt om de Mobility-servicesoftware op de machines te pushen. De lijst met beschikbare accounts is opgehaald en opgeslagen in de ***$AccountHandles*** variabele.
* De toewijzing van beveiligingscontainervoor het replicatiebeleid dat moet worden gebruikt voor replicatie.
* De resourcegroep waarin virtuele machines moeten worden gemaakt bij failover.
* Optioneel moet het virtuele Azure-netwerk en subnet waarop de defecte virtuele machine moet worden aangesloten.

Repliceer nu de volgende virtuele machines met de instellingen die in deze tabel zijn opgegeven


|Virtuele machine  |Processerver        |Opslagaccount              |Logboekopslagaccount  |Beleid           |Account voor de installatie van mobility-diensten|Doelgroepgroep  | Virtueel netwerk targeten  |Doelsubnet  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N.v.t.| logstorageaccount1                 |Replicatiebeleid|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |Replicatiebeleid|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicatiestdstorageaccount1| N.v.t.                 |Replicatiebeleid|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Zodra de replicatietaak inschakelen is voltooid, wordt de eerste replicatie gestart voor de virtuele machines. De eerste replicatie kan een tijdje duren, afhankelijk van de hoeveelheid gegevens die moet worden gerepliceerd en de beschikbare bandbreedte voor replicatie. Nadat de eerste replicatie is voltooid, wordt de virtuele machine verplaatst naar een beveiligde status. Zodra de virtuele machine een beschermde status bereikt, u een testfailover voor de virtuele machine uitvoeren, toevoegen aan herstelplannen enz.

U de replicatiestatus en replicatiestatus van de virtuele machine controleren met de cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Failoverinstellingen configureren

Failoverinstellingen voor beveiligde machines kunnen worden bijgewerkt met de cmdlet Set-ASRReplicationProtectedItem. Enkele van de instellingen die via deze cmdlet kunnen worden bijgewerkt, zijn:
* Naam van de virtuele machine die moet worden gemaakt bij failover
* VM-grootte van de virtuele machine die moet worden gemaakt bij failover
* Azure virtueel netwerk en subnet dat de NIC's van de virtuele machine moeten worden aangesloten op failover
* Failover naar beheerde schijven
* Azure-voordeel voor hybride gebruik toepassen
* Wijs een statisch IP-adres toe van het virtuele doelnetwerk dat bij failover aan de virtuele machine moet worden toegewezen.

In dit voorbeeld werken we de VM-grootte van de virtuele machine bij die moet worden gemaakt bij failover voor de virtuele machine *Win2K12VM1* en geven we op dat de virtuele machine beheerde schijven gebruikt bij failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Voer als volgt een DR-drill (testfailover) uit:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Zodra de testfailovertaak is voltooid, zult u merken dat een virtuele machine die is gekoppeld aan *"-Test"* (Win2K12VM1-Test in dit geval) op naam wordt gemaakt in Azure.
3. U nu verbinding maken met de test mislukt via virtuele machine, en valideren van de test failover.
4. Ruim de testfailover op met de cmdlet Start-ASRTestFailoverCleanupJob. Met deze bewerking wordt de virtuele machine verwijderd die is gemaakt als onderdeel van de failoverbewerking van de test.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Failover naar Azure

In deze stap, we niet over de virtuele machine Win2K12VM1 naar een specifiek herstelpunt.

1. Ontvang een lijst met beschikbare herstelpunten die u gebruiken voor de failover:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Nadat deze is mislukt, u de failoverbewerking vastleggen en omgekeerde replicatie van Azure opnieuw instellen naar de on-premises VMware-site.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het automatiseren van meer taken met de [Azure Site Recovery PowerShell-referentie](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
