---
title: Versleuteling aan de server zijde van Azure Managed Disks-Azure CLI
description: Azure Storage beveiligt uw gegevens door deze te versleutelen voordat deze in de opslag clusters worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw beheerde schijven, of u kunt door de klant beheerde sleutels gebruiken om versleuteling te beheren met uw eigen sleutels.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 027efd268ee80fbaf921b42d09cc424c8e8483ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136920"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Versleuteling aan de server zijde van Azure Managed disks

Met Azure Managed disks worden uw gegevens standaard automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Versleuteling aan de server zijde beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2.   

Versleuteling heeft geen invloed op de prestaties van Managed disks. Er zijn geen extra kosten voor het versleutelen van de versleuteling.

Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

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


In de volgende lijst wordt het diagram in nog meer details uitgelegd:

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
    Als u dit wilt omzeilen, moet u [alle gegevens](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt.
- Alleen [' soft ' en ' hard ' RSA-sleutels](../../key-vault/keys/about-keys.md) met een grootte van 2048 worden ondersteund, geen andere sleutels of grootten.
- Schijven die zijn gemaakt op basis van aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten zich in hetzelfde abonnement bevinden.
- Moment opnamen die zijn gemaakt op basis van schijven die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels kunnen niet worden gebruikt in de galerie met gedeelde afbeeldingen.
- Alle resources met betrekking tot uw door de klant beheerde sleutels (Azure Key kluizen, schijf versleutelings sets, Vm's, schijven en moment opnamen) moeten zich in hetzelfde abonnement en dezelfde regio bevinden.
- Schijven, moment opnamen en installatie kopieën die zijn versleuteld met door de klant beheerde sleutels, kunnen niet worden verplaatst naar een ander abonnement.
- Als u de Azure Portal gebruikt om uw schijf versleutelings te maken, kunt u nu geen moment opnamen gebruiken.
- Beheerde schijven die zijn versleuteld met door de klant beheerde sleutels, kunnen niet ook worden versleuteld met Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Uw Azure Key Vault en DiskEncryptionSet instellen

1. Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account in met [AZ login](/cli/azure/reference-index).

1. Maak een instantie van Azure Key Vault en versleutelings sleutel.

    Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.

    > [!IMPORTANT]
    > Zorg dat u niet Camel de regio, als u dit doet, er problemen kunnen optreden bij het toewijzen van extra schijven aan de resource in de Azure Portal.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Maak een instantie van een DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Verleen de DiskEncryptionSet-resource toegang tot de sleutel kluis. 

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet in uw Azure Active Directory heeft gemaakt. Als er een fout optreedt, zoals ' kan het Active Directory-object niet vinden ' tijdens het uitvoeren van de volgende opdracht, wacht u enkele minuten en probeert u het opnieuw.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een virtuele machine maken met behulp van een Marketplace-installatie kopie, het besturings systeem en de gegevens schijven versleutelen met door de klant beheerde sleutels

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-managed-disks"></a>Bestaande beheerde schijven versleutelen 

Uw bestaande schijven mogen niet worden gekoppeld aan een actieve virtuele machine, zodat u ze kunt versleutelen met het volgende script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een schaalset voor virtuele machines maken met behulp van een Marketplace-installatie kopie, het besturings systeem en de gegevens schijven versleutelen met door de klant beheerde sleutels

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Een lege schijf maken die is versleuteld met versleuteling aan de server zijde met door de klant beheerde sleutels en deze koppelen aan een VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Wijzig de sleutel van een DiskEncryptionSet om de sleutel te roteren voor alle resources die verwijzen naar de DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>De status van een schijf met versleuteling aan de server zijde zoeken

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de server zijde versus Azure Disk Encryption

[Azure Disk Encryption voor virtuele machines en virtuele-machine schaal sets](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels in de gast-VM.  Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de Azure Resource Manager sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Sleutelkluis?](../../key-vault/general/overview.md)
- [Computers repliceren met door de klant beheerde sleutels ingeschakelde schijven](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Herstel na nood geval instellen voor virtuele VMware-machines in azure met Power shell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Herstel na nood geval instellen op Azure voor Hyper-V-Vm's met behulp van Power shell en Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
