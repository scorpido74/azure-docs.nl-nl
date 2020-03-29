---
title: Een VM verplaatsen naar azure cli
description: Een VM verplaatsen naar een ander Azure-abonnement of een andere brongroep met de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944592"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Een vm verplaatsen naar een ander abonnement of resourcegroep
In dit artikel u zich uitleven hoe u een virtuele machine (VM) verplaatst tussen resourcegroepen of abonnementen. Het verplaatsen van een VM tussen abonnementen kan handig zijn als u een VM in een persoonlijk abonnement hebt gemaakt en deze nu wilt verplaatsen naar het abonnement van uw bedrijf.

> [!IMPORTANT]
>Nieuwe resource-id's worden gemaakt als onderdeel van de verhuizing. Nadat de VM is verplaatst, moet u uw hulpprogramma's en scripts bijwerken om de nieuwe bron-id's te gebruiken.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>De Azure CLI gebruiken om een VM te verplaatsen


Voordat u uw VM verplaatsen met behulp van de Azure CLI, moet u ervoor zorgen dat de bron- en doelabonnementen binnen dezelfde tenant bestaan. Als u wilt controleren of beide abonnementen dezelfde tenant-id hebben, gebruikt u [de AZ-accountshow](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Als de tenant-id's voor de bron- en bestemmingsabonnementen niet hetzelfde zijn, moet u contact opnemen met [ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om de resources naar een nieuwe tenant te verplaatsen.

Als u een vm wilt verplaatsen, moet u de VM en al zijn ondersteunende resources verplaatsen. Gebruik de opdracht [az-resourcelijst](/cli/azure/resource) om alle bronnen in een resourcegroep en hun id's weer te geven. Het helpt om de uitvoer van deze opdracht naar een bestand te sturen, zodat u de id's kopiëren en plakken in latere opdrachten.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Als u een VM en de bijbehorende resources naar een andere resourcegroep wilt verplaatsen, gebruikt u [az-bronverplaatsing](/cli/azure/resource). In het volgende voorbeeld ziet u hoe u een VM en de meest voorkomende resources verplaatst. Gebruik de parameter **-id's** en geef door in een door komma's gescheiden lijst (zonder spaties) id's voor de resources om te verplaatsen.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Als u de VM en de bijbehorende resources naar een ander abonnement wilt verplaatsen, voegt u de parameter **--destination-subscriptionId** toe om het bestemmingsabonnement op te geven.

Wanneer u wordt gevraagd te bevestigen dat u de opgegeven resources wilt verplaatsen, voert u **Y** in om te bevestigen.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Volgende stappen
U veel verschillende typen resources verplaatsen tussen resourcegroepen en abonnementen. Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement voor](../../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie.    
