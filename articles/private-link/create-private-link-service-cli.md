---
title: Een Azure Private Link-service maken met Azure CLI
description: Meer informatie over het maken van een Azure Private Link-service met Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350244"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Een Private Link-service maken met Azure CLI
In dit artikel ziet u hoe u een Private Link-service maakt in Azure met Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u besluit Azure CLI lokaal te installeren en te gebruiken, moet u in deze quickstart de nieuwste Azure CLI-versie gebruiken. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.
## <a name="create-a-private-link-service"></a>Een Private Link-service maken
### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een virtueel netwerk kunt maken, moet u een resourcegroep maken die het virtuele netwerk host. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *Westcentralus* ge:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In dit voorbeeld wordt een standaard virtueel netwerk met de naam *myVirtualNetwork* gemaakt met één subnet met de naam *mySubnet:*

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Een subnet maken
Maak een subnet voor het virtuele netwerk met [az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In dit voorbeeld wordt een subnet met de naam *mySubnet* in het virtuele *netwerk van myVirtualNetwork* ge:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Een interne load balancer maken 
Maak een interne load balancer met [az network lb create](/cli/azure/network/lb#az-network-lb-create). In dit voorbeeld wordt een interne load balancer met de naam *myILB* in resourcegroep met de naam *myResourceGroup .* 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Een load balancer-statustest maken

Een statustest controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen ontvangen. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-IP-pool om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak met [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) de regel *myHTTPRule* voor het luisteren naar poort 80 in de front-endgroep *myFrontEnd* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Back-endservers maken

In dit voorbeeld hebben we geen betrekking op het maken van virtuele machines. U de stappen volgen in [Een interne load balancer maken om vm's met een balans te laden met Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) om twee virtuele machines te maken die als backendservers voor de load balancer kunnen worden gebruikt. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Netwerkbeleid voor private koppeling-serviceop subnet uitschakelen 
Private Link-service vereist een IP van elk subnet van uw keuze binnen een virtueel netwerk. Momenteel ondersteunen we geen netwerkbeleid voor deze IP's.  Daarom moeten we het netwerkbeleid op het subnet uitschakelen. Werk het subnet bij om het netwerkbeleid van Private Link-service uit te schakelen met [de vnet-subnetupdate van het AZ-netwerk.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Een Private Link-service maken  
 
Maak een Private Link-service met behulp van standaard load balancer frontend IP-configuratie met [az-netwerk private-link-service maken](/cli/azure/network/private-link-service#az-network-private-link-service-create). In dit voorbeeld wordt een Private Link-service met de naam *myPLS* gemaakt met de naam MyLoadBalancer met de naam *myLoadBalancer* in de resourcegroep *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Neem, eenmaal gemaakt, nota van de Private Link Service ID. Dat heb je later nodig voor het aanvragen van verbinding met deze service.  
 
In dit stadium wordt uw Private Link-service met succes gemaakt en is u klaar om het verkeer te ontvangen. Houd er rekening mee dat het bovenstaande voorbeeld alleen is om aan te tonen dat u een Private Link-service maakt met Azure CLI.  We hebben de backendpools van de load balancer of een toepassing op de backendpools niet geconfigureerd om naar het verkeer te luisteren. Als u end-to-end verkeersstromen wilt zien, wordt u ten zeerste aangeraden om uw toepassing achter uw Standaard Load Balancer te configureren.  
 
Vervolgens laten we zien hoe we deze service kunnen toewijzen aan een privéeindpunt in verschillende virtuele netwerken met Azure CLI. Nogmaals, het voorbeeld is beperkt tot het maken van het privéeindpunt en het maken van verbinding met de Privékoppelingsservice die hierboven is gemaakt met Azure CLI. Bovendien u virtuele machines in het virtuele netwerk maken om verkeer naar het privéeindpunt te verzenden/ontvangen.        
 
## <a name="private-endpoints"></a>Privéeindpunten

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken 
Maak een virtueel netwerk met [az-netwerk vnet maken](/cli/azure/network/vnet#az-network-vnet-create). In dit voorbeeld wordt een virtueel netwerk met de naam *myPEVNet* in resourcegroep met de naam *myResourcegroup:* 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Het subnet maken 
Maak een subnet in virtueel netwerk met [az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In dit voorbeeld wordt een subnet met de naam *mySubnet* in virtueel netwerk met de naam *myPEVnet* in resourcegroep *myResourcegroup:* 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Privé-eindpuntnetwerkbeleid op subnet uitschakelen 
Privéeindpunt kan worden gemaakt in elk subnet van uw keuze binnen een virtueel netwerk. Momenteel ondersteunen we geen netwerkbeleid voor privéeindpunten.  Daarom moeten we het netwerkbeleid op het subnet uitschakelen. Werk het subnet bij om het privé-eindpuntnetwerkbeleid uit te schakelen met [de vnet-subnetupdate van het AZ-netwerk.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Privéeindpunt maken en verbinding maken met de privékoppelingsservice 
Maak een privéeindpunt voor het consumeren van de Privékoppelingsservice die hierboven in uw virtuele netwerk is gemaakt:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
U de *private-connection-resource-id* krijgen met `az network private-link-service show` op Private Link-service. De ID ziet eruit als volgt:   
/subscriptions/subID/resourceGroepen/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicenaam** 
 
## <a name="show-private-link-service-connections"></a>Privékoppelingsserviceverbindingen weergeven 
 
Bekijk verbindingsaanvragen op uw Private [Link-service via de private-link-service van het AZ-netwerk.](/cli/azure/network/private-link-service#az-network-private-link-service-show)    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Private Link-service](private-link-service-overview.md)
 
