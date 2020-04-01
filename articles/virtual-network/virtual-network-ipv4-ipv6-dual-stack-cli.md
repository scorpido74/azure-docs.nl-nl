---
title: IPv6 dual stack applicatie implementeren - Basic Load Balancer - CLI
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u een IPv6 dual stack-toepassing implementeert in het virtuele Azure-netwerk met Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 396c37d4c8de6a890102e435c5ec6cc70b598638
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421020"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Een IPv6 dual stack-toepassing implementeren met Basic Load Balancer - CLI

In dit artikel ziet u hoe u een dual stack -toepassing (IPv4 + IPv6) implementeert met Basic Load Balancer met Azure CLI die een dual stack virtueel netwerk bevat met een dual stack-subnet, een Basic Load Balancer met dubbele (IPv4 + IPv6) front-endconfiguraties, VM's met NIC's met een dubbele IP-configuratie, dubbele netwerkbeveiligingsgroepregels en dubbele openbare IP-adressen.

Zie [Een IPv6 dual stack-toepassing implementeren met Standard Load Balancer met Standaardload Balancer.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)


Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u besluit Azure CLI lokaal te installeren en te gebruiken, moet u azure CLI-versie 2.0.49 of hoger gebruiken. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw virtuele dual-stacknetwerk maken, moet u een resourcegroep maken met [de AZ-groep.](/cli/azure/group) In het volgende voorbeeld wordt een resourcegroep met de naam *DsResourceGroup01* op de *locatie Eastus geaald:*

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>IPv4- en IPv6-openbare IP-adressen maken voor load balancer
Om toegang te krijgen tot uw IPv4- en IPv6-eindpunten op internet, hebt u IPv4- en IPv6-openbare IP-adressen nodig voor de load balancer. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voorbeeld wordt iPv4- en IPv6-openbaar IP-adres gemaakt met *de* naam dsPublicIP_v4 en *dsPublicIP_v6* in de brongroep *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Openbare IP-adressen voor VM's maken

Om op afstand toegang te krijgen tot uw VM's op internet, hebt u IPv4-openbare IP-adressen voor de VM's nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Load balancer van het type Basic maken

In deze sectie configureert u dual frontend IP (IPv4 en IPv6) en de back-end address pool voor de load balancer en maakt u vervolgens een Basic Load Balancer.

### <a name="create-load-balancer"></a>Load balancer maken

Maak de Basic Load Balancer met [az-netwerk lb maak](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met de naam **dsLB** een frontendpool met de naam **dsLbFrontEnd_v4,** een backendpool met de naam **dsLbBackEndPool_v4** die is gekoppeld aan het openbare IP-adres van IPv4 **dsPublicIP_v4** die u in de vorige stap hebt gemaakt. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6-frontend maken

Maak een IPV6 frontend IP met [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). In het volgende voorbeeld wordt een IP-configuratie aan de frontend met de naam *dsLbFrontEnd_v6* en wordt het *dsPublicIP_v6-adres* gekoppeld:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6-back-endadresgroep configureren

Maak een IPv6 back-end adres pools met [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). In het volgende voorbeeld wordt back-end-adresgroep met de naam *dsLbBackEndPool_v6* vm's met IPv6 NIC-configuraties op te nemen:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Een statustest maken
Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) een statustest om de status van de virtuele machines te bewaken. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. 

Gebruik [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) om een load balancer-regel te maken. In het volgende voorbeeld worden regels voor load balancer met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt verkeer op *TCP-poort* *80* in evenwicht gebracht met de IP-configuraties van IPv4 en IPv6:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u sommige VM's implementeert, moet u ondersteunende netwerkbronnen maken - beschikbaarheidsset, netwerkbeveiligingsgroep, virtueel netwerk en virtuele NIC's. 
### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Als u de beschikbaarheid van uw app wilt verbeteren, plaatst u uw VM's in een beschikbaarheidsset.

Maak een beschikbaarheidsset met [de beschikbaarheidsset van AZ VM.](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *dsAVset geopperd:*

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep voor de regels die inkomende en uitgaande communicatie in uw VNET regelen.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [het AZ-netwerk nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Een netwerkbeveiligingsgroepregel maken voor inkomende en uitgaande verbindingen

Maak een netwerkbeveiligingsgroepregel om RDP-verbindingen toe te staan via poort 3389, internetverbinding via poort 80 en voor uitgaande verbindingen met [de NSG-regel voor az-netwerk.](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). In het volgende voorbeeld wordt een virtueel netwerk met de naam *dsVNET* met subnetten *dsSubNET_v4* en *dsSubNET_v6:*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC's maken

Maak virtuele NIC's voor elke VM met [az-netwerk nic maken](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). In het volgende voorbeeld wordt voor elke virtuele virtuele NIC voor elke virtuele virtuele afbeelding smaakt. Elke NIC heeft twee IP-configuraties (1 IPv4 config, 1 IPv6 config). U maakt de IPV6-configuratie met [az-netwerk nic ip-config maken](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).

```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de VM's met [az vm maken](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan. 

Maak virtuele machine *dsVM0* als volgt:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Maak virtuele machine *dsVM1* als volgt:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6 dual stack virtueel netwerk weergeven in Azure portal
U het virtuele IPv6-dual stack-netwerk in Azure-portal als volgt bekijken:
1. Voer in de zoekbalk van het portaal *dsVnet*in.
2. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit. Hiermee wordt de **overzichtspagina** van het virtuele dual stack-netwerk genaamd *dsVnet*gelanceerd. De dual stack virtuele netwerk toont de twee NIC's met zowel IPv4 en IPv6 configuraties gelegen in de dual stack subnet genaamd *dsSubnet*.

  ![IPv6 dual stack virtueel netwerk in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Basic Load Balancer gemaakt met een dubbele ip-configuratie (IPv4 en IPv6). U hebt ook een twee virtuele machines gemaakt die NIC's met dubbele IP-configuraties (IPV4 + IPv6) bevatten die zijn toegevoegd aan de back-endpool van de load balancer. Zie [Wat is IPv6 voor Azure Virtual Network voor](ipv6-overview.md) meer informatie over IPv6-ondersteuning in virtuele Azure-netwerken?
