---
title: Poorten openen voor een VM met Azure CLI
description: Meer informatie over het openen van een poort / het maken van een eindpunt voor uw VM met behulp van de Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066635"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Poorten en eindpunten openen voor een VM met de Azure CLI

U opent een poort of maakt een eindpunt voor een virtuele machine (VM) in Azure door een netwerkfilter te maken op een subnet- of VM-netwerkinterface. U plaatst deze filters, die zowel binnenkomend als uitgaand verkeer beheren, op een netwerkbeveiligingsgroep die is gekoppeld aan de bron die het verkeer ontvangt. Laten we een algemeen voorbeeld van webverkeer op poort 80 gebruiken. In dit artikel ziet u hoe u een poort opent voor een vm met de Azure CLI. 


Als u een netwerkbeveiligingsgroep en -regels wilt maken, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) installeren en aanmelden bij een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen zijn *myResourceGroup,* *myNetworkSecurityGroup*en *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Open snel een poort voor een virtuele machine
Als u snel een poort voor een VM moet openen in een dev/testscenario, u de opdracht [open poort van AZ VM](/cli/azure/vm) gebruiken. Met deze opdracht wordt een netwerkbeveiligingsgroep, wordt een regel toegevoegd en wordt deze op een VM of subnet toepast. In het volgende voorbeeld wordt poort *80* geopend op de VM met de naam *myVM* in de resourcegroep *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Ga verder met de extra stappen in dit artikel voor meer controle over de regels, zoals het definiëren van een ip-adresbereik voor bronnen.


## <a name="create-a-network-security-group-and-rules"></a>Een netwerkbeveiligingsgroep en -regels maken
Maak de netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* op de *locatie Eastus* ge:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Voeg een regel toe met [de NSG-regel van het AZ-netwerk](/cli/azure/network/nsg/rule) om HTTP-verkeer toe te staan voor uw webserver (of aan te passen voor uw eigen scenario, zoals SSH-toegang of databaseconnectiviteit). In het volgende voorbeeld wordt een regel met de naam *myNetworkSecurityGroupRule* maakt om TCP-verkeer toe te staan op poort 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Netwerkbeveiligingsgroep toepassen op VM
Koppel de Network Security Group aan de netwerkinterface (NIC) van uw VM aan [de az-netwerknic-update.](/cli/azure/network/nic) In het volgende voorbeeld wordt een bestaande NIC met de naam *myNic* gekoppeld aan de Network Security Group genaamd *myNetworkSecurityGroup:*

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

U uw Network Security Group ook koppelen aan een virtueel netwerksubnet met [een vnet-subnetupdate van het AZ-netwerk](/cli/azure/network/vnet/subnet) in plaats van alleen aan de netwerkinterface op één VM. In het volgende voorbeeld wordt een bestaand subnet met de naam *mySubnet* in het virtuele *myVnet-netwerk* gekoppeld aan de Network Security Group genaamd *myNetworkSecurityGroup:*

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Meer informatie over netwerkbeveiligingsgroepen
Met de snelle opdrachten u aan de slag met verkeer dat naar uw VM stroomt. Netwerkbeveiligingsgroepen bieden veel geweldige functies en granulariteit voor het beheren van de toegang tot uw bronnen. U hier meer lezen over [het maken van een netwerkbeveiligingsgroep en ACL-regels.](tutorial-virtual-network.md#secure-network-traffic)

Voor zeer beschikbare webtoepassingen moet u uw VM's achter een Azure Load Balancer plaatsen. De load balancer distribueert verkeer naar VM's, met een netwerkbeveiligingsgroep die verkeersfiltering biedt. Zie Voor meer informatie [hoe u linux-virtuele machines in Azure in balans laden om een zeer beschikbare toepassing te maken.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Volgende stappen
In dit voorbeeld hebt u een eenvoudige regel gemaakt om HTTP-verkeer toe te staan. In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:

* [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Wat is een netwerkbeveiligingsgroep (NSG)?](../../virtual-network/security-overview.md)
