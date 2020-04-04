---
title: Server-side encryptie van Azure Managed Disks - PowerShell
description: Azure Storage beschermt uw gegevens door deze in rust te versleutelen voordat deze worden weergegeven in opslagclusters. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw beheerde schijven, of u door de klant beheerde sleutels gebruiken om versleuteling met uw eigen sleutels te beheren.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8e6f4b58cf17a7502e2c0d67e4fa67af7cdb3f5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632956"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Server-side encryptie van azure beheerde schijven

Azure-beheerde schijven versleutelen uw gegevens standaard automatisch wanneer deze in de cloud blijven staan. Server-side encryptie beschermt uw gegevens en helpt u om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Gegevens in azure beheerde schijven worden transparant versleuteld met behulp van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de sterkste blokcijfers die beschikbaar zijn, en is FIPS 140-2-compatibel.

Versleuteling heeft geen invloed op de prestaties van beheerde schijven. Er zijn geen extra kosten voor de encryptie.

Zie [Cryptografie API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) voor meer informatie over de cryptografische modules die ten grondslag liggen aan Azure managed disks

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

U vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf, of u versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, u een *door de klant beheerde sleutel* opgeven die u moet gebruiken voor het versleutelen en decoderen van alle gegevens in beheerde schijven. 

In de volgende secties worden elk van de opties voor sleutelbeheer nader beschreven.

## <a name="platform-managed-keys"></a>Door platforms beheerde toetsen

Managed Disks maken standaard gebruik van door het platform beheerde versleutelingssleutels. Vanaf 10 juni 2017 worden alle nieuwe beheerde schijven, snapshots, afbeeldingen en nieuwe gegevens die naar bestaande beheerde schijven zijn geschreven, automatisch versleuteld-at-rest met door het platform beheerde sleutels.

## <a name="customer-managed-keys"></a>Door klant beheerde sleutels

U ervoor kiezen om versleuteling te beheren op het niveau van elke beheerde schijf, met uw eigen sleutels. Server-side encryptie voor beheerde schijven met door de klant beheerde sleutels biedt een geïntegreerde ervaring met Azure Key Vault. U [uw RSA-sleutels](../../key-vault/key-vault-hsm-protected-keys.md) importeren in uw Key Vault of nieuwe RSA-sleutels genereren in Azure Key Vault. 

