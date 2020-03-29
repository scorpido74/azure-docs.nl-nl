---
title: Schijven versleutelen voor Azure-schaalsets met Azure CLI
description: Meer informatie over het gebruik van Azure PowerShell om VM-exemplaren en gekoppelde schijven te versleutelen in een Windows-set voor virtuele machines
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279082"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Besturingssysteem en gekoppelde gegevensschijven versleutelen in een virtuele machineschaalset met de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Met deze quickstart u zien hoe u de Azure CLI gebruiken om een schaalset voor virtuele machines te maken en te versleutelen. Zie [Azure Disk Encryption for Virtual Machine Scale Sets voor](disk-encryption-overview.md)meer informatie over het toepassen van Azure Disk-versleuteling op een virtuele machineschaalset.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, vereist deze zelfstudie dat u de Azure CLI-versie 2.0.31 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Een schaalset maken

Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt die automatisch wordt bijgewerkt als er wijzigingen zijn. Daarnaast worden er SSH-sleutels gegenereerd als deze niet bestaan in *~/.ssh/id_rsa*. Aan elke VM-instantie is een gegevensschijf van 32 Gb gekoppeld en de Azure [Custom Script-extensie](../virtual-machines/linux/extensions-customscript.md) wordt gebruikt om de gegevensschijven voor te bereiden met [de AZ VMSS-extensieset:](/cli/azure/vmss/extension)

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Een Azure-sleutelkluis maken die is ingeschakeld voor schijfversleuteling

Azure Key Vault kan sleutels, geheimen of wachtwoorden opslaan waarmee u ze veilig implementeren in uw toepassingen en services. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u uw sleutels importeren of genereren in Hardware Security Modules (HSM's) gecertificeerd volgens FIPS 140-2 niveau 2-standaarden. Deze cryptografische sleutels worden gebruikt om virtuele schijven die aan uw VM zijn gekoppeld, te versleutelen en te decoderen. U behoudt de controle over deze cryptografische sleutels en het gebruik ervan controleren.

Definieer uw eigen unieke *keyvault_name.* Maak vervolgens een KeyVault met [az keyvault maken](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) in hetzelfde abonnement en dezelfde regio als de schaal set, en stel de *--enabled-for-disk-encryptie* access policy.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Een bestaande key vault gebruiken

Deze stap is alleen vereist als u een bestaande Key Vault hebt die u wilt gebruiken met schijfversleuteling. Sla deze stap over als u in de vorige sectie een sleutelkluis hebt gemaakt.

Definieer uw eigen unieke *keyvault_name.* Vervolgens hebt u uw KeyVault bijgewerkt met [de AZ KeyVault-update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) en stelt u het toegangsbeleid *voor -enabled-for-disk-encryption* in.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Versleuteling inschakelen

Als u VM-exemplaren wilt versleutelen in een schaalset, krijgt u eerst informatie over de Key Vault-bron-id met [de AZ KeyVault-show.](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) Deze variabelen worden gebruikt om vervolgens het versleutelingsproces te starten met [az vmss-encryptie:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Het kan een minuut of twee duren voordat het versleutelingsproces is gestart.

Aangezien de schaalset het upgradebeleid is op de schaalset die in een eerdere stap is gemaakt, is ingesteld op *automatisch,* starten de VM-instanties automatisch het versleutelingsproces. Start op schaalsets waar het upgradebeleid moet worden handleiding, het versleutelingsbeleid voor de [VM-exemplaren met az vmss-update-exemplaren.](/cli/azure/vmss#az-vmss-update-instances)

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Versleuteling inschakelen met KEK om de sleutel te verpakken

U ook een sleutelversleutelingssleutel gebruiken voor extra beveiliging bij het versleutelen van de schaalset van virtuele machines.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks:</br>
/abonnementen/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Versleutelingsvoortgang controleren

Gebruik de AZ [VMSS-encryptie om](/cli/azure/vmss/encryption#az-vmss-encryption-show)de status van schijfversleuteling te controleren:

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Wanneer VM-exemplaren worden versleuteld, wordt in de statuscode *EncryptionState/encrypted*gerapporteerd, zoals in de volgende voorbeelduitvoer wordt weergegeven:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen

Als u geen versleutelde vm-exemplarenschijven meer wilt gebruiken, u de versleuteling uitschakelen met [az vmss-versleuteling die](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) als volgt wordt uitgeschakeld:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Volgende stappen

- In dit artikel hebt u de Azure CLI gebruikt om een schaalset voor virtuele machines te versleutelen. U ook [Azure PowerShell-](disk-encryption-powershell.md) of [Azure Resource Manager-sjablonen](disk-encryption-azure-resource-manager.md)gebruiken.
- Als u Azure Disk Encryption wilt laten toepassen nadat een andere extensie is ingericht, u [extensiesequencing](virtual-machine-scale-sets-extension-sequencing.md)gebruiken. 
- Een end-to-end batch bestand voorbeeld voor Linux schaal set data disk encryptie kan [hier](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)worden gevonden. In dit voorbeeld wordt een resourcegroep gemaakt, Linux-schaalset, wordt een gegevensschijf van 5 GB gemonteerd en wordt de detectie van de virtuele machineschaal versleuteld.
