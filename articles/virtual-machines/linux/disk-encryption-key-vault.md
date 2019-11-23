---
title: Een sleutel kluis voor Azure Disk Encryption maken en configureren
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutel kluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 90306c55d976670f432d146d94764c4d90b8667d
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828500"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutel kluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijf versleuteling te beheren en te beheren.  Zie voor meer informatie over sleutelkluizen [aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en [uw key vault beveiligen](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md) voor meer informatie.

Een sleutel kluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

1. Een resource groep maken, indien nodig.
2. Een sleutel kluis maken. 
3. Geavanceerd toegangs beleid voor sleutel kluis instellen.

Deze stappen worden geïllustreerd in de volgende Quick starts:

- [Een Linux-VM maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure PowerShell](disk-encryption-cli-quickstart.md)

U kunt eventueel ook een sleutel versleutelings sleutel genereren of importeren (KEK).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en [Azure Disk Encryption vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogram ma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel uitvoeren met de [Azure cli](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/overview)of de [Azure Portal](https://portal.azure.com). 

Terwijl de portal toegankelijk is via uw browser, hebben Azure CLI en Azure PowerShell lokale installatie nodig. Zie [Azure Disk Encryption voor Linux: hulpprogram Ma's installeren](disk-encryption-linux.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te [melden met Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), u aan te [melden met Azure Power shell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure portal toe te voegen wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

*Als u al een resource groep hebt, kunt u door gaan met [het maken van een sleutel kluis](#create-a-key-vault).*

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

Maak een resource groep met behulp van de [AZ-groep](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure cli-opdracht maken, de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell of uit de [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

*Als u al een sleutel kluis hebt, kunt u door gaan met [Geavanceerd toegangs beleid voor sleutel kluis instellen](#set-key-vault-advanced-access-policies).*

Een sleutel kluis maken met behulp van de opdracht [AZ sleutel kluis Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, de opdracht [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure power shell, de [Azure Portal](https://portal.azure.com)of een [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Om ervoor te zorgen dat versleutelings geheimen geen regionale grenzen overschrijden, Azure Disk Encryption vereist dat de Key Vault en de virtuele machines in dezelfde regio naast elkaar staan. Maak en gebruik een Key Vault dat zich in dezelfde regio bevindt als de Vm's die moeten worden versleuteld. 

Elk Key Vault moet een unieke naam hebben. Vervang < uw-unieke kluis naam > door de naam van uw sleutel kluis in de volgende voor beelden.

### <a name="azure-cli"></a>Azure-CLI

Wanneer u een sleutel kluis maakt met behulp van Azure CLI, voegt u de vlag '--ingeschakeld-voor-Disk-Encryption ' toe.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Wanneer u een sleutel kluis maakt met behulp van Azure PowerShell, voegt u de vlag '-EnabledForDiskEncryption ' toe.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sjabloon

U kunt ook een sleutel kluis maken met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik op de Azure-quickstart-sjabloon, **implementeren in Azure**.
2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de Key Vault naam, de object-ID, de juridische voor waarden en de overeenkomst en klik vervolgens op **kopen**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen

Het Azure-platform moet toegang hebben tot de sleutels of geheimen in uw key vault zodat ze beschikbaar voor de virtuele machine voor opstarten en ontsleutelen van de volumes. 

Als u de sleutel kluis voor schijf versleuteling, implementatie of sjabloon implementatie niet hebt ingeschakeld op het moment van maken (zoals in de vorige stap is gedemonstreerd), moet u het geavanceerde toegangs beleid bijwerken.  

### <a name="azure-cli"></a>Azure-CLI

Gebruik [az keyvault update](/cli/azure/keyvault#az-keyvault-update) om in te schakelen schijfversleuteling voor de key vault. 

 - **Key Vault inschakelen voor schijfversleuteling:** ingeschakeld voor disk encryption is vereist. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault inschakelen voor implementatie, indien nodig:** kunnen de Microsoft.Compute-resourceprovider geheimen ophalen uit deze key vault wanneer deze sleutelkluis wordt verwezen in de resources worden gemaakt, bijvoorbeeld bij het maken van een virtuele machine.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** toestaan Resource Manager geheimen ophalen uit de kluis.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Gebruik de sleutel kluis Power shell [-cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om schijf versleuteling in te scha kelen voor de sleutel kluis.

  - **Key Vault inschakelen voor schijfversleuteling:** EnabledForDiskEncryption is vereist voor Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Key Vault inschakelen voor implementatie, indien nodig:** kunnen de Microsoft.Compute-resourceprovider geheimen ophalen uit deze key vault wanneer deze sleutelkluis wordt verwezen in de resources worden gemaakt, bijvoorbeeld bij het maken van een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** kunnen Azure Resource Manager geheimen ophalen uit deze key vault wanneer deze sleutelkluis wordt verwezen in de sjabloonimplementatie van een.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw sleutel kluis, ga naar **toegangs beleid**en **Klik om geavanceerd toegangs beleid weer te geven**.
2. Selecteer het selectievakje **toegang tot Azure Disk Encryption voor volumeversleuteling**.
3. Selecteer **toegang tot Azure Virtual Machines voor implementatie inschakelen** en/of **inschakelen toegang tot Azure Resource Manager voor sjabloonimplementatie**, indien nodig. 
4. Klik op **Opslaan**.

    ![Azure-sleutelkluis geavanceerde toegangsbeleid](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Een Key Encryption Key instellen (KEK)

Als u wilt een sleutel key-versleuteling (KEK) te gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, moet u een KEK-sleutel toevoegen aan uw key vault. Wanneer een sleutel van versleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel het verpakken van de geheimen van de versleuteling voor het schrijven naar de Key Vault.

U kunt een nieuwe KEK genereren met behulp van de opdracht voor het maken van de Azure CLI [AZ-sleutel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , de Azure PowerShell [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet of de [Azure Portal](https://portal.azure.com/). U moet een RSA-sleutel type genereren. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van elliptische curve toetsen.

U kunt in plaats daarvan een KEK importeren uit uw on-premises sleutel beheer HSM. Zie voor meer informatie, [Key Vault-documentatie](../../key-vault/key-vault-hsm-protected-keys.md). 

Uw sleutel kluis KEK-Url's moeten een versie nummer hebben. Azure wordt deze beperking van versiebeheer afgedwongen. Zie de volgende voorbeelden voor geldige geheim en KEK-sleutel-URL's:

* Voor beeld van een geldige geheime URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Voor beeld van een geldige KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption biedt geen ondersteuning voor het opgeven van de poortnummers als onderdeel van de key vault-geheimen en KEK-sleutel-URL's. Zie voor voorbeelden van niet-ondersteunde en ondersteunde key vault-URL's in de volgende voorbeelden:

  * Aanvaard bare sleutel kluis-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Onaanvaardbaar URL voor sleutel kluis: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht voor het maken van een nieuwe KEK in de Azure CLI [AZ-sleutel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) voor het genereren van sleutels en sla deze op in uw sleutel kluis.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. U kunt in plaats daarvan een persoonlijke sleutel importeren met behulp van de Azure CLI [AZ sleutel import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) opdracht:

In beide gevallen moet u de naam van uw KEK opgeven voor de para meter Azure CLI [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Gebruik de Azure PowerShell cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) om een nieuwe KEK te genereren en op te slaan in uw sleutel kluis.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

U kunt in plaats daarvan een persoonlijke sleutel importeren met behulp van de Azure PowerShell [AZ Key kluis-sleutel importeren](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

In beide gevallen geeft u de ID van uw KEK-sleutel kluis en de URL van uw KEK aan de Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId en-KeyEncryptionKeyUrl para meters. Houd er rekening mee dat in dit voor beeld wordt aangenomen dat u dezelfde sleutel kluis gebruikt voor zowel de schijf versleutelings sleutel als de KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Volgende stappen

- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption scenario's voor Linux-vm's](disk-encryption-linux.md) leren
- Meer informatie over het [oplossen van Azure Disk Encryption](disk-encryption-troubleshooting.md)
- De [voorbeeld scripts van Azure Disk Encryption](disk-encryption-sample-scripts.md) lezen
