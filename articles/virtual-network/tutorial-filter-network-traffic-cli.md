---
title: Netwerkverkeer filteren - Azure CLI | Microsoft Documenten
description: In dit artikel leert u hoe u netwerkverkeer filtert naar een subnet, met een netwerkbeveiligingsgroep, met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 72c8b4d57b5064af34665cff1386179e62324938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235078"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Netwerkverkeer filteren met een netwerkbeveiligingsgroep met de Azure CLI

U kunt het netwerkverkeer inkomend in en uitgaand naar een subnet van een virtueel netwerk filteren met een netwerkbeveiligingsgroep. Netwerkbeveiligingsgroepen bevatten beveiligingsregels die netwerkverkeer filteren op IP-adres, poort en protocol. Beveiligingsregels worden toegepast op resources die zijn geïmplementeerd in een subnet. In dit artikel leert u het volgende:

* Een netwerkbeveiligingsgroep en beveiligingsregels maken
* Een virtueel netwerk maken en een netwerkbeveiligingsgroep koppelen aan een subnet
* Virtuele machines (VM) implementeren in een subnet
* Verkeersfilters testen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.28 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerkbeveiligingsgroep bevat beveiligingsregels. Beveiligingsregels geven een bron en doel op. Bronnen en doelen kunnen toepassingsbeveiligingsgroepen zijn.

### <a name="create-application-security-groups"></a>Toepassingsbeveiligingsgroepen maken

Maak eerst een resourcegroep voor alle bronnen die in dit artikel zijn gemaakt met [de AZ-groep maken](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam gemaakt op de locatie *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een applicatiebeveiligingsgroep met [az-netwerkasg create](/cli/azure/network/asg). Met een toepassingsbeveiligingsgroep kunt u servers met vergelijkbare poortfiltervereisten groeperen. In het volgende voorbeeld worden twee toepassingsbeveiligingsgroepen gemaakt.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNsg* gemaakt: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Beveiligingsregels maken

Maak een beveiligingsregel met [de NSG-regel van het AZ-netwerk.](/cli/azure/network/nsg/rule) In het volgende voorbeeld wordt een regel gemaakt die inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myWebServers* toestaat via de poorten 80 en 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

In het volgende voorbeeld wordt een regel verbonden waarmee verkeer van internet naar de beveiligingsgroep *van de myMgmtServers-toepassing* via poort 22 kan worden binnengeleid:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

In dit artikel wordt SSH (poort 22) blootgesteld aan het internet voor de *myAsgMgmtServers* VM. Voor productieomgevingen wordt in plaats van poort 22 bloot te stellen aan het internet, aanbevolen om verbinding te maken met Azure-bronnen die u wilt beheren via een [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [privénetwerkverbinding.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Voeg een subnet toe aan een virtueel netwerk met [het az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet). In het volgende voorbeeld wordt een subnet met de naam *mySubnet* toegevoegd aan het virtuele netwerk en wordt de netwerkbeveiligingsgroep *myNsg* hieraan gekoppeld:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee virtuele machines in het virtuele netwerk, zodat u het filteren van verkeer in een latere stap kunt controleren. 

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een virtuele machine gemaakt die als een webserver fungeert. De `--asgs myAsgWebServers` optie zorgt ervoor dat Azure de netwerkinterface die het maakt voor de VM een lid van de *myAsgWebServers-toepassingsbeveiligingsgroep* maakt.

De `--nsg ""` optie is opgegeven om te voorkomen dat Azure een standaardnetwerkbeveiligingsgroep maakt voor de netwerkinterface die Azure maakt wanneer de vm wordt gemaakt. Om dit artikel te stroomlijnen, wordt een wachtwoord gebruikt. Sleutels worden meestal gebruikt in productie-implementaties. Als u sleutels gebruikt, moet u ook SSH-agent forwarding configureren voor de resterende stappen. Zie voor meer informatie de documentatie voor uw SSH-client. Vervang `<replace-with-your-password>` in de volgende opdracht een wachtwoord naar keuze.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, wordt de uitvoer die vergelijkbaar is met het volgende voorbeeld geretourneerd: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt om toegang te krijgen tot de VM vanaf het internet in een latere stap.  Maak een VM die fungeert als een beheerserver:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, noteert u het **publicIpAddress** in de geretourneerde uitvoer. Dit adres wordt gebruikt om toegang te krijgen tot de VM in de volgende stap. Ga pas verder met de volgende stap als Azure klaar is met het maken van de virtuele machine.

## <a name="test-traffic-filters"></a>Verkeersfilters testen

Gebruik de opdracht die volgt om een SSH-sessie te maken met de *myVmMgmt* VM. Vervang * \<publicIpAddress>* door het openbare IP-adres van uw vm. In het bovenstaande voorbeeld is het IP-adres *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Wanneer u om een wachtwoord wordt gevraagd, voert u het wachtwoord in dat u hebt ingevoerd in [VM's maken.](#create-virtual-machines)

De verbinding slaagt, omdat poort 22 is toegestaan inkomende van het internet naar de *myAsgMgmtServers* applicatie beveiligingsgroep die de netwerkinterface gekoppeld aan de *myVmMgmt* VM is in.

Gebruik de volgende opdracht om SSH naar de *myVmWeb* VM te gaan vanuit de *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

De verbinding slaagt omdat een standaardbeveiligingsregel binnen elke netwerkbeveiligingsgroep verkeer via alle poorten tussen alle IP-adressen binnen een virtueel netwerk toestaat. U Niet SSH naar de *myVmWeb* VM vanaf het internet, omdat de beveiligingsregel voor de *myAsgWebServers* niet toestaan poort 22 inkomende van het internet.

Gebruik de volgende opdrachten om de nginx-webserver op de *myVmWeb* VM te installeren:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

De *myVmWeb* VM mag uitgaan naar het internet om nginx op te halen, omdat een standaard beveiligingsregel al het uitgaande verkeer naar het internet toestaat. Sluit de *myVmWeb* SSH-sessie af, waardoor u op de `username@myVmMgmt:~$` prompt van de *myVmMgmt* VM overblijft. Voer de volgende opdracht in om het welkomstscherm van nginx op te halen van de *myVmWeb* VM:

```bash
curl myVmWeb
```

Afmelden bij de *myVmMgmt* VM. Voer de myVmWeb-webserver van buiten Azure in `curl <publicIpAddress>` om te controleren of u de *myVmWeb-webserver* van buiten Azure openen. De verbinding slaagt, omdat poort 80 is toegestaan inkomende van het internet naar de *myAsgWebServers* applicatie beveiligingsgroep die de netwerkinterface gekoppeld aan de *myVmWeb* VM is in.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de verwijdering van [de AZ-groep](/cli/azure/group) om de brongroep en alle bronnen die deze bevat te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een netwerkbeveiligingsgroep gemaakt en deze gekoppeld aan een virtueel netwerksubnet. Zie [Overzicht van netwerkbeveiligingsgroepen](security-overview.md) en [Een beveiligingsgroep beheren](manage-network-security-group.md) voor meer informatie over netwerkbeveiligingsgroepen.

Azure routeert standaard verkeer tussen subnetten. In plaats daarvan kunt u verkeer routeren tussen subnetten via een virtuele machine, die bijvoorbeeld als een firewall fungeert. Zie [Een routetabel maken](tutorial-create-route-table-cli.md)voor meer informatie.
