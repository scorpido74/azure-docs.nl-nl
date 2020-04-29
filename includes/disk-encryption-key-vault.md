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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314092"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

*Als u al een resource groep hebt, kunt u door gaan met [het maken van een sleutel kluis](#create-a-key-vault).*

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

Maak een resource groep met behulp van de [AZ-groep](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure cli-opdracht maken, de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell of uit de [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

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
> Uw sleutel kluis en Vm's moeten zich in hetzelfde abonnement benemen. Om ervoor te zorgen dat versleutelings geheimen geen regionale grenzen overschrijden, Azure Disk Encryption vereist dat de Key Vault en de virtuele machines in dezelfde regio naast elkaar staan. Maak en gebruik een Key Vault dat zich in hetzelfde abonnement en dezelfde regio bevindt als de virtuele machines die moeten worden versleuteld. 

Elk Key Vault moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.

### <a name="azure-cli"></a>Azure CLI

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

1. Klik in de Azure Quick Start-sjabloon op **implementeren naar Azure**.
2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de Key Vault naam, de object-ID, de juridische voor waarden en de overeenkomst en klik vervolgens op **kopen**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen

Het Azure-platform moet toegang hebben tot de versleutelings sleutels of geheimen in uw sleutel kluis om ze beschikbaar te maken voor de VM voor het opstarten en ontsleutelen van de volumes. 

Als u de sleutel kluis voor schijf versleuteling, implementatie of sjabloon implementatie niet hebt ingeschakeld op het moment van maken (zoals in de vorige stap is gedemonstreerd), moet u het geavanceerde toegangs beleid bijwerken.  

### <a name="azure-cli"></a>Azure CLI

Gebruik [AZ Key kluis update](/cli/azure/keyvault#az-keyvault-update) om schijf versleuteling in te scha kelen voor de sleutel kluis. 

 - **Key Vault voor schijf versleuteling inschakelen:** Ingeschakeld-voor-schijf versleuteling is vereist. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de micro soft. Compute-resource provider geheimen ophalen van deze sleutel kluis wanneer er naar deze sleutel kluis wordt verwezen bij het maken van een virtuele machine.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Schakel Key Vault voor sjabloon implementatie in, indien nodig:** Resource Manager mag geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Gebruik de sleutel kluis Power shell [-cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om schijf versleuteling in te scha kelen voor de sleutel kluis.

  - **Key Vault voor schijf versleuteling inschakelen:** EnabledForDiskEncryption is vereist voor Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de micro soft. Compute-resource provider geheimen ophalen van deze sleutel kluis wanneer er naar deze sleutel kluis wordt verwezen bij het maken van een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Schakel Key Vault voor sjabloon implementatie in, indien nodig:** Hiermee kunnen Azure Resource Manager geheimen van deze sleutel kluis ophalen wanneer naar deze sleutel kluis wordt verwezen in een sjabloon implementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw sleutel kluis, ga naar **toegangs beleid**en **Klik om geavanceerd toegangs beleid weer te geven**.
2. Schakel het selectie vakje **toegang tot Azure Disk Encryption voor volume versleuteling inschakelen**in.
3. Selecteer **toegang tot Azure virtual machines inschakelen voor implementatie** en/of **toegang tot Azure Resource Manager voor sjabloon implementatie**inschakelen, indien nodig. 
4. Klik op **Opslaan**.

    ![Geavanceerd toegangs beleid voor Azure Key kluis](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Een Key Encryption Key instellen (KEK)

Als u een sleutel versleutelings sleutel (KEK) wilt gebruiken voor een extra beveiligingslaag voor de versleutelings sleutels, voegt u een KEK toe aan uw sleutel kluis. Wanneer een sleutel versleutelings sleutel is opgegeven, wordt met Azure Disk Encryption die sleutel gebruikt om de versleutelings geheimen te verpakken voordat er naar Key Vault wordt geschreven.

U kunt een nieuwe KEK genereren met behulp van de opdracht voor het maken van de Azure CLI [AZ-sleutel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , de Azure PowerShell [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet of de [Azure Portal](https://portal.azure.com/). U moet een RSA-sleutel type genereren. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van elliptische curve toetsen.

U kunt in plaats daarvan een KEK importeren uit uw on-premises sleutel beheer HSM. Zie [Key Vault-documentatie](/azure/key-vault/key-vault-hsm-protected-keys)voor meer informatie.

Uw sleutel kluis KEK-Url's moeten een versie nummer hebben. Azure dwingt deze beperking van versie beheer af. Zie de volgende voor beelden voor geldige geheime en KEK-Url's:

* Voor beeld van een geldige geheime URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Voor beeld van een geldige KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption biedt geen ondersteuning voor het opgeven van poort nummers als onderdeel van sleutel kluis geheimen en KEK-Url's. Voor voor beelden van niet-ondersteunde en ondersteunde sleutel kluis-Url's, zie de volgende voor beelden:

  * Aanvaard bare sleutel kluis-URL:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Onaanvaardbaar URL voor sleutel kluis:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Gebruik de opdracht voor het maken van een nieuwe KEK in de Azure CLI [AZ-sleutel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) voor het genereren van sleutels en sla deze op in uw sleutel kluis.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

U kunt in plaats daarvan een persoonlijke sleutel importeren met behulp van de Azure CLI [AZ sleutel import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) opdracht:

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
