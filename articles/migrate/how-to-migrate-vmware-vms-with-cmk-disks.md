---
title: VMware-virtuele machines migreren naar Azure met server-side encryption(SSE) en door de klant beheerde sleutels (CMK) met Azure Migrate Server Migration
description: Meer informatie over het migreren van VMware VM's naar Azure met server-side encryption(SSE) en door de klant beheerde sleutels (CMK) met Azure Migrate Server Migration
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269482"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>VMware VM's migreren naar Azure VM's ingeschakeld met server-side encryptie en door de klant beheerde sleutels

In dit artikel wordt beschreven hoe u VMware VM's migreert naar virtuele Azure-machines met schijven die zijn versleuteld met server-side encryption (SSE) met door de klant beheerde sleutels (CMK), met azure migrate server migratie (agentless replicatie).

Met de migratieportal-ervaring azure migreren server- poortervaring u [VMware VM's migreren naar Azure met agentless replicatie.](tutorial-migrate-vmware.md) De portalervaring biedt momenteel niet de mogelijkheid om SSE met CMK in te schakelen voor uw gerepliceerde schijven in Azure. De mogelijkheid om SSE met CMK in te schakelen voor de gerepliceerde schijven is momenteel alleen beschikbaar via REST API. In dit artikel ziet u hoe u een [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) maakt en implementeert om een Vm van VMware te repliceren en de gerepliceerde schijven in Azure te configureren om SSE met CMK te gebruiken.

De voorbeelden in dit artikel gebruiken [Azure PowerShell](/powershell/azure/new-azureps-module-az) om de taken uit te voeren die nodig zijn om de resourcemanagersjabloon te maken en te implementeren.

[Meer informatie](../virtual-machines/windows/disk-encryption.md) over server-side encryptie (SSE) met door de klant beheerde sleutels (CMK) voor beheerde schijven.

## <a name="prerequisites"></a>Vereisten

- [Bekijk de zelfstudie](tutorial-migrate-vmware.md) over migratie van VMware VM's naar Azure met agentless replicatie om inzicht te krijgen in de vereisten voor hulpprogramma's.
- [Volg deze instructies](how-to-add-tool-first-time.md) om een Azure Migrate-project te maken en voeg het hulpprogramma **Azure Migreren: Servermigratie** toe aan het project.
- [Volg deze instructies](how-to-set-up-appliance-vmware.md) om het Azure Migrate-toestel voor VMware in uw on-premises omgeving en volledige detectie in te stellen.

## <a name="prepare-for-replication"></a>Voorbereiden op replicatie

Zodra vm-detectie is voltooid, wordt in de regel Gedetecteerde servers op de tegel Servermigratie een aantal VMware VM's weergegeven die door het toestel zijn gedetecteerd.

Voordat u vm's repliceren, moet de replicatie-infrastructuur worden voorbereid.

1. Een instantie ServiceBus maken in het doelgebied. De servicebus wordt gebruikt door het on-premises Azure Migrate-toestel om te communiceren met de servermigratieservice om replicatie en migratie te coördineren.
2. Maak een opslagaccount voor de overdracht van bewerkingslogboeken van replicatie.
3. Maak een opslagaccount waarnaar het Azure Migrate-toestel replicatiegegevens uploadt.
4. Maak een Key Vault en configureer de Key Vault om handtekeningtokens voor gedeelde toegang te beheren voor blobtoegang op de opslagaccounts die in stap 3 en 4 zijn gemaakt.
5. Genereer een handtekeningtoken voor gedeelde toegang voor de servicebus die in stap 1 is gemaakt en maak een geheim voor het token in de Sleutelkluis die in de vorige stap is gemaakt.
6. Maak een key vault-toegangsbeleid om het on-premises Azure Migrate-toestel (met behulp van de toestelAAD-app) en de Server Migration Service toegang te geven tot de Key Vault.
7. Maak een replicatiebeleid en configureer de servermigratieservice met details over de replicatie-infrastructuur die in de vorige stap is gemaakt.

De replicatie-infrastructuur moet worden gemaakt in het doelazure-gebied voor de migratie en in het doelAzure-abonnement waarnaar de VM's worden gemigreerd.

De portalervaring Servermigratie vereenvoudigt de voorbereiding van de replicatie-infrastructuur door dit automatisch voor u te doen wanneer u een vm voor de eerste keer in een project repliceert. In dit artikel gaan we ervan uit dat u al een of meer VM's hebt gerepliceerd met behulp van de portalervaring en dat de replicatie-infrastructuur al is gemaakt. We bekijken hoe we details van de bestaande replicatie-infrastructuur kunnen ontdekken en hoe u deze gegevens gebruiken als invoer voor de resourcemanagersjabloon die wordt gebruikt om replicatie met CMK in te stellen.

### <a name="identifying-replication-infrastructure-components"></a>Onderdelen van replicatie-infrastructuur identificeren

1. Ga op de Azure-portal naar de pagina resourcegroepen en selecteer de resourcegroep waarin het Azure Migrate-project is gemaakt.
2. Selecteer **Implementaties** in het linkermenu en zoek naar een implementatienaam die begint met de tekenreeks *'Microsoft.MigrateV2.VMwareV2EnableMigrate'.* U ziet een lijst met Resource Manager-sjablonen die zijn gemaakt door de portalervaring om replicatie voor VM's in dit project in te stellen. We downloaden zo'n sjabloon en gebruiken dat als basis om de sjabloon voor te bereiden op replicatie met CMK.
3. Als u de sjabloon wilt downloaden, selecteert u een implementatie die overeenkomt met het tekenreekspatroon in de vorige stap > **Selecteer Sjabloon** in het linkermenu > Klik **op Downloaden** in het bovenste menu. Sla het bestand template.json lokaal op. U bewerkt dit sjabloonbestand in de laatste stap.

## <a name="create-a-disk-encryption-set"></a>Een schijfversleutelingsset maken

Een schijfversleutelingssetsetobject brengt beheerde schijven in een sleutelkluis met de CMK die voor SSE moet worden gebruikt. Als u VM's wilt repliceren met CMK, maakt u een schijfversleutelingsset en geeft u deze door als invoer voor de replicatiebewerking.

Volg [hier](../virtual-machines/windows/disk-encryption.md#powershell) het voorbeeld om een schijfversleutelingsset te maken met Azure PowerShell. Controleer of de schijfversleutelingsset is gemaakt in het doelabonnement waarnaar VM's worden gemigreerd en in het doelazure-gebied voor de migratie.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Informatie over de VMware VM om te migreren

In deze stap gebruikt u Azure PowerShell om de details van de VM te krijgen die moet worden gemigreerd. Deze gegevens worden gebruikt om de resourcemanagersjabloon voor replicatie te maken. Concreet zijn de twee eigenschappen van belang:

- De machine Resource ID voor de ontdekte VM's.
- De lijst met schijven voor de VM en hun schijf-id's.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Kopieer de waarde van de SiteId-tekenreeks die overeenkomt met het Azure Migrate-toestel dat de VM wordt gedetecteerd. In het bovenstaande voorbeeld is de SiteId *"/abonnementen/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVCMMwareK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Kopieer de resourceid-, naam- en schijfwaardewaarden voor de te migreren machine.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Sjabloon Resourcemanager maken voor replicatie

- Open het sjabloonbestand Resourcebeheer dat u hebt gedownload in de stap **Replicatie-infrastructuuronderdelen identificeren** in een editor naar keuze.
- Verwijder alle brondefinities uit de sjabloon, behalve bronnen die van het type *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"* zijn
- Als er meerdere resourcedefinities van het bovenstaande type zijn, verwijdert u alles behalve één. Verwijder eventuele **afhankelijke eigenschappenOp eigenschapsdefinities** uit de resourcedefinitie.
- Aan het einde van deze stap moet u een bestand hebben dat lijkt op het onderstaande voorbeeld en dezelfde set eigenschappen heeft.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Bewerk de **eigenschap naam** in de resourcedefinitie. Vervang de tekenreeks die de laatste "/" in de eigenschap naam volgt door de waarde van *$machine. Naam*(van de vorige stap).
- Wijzig de waarde van de eigenschap **properties.providerSpecificDetails.vmwareMachineId** met een waarde van *$machine. ResourceId*(uit de vorige stap).
- Stel de waarden voor **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** in op respectievelijk de doelgroep-ID, doelgroep-id voor virtuele netwerkbronnen en subnetnaam target.
- Stel de waarde van **licentieType** in op 'WindowsServer' om Azure Hybrid Benefit toe te passen voor deze vm. Als deze VM niet in aanmerking komt voor Azure Hybrid Benefit, stelt u de waarde van **licenseType** in op NoLicenseType.
- Wijzig de waarde van de eigenschap **targetVmName** in de gewenste Azure-naam voor virtuele machines voor de gemigreerde vm.
- Voeg eventueel een eigenschap met de naam **targetVmSize** toe onder de eigenschap **targetVmName.** Stel de waarde van de eigenschap **targetVmSize** in op de gewenste Azure virtuele machinegrootte voor de gemigreerde VM.
- De eigenschap **disksToInclude** is een lijst met schijfinvoervoor replicatie met elk lijstitem dat één on-premises schijf vertegenwoordigt. Maak evenveel lijstitems als het aantal schijven op de on-premises VM. Vervang de eigenschap **diskId** in het lijstitem door de uuid van de schijven die in de vorige stap zijn geïdentificeerd. Stel de **isOSDisk-waarde** in op 'true' voor de OS-schijf van de VM en 'false' voor alle andere schijven. Laat de **logStorageAccountId** en de **logStorageAccountSasSecretName-eigenschappen** ongewijzigd. Stel de **diskType-waarde** in op het type Azure Managed Disk (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) dat voor de schijf moet worden gebruikt. Voor de schijven die moeten worden versleuteld met CMK, voegt u een eigenschap met de naam **diskEncryptionSetId** toe en stelt u de waarde in op de bron-ID van de schijfversleutelingsset die is gemaakt(**$des. Id**) in de stap *Een schijfversleutelingsset maken*
- Sla het bewerkte sjabloonbestand op. In het bovenstaande voorbeeld ziet het bewerkte sjabloonbestand er als volgt uit:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Replicatie instellen

U nu de sjabloon bewerkt ResourceManager implementeren in de projectbrongroep om replicatie voor de VM in te stellen. Meer informatie over het [implementeren van resources met Azure Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Volgende stappen

[Monitor de replicatiestatus](tutorial-migrate-vmware.md#track-and-monitor) via de portalervaring en voer Testmigraties en migratie uit.
