---
title: Server-side encryptie van Azure Managed Disks - Azure CLI
description: Azure Storage beschermt uw gegevens door deze in rust te versleutelen voordat deze worden weergegeven in opslagclusters. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw beheerde schijven, of u door de klant beheerde sleutels gebruiken om versleuteling met uw eigen sleutels te beheren.
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 912677a10d7098b891a4f6972b61761cd72cf292
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585947"
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


In de volgende lijst wordt het diagram nog gedetailleerder uitgelegd:

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
    Als u dit moet omzeilen, moet u [alle gegevens kopiëren](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) naar een geheel andere beheerde schijf die geen door de klant beheerde sleutels gebruikt.
- Alleen ["zachte" en "harde" RSA-toetsen](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) van grootte 2048 worden ondersteund, geen andere toetsen of maten.
- Schijven die zijn gemaakt met aangepaste afbeeldingen die zijn versleuteld met behulp van server-side encryptie en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten in hetzelfde abonnement zijn.
- Momentopnamen die zijn gemaakt van schijven die zijn versleuteld met server-side encryptie en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Aangepaste afbeeldingen die zijn versleuteld met serverversleuteling en door de klant beheerde sleutels, kunnen niet worden gebruikt in de gedeelde afbeeldingsgalerie.
- Alle resources met betrekking tot uw door de klant beheerde sleutels (Azure Key Vaults, disk encryption sets, VM's, schijven en snapshots) moeten zich in hetzelfde abonnement en dezelfde regio bevinden.
- Schijven, momentopnamen en afbeeldingen die zijn versleuteld met door de klant beheerde sleutels, kunnen niet naar een ander abonnement worden verplaatst.
- Als u de Azure-portal gebruikt om uw schijfversleutelingsset te maken, u voorlopig geen momentopnamen gebruiken.
- Beheerde schijven die zijn versleuteld met door de klant beheerde sleutels, kunnen ook niet worden versleuteld met Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Uw Azure Key Vault en DiskEncryptionSet instellen

1. Zorg ervoor dat u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en ingelogd bent op een Azure-account met [az-aanmelding.](/cli/azure/reference-index)

1. Maak een exemplaar van Azure Key Vault en versleutelingssleutel.

    Bij het maken van de Key Vault-instantie moet u bescherming voor zachte verwijdering en zuivering inschakelen. Soft delete zorgt ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde bewaarperiode (standaard 90 dagen). Verwijderingsbeveiliging zorgt ervoor dat een verwijderde sleutel niet permanent kan worden verwijderd totdat de bewaartermijn is verstreken. Deze instellingen beschermen u tegen het verlies van gegevens als gevolg van onbedoelde verwijdering. Deze instellingen zijn verplicht bij het gebruik van een Key Vault voor het versleutelen van beheerde schijven.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
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

1.    Geef de DiskEncryptionSet-bron toegang tot de sleutelkluis. 

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet maakt in uw Azure Active Directory. Als u een foutmelding krijgt zoals 'Het Active Directory-object niet kunnen vinden' bij het uitvoeren van de volgende opdracht, wacht u een paar minuten en probeert u het opnieuw.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een VM maken met behulp van een Marketplace-afbeelding, waarbij het besturingssysteem en gegevensschijven worden versleuteld met door de klant beheerde sleutels

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Bestaande niet-gekoppelde beheerde schijven versleutelen 

Uw bestaande schijven mogen niet aan een draaiende VM worden gekoppeld, zodat u ze versleutelen met behulp van het volgende script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Een virtuele machineschaalset maken met behulp van een Marketplace-afbeelding, waarbij het besturingssysteem en gegevensschijven worden versleuteld met door de klant beheerde sleutels

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Een lege schijf maken die is versleuteld met serverversleuteling met door de klant beheerde sleutels en deze aan een vm koppelen

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>De sleutel van een DiskEncryptionSet wijzigen om de sleutel te roteren voor alle bronnen die verwijzen naar de DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>De status van serverversleuteling van een schijf zoeken

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt een beheerde identiteit automatisch toegewezen aan uw resources onder de covers. Als u vervolgens het abonnement, de resourcegroep of de beheerde schijf van de ene Azure AD-map naar de andere verplaatst, wordt de beheerde identiteit die aan beheerde schijven is gekoppeld, niet overgedragen naar de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [Een abonnement overbrengen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt een beheerde identiteit automatisch toegewezen aan uw resources onder de covers. Als u vervolgens het abonnement, de resourcegroep of de beheerde schijf van de ene Azure AD-map naar de andere verplaatst, wordt de beheerde identiteit die aan beheerde schijven is gekoppeld, niet overgedragen naar de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [Een abonnement overbrengen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Server-side encryptie versus Azure-schijfversleuteling

[Azure Disk Encryption voor virtuele machines en virtuele machineschaalsets](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker-functie](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) van Windows en de [DM-Crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels binnen de gast-VM.  Server-side encryptie met door de klant beheerde sleutels verbetert op ADE doordat u alle besturingssysteemtypen en afbeeldingen voor uw VM's gebruiken door gegevens te versleutelen in de opslagservice.

## <a name="next-steps"></a>Volgende stappen

- [De Azure Resource Manager-sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels verkennen](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Machines repliceren met schijven met klantbeheerde sleutels](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Noodherstel van VMware VM's instellen op Azure met PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Disaster recovery instellen voor Azure voor Hyper V VM's met PowerShell en Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
