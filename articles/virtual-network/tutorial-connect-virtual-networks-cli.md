---
title: Virtuele netwerken verbinden met VNet-peering - Azure CLI
description: In dit artikel leert u hoe u virtuele netwerken verbinden met virtueel netwerkpeeren met azure cli.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: aa2d75173b14e768a207336b54b3dc10a8c3ea5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235167"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuele netwerken verbinden met virtueel netwerkpeeren met de Azure CLI

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In dit artikel leert u het volgende:

* Twee virtuele netwerken maken
* Twee virtuele netwerken koppelen met virtueel-netwerkpeering
* Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
* Communicatie tussen virtuele machines

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.28 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

Voordat u een virtueel netwerk maakt, moet u een brongroep maken voor het virtuele netwerk en alle andere bronnen die in dit artikel zijn gemaakt. Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork1 met* het adresvoorvoegsel *10.0.0.0/16 .*

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Maak een virtueel netwerk met de naam *myVirtualNetwork2* met het adresvoorvoegsel *10.1.0.0/16:*

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

Peerings zijn vastgesteld tussen virtuele netwerk-id's, dus je moet eerst de ID van elk virtueel netwerk met [az-netwerk vnet show](/cli/azure/network/vnet) en sla de ID in een variabele.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Maak een peering van *myVirtualNetwork1* naar *myVirtualNetwork2* met [az-netwerk vnet peering maken](/cli/azure/network/vnet/peering). Als `--allow-vnet-access` de parameter niet is opgegeven, wordt een peering vastgesteld, maar kan er geen communicatie doorheen stromen.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **peeringstate** *is gestart*. Het peering blijft in de *geïnitieerde* status totdat u het peering maakt van *myVirtualNetwork2* naar *myVirtualNetwork1*. Maak een peering van *myVirtualNetwork2* naar *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **peeringstate** is *verbonden*. Azure heeft ook de peeringstatus van de *myVirtualNetwork1-myVirtualNetwork2-peering* gewijzigd in *Verbonden*. Controleer of de peeringstatus voor de *myVirtualNetwork1-myVirtualNetwork2-peering* is gewijzigd *in Verbonden* met [vnet-peeringshow van het AZ-netwerk.](/cli/azure/network/vnet/peering)

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Bronnen in het ene virtuele netwerk kunnen niet communiceren met bronnen in het andere virtuele netwerk totdat de **peeringstate** voor de peerings in beide virtuele netwerken *is verbonden.* 

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVm1* in het virtuele *netwerk myVirtualNetwork1* gemaakt. Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. Met `--no-wait` de optie wordt de VM op de achtergrond gemaakt, zodat u door gaan naar de volgende stap.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>De tweede VM maken

Maak een VM in het virtuele *myVirtualNetwork2-netwerk.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die vergelijkbaar is met het volgende voorbeeld: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt om toegang te krijgen tot de VM vanaf het internet in een latere stap.

## <a name="communicate-between-vms"></a>Communicatie tussen virtuele machines

Gebruik de volgende opdracht om een SSH-sessie te maken met de *myVm2* VM. Vervang `<publicIpAddress>` door het openbare IP-adres van uw vm. In het vorige voorbeeld is het openbare IP-adres *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Ping de VM in *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

U ontvangt vier antwoorden. 

Sluit de SSH-sessie af op de *myVm2* VM. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de verwijdering van [de AZ-groep](/cli/azure/group) om de brongroep en alle bronnen die deze bevat te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u twee netwerken in dezelfde Azure-regio verbinden, met virtuele netwerkpeering. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region) en in [ verschillende Azure-abonnementen ](create-peering-different-subscriptions.md#cli) 'peeren', en peering gebruiken om [netwerkontwerpen met een stertopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) te maken. Zie voor meer informatie over virtueel-netwerkpeering [Peering op virtueel netwerk](virtual-network-peering-overview.md) en [Virtueel-netwerkpeerings beheren](virtual-network-manage-peering.md).

U [uw eigen computer verbinden met een virtueel netwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN en communiceren met bronnen in een virtueel netwerk of in peered virtuele netwerken. Zie [scriptvoorbeelden](cli-samples.md)voor herbruikbare scripts om veel van de taken in de virtuele netwerkartikelen te voltooien.
