---
title: Interne DNS gebruiken voor vm-naamomzetting met de Azure CLI
description: Virtuele netwerkinterfacekaarten maken en interne DNS gebruiken voor vm-naamomzetting op Azure met de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 07a78e4987a844627824ac5034046cf6a393ad8d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757839"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Virtuele netwerkinterfacekaarten maken en interne DNS gebruiken voor vm-naamomzetting op Azure

In dit artikel ziet u hoe u statische interne DNS-namen voor Linux-VM's instelt met behulp van virtuele netwerkinterfacekaarten (vNics) en DNS-labelnamen met de Azure CLI. Statische DNS-namen worden gebruikt voor permanente infrastructuurservices zoals een Jenkins-buildserver, die wordt gebruikt voor dit document of een Git-server.

De vereisten zijn:

* [een Azure-account](https://azure.microsoft.com/pricing/free-trial/)
* [bestanden voor openbare en persoonlijke SSH-sleutels](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snelle opdrachten
Als u de taak snel moet uitvoeren, worden in de volgende sectie de benodigde opdrachten beschreven. Meer gedetailleerde informatie en context voor elke stap is te vinden in de rest van het document, [te beginnen hier](#detailed-walkthrough). Om deze stappen uit te voeren, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index).

Pre-requirements: Resource Group, virtueel netwerk en subnet, Network Security Group met SSH inbound.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Een virtuele netwerkinterfacekaart maken met een statische interne DNS-naam
Maak de vNic met [az-netwerk nic maken](/cli/azure/network/nic). De `--internal-dns-name` CLI-vlag is voor het instellen van het DNS-label, dat de statische DNS-naam voor de virtuele netwerkinterfacekaart (vNic) biedt. In het volgende voorbeeld `myNic`wordt een vNic met de naam, verbindt deze met het `myVnet` virtuele netwerk en wordt een interne DNS-naamrecord met de naam : `jenkins`

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Een VM implementeren en de vNic verbinden
Maak een VM met [az vm create](/cli/azure/vm). De `--nics` vlag verbindt de vNic met de VM tijdens de implementatie met Azure. In het volgende voorbeeld `myVM` wordt een VM met de `myNic` naam Azure Managed Disks gemaakt en wordt de vNic gekoppeld aan de vorige stap bevestigd:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Gedetailleerd overzicht

Een cicd-infrastructuur (full continuous integration and continuous deployment) op Azure vereist dat bepaalde servers statische of langdurige servers zijn. Het wordt aanbevolen dat Azure-assets zoals de virtuele netwerken en netwerkbeveiligingsgroepen statische en langlevende resources zijn die zelden worden geïmplementeerd. Zodra een virtueel netwerk is geïmplementeerd, kan het worden hergebruikt door nieuwe implementaties zonder nadelige gevolgen voor de infrastructuur. U later een Git repository-server toevoegen of een Jenkins-automatiseringsserver levert CiCd aan dit virtuele netwerk voor uw ontwikkel- of testomgevingen.  

Interne DNS-namen zijn alleen oplosbaar in een virtueel Azure-netwerk. Omdat de DNS-namen intern zijn, zijn ze niet oplosbaar voor het externe internet, waardoor extra beveiliging voor de infrastructuur wordt geboden.

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeeldparameternamen `myResourceGroup`zijn `myNic`, `myVM`en .

## <a name="create-the-resource-group"></a>De resourcegroep maken
Maak eerst de resourcegroep met [de AZ-groep maken](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

De volgende stap is het bouwen van een virtueel netwerk om de VM's te lanceren in. Het virtuele netwerk bevat één subnet voor deze walkthrough. Zie [Een virtueel netwerk maken](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)voor meer informatie over virtuele Azure-netwerken. 

Maak het virtuele netwerk met [az-netwerk vnet maken](/cli/azure/network/vnet). In het volgende voorbeeld `myVnet` wordt een `mySubnet`virtueel netwerk met de naam en subnet met de naam:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>De netwerkbeveiligingsgroep maken
Azure Network Security Groups zijn gelijk aan een firewall op de netwerklaag. Zie [NsGs maken in de Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie over netwerkbeveiligingsgroepen. 

Maak de netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt `myNetworkSecurityGroup`een netwerkbeveiligingsgroep met de naam:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Een inkomende regel toevoegen om SSH toe te staan
Voeg een inkomende regel toe voor de netwerkbeveiligingsgroep met [de NSG-regel van het AZ-netwerk.](/cli/azure/network/nsg/rule) In het volgende voorbeeld `myRuleAllowSSH`wordt een regel met de naam :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Het subnet koppelen aan de netwerkbeveiligingsgroep
Als u het subnet wilt koppelen aan de Network Security Group, gebruikt u [de vnet-subnetupdate van het AZ-netwerk.](/cli/azure/network/vnet/subnet) In het volgende voorbeeld `mySubnet` wordt de subnetnaam gekoppeld aan de netwerkbeveiligingsgroep met de naam: `myNetworkSecurityGroup`

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>De virtuele netwerkinterfacekaart en statische DNS-namen maken
Azure is zeer flexibel, maar om DNS-namen te gebruiken voor vm-naamomzetting, moet u virtuele netwerkinterfacekaarten (vNics) maken die een DNS-label bevatten. vNics zijn belangrijk omdat u ze hergebruiken door ze aan te sluiten op verschillende VM's gedurende de levenscyclus van de infrastructuur. Deze benadering houdt de vNic als een statische bron, terwijl de VM's tijdelijk kunnen zijn. Door dns-labeling op de vNic te gebruiken, kunnen we eenvoudige naamomzetting van andere VM's in het VNet inschakelen. Met behulp van oplosbare namen kunnen andere VM's `Jenkins` toegang krijgen tot `gitrepo`de automatiseringsserver met de DNS-naam of de Git-server als .  

Maak de vNic met [az-netwerk nic maken](/cli/azure/network/nic). In het volgende voorbeeld `myNic`wordt een vNic met `myVnet`de naam, verbindt deze `jenkins`met het virtuele netwerk met de `myVnet` naam en wordt een interne DNS-naamrecord met de naam :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>De VM implementeren in de virtuele netwerkinfrastructuur
We hebben nu een virtueel netwerk en subnet, een Network Security Group die optreedt als een firewall om ons subnet te beschermen door het blokkeren van alle inkomende verkeer, behalve poort 22 voor SSH, en een vNic. U nu een VM implementeren in deze bestaande netwerkinfrastructuur.

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt een VM met de `myNic` naam Azure Managed Disks gemaakt en wordt de vNic gekoppeld aan de vorige stap bevestigd:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Door de CLI-vlaggen te gebruiken om bestaande bronnen uit te roepen, instrueren we Azure om de VM binnen het bestaande netwerk te implementeren. Nogmaals, zodra een VNet en subnet zijn geïmplementeerd, kunnen ze worden overgelaten als statische of permanente bronnen binnen uw Azure-regio.  

## <a name="next-steps"></a>Volgende stappen
* [Rechtstreeks uw eigen aangepaste omgeving maken voor een virtuele Linux-machine met Azure CLI-opdrachten](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een Linux-vm op Azure maken met behulp van sjablonen](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
