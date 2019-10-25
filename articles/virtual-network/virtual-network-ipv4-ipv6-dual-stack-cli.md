---
title: Een IPv6-toepassing met dubbele stack implementeren met behulp van basis Load Balancer in azure-CLI
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u een IPv6 Dual stack-toepassing implementeert in een virtueel Azure-netwerk met behulp van Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d4ca26606eb8be5b9092f40b70b57b9d5d85385c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804013"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli-preview"></a>Een IPv6-toepassing met dubbele stack implementeren met behulp van Basic Load Balancer-CLI (preview)

In dit artikel wordt beschreven hoe u een dual stack (IPv4 + IPv6)-toepassing implementeert met een basis Load Balancer met behulp van Azure CLI met een dual stack virtueel netwerk met een dual stack-subnet, een basis Load Balancer met dubbele (IPv4 + IPv6) front-end configuraties, Vm's met Nic's met een dubbele IP-configuratie, twee regels voor netwerk beveiligings groepen en dubbele open bare Ip's.

Als u een toepassing met dubbele stack (IPV4 + IPv6) wilt implementeren met behulp van Standard Load Balancer, gaat u naar [een IPv6-toepassing met dubbele stack implementeren met Standard Load Balancer met behulp van Azure cli](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

> [!Important]
> De IPv6-dubbele stack voor Azure Virtual Network is momenteel beschikbaar als open bare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze Snelstartgids gebruikmaken van Azure CLI versie 2.0.49 of hoger. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="prerequisites"></a>Vereisten
Als u de IPv6-functie voor virtuele netwerken van Azure wilt gebruiken, moet u uw abonnement als volgt configureren met behulp van Azure CLI:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. U kunt de registratie status controleren door de volgende Azure CLI-opdracht uit te voeren:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Nadat de registratie is voltooid, voert u de volgende opdracht uit:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een virtueel netwerk met twee stacks kunt maken, moet u een resource groep maken met [AZ Group Create](/cli/azure/group). In het volgende voor beeld wordt een resource groep met de naam *DsResourceGroup01* gemaakt op de locatie *eastus* :

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Open bare IPv4-en IPv6-adressen maken voor load balancer
Als u toegang wilt krijgen tot uw IPv4-en IPv6-eind punten op internet, hebt u open bare IPv4-en IPv6-adressen voor de load balancer nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voor beeld wordt het IPv4-en IPv6-open bare IP-adres gemaakt met de naam *dsPublicIP_v4* en *dsPublicIP_v6* in de resource groep *DsResourceGroup01* :

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

## <a name="create-public-ip-addresses-for-vms"></a>Open bare IP-adressen voor Vm's maken

Als u op afstand toegang wilt krijgen tot uw virtuele machines op internet, hebt u open bare IPv4-IP-adressen voor de virtuele machines nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip).

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

In deze sectie configureert u dual front-end-IP (IPv4 en IPv6) en de back-end-adres groep voor de load balancer en maakt u vervolgens een basis Load Balancer.

### <a name="create-load-balancer"></a>Load balancer maken

Maak de basis Load Balancer met [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met de naam **dsLB** die een front-end-groep met de naam **dsLbFrontEnd_v4**bevat, een back-end-pool met de naam **dsLbBackEndPool_v4** die is gekoppeld aan het open bare IP-adres **van IPv4 dsPublicIP_v4** die u in de vorige stap hebt gemaakt. 

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

### <a name="create-ipv6-frontend"></a>IPv6-front-end maken

Maak een IPV6-frontend-IP met [AZ Network lb frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). In het volgende voor beeld wordt een front-end-IP-configuratie met de naam *dsLbFrontEnd_v6* gemaakt en wordt het *dsPublicIP_v6* -adres gekoppeld:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Adres groep voor IPv6-back-end configureren

Maak een IPv6-back-end-adres groep met [AZ Network lb address-pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). In het volgende voor beeld wordt een back-end-adres groep met de naam *dsLbBackEndPool_v6* gemaakt voor het toevoegen van Vm's met IPv6-NIC-configuraties:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. 

Gebruik [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) om een load balancer-regel te maken. In het volgende voor beeld worden load balancer-regels gemaakt met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt verkeer op *TCP* -poort *80* gebalanceerd naar de IPv4-en IPv6-frontend IP-configuraties:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u enkele Vm's implementeert, moet u ondersteunende netwerk bronnen maken-beschikbaarheidsset, netwerk beveiligings groep, virtueel netwerk en virtuele Nic's. 
### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Als u de beschik baarheid van uw app wilt verbeteren, plaatst u uw virtuele machines in een beschikbaarheidsset.

Maak een beschikbaarheidsset met behulp van [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). In het volgende voor beeld wordt een beschikbaarheidsset gemaakt met de naam *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep voor de regels die van toepassing zijn op binnenkomende en uitgaande communicatie in uw VNET.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerk beveiligings groep maken met [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Een regel voor de netwerk beveiligings groep maken voor binnenkomende en uitgaande verbindingen

Maak een regel voor de netwerk beveiligings groep om RDP-verbindingen toe te staan via poort 3389, Internet verbinding via poort 80 en voor uitgaande verbindingen met [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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


### <a name="create-a-virtual-network"></a>Maak een virtueel netwerk

Maak een virtueel netwerk met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *dsVNET* met subnetten *dsSubNET_v4* en *dsSubNET_v6*:

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

Maak virtuele Nic's voor elke VM met [AZ Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). In het volgende voor beeld wordt een virtuele NIC gemaakt voor elke VM. Elke NIC heeft twee IP-configuraties (1 IPv4-configuratie, 1 IPv6-configuratie). U maakt de IPV6-configuratie met [AZ Network NIC IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Maak de virtuele machines met [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan. 

Maak de *dsVM0* van de virtuele machine als volgt:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```
Maak de *dsVM1* van de virtuele machine als volgt:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack in Azure Portal weer geven
U kunt het virtuele IPv6-netwerk met dubbele stack als volgt weer geven in Azure Portal:
1. Voer in de zoek balk van de portal *dsVnet*in.
2. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit. Hiermee opent u de **overzichts** pagina van het virtuele netwerk met dubbele stack met de naam *dsVnet*. Het virtuele netwerk met dubbele stack toont de twee Nic's met zowel IPv4-als IPv6-configuraties die zich bevinden in het dubbele stack-subnet met de naam *dsSubnet*.

  ![Virtueel IPv6-netwerk met dubbele stack in azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Het virtuele netwerk van IPv6 voor Azure is beschikbaar in het Azure Portal alleen-lezen voor deze preview-versie.


## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een basis Load Balancer met een dubbele frontend-IP-configuratie (IPv4 en IPv6) gemaakt. U hebt ook een twee virtuele machines gemaakt die Nic's bevatten met dubbele IP-configuraties (IPV4 + IPv6) die zijn toegevoegd aan de back-end-pool van de load balancer. Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md) voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.
