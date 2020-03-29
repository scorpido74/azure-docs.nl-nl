---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel vindt u stappen voor het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970631"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren en te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en Uw [sleutelkluis beveiligen](../../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over sleutelkluizen. 

> [!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md) voor meer informatie.

Het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption omvat drie stappen:

1. Indien nodig een resourcegroep maken.
2. Het creëren van een sleutelkluis. 
3. Het instellen van geavanceerde toegangsbeleidsregels voor belangrijke vaults.

Deze stappen worden geïllustreerd in de volgende snelstarts:

- [Een Linux-VM maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure PowerShell](disk-encryption-cli-quickstart.md)

U ook, indien u dat wenst, een sleutelversleutelingssleutel (KEK) genereren of importeren.

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in de [vereisten voor Azure Disk Encryption CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en Azure Disk [Encryption-vereisten PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

De stappen in dit artikel kunnen worden voltooid met de [Azure CLI,](/cli/azure/)de [Azure PowerShell Az-module](/powershell/azure/overview)of de [Azure-portal](https://portal.azure.com). 

Hoewel de portal toegankelijk is via uw browser, vereisen Azure CLI en Azure PowerShell lokale installatie. zie [Azure Disk Encryption for Linux: Installeer hulpprogramma's](disk-encryption-linux.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door [u aan te melden bij Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), zich aan te melden bij Azure [Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure-portal te leveren wanneer daarom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

*Als u al een resourcegroep hebt, u doorgaan [met Het maken van een sleutelkluis.](#create-a-key-vault)*

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

Maak een resourcegroep met de [opdracht AZ-groep maak](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, de opdracht [Nieuw-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell of vanuit de [Azure-portal.](https://portal.azure.com)

### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

*Als u al een sleutelkluis hebt, u doorgaan [met het geavanceerde toegangsbeleid voor belangrijke kluizen instellen.](#set-key-vault-advanced-access-policies)*

Maak een sleutelkluis met de sjabloon [AZ KeyVault maak](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, de opdracht [Nieuw-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, de [Azure-portal](https://portal.azure.com)of een [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Om ervoor te zorgen dat versleutelingsgeheimen de regionale grenzen niet overschrijden, vereist Azure Disk Encryption dat de Key Vault en de VM's in dezelfde regio worden geplaatst. Maak en gebruik een Key Vault die zich in dezelfde regio bevindt als de VM's die moeten worden versleuteld. 

Elke Key Vault moet een unieke naam hebben. Vervang <uw-unieke-keyvault-naam> met de naam van uw sleutelkluis in de volgende voorbeelden.

### <a name="azure-cli"></a>Azure-CLI

Voeg bij het maken van een sleutelkluis met Azure CLI de vlag '--enabled-for-disk-encryption' toe.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Voeg bij het maken van een sleutelkluis met Azure PowerShell de vlag 'EnabledForDiskEncryption' toe.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sjabloon

U ook een sleutelkluis maken met behulp van de [sjabloon Resourcemanager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik op de Azure-snelstartsjabloon op **Implementeren naar Azure**.
2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de naam van de sleutelkluis, object-id, juridische voorwaarden en overeenkomst en klik op **Kopen**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen

Het Azure-platform heeft toegang nodig tot de versleutelingssleutels of -geheimen in uw sleutelkluis om ze beschikbaar te maken voor de VM voor het opstarten en decoderen van de volumes. 

Als u uw sleutelkluis niet hebt ingeschakeld voor schijfversleuteling, -implementatie of sjabloonimplementatie op het moment van maken (zoals in de vorige stap is aangetoond), moet u het geavanceerde toegangsbeleid bijwerken.  

### <a name="azure-cli"></a>Azure-CLI

Gebruik [de AZ KeyVault-update](/cli/azure/keyvault#az-keyvault-update) om schijfversleuteling voor de sleutelkluis in te schakelen. 

 - **Key Vault inschakelen voor schijfversleuteling:** Ingeschakeld-voor-schijf-encryptie is vereist. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de Microsoft.Compute-resourceprovider geheimen ophalen uit deze sleutelkluis wanneer naar deze sleutelkluis wordt verwezen in het maken van resources, bijvoorbeeld bij het maken van een virtuele machine.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Schakel Key Vault in voor sjabloonimplementatie, indien nodig:** Laat Resource Manager geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Gebruik de key vault PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om schijfversleuteling voor de sleutelkluis in te schakelen.

  - **Key Vault inschakelen voor schijfversleuteling:** EnabledForDiskEncryption is vereist voor Azure Disk-versleuteling.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de Microsoft.Compute-resourceprovider geheimen ophalen uit deze sleutelkluis wanneer naar deze sleutelkluis wordt verwezen in het maken van resources, bijvoorbeeld bij het maken van een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Schakel Key Vault in voor sjabloonimplementatie, indien nodig:** Hiermee kan Azure Resource Manager geheimen uit deze sleutelkluis krijgen wanneer naar deze sleutelkluis wordt verwezen in een sjabloonimplementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw sleutelkluis, ga naar **Toegangsbeleid**en **klik om geavanceerd toegangsbeleid weer te geven**.
2. Schakel het vak met het label **Toegang tot Azure-schijfversleuteling inschakelen in voor volumeversleuteling**.
3. Selecteer **Toegang tot Azure Virtual Machines inschakelen voor implementatie** en/of Toegang tot Azure Resource Manager inschakelen voor **sjabloonimplementatie**, indien nodig. 
4. Klik op **Opslaan**.

    ![Azure key vault advanced access policies](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Een sleutelversleutelingssleutel instellen (KEK)

Als u een sleutelversleutelingssleutel (KEK) wilt gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar Key Vault gaat.

U een nieuwe KEK genereren met de opdracht Azure CLI [az keyvault-key create,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) de Azure PowerShell [Add-AzKeyVaultKey-cmdlet](/powershell/module/az.keyvault/add-azkeyvaultkey) of de [Azure-portal.](https://portal.azure.com/) U moet een RSA-sleuteltype genereren; Azure Disk Encryption biedt nog geen ondersteuning voor elliptische curvetoetsen.

U in plaats daarvan een KEK importeren uit uw on-premises key management HSM. Zie [Key Vault Documentation voor](../../key-vault/key-vault-hsm-protected-keys.md)meer informatie. 

Uw key vault KEK URL's moeten worden geversioned. Azure handhaaft deze beperking van versiebeheer. Zie de volgende voorbeelden voor geldige geheime en KEK-URL's:

* Voorbeeld van een geldige geheime URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Voorbeeld van een geldige KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption ondersteunt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Zie de volgende voorbeelden voor niet-ondersteunde en ondersteunde URL's voor sleutelkluizen:

  * URL van acceptabele sleutelkluis:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL van onaanvaardbare sleutelkluis:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht Azure CLI [az keyvault-toets om](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) een nieuwe KEK te genereren en op te slaan in uw sleutelkluis.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. U in plaats daarvan een privésleutel importeren met de opdracht Azure CLI [az keyvault-sleutelimporteren:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

In beide gevallen levert u de naam van uw KEK aan de parameter Azure CLI [az vm-encryptie](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Gebruik de Azure PowerShell [Add-AzKeyVaultKey-cmdlet](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) om een nieuwe KEK te genereren en op te slaan in uw sleutelkluis.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

U in plaats daarvan een privésleutel importeren met de opdracht Azure PowerShell [az keyvault-sleutelinvoer.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

In beide gevallen levert u de ID van uw KEK-sleutelkluis en de URL van uw KEK aan de Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId en -KeyEncryptionKeyUrl-parameters. Houd er rekening mee dat in dit voorbeeld wordt aangenomen dat u dezelfde sleutelkluis gebruikt voor zowel de schijfversleutelingssleutel als de KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Azure [Disk Encryption-scenario's op Linux VM's](disk-encryption-linux.md)
- Meer informatie over het [oplossen van problemen met Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Lees de [voorbeeldscripts azure diskversleuteling](disk-encryption-sample-scripts.md)
