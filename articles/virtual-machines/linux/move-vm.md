---
title: Een Linux-VM verplaatsen in azure
description: Een Linux-VM verplaatsen naar een ander Azure-abonnement of resource groep in het Resource Manager-implementatie model.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 2ba8a8cdc324f46e25f9665cfce0aa07fc948e88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979028"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Een Linux-VM verplaatsen naar een ander abonnement of een andere resource groep
In dit artikel wordt uitgelegd hoe u een virtuele Linux-machine (VM) verplaatst tussen resource groepen of abonnementen. Het verplaatsen van een virtuele machine tussen abonnementen kan handig zijn als u een virtuele machine in een persoonlijk abonnement hebt gemaakt en u deze nu wilt verplaatsen naar het abonnement van uw bedrijf.

> [!IMPORTANT]
>U kunt Azure Managed Disks op dit moment niet verplaatsen.
>
>Nieuwe resource-Id's worden gemaakt als onderdeel van de verplaatsing. Nadat de VM is verplaatst, moet u uw hulp middelen en scripts bijwerken om de nieuwe resource-Id's te gebruiken.
>
>

## <a name="use-the-azure-cli-to-move-a-vm"></a>De Azure CLI gebruiken om een virtuele machine te verplaatsen


Voordat u uw virtuele machine kunt verplaatsen met behulp van de Azure CLI, moet u ervoor zorgen dat de bron-en doel abonnementen binnen dezelfde Tenant bestaan. Gebruik [AZ account show](/cli/azure/account)om te controleren of beide abonnementen dezelfde Tenant-id hebben.

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Als de Tenant-Id's voor de bron-en doel abonnementen niet hetzelfde zijn, moet u contact opnemen met de [ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om de resources te verplaatsen naar een nieuwe Tenant.

Als u een virtuele machine wilt verplaatsen, moet u de virtuele machine en alle ondersteunende bronnen verplaatsen. Gebruik de opdracht [AZ Resource List](/cli/azure/resource) om alle resources in een resource groep en de bijbehorende id's weer te geven. Het helpt om de uitvoer van deze opdracht door te sluizen naar een bestand, zodat u de Id's in latere opdrachten kunt kopiëren en plakken.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Als u een virtuele machine en de bijbehorende resources wilt verplaatsen naar een andere resource groep, gebruikt u [AZ resource Move](/cli/azure/resource). In het volgende voor beeld ziet u hoe u een virtuele machine verplaatst en de meest voorkomende bronnen die nodig zijn. Gebruik de para meter **-id's** en geef een door komma's gescheiden lijst (zonder spaties) van id's door om de resources te verplaatsen.

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

Als u de virtuele machine en de bijbehorende resources naar een ander abonnement wilt verplaatsen, voegt u de para meter **--Destination-subscriptionId** toe om het doel abonnement op te geven.

Wanneer u wordt gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen, voert u **Y** om te bevestigen.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Volgende stappen
U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie.    
