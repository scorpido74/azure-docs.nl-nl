---
title: Een virtuele Linux-machine maken en versleutelen met Azure CLI
description: In deze Quick Start leert u hoe u Azure CLI gebruikt om een virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 14b6f07ead59f3d86b80489460956dd37fb7378b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970696"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Snelstartgids: een virtuele Linux-machine maken en versleutelen met de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure CLI gebruikt om een virtuele Linux-machine (VM) te maken en te versleutelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstartgids de Azure CLI versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In het volgende voorbeeld van uitvoer ziet u dat het maken van de virtuele machine is geslaagd.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een Key Vault maken die is geconfigureerd voor versleutelings sleutels

De versleutelings sleutel van Azure Disk Encryption wordt opgeslagen in een Azure Key Vault. Maak een Key Vault met [AZ-sleutel kluis maken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Als u de Key Vault wilt inschakelen om versleutelings sleutels op te slaan, gebruikt u de para meter---for-Disk-Encryption.

> [!Important]
> Elke sleutel kluis moet een naam hebben die uniek is binnen Azure. Vervang in de onderstaande voor beelden < uw-sleutel kluis naam > door de naam die u kiest.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw virtuele machine met [AZ VM Encryption](/cli/azure/vm/encryption?view=azure-cli-latest), zodat u uw unieke key Vault naam kunt opgeven voor de para meter--Disk-Encryption-sleutel kluis.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Na het moment dat het proces wordt geretourneerd, wordt de versleutelings aanvraag geaccepteerd. Gebruik de opdracht show om de voortgang te controleren. De opdracht ' weer geven ' is [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Wanneer versleuteling is ingeschakeld, wordt het volgende weer gegeven in de geretourneerde uitvoer:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de opdracht [AZ Group delete](/cli/azure/group) gebruiken om de resource groep, de virtuele machine en de Key Vault te verwijderen. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een virtuele machine gemaakt, een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over meer Azure Disk Encryption voor Linux-Vm's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)