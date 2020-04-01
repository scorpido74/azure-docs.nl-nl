---
title: Routenetwerkverkeer - Azure CLI | Microsoft Documenten
description: In dit artikel leest u hoe u netwerkverkeer routeert met een routetabel met de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 5fa94b93e081ab6334c39b848068f50682f5f1f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235056"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Netwerkverkeer routeren met een routetabel met de Azure CLI

Azure routeert standaard automatisch verkeer tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. De mogelijkheid voor het maken van aangepaste routes is handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In dit artikel leert u het volgende:

* Een routetabel maken
* Een route maken
* Een virtueel netwerk met meerdere subnetten maken
* Een routetabel aan een subnet koppelen
* Een NVA maken voor het routeren van verkeer
* Virtuele machines (VM's) implementeren in verschillende subnetten
* Verkeer van het ene subnet naar het andere leiden via een NVA

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart de Azure CLI versie 2.0.28 of later uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Een routetabel maken

Voordat u een routetabel maken, maakt u een resourcegroep met [de AZ-groep voor](/cli/azure/group) alle bronnen die in dit artikel zijn gemaakt. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een routetabel met [de routetabel van az-netwerk.](/cli/azure/network/route-table#az-network-route-table-create) In het volgende voorbeeld wordt een routetabel met de naam *myRouteTablePublic gemaakt.* 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Een route maken

Maak een route in de routetabel met [de route-tabelroute van az-netwerk.](/cli/azure/network/route-table/route#az-network-route-table-route-create) 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken. Maak een virtueel netwerk met één subnet met [az-netwerk vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Maak twee extra subnetten met [az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Koppel de *tabel myRouteTablePublic* route aan het *openbare* subnet aan [subnetupdate van het AZ-netwerk.](/cli/azure/network/vnet/subnet)

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Een NVA maken

Een NVA is een VM die een netwerkfunctie uitvoert, zoals routering, firewall of WAN-optimalisatie.

Maak een NVA in het *DMZ-subnet* met [az vm create](/cli/azure/vm). Wanneer u een vm maakt, maakt en wijst Azure standaard een openbaar IP-adres toe aan de vm. De `--public-ip-address ""` parameter instrueert Azure om geen openbaar IP-adres aan de VM te maken en toe te wijzen, omdat de VM niet vanaf internet hoeft te worden verbonden. Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door naar de volgende stap totdat Azure klaar is met het maken van de VM en de uitvoer over de VM retourneert. 

Een netwerkinterface kan alleen netwerkverkeer doorsturen dat niet voor zijn eigen IP-adres bestemd is, als doorsturen via IP voor de netwerkinterface is ingeschakeld. Schakel IP forwarding in voor de netwerkinterface met [de AZ Network Nic update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

In de VM moet het besturingssysteem of een toepassing die wordt uitgevoerd op de virtuele machine, ook netwerkverkeer kunnen doorsturen. Ip forwarding inschakelen binnen het besturingssysteem van de VM met [az vm-extensieset:](/cli/azure/vm/extension)

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Het kan tot een minuut duren voordat de opdracht is uitgevoerd.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM's in het virtuele netwerk, zodat u valideren dat verkeer van het *openbare* subnet in een latere stap via het subnet *Privé* via de NVA wordt doorgestuurd. 

Maak een VM in het *subnet Openbaar* met [az vm maken](/cli/azure/vm). Met `--no-wait` de parameter kan Azure de opdracht op de achtergrond uitvoeren, zodat u door gaan naar de volgende opdracht. Om dit artikel te stroomlijnen, wordt een wachtwoord gebruikt. Sleutels worden meestal gebruikt in productie-implementaties. Als u sleutels gebruikt, moet u ook SSH-agent forwarding configureren. Zie voor meer informatie de documentatie voor uw SSH-client. Vervang `<replace-with-your-password>` in de volgende opdracht een wachtwoord naar keuze.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Maak een VM in het *subnet Privé.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die vergelijkbaar is met het volgende voorbeeld: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt om toegang te krijgen tot de VM vanaf het internet in een latere stap.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

Gebruik de volgende opdracht om een SSH-sessie te maken met de *myVmPrivate* VM. Vervang * \<publicIpAddress>* door het openbare IP-adres van uw vm. In het bovenstaande voorbeeld is het IP-adres *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Wanneer u om een wachtwoord wordt gevraagd, voert u het wachtwoord in dat u hebt geselecteerd in [Virtuele machines maken.](#create-virtual-machines)

Gebruik de volgende opdracht om traceroute te installeren op de *myVmPrivate* VM:

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de routering voor netwerkverkeer naar de *myVmPublic* VM van de *myVmPrivate* VM te testen.

```bash
traceroute myVmPublic
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

U ziet dat verkeer rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. De standaardroutes van Azure, routeverkeer rechtstreeks tussen subnetten. 

Gebruik de volgende opdracht om SSH te gebruiken voor de *myVmPublic* VM van de *myVmPrivate* VM:

```bash
ssh azureuser@myVmPublic
```

Gebruik de volgende opdracht om traceroute te installeren op de *myVmPublic* VM:

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de routering voor netwerkverkeer naar de *myVmPrivate* VM van de *myVmPublic* VM te testen.

```bash
traceroute myVmPrivate
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

U ziet dat de eerste hop 10.0.2.4 is, het privé IP-adres van het NVA. De tweede hop is 10.0.1.4, het privé- IP-adres van de VM *myVmPrivate*. Door de route die is toegevoegd aan de routetabel *myRouteTablePublic* en gekoppeld aan het *Openbare* subnet leidt Azure het verkeer via het NVA in plaats van rechtstreeks naar het *Privé*-subnet.

Sluit de SSH-sessies af op zowel de *myVmPublic-* als *de myVmPrivate* VM's.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de verwijdering van [de AZ-groep](/cli/azure/group) om de brongroep en alle bronnen die deze bevat te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een routetabel gemaakt en deze gekoppeld aan een subnet. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. U kunt allerlei vooraf geconfigureerde NVA's die netwerkfuncties uitvoeren zoals firewalls en WAN-optimalisatie, implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen resources voor sommige Azure PaaS-diensten niet in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot de resources van sommige Azure PaaS-diensten echter nog steeds beperken tot alleen verkeer vanaf een subnet van een virtueel netwerk. Zie [Netwerktoegang tot PaaS-bronnen beperken voor](tutorial-restrict-network-access-to-resources-cli.md)meer informatie.
