---
title: Load balancing en uitgaande regels configureren met Azure CLI
titleSuffix: Azure Load Balancer
description: In dit artikel ziet u hoe u load balancing en outbound rules configureert in een Standaard Load Balancer met behulp van de Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225476"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Taakverdeling en uitgaande regels in Standard Load Balancer configureren met Azure CLI

In deze snelstart ziet u hoe u uitgaande regels configureert in Standard Load Balancer met Azure CLI.  

Wanneer u klaar bent, bevat de load balancer-bron twee frontends en regels die eraan zijn gekoppeld: een voor binnenkomend en een andere voor uitgaande.  Elke frontend heeft een verwijzing naar een openbaar IP-adres en dit scenario gebruikt een ander openbaar IP-adres voor binnenkomend versus uitgaand.   De regel voor taakverdeling biedt alleen inkomende taakverdeling en de uitgaande regel regelt de uitgaande NAT die voor de VM is voorzien.  Deze quickstart maakt gebruik van twee afzonderlijke backendpools, een voor binnenkomend en één voor uitgaande, om de mogelijkheden te illustreren en flexibiliteit voor dit scenario mogelijk te maken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myresourcegroupoutbound* op de locatie *eastus2* gea...

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtueel netwerk maken
Maak een virtueel netwerk met de naam *myvnetoutbound* met een subnet genaamd *mysubnetoutbound* in de *myresourcegroupoutbound* met behulp van [az-netwerk vnet maken](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Binnenkomend openbaar IP-adres maken 

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Een Standard Load Balancer biedt alleen ondersteuning voor standaard, openbare IP-adressen. Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een standaard IP-adres met de naam *mypublicipinbound* te maken in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Uitgaand openbaar IP-adres maken 

Maak een standaard IP-adres voor de uitgaande configuratie van Load Balancer met behulp van [het public-ip-netwerk van az-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
  - Een frontend IP dat het binnenkomende netwerkverkeer op de load balancer ontvangt.
  - Een backend pool waar het frontend IP het load balanced netwerkverkeer stuurt.
  - Een backend pool voor uitgaande connectiviteit. 
  - Een statussonde die de status van de backend VM-exemplaren bepaalt.
  - Een inkomende regel voor load balancer die bepaalt hoe verkeer wordt gedistribueerd naar de VM's.
  - Een outbound-regel voor loadbalancer die bepaalt hoe verkeer wordt gedistribueerd vanuit de VM's.

### <a name="create-load-balancer"></a>Load Balancer maken

Maak een Load Balancer met het binnenkomende IP-adres met behulp van [az-netwerk lb maken](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met de naam *lb* die een inkomende frontend IP-configuratie en een backend pool *bepoolinbound* bevat die is gekoppeld aan het openbare IP-adres *mypublicipinbound* dat u in de vorige stap hebt gemaakt.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Uitgaande groep maken

Maak een extra backend-adresgroep om uitgaande connectiviteit te definiëren voor een pool van VM's met [het AZ-netwerk lb-adresgroep maken](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met de naam *bepooloutbound*.  Het maken van een aparte uitgaande pool biedt maximale flexibiliteit, maar u deze stap weglaten en alleen de binnenkomende *bepoolinbound* gebruiken.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Outbound frontend IP maken
Maak de uitgaande frontend IP-configuratie voor de Load Balancer met [az network lb frontend-ip-create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met een uitgaande frontend IP-configuratie met de naam *myfrontendoutbound* die is gekoppeld aan het openbare IP-adres *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Statussonde maken

Een statuscontrole controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen verzenden. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Regel voor taakverdeling maken

Een regel voor load balancer definieert de IP-configuratie aan de frontend voor het binnenkomende verkeer en de backendpool om het verkeer te ontvangen, samen met de vereiste bron- en doelpoort. Maak een load balancer regel *myinboundlbrule* met [az network lb regel maken](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) voor het luisteren naar poort 80 in de frontend pool *myfrontendinbound* en het verzenden van load-balanced netwerkverkeer naar de backend adres pool *bepool* ook met behulp van poort 80. 

>[!NOTE]
>Met deze regel voor taakverdeling schakelt u automatische uitgaande (S)NAT uit als gevolg van deze regel met de parameter --disable-outbound-snat. Uitgaande NAT wordt alleen geleverd door de uitgaande regel.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Uitgaande regel maken

Een uitgaande regel definieert het frontend public IP, vertegenwoordigd door de frontend *myfrontendoutbound*, die wordt gebruikt voor alle uitgaande NAT-verkeer, evenals de backend pool waarop deze regel van toepassing is.  Maak een uitgaande *regel myoutboundrule* voor uitgaande netwerkvertaling van alle virtuele machines (NIC IP-configuraties) in de backendpool van *Bepool.*  De onderstaande opdracht wijzigt ook de uitgaande idle time-out van 4 naar 15 minuten en wijst 10000 SNAT-poorten toe in plaats van 1024.  Controleer [uitgaande regels](https://aka.ms/lboutboundrules) voor meer details.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Als u geen afzonderlijke uitgaande groep wilt gebruiken, u het argument adresgroep in de vorige opdracht wijzigen om in plaats daarvan *bepoolinbound* op te geven.  We raden aan om aparte pools te gebruiken voor flexibiliteit en leesbaarheid van de resulterende configuratie.

Op dit punt u doorgaan met het toevoegen van uw VM's aan de backend pool *bepoolinbound* __en__ *bepooloutbound* door de IP-configuratie van de respectieve NIC-bronnen bij te werken met behulp van [az-netwerk nic ip-config-adresgroep toevoegen.](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u Standard Load Balancer gemaakt, die zowel inkomende belastingsbalansverkeersregels, geconfigureerde als statussonde voor de VM's in de backendpool configureert. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
