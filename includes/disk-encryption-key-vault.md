---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 6f7f319d2ebb4cd39933addf04f249df02d7819f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314092"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

*Als u al een resourcegroep hebt, u doorgaan [met Het maken van een sleutelkluis.](#create-a-key-vault)*

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

Maak een resourcegroep met de [opdracht AZ-groep maak](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, de opdracht [Nieuw-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell of vanuit de [Azure-portal.](https://portal.azure.com)

### <a name="azure-cli"></a>Azure CLI

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
> Uw sleutelkluis en VM's moeten in hetzelfde abonnement zitten. Om ervoor te zorgen dat versleutelingsgeheimen de regionale grenzen niet overschrijden, vereist Azure Disk Encryption dat de Key Vault en de VM's in dezelfde regio worden geplaatst. Maak en gebruik een Key Vault die zich in hetzelfde abonnement en dezelfde regio bevindt als de VM's die moeten worden versleuteld. 

Elke Key Vault moet een unieke naam hebben. Vervang <uw-unieke-keyvault-naam> met de naam van uw sleutelkluis in de volgende voorbeelden.

### <a name="azure-cli"></a>Azure CLI

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

### <a name="azure-cli"></a>Azure CLI

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

    ![Azure key vault advanced access policies](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Een sleutelversleutelingssleutel instellen (KEK)

Als u een sleutelversleutelingssleutel (KEK) wilt gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar Key Vault gaat.

U een nieuwe KEK genereren met de opdracht Azure CLI [az keyvault-key create,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) de Azure PowerShell [Add-AzKeyVaultKey-cmdlet](/powershell/module/az.keyvault/add-azkeyvaultkey) of de [Azure-portal.](https://portal.azure.com/) U moet een RSA-sleuteltype genereren; Azure Disk Encryption biedt nog geen ondersteuning voor elliptische curvetoetsen.

U in plaats daarvan een KEK importeren uit uw on-premises key management HSM. Zie [Key Vault Documentation voor](/azure/key-vault/key-vault-hsm-protected-keys)meer informatie.

Uw key vault KEK URL's moeten worden geversioned. Azure handhaaft deze beperking van versiebeheer. Zie de volgende voorbeelden voor geldige geheime en KEK-URL's:

* Voorbeeld van een geldige geheime URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Voorbeeld van een geldige KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption ondersteunt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Zie de volgende voorbeelden voor niet-ondersteunde en ondersteunde URL's voor sleutelkluizen:

  * URL van acceptabele sleutelkluis:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL van onaanvaardbare sleutelkluis:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Gebruik de opdracht Azure CLI [az keyvault-toets om](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) een nieuwe KEK te genereren en op te slaan in uw sleutelkluis.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

U in plaats daarvan een privésleutel importeren met de opdracht Azure CLI [az keyvault-sleutelimporteren:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

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
