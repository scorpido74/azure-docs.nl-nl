---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0659e57f5a5b223c199becf492b27c7a70cbdc63
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612042"
---
Met Azure Managed disks worden uw gegevens standaard automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Versleuteling aan de server zijde beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2.   

Versleuteling heeft geen invloed op de prestaties van Managed disks. Er zijn geen extra kosten voor het versleutelen van de versleuteling.

Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels (open bare preview). Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in Managed disks. 

In de volgende secties worden de opties voor sleutel beheer in meer detail beschreven.

## <a name="platform-managed-keys"></a>Door het platform beheerde sleutels

Beheerde schijven gebruiken standaard versleutelings sleutels die door het platform worden beheerd. Vanaf 10 juni 2017 worden alle nieuwe beheerde schijven, moment opnamen, installatie kopieën en nieuwe gegevens die worden geschreven naar bestaande beheerde schijven automatisch versleuteld met door het platform beheerde sleutels. 

## <a name="customer-managed-keys-public-preview"></a>Door de klant beheerde sleutels (open bare preview)

U kunt ervoor kiezen om versleuteling te beheren op het niveau van elke beheerde schijf, met uw eigen sleutels. Versleuteling aan de server zijde voor Managed disks met door de klant beheerde sleutels biedt een geïntegreerde ervaring met Azure Key Vault. U kunt [uw RSA-sleutels](../articles/key-vault/key-vault-hsm-protected-keys.md) importeren naar uw Key Vault of nieuwe RSA-sleutels in azure Key Vault genereren. Met Azure Managed disks wordt de versleuteling en ontsleuteling op een volledig transparante manier verwerkt met behulp van [envelop versleuteling](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique). Hiermee worden gegevens versleuteld met behulp van een [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-gegevens versleutelings sleutel (dek), die op zijn beurt wordt beveiligd met uw sleutels. U moet toegang verlenen tot beheerde schijven in uw Key Vault om uw sleutels te gebruiken voor het versleutelen en ontsleutelen van de DEK. Op die manier kunt u uw gegevens en sleutels volledig beheren. U kunt uw sleutels uitschakelen of de toegang tot beheerde schijven op elk gewenst moment intrekken. U kunt ook het gebruik van de versleutelings sleutel met Azure Key Vault bewaking controleren om ervoor te zorgen dat alleen beheerde schijven of andere vertrouwde Azure-Services toegang krijgen tot uw sleutels.

In het volgende diagram ziet u hoe beheerde schijven gebruikmaken van Azure Active Directory en Azure Key Vault om aanvragen te doen met behulp van de door de klant beheerde sleutel:

![Workflow voor door de klant beheerde sleutels voor beheerde schijven](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault beheerder maakt essentiële bronnen voor de kluis.
1. Met de sleutel kluis beheerder worden de RSA-sleutels geïmporteerd in Key Vault of nieuwe RSA-sleutels in Key Vault gegenereerd.
1. Die beheerder maakt een exemplaar van de bron voor de schijf versleutelings en geeft een Azure Key Vault-id en een sleutel-URL op. Schijf Encryption set is een nieuwe bron die is geïntroduceerd voor het vereenvoudigen van het sleutel beheer voor beheerde schijven. 
1. Wanneer een schijf versleutelings wordt gemaakt, wordt een door het [systeem toegewezen beheerde identiteit](../articles/active-directory/managed-identities-azure-resources/overview.md) gemaakt in azure Active Directory (AD) en gekoppeld aan de schijf versleutelings. 
1. De beheerder van de Azure-sleutel kluis verleent vervolgens de beheerde identiteits machtiging voor het uitvoeren van bewerkingen in de sleutel kluis.
1. Een VM-gebruiker maakt schijven door deze te koppelen aan de schijf versleutelings. De VM-gebruiker kan ook versleuteling aan de server zijde inschakelen met door de klant beheerde sleutels voor bestaande resources door ze te koppelen aan de schijf versleutelings. 
1. Beheerde schijven gebruiken de beheerde identiteit voor het verzenden van aanvragen naar de Azure Key Vault.
1. Voor het lezen of schrijven van gegevens, worden met beheerde schijven aanvragen verzonden naar Azure Key Vault om de gegevens versleutelings sleutel te versleutelen en ontsleutelen (uitpakken), zodat de gegevens kunnen worden versleuteld en ontsleuteld. 

Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) en [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie over het intrekken van de toegang tot door de klant beheerde sleutels. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

### <a name="supported-scenarios-and-restrictions"></a>Ondersteunde scenario's en beperkingen

Tijdens de preview worden alleen de volgende scenario's ondersteund:

- Een virtuele machine (VM) maken op basis van een installatie kopie van Azure Marketplace en de besturingssysteem schijf versleutelen met versleuteling aan de server zijde met door de klant beheerde sleutels.
- Maak een aangepaste installatie kopie die is versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels.
- Een virtuele machine maken op basis van een aangepaste installatie kopie en de besturingssysteem schijf versleutelen met versleuteling aan de server zijde en door de klant beheerde sleutels.
- Maak gegevens schijven die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels.
- Maak moment opnamen die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels.
- Maak schaal sets voor virtuele machines die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels.

De preview heeft ook de volgende beperkingen:

- Alleen beschikbaar in West-Centraal vs.
- Schijven die zijn gemaakt op basis van aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten zich in hetzelfde abonnement bevinden.
- Moment opnamen die zijn gemaakt op basis van schijven die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels kunnen niet worden gebruikt in de galerie met gedeelde afbeeldingen.
- Uw Key Vault moeten zich in hetzelfde abonnement en dezelfde regio bevinden als uw door de klant beheerde sleutels.
- Schijven, moment opnamen en installatie kopieën die zijn versleuteld met door de klant beheerde sleutels, kunnen niet worden verplaatst naar een ander abonnement.

### <a name="setting-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

1.  Maak een instantie van Azure Key Vault en versleutelings sleutel.

    Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  Maak een instantie van een DiskEncryptionSet. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  Verleen de DiskEncryptionSet-resource toegang tot de sleutel kluis.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Een virtuele machine maken met behulp van een Marketplace-installatie kopie, het besturings systeem en de gegevens schijven versleutelen met door de klant beheerde sleutels via een resource manager-sjabloon

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Een lege schijf maken die is versleuteld met versleuteling aan de server zijde met door de klant beheerde sleutels en deze koppelen aan een VM

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
Update-AzVM -ResourceGroupName $rgName -VM $vm
```


> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de server zijde versus Azure Disk Encryption

[Azure Disk Encryption](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels in de gast-VM.  Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
