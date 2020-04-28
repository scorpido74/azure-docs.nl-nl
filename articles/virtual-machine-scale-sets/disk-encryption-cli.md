---
title: Schijven voor Azure-schaal sets versleutelen met Azure CLI
description: Meer informatie over het gebruik van Azure PowerShell voor het versleutelen van VM-exemplaren en gekoppelde schijven in een Windows-schaalset voor virtuele machines
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279082"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Besturings systeem en gekoppelde gegevens schijven versleutelen in een schaalset voor virtuele machines met Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure CLI kunt gebruiken om een schaalset voor virtuele machines te maken en te versleutelen. Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md)voor meer informatie over het Toep assen van Azure Disk Encryption voor een schaalset voor virtuele machines.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Een schaalset maken

Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt die automatisch wordt bijgewerkt als er wijzigingen zijn. Daarnaast worden er SSH-sleutels gegenereerd als deze niet bestaan in *~/.ssh/id_rsa*. Een gegevens schijf van 32 GB is gekoppeld aan elk VM-exemplaar en de [aangepaste script extensie](../virtual-machines/linux/extensions-customscript.md) van Azure wordt gebruikt om de gegevens schijven voor te bereiden met [AZ vmss extension set](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Een Azure-sleutel kluis maken die is ingeschakeld voor schijf versleuteling

Azure Key Vault kunnen sleutels, geheimen of wacht woorden opslaan waarmee u deze veilig kunt implementeren in uw toepassingen en services. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van software beveiliging, of u kunt uw sleutels in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden importeren of genereren. Deze cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U behoudt de controle over deze cryptografische sleutels en kunt hun gebruik controleren.

Definieer uw eigen unieke *keyvault_name*. Maak vervolgens een sleutel kluis met AZ-sleutel [kluis maken](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) in hetzelfde abonnement en dezelfde regio als de schaalset en stel het toegangs beleid *-ingeschakeld voor-schijf versleuteling* in.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Een bestaande Key Vault gebruiken

Deze stap is alleen vereist als u een bestaande Key Vault hebt die u wilt gebruiken met schijf versleuteling. Sla deze stap over als u in de vorige sectie een Key Vault hebt gemaakt.

Definieer uw eigen unieke *keyvault_name*. Vervolgens hebt u uw sleutel kluis bijgewerkt met AZ-sleutel [kluis update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) en stelt u het toegangs beleid *-ingeschakeld voor schijf versleuteling* in.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Versleuteling inschakelen

Als u VM-exemplaren in een schaalset wilt versleutelen, moet u eerst bepaalde informatie over de Key Vault Resource-ID ophalen met AZ-sleutel [kluis weer geven](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Deze variabelen worden gebruikt voor het starten van het versleutelings proces met [AZ vmss Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Het kan een paar minuten duren voordat het versleutelings proces wordt gestart.

Als de schaalset is ingesteld op het upgrade beleid voor de schaalset die in een eerdere stap is *gemaakt, wordt*het versleutelings proces automatisch gestart door de VM-exemplaren. Op schaal sets waarbij het upgrade beleid is ingesteld op hand matig, start u het versleutelings beleid voor de VM-exemplaren met [AZ vmss update instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Versleuteling inschakelen met KEK om de sleutel in te pakken

U kunt ook een sleutel versleutelings sleutel voor extra beveiliging gebruiken bij het versleutelen van de schaalset voor virtuele machines.

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
>  De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:</br>
/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br></br>
> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK, zoals in:</br>
https://[sleutel kluis naam]. kluis. Azure. net/sleutels/[kekname]/[Kek-unique-id]

## <a name="check-encryption-progress"></a>De voortgang van de versleuteling controleren

Gebruik [AZ vmss Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)om de status van schijf versleuteling te controleren:

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Wanneer VM-exemplaren worden versleuteld, rapporteert de status code *EncryptionState/versleuteld*, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

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

Als u geen schijven met versleutelde VM-exemplaren meer wilt gebruiken, kunt u versleuteling als volgt uitschakelen met [AZ vmss Encryption Disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) :

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Volgende stappen

- In dit artikel hebt u de Azure CLI gebruikt om een schaalset voor virtuele machines te versleutelen. U kunt ook [Azure PowerShell](disk-encryption-powershell.md) -of [Azure Resource Manager sjablonen](disk-encryption-azure-resource-manager.md)gebruiken.
- Als u Azure Disk Encryption wilt Toep assen nadat een andere uitbrei ding is ingericht, kunt u [extensie volgorde bepaling](virtual-machine-scale-sets-extension-sequencing.md)gebruiken. 
- Een end-to-end batch-voorbeeld bestand voor het versleutelen van gegevens schijven voor Linux-schaal sets vindt u [hier](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). In dit voor beeld wordt een resource groep, een Linux-schaalset gemaakt, een gegevens schijf van 5 GB gekoppeld en de schaalset voor virtuele machines versleuteld.
