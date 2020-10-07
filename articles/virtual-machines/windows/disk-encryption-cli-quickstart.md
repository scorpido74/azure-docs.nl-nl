---
title: Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI
description: In deze quickstart leert u hoe u Azure CLI gebruikt om een virtuele Windows-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e501cd74d652e6415b6e8f750ddfb3d182a8387
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87496131"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Quickstart: Een virtuele Windows-machine maken en versleutelen met behulp van de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze quickstart wordt beschreven hoe u de Azure CLI gebruikt om een virtuele Windows Server 2016-machine (VM) te maken en te versleutelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.30 of hoger van de Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

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

De versleutelingssleutel van Azure Disk Encryption wordt opgeslagen in een Azure-sleutelkluis. Een sleutelkluis maken met [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Gebruik de parameter --enabled-for-disk-encryption om de sleutelkluis in te schakelen voor het opslaan van versleutelingssleutels.
> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. In het volgende voorbeeld wordt een sleutelkluis gemaakt met de naam *myKV*, maar u moet deze een andere naam geven.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw virtuele machine met [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), zodat u uw unieke sleutelkluisnaam kunt opgeven voor de parameter --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

U kunt controleren of versleuteling is ingeschakeld op uw virtuele machine met [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

U ziet het volgende in de geretourneerde uitvoer:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep, de VM en sleutelkluis te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele machine gemaakt, een sleutelkluis gemaakt die is ingeschakeld voor versleutelingssleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over de vereisten voor Azure Disk Encryption voor IaaS-VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
