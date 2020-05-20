---
title: Versleuteling aan de server zijde van Azure Managed Disks-Power shell
description: Azure Storage beveiligt uw gegevens door deze te versleutelen voordat deze in de opslag clusters worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw beheerde schijven, of u kunt door de klant beheerde sleutels gebruiken om versleuteling te beheren met uw eigen sleutels.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 164ce87df77d81a7d36d4448f5d8da8287ed0a01
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656714"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Versleuteling aan de server zijde van Azure Managed disks

Met Azure Managed disks worden uw gegevens standaard automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met SSE (server side Encryption) beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2. Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

Versleuteling heeft geen invloed op de prestaties van beheerde schijven en er zijn geen extra kosten voor de versleuteling. 

> [!NOTE]
> Tijdelijke schijven zijn geen beheerde schijven en worden niet versleuteld door SSE; Zie [overzicht van Managed disks: Disk roles](managed-disks-overview.md#disk-roles)(Engelstalig) voor meer informatie over tijdelijke schijven.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in Managed disks. 

In de volgende secties worden de opties voor sleutel beheer in meer detail beschreven.

## <a name="platform-managed-keys"></a>Door het platform beheerde sleutels

Beheerde schijven gebruiken standaard versleutelings sleutels die door het platform worden beheerd. Vanaf 10 juni 2017 worden alle nieuwe beheerde schijven, moment opnamen, installatie kopieën en nieuwe gegevens die worden geschreven naar bestaande beheerde schijven automatisch versleuteld met door het platform beheerde sleutels.

## <a name="customer-managed-keys"></a>Door klant beheerde sleutels

U kunt ervoor kiezen om versleuteling te beheren op het niveau van elke beheerde schijf, met uw eigen sleutels. Versleuteling aan de server zijde voor Managed disks met door de klant beheerde sleutels biedt een geïntegreerde ervaring met Azure Key Vault. U kunt [uw RSA-sleutels](../../key-vault/keys/hsm-protected-keys.md) importeren naar uw Key Vault of nieuwe RSA-sleutels in azure Key Vault genereren. 

Met Azure Managed disks wordt de versleuteling en ontsleuteling op een volledig transparante manier verwerkt met behulp van [envelop versleuteling](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Hiermee worden gegevens versleuteld met behulp van een [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-gegevens versleutelings sleutel (dek), die op zijn beurt wordt beveiligd met uw sleutels. De opslag service genereert gegevens versleutelings sleutels en versleutelt deze met door de klant beheerde sleutels met behulp van RSA-versleuteling. Met de envelop versleuteling kunt u uw sleutels regel matig draaien (wijzigen) volgens uw nalevings beleid zonder dat dit van invloed is op uw Vm's. Wanneer u uw sleutels roteert, versleutelt de opslag service de versleutelings sleutels opnieuw met de nieuwe door de klant beheerde sleutels. 

U moet toegang verlenen tot beheerde schijven in uw Key Vault om uw sleutels te gebruiken voor het versleutelen en ontsleutelen van de DEK. Op die manier kunt u uw gegevens en sleutels volledig beheren. U kunt uw sleutels uitschakelen of de toegang tot beheerde schijven op elk gewenst moment intrekken. U kunt ook het gebruik van de versleutelings sleutel met Azure Key Vault bewaking controleren om ervoor te zorgen dat alleen beheerde schijven of andere vertrouwde Azure-Services toegang krijgen tot uw sleutels.

Voor Premium Ssd's, Standard Ssd's en Standard Hdd's: wanneer u uw sleutel uitschakelt of verwijdert, worden alle Vm's met schijven die gebruikmaken van die sleutel automatisch afgesloten. Daarna kan de virtuele machines niet worden gebruikt, tenzij de sleutel opnieuw wordt ingeschakeld of u een nieuwe sleutel toewijst.

Wanneer u een sleutel uitschakelt of verwijdert, worden virtuele machines met ultra schijven die de sleutel gebruiken niet automatisch afgesloten voor Ultra schijven. Zodra u de toewijzing van de Vm's ongedaan maakt en opnieuw start, worden de schijven niet meer gebruikt en worden de Vm's niet weer online gezet. Als u de virtuele machines weer online wilt zetten, moet u een nieuwe sleutel toewijzen of de bestaande sleutel inschakelen.

In het volgende diagram ziet u hoe beheerde schijven gebruikmaken van Azure Active Directory en Azure Key Vault om aanvragen te maken met de door de klant beheerde sleutel:

![Werk stroom voor beheerde schijven en door de klant beheerde sleutels. Een beheerder maakt een Azure Key Vault, maakt vervolgens een set schijf versleuteling en stelt de schijf versleuteling in. De set is gekoppeld aan een virtuele machine, waardoor de schijf gebruik kan maken van Azure AD voor verificatie](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


In de volgende lijst wordt het diagram gedetailleerd beschreven:

1. Een Azure Key Vault beheerder maakt essentiële bronnen voor de kluis.
1. Met de sleutel kluis beheerder worden de RSA-sleutels geïmporteerd in Key Vault of nieuwe RSA-sleutels in Key Vault gegenereerd.
1. Die beheerder maakt een exemplaar van de bron voor de schijf versleutelings en geeft een Azure Key Vault-id en een sleutel-URL op. Schijf Encryption set is een nieuwe bron die is geïntroduceerd voor het vereenvoudigen van het sleutel beheer voor beheerde schijven. 
1. Wanneer een schijf versleutelings set wordt gemaakt, wordt een door het [systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gemaakt in azure Active Directory (AD) en gekoppeld aan de schijf versleuteling. 
1. De beheerder van de Azure-sleutel kluis verleent vervolgens de beheerde identiteits machtiging voor het uitvoeren van bewerkingen in de sleutel kluis.
1. Een VM-gebruiker maakt schijven door deze te koppelen aan de schijf versleutelings. De VM-gebruiker kan ook versleuteling aan de server zijde inschakelen met door de klant beheerde sleutels voor bestaande resources door ze te koppelen aan de schijf versleutelings. 
1. Beheerde schijven gebruiken de beheerde identiteit voor het verzenden van aanvragen naar de Azure Key Vault.
1. Voor het lezen of schrijven van gegevens, worden met beheerde schijven aanvragen verzonden naar Azure Key Vault om de gegevens versleutelings sleutel te versleutelen en ontsleutelen (uitpakken), zodat de gegevens kunnen worden versleuteld en ontsleuteld. 

Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) en [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie over het intrekken van de toegang tot door de klant beheerde sleutels. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Beperkingen

De door de klant beheerde sleutels hebben nu de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omzeilen, moet u [alle gegevens](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt.
- Alleen [' soft ' en ' hard ' RSA-sleutels](../../key-vault/keys/about-keys.md) met een grootte van 2080 worden ondersteund, geen andere sleutels of grootten.
- Schijven die zijn gemaakt op basis van aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten zich in hetzelfde abonnement bevinden.
- Moment opnamen die zijn gemaakt op basis van schijven die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Alle resources met betrekking tot uw door de klant beheerde sleutels (Azure Key kluizen, schijf versleutelings sets, Vm's, schijven en moment opnamen) moeten zich in hetzelfde abonnement en dezelfde regio bevinden.
- Schijven, moment opnamen en installatie kopieën die zijn versleuteld met door de klant beheerde sleutels, kunnen niet worden verplaatst naar een ander abonnement.
- Als u de Azure Portal gebruikt om uw schijf versleutelings te maken, kunt u nu geen moment opnamen gebruiken.
- Beheerde schijven die zijn versleuteld met versleuteling aan de server zijde met door de klant beheerde sleutels, kunnen niet ook worden versleuteld met Azure Disk Encryption en andersom
- Zie [Preview: door de klant beheerde sleutels gebruiken voor het versleutelen van afbeeldingen](../image-version-encryption.md)voor meer informatie over het gebruik van door de klant beheerde sleutels met galerieën met gedeelde afbeeldingen.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Uw Azure Key Vault en DiskEncryptionSet instellen

1. Zorg ervoor dat u de nieuwste [versie van Azure PowerShell](/powershell/azure/install-az-ps)hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account in met Connect-AzAccount

1. Maak een instantie van Azure Key Vault en versleutelings sleutel.

    Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Maak een instantie van een DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Verleen de DiskEncryptionSet-resource toegang tot de sleutel kluis.

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet in uw Azure Active Directory heeft gemaakt. Als er een fout optreedt, zoals ' kan het Active Directory-object niet vinden ' tijdens het uitvoeren van de volgende opdracht, wacht u enkele minuten en probeert u het opnieuw.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een virtuele machine maken met behulp van een Marketplace-installatie kopie, het besturings systeem en de gegevens schijven versleutelen met door de klant beheerde sleutels

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Een lege schijf maken die is versleuteld met versleuteling aan de server zijde met door de klant beheerde sleutels en deze koppelen aan een VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>Bestaande beheerde schijven versleutelen 

Uw bestaande schijven mogen niet worden gekoppeld aan een actieve virtuele machine, zodat u ze kunt versleutelen met het volgende script:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een schaalset voor virtuele machines maken met behulp van een Marketplace-installatie kopie, het besturings systeem en de gegevens schijven versleutelen met door de klant beheerde sleutels

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Wijzig de sleutel van een DiskEncryptionSet om de sleutel te roteren voor alle resources die verwijzen naar de DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>De status van een schijf met versleuteling aan de server zijde zoeken

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de server zijde versus Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -functie van Windows voor het versleutelen van beheerde schijven met door de klant beheerde sleutels in de gast-VM.  Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de Azure Resource Manager sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Sleutelkluis?](../../key-vault/general/overview.md)
- [Computers repliceren met door de klant beheerde sleutels ingeschakelde schijven](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Herstel na nood geval instellen voor virtuele VMware-machines in azure met Power shell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Herstel na nood geval instellen op Azure voor Hyper-V-Vm's met behulp van Power shell en Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