Azure beheerde schijven verwerkt de versleuteling en decryptie op een volledig transparante manier met behulp van [envelopversleuteling.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Het versleutelt gegevens met behulp van een [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 gebaseerde data encryptie sleutel (DEK), die op zijn beurt, is beveiligd met behulp van uw sleutels. De opslagservice genereert gegevensversleutelingssleutels en versleutelt deze met door de klant beheerde sleutels met RSA-versleuteling. Met de envelopversleuteling u uw sleutels periodiek roteren (wijzigen) volgens uw nalevingsbeleid zonder dat dit gevolgen heeft voor uw VM's. Wanneer u uw sleutels draait, versleutelt de opslagservice de gegevensversleutelingssleutels opnieuw met de nieuwe door de klant beheerde sleutels. 

U moet toegang verlenen tot beheerde schijven in uw Key Vault om uw sleutels te gebruiken voor het versleutelen en decoderen van de DEK. Hierdoor u volledige controle over uw gegevens en sleutels. U uw sleutels op elk gewenst moment uitschakelen of de toegang tot beheerde schijven intrekken. U ook het gebruik van de versleutelingssleutel controleren met Azure Key Vault-bewaking om ervoor te zorgen dat alleen beheerde schijven of andere vertrouwde Azure-services toegang hebben tot uw sleutels.

Voor premium SSD's, standaard SSD's en standaard HDD's: Wanneer u uw sleutel uitschakelt of verwijdert, worden alle VM's met schijven met die sleutel automatisch afgesloten. Hierna zijn de VM's niet meer bruikbaar, tenzij de sleutel opnieuw is ingeschakeld of u een nieuwe sleutel toewijst.

Voor ultraschijven wordt bij het uitschakelen of verwijderen van een sleutel geen VM's met ultraschijven met de sleutel automatisch afgesloten. Zodra u de vm's deallocate en opnieuw start, worden de schijven gestopt met het gebruik van de sleutel en dan komen VM's niet meer online. Als u de VM's weer online wilt brengen, moet u een nieuwe sleutel toewijzen of de bestaande sleutel inschakelen.

In het volgende diagram ziet u hoe beheerde schijven Azure Active Directory en Azure Key Vault gebruiken om aanvragen in te dienen met behulp van de door de klant beheerde sleutel:

![Werkstroom voor beheerde schijf- en klantbeheersleutels. Een beheerder maakt een Azure Key Vault, maakt vervolgens een schijfversleutelingsset en stelt de schijfversleutelingsset in. De set is gekoppeld aan een VM, waarmee de schijf gebruik kan maken van Azure AD om te verifiëren](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


In de volgende lijst wordt het diagram nader uitgelegd:

1. Een Azure Key Vault-beheerder maakt belangrijke vaultresources.
1. De key vault admin importeert zijn RSA-sleutels naar Key Vault of genereert nieuwe RSA-sleutels in Key Vault.
1. Die beheerder maakt een instantie van schijfversleutelingssetbron, waarbij een Azure Key Vault ID en een belangrijke URL worden opgegeven. Disk Encryption Set is een nieuwe bron geïntroduceerd voor het vereenvoudigen van het sleutelbeheer voor beheerde schijven. 
1. Wanneer een schijfversleutelingsset wordt gemaakt, wordt een [door het systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gemaakt in Azure Active Directory (AD) en gekoppeld aan de schijfversleutelingsset. 
1. De azure key vault administrator verleent vervolgens de beheerde identiteitstoestemming om bewerkingen uit te voeren in de sleutelkluis.
1. Een VM-gebruiker maakt schijven door ze te koppelen aan de schijfversleutelingsset. De VM-gebruiker kan ook server-side encryptie inschakelen met door de klant beheerde sleutels voor bestaande bronnen door ze te koppelen aan de schijfversleutelingsset. 
1. Beheerde schijven gebruiken de beheerde identiteit om aanvragen naar de Azure Key Vault te verzenden.
1. Voor het lezen of schrijven van gegevens stuurt beheerde schijven aanvragen naar Azure Key Vault om de gegevensversleutelingssleutel te versleutelen (wrap) te versleutelen en uit te pakken om versleuteling en decryptie van de gegevens uit te voeren. 

Zie [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) en [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)om de toegang tot door de klant beheerde sleutels in te trekken. Als u de toegang intrekt, wordt de toegang tot alle gegevens in het opslagaccount effectief verbroken, omdat de versleutelingssleutel niet toegankelijk is voor Azure Storage.

### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Beperkingen

Voor nu hebben door de klant beheerde sleutels de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, u deze niet uitschakelen.
    Als u dit moet omzeilen, moet u [alle gegevens kopiëren](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) naar een geheel andere beheerde schijf die geen door de klant beheerde sleutels gebruikt.
- Alleen ["zachte" en "harde" RSA-toetsen](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) van grootte 2080 worden ondersteund, geen andere toetsen of maten.
- Schijven die zijn gemaakt met aangepaste afbeeldingen die zijn versleuteld met behulp van server-side encryptie en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten in hetzelfde abonnement zijn.
- Momentopnamen die zijn gemaakt van schijven die zijn versleuteld met server-side encryptie en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Aangepaste afbeeldingen die zijn versleuteld met serverversleuteling en door de klant beheerde sleutels, kunnen niet worden gebruikt in de gedeelde afbeeldingsgalerie.
- Alle resources met betrekking tot uw door de klant beheerde sleutels (Azure Key Vaults, disk encryption sets, VM's, schijven en snapshots) moeten zich in hetzelfde abonnement en dezelfde regio bevinden.
- Schijven, momentopnamen en afbeeldingen die zijn versleuteld met door de klant beheerde sleutels, kunnen niet naar een ander abonnement worden verplaatst.
- Als u de Azure-portal gebruikt om uw schijfversleutelingsset te maken, u voorlopig geen momentopnamen gebruiken.
- Beheerde schijven die zijn versleuteld met door de klant beheerde sleutels, kunnen ook niet worden versleuteld met Azure Disk Encryption.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Uw Azure Key Vault en DiskEncryptionSet instellen

1. Zorg ervoor dat u de nieuwste [Azure PowerShell-versie](/powershell/azure/install-az-ps)hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account met Connect-AzAccount

1. Maak een exemplaar van Azure Key Vault en versleutelingssleutel.

    Bij het maken van de Key Vault-instantie moet u bescherming voor zachte verwijdering en zuivering inschakelen. Soft delete zorgt ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde bewaarperiode (standaard 90 dagen). Verwijderingsbeveiliging zorgt ervoor dat een verwijderde sleutel niet permanent kan worden verwijderd totdat de bewaartermijn is verstreken. Deze instellingen beschermen u tegen het verlies van gegevens als gevolg van onbedoelde verwijdering. Deze instellingen zijn verplicht bij het gebruik van een Key Vault voor het versleutelen van beheerde schijven.

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

1.    Geef de DiskEncryptionSet-bron toegang tot de sleutelkluis.

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet maakt in uw Azure Active Directory. Als u een foutmelding krijgt zoals 'Het Active Directory-object niet kunnen vinden' bij het uitvoeren van de volgende opdracht, wacht u een paar minuten en probeert u het opnieuw.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een VM maken met behulp van een Marketplace-afbeelding, waarbij het besturingssysteem en gegevensschijven worden versleuteld met door de klant beheerde sleutels

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Een lege schijf maken die is versleuteld met serverversleuteling met door de klant beheerde sleutels en deze aan een vm koppelen

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

#### <a name="encrypt-existing-unattached-managed-disks"></a>Bestaande niet-gekoppelde beheerde schijven versleutelen 

Uw bestaande schijven mogen niet aan een draaiende VM worden gekoppeld, zodat u ze versleutelen met behulp van het volgende script:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een virtuele machineschaalset maken met behulp van een Marketplace-afbeelding, waarbij het besturingssysteem en gegevensschijven worden versleuteld met door de klant beheerde sleutels

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>De sleutel van een DiskEncryptionSet wijzigen om de sleutel te roteren voor alle bronnen die verwijzen naar de DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>De status van serverversleuteling van een schijf zoeken

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt een beheerde identiteit automatisch toegewezen aan uw resources onder de covers. Als u vervolgens het abonnement, de resourcegroep of de beheerde schijf van de ene Azure AD-map naar de andere verplaatst, wordt de beheerde identiteit die aan beheerde schijven is gekoppeld, niet overgedragen naar de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [Een abonnement overbrengen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt een beheerde identiteit automatisch toegewezen aan uw resources onder de covers. Als u vervolgens het abonnement, de resourcegroep of de beheerde schijf van de ene Azure AD-map naar de andere verplaatst, wordt de beheerde identiteit die aan beheerde schijven is gekoppeld, niet overgedragen naar de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [Een abonnement overbrengen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Server-side encryptie versus Azure-schijfversleuteling

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker-functie](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) van Windows en de [DM-Crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels binnen de gast-VM.  Server-side encryptie met door de klant beheerde sleutels verbetert op ADE doordat u alle besturingssysteemtypen en afbeeldingen voor uw VM's gebruiken door gegevens te versleutelen in de opslagservice.

## <a name="next-steps"></a>Volgende stappen

- [De Azure Resource Manager-sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels verkennen](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Machines repliceren met schijven met klantbeheerde sleutels](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Noodherstel van VMware VM's instellen op Azure met PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Disaster recovery instellen voor Azure voor Hyper V VM's met PowerShell en Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
