---
title: Interne DNS gebruiken voor VM-naam omzetting met Azure CLI | Microsoft Docs
description: Virtuele netwerk interface kaarten maken en interne DNS gebruiken voor VM-naam omzetting in azure met de Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: b143a28216d0d8417615ad2c40a3b66c9c5d1be2
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173864"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Virtuele netwerk interface kaarten maken en interne DNS gebruiken voor VM-naam omzetting in azure

Dit artikel laat u zien hoe u statische interne DNS-namen voor Linux-Vm's kunt instellen met behulp van virtuele netwerk interface kaarten (Vnic's) en DNS-label namen met de Azure CLI. Statische DNS-namen worden gebruikt voor permanente infrastructuur services, zoals een Jenkins build-server, die wordt gebruikt voor dit document of een Git-server.

De vereisten zijn:

* [een Azure-account.](https://azure.microsoft.com/pricing/free-trial/)
* [bestanden voor openbare en persoonlijke SSH-sleutels](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snelle opdrachten
Als u de taak snel moet uitvoeren, wordt in de volgende sectie meer informatie over de benodigde opdrachten beschreven. Meer gedetailleerde informatie en context voor elke stap vindt u in de rest van het document, [beginnend hier](#detailed-walkthrough). Als u deze stappen wilt uitvoeren, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

Vereisten: Resource groep, virtueel netwerk en subnet, netwerk beveiligings groep met SSH-binnenkomend.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Een virtuele netwerk interface kaart maken met een statische interne DNS-naam
Maak de vNic met [AZ Network NIC Create](/cli/azure/network/nic). De `--internal-dns-name` cli-vlag is voor het instellen van het DNS-label, dat de statische DNS-naam voor de virtuele netwerk interface kaart (vNic) biedt. In het volgende voor beeld wordt een `myNic`vNic gemaakt met de naam `myVnet` , wordt dit verbonden met het virtuele netwerk en wordt een `jenkins`interne DNS-naam record gemaakt met de naam:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Een virtuele machine implementeren en verbinding maken met de vNic
Maak een VM met [az vm create](/cli/azure/vm). De `--nics` vlag verbindt de vNic met de virtuele machine tijdens de implementatie naar Azure. In het volgende voor beeld wordt een `myVM` VM gemaakt met de naam Azure Managed disks en wordt `myNic` de vNic gekoppeld aan de naam van de vorige stap:

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

Voor een volledige continue integratie-infra structuur (continue implementatie en CiCd) op Azure moeten bepaalde servers statische of lange servers zijn. Het is raadzaam dat Azure-assets, zoals de virtuele netwerken en netwerk beveiligings groepen, statische en lange levens bronnen zijn die zelden worden geïmplementeerd. Zodra een virtueel netwerk is geïmplementeerd, kan het opnieuw worden gebruikt door nieuwe implementaties zonder dat dit gevolgen heeft voor de infra structuur. U kunt later een Git-opslagplaats server toevoegen of een Jenkins Automation-Server levert CiCd aan dit virtuele netwerk voor uw ontwikkel-en test omgevingen.  

Interne DNS-namen kunnen alleen worden omgezet in een virtueel Azure-netwerk. Omdat de DNS-namen intern zijn, kunnen ze niet worden omgezet naar het externe Internet, waardoor er geen extra beveiliging is voor de infra structuur.

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter `myResourceGroup`namen `myNic`zijn, `myVM`en.

## <a name="create-the-resource-group"></a>De resourcegroep maken
Maak eerst de resource groep met [AZ Group Create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

De volgende stap bestaat uit het bouwen van een virtueel netwerk om de Vm's in te starten. Het virtuele netwerk bevat één subnet voor deze walkthrough. Zie [een virtueel netwerk maken](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)voor meer informatie over virtuele Azure-netwerken. 

Maak het virtuele netwerk met [AZ Network vnet Create](/cli/azure/network/vnet). In het volgende voor beeld wordt een virtueel `myVnet` netwerk gemaakt met `mySubnet`de naam en het subnet met de naam:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>De netwerk beveiligings groep maken
Azure-netwerk beveiligings groepen zijn gelijk aan een firewall op de netwerklaag. Zie [nsg's maken in de Azure cli](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie over netwerk beveiligings groepen. 

Maak de netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voor beeld wordt een netwerk beveiligings `myNetworkSecurityGroup`groep gemaakt met de naam:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Een regel voor binnenkomende verbindingen voor het toestaan van SSH toevoegen
Voeg een regel voor binnenkomende verbindingen toe voor de netwerk beveiligings groep met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). In het volgende voor beeld wordt een `myRuleAllowSSH`regel gemaakt met de naam:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Het subnet koppelen aan de netwerk beveiligings groep
Gebruik [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet)om het subnet te koppelen aan de netwerk beveiligings groep. In het volgende voor beeld wordt de `mySubnet` naam van het subnet gekoppeld aan `myNetworkSecurityGroup`de netwerk beveiligings groep met de naam:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>De virtuele netwerk interface kaart en statische DNS-namen maken
Azure is zeer flexibel, maar om DNS-namen voor VM-naam omzetting te gebruiken, moet u virtuele netwerk interface kaarten (Vnic's) maken die een DNS-label bevatten. Vnic's zijn belang rijk omdat u ze opnieuw kunt gebruiken door deze te verbinden met verschillende Vm's gedurende de levens cyclus van de infra structuur. Deze aanpak houdt de vNic als een statische resource terwijl de Vm's tijdelijk kunnen zijn. Door DNS-labels op de vNic te gebruiken, kunnen we eenvoudige naam omzetting mogelijk maken van andere virtuele machines in het VNet. Met behulp van opgeloste namen kunnen andere vm's toegang krijgen tot de Automation- `Jenkins` server via de DNS- `gitrepo`naam of de Git-server als.  

Maak de vNic met [AZ Network NIC Create](/cli/azure/network/nic). In het volgende voor beeld wordt een `myNic`vNic gemaakt met de naam, verbonden met `myVnet`het virtuele netwerk met de naam `jenkins`en wordt een interne DNS-naam record gemaakt met de `myVnet` naam:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementeer de VM in de infra structuur van het virtuele netwerk
We hebben nu een virtueel netwerk en subnet, een netwerk beveiligings groep die fungeert als een firewall om ons subnet te beveiligen door al het binnenkomende verkeer behalve poort 22 voor SSH en een vNic te blok keren. U kunt nu een virtuele machine implementeren in deze bestaande netwerk infrastructuur.

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voor beeld wordt een `myVM` VM gemaakt met de naam Azure Managed disks en wordt `myNic` de vNic gekoppeld aan de naam van de vorige stap:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Door gebruik te maken van de CLI-vlaggen om bestaande resources aan te roepen, geven we Azure de opdracht om de virtuele machine in het bestaande netwerk te implementeren. Als u wilt herhalen, kunt u, nadat een VNet en subnet is geïmplementeerd, worden uitgevoerd als statische of permanente resources binnen uw Azure-regio.  

## <a name="next-steps"></a>Volgende stappen
* [Rechtstreeks uw eigen aangepaste omgeving maken voor een virtuele Linux-machine met Azure CLI-opdrachten](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een virtuele Linux-machine in azure maken met behulp van sjablonen](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
