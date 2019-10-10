---
title: Een Windows-VM maken en versleutelen met Azure CLI
description: In deze Quick Start leert u hoe u Azure CLI gebruikt om een virtuele Windows-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 9bbe74bd2f3137443b4e239201c604d9de52582e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246065"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Snelstartgids: een virtuele Windows-machine maken en versleutelen met Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure CLI gebruikt om een Windows Server 2016-virtuele machine (VM) te maken en te versleutelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstartgids de Azure CLI versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt. In dit voorbeeld wordt *azureuser* voor de naam van een gebruiker met beheerdersrechten en *myPassword12* als het wachtwoord gebruikt. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
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
> Elk Key Vault moet een unieke naam hebben. In het volgende voor beeld wordt een Key Vault gemaakt met de naam *myKV*, maar u moet iets anders een naam hebben.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw virtuele machine met [AZ VM Encryption](/cli/azure/vm/encryption?view=azure-cli-latest), zodat u uw unieke key Vault naam kunt opgeven voor de para meter--Disk-Encryption-sleutel kluis.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

U kunt controleren of versleuteling is ingeschakeld op uw virtuele machine met [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Het volgende wordt weer gegeven in de geretourneerde uitvoer:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de opdracht [AZ Group delete](/cli/azure/group) gebruiken om de resource groep, de virtuele machine en de Key Vault te verwijderen. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een virtuele machine gemaakt, een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over de vereisten voor Azure Disk Encryption voor IaaS-VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)

