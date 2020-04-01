---
title: Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI
description: In deze quickstart leert u hoe u Azure CLI gebruiken om een virtuele Windows-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 786a906241b355cdae403c6ed08b60eb27045d6f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385245"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Snelstart: een Windows-vm maken en versleutelen met de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Met deze quickstart u zien hoe u de Azure CLI gebruiken om een virtuele windows server (VM) te maken en te versleutelen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de Azure CLI lokaal te installeren en te gebruiken, moet u met deze quickstart de Azure CLI-versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

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

```
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een sleutelkluis maken die is geconfigureerd voor versleutelingssleutels

Azure-schijfversleuteling slaat de versleutelingssleutel op in een Azure Key Vault. Maak een Key Vault met [az keyvault maken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Als u de Sleutelkluis wilt inschakelen om versleutelingssleutels op te slaan, gebruikt u de parameter --enabled-for-disk-encryption.
> [!Important]
> Elke Key Vault moet een unieke naam hebben. In het volgende voorbeeld wordt een Key Vault met de naam *myKV*gemaakt, maar u moet de uwe iets anders noemen.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw VM met [az vm-encryptie](/cli/azure/vm/encryption?view=azure-cli-latest)en geef uw unieke Key Vault-naam aan de parameter --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

U controleren of versleuteling is ingeschakeld op uw VM met [az vm-show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

U ziet het volgende in de geretourneerde uitvoer:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de opdracht verwijderen van de [AZ-groep](/cli/azure/group) gebruiken om de brongroep, VM en Key Vault te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele machine gemaakt, een key vault gemaakt die is ingeschakeld voor versleutelingssleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over de vereisten voor Azure Disk Encryption voor IaaS-VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
