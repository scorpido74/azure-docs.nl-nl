---
title: Een subnetdelegatie toevoegen of verwijderen in een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen of verwijderen van een gedelegeerd subnet voor een service in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201863"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Een subnetdelegatie toevoegen of verwijderen

Subnetdelegatie geeft expliciete machtigingen aan de service om servicespecifieke bronnen in het subnet te maken met behulp van een unieke id bij het implementeren van de service. In dit artikel wordt beschreven hoe u een gedelegeerd subnet voor een Azure-service toevoegt of verwijdert.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet dat u later delegeren aan een Azure-service.

1. Selecteer linksboven**Networking** > in het scherm de optie Een**virtueel netwerknetwerk voor** **bronnen** > maken .
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork in*. |
    | Adresruimte | Voer *10.0.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **EastUS**.|
    | Subnet - Naam | Voer *mySubnet*in . |
    | Subnet - adresbereik | Voer *10.0.0.0/24* in. |
    |||
1. Laat de rest als standaard en selecteer **Vervolgens Maken**.

### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende toestemming nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegert u het subnet dat u in de vorige sectie hebt gemaakt aan een Azure-service.

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoekresultaten .
3. Selecteer **Subnetten**onder **INSTELLINGEN**en selecteer **mySubnet**.
4. Selecteer op de pagina *mySubnet* voor de **subnetdelegatielijst** uit de services die worden vermeld onder **Subnet delegeren naar een service** (bijvoorbeeld **Microsoft.DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegatie uit een Azure-service verwijderen

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoekresultaten .
3. Selecteer **Subnetten**onder **INSTELLINGEN**en selecteer **mySubnet**.
4. Selecteer op de pagina *MySubnet* voor de lijst **subnetdelegatie** de optie **Geen** uit de services die worden vermeld onder **Subnet delegeren aan een service**. 

## <a name="azure-cli"></a>Azure-CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u besluit Azure CLI lokaal te installeren en te gebruiken, moet u in dit artikel Azure CLI-versie 2.0.28 of hoger gebruiken. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroup** op de **locatie Eastus geaald:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) in **myResourceGroup** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende toestemming nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegert u het subnet dat u in de vorige sectie hebt gemaakt aan een Azure-service. 

Gebruik [de vnet-subnetupdate van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) om het subnet met de naam **mySubnet** bij te werken met een delegatie naar een Azure-service.  In dit voorbeeld wordt **Microsoft.DBforPostgreSQL/serversv2** gebruikt voor de voorbeelddelegatie:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Gebruik het [az-netwerk vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)om te controleren of de delegatie is toegepast. Controleer of de service is gedelegeerd aan het subnet onder **eigenschapserviceNaam:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegatie uit een Azure-service verwijderen

Gebruik [de vnet-subnetupdate van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) om de delegatie uit het subnet met de naam **mySubnet**te verwijderen:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Gebruik het [az-netwerk vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)om te controleren of de delegatie is verwijderd. Controleer of de service uit het subnet wordt verwijderd onder de **eigenschappenserviceNaam:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Uitvoer van de opdracht is een nulbeugel:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Verbinding maken met Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een virtueel netwerk genaamd **myVnet** met een subnet genaamd **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroup** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **10.0.0.0/16**. Het subnet binnen het virtuele netwerk is **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende toestemming nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegert u het subnet dat u in de vorige sectie hebt gemaakt aan een Azure-service. 

Gebruik [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) om het subnet met de naam **mySubnet** bij te werken met een delegatie met de naam **myDelegation** naar een Azure-service.  In dit voorbeeld wordt **Microsoft.DBforPostgreSQL/serversv2** gebruikt voor de voorbeelddelegatie:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) om de delegatie te verifiëren:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegatie uit een Azure-service verwijderen

Gebruik [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) om de delegatie van het subnet genaamd **mySubnet**te verwijderen:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) om te controleren of de delegatie is verwijderd:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [beheren van subnetten in Azure](virtual-network-manage-subnet.md).
