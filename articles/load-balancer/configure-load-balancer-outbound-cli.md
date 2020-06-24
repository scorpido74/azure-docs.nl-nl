---
title: Taak verdeling en regels voor uitgaande verbindingen configureren met behulp van Azure CLI
titleSuffix: Azure Load Balancer
description: In dit artikel wordt beschreven hoe u taak verdeling en uitgaande regels configureert in een Standard Load Balancer met behulp van de Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 2b326c6c58b4685b6e73d0f9a641a2f90807d705
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84803751"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Taakverdeling en uitgaande regels in Standard Load Balancer configureren met Azure CLI

In deze Quick start ziet u hoe u uitgaande regels configureert in Standard Load Balancer met behulp van Azure CLI.  

Wanneer u klaar bent, bevat de Load Balancer resource twee front-end-en-regels die eraan zijn gekoppeld: één voor inkomend en een andere voor uitgaand verkeer.  Elke front-end bevat een verwijzing naar een openbaar IP-adres. dit scenario maakt gebruik van een ander openbaar IP-adres voor inkomend verkeer versus uitgaand verkeer.   De taakverdelings regel biedt alleen binnenkomende taak verdeling en de uitgaande regel bepaalt de uitgaande NAT voor de virtuele machine.  In deze Quick Start worden twee afzonderlijke back-endservers gebruikt, één voor inkomend en één voor uitgaand, om de mogelijkheid te illustreren en flexibiliteit te bieden voor dit scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam *myresourcegroupoutbound* gemaakt op de locatie *eastus2* :

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtueel netwerk maken
Maak een virtueel netwerk met de naam *myvnetoutbound* met een subnet met de naam *mysubnetoutbound* in de *myresourcegroupoutbound* met [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Inkomend openbaar IP-adres maken 

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Een Standard Load Balancer biedt alleen ondersteuning voor standaard, openbare IP-adressen. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een standaard openbaar IP-adres te maken met de naam *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Uitgaand openbaar IP-adres maken 

Maak een standaard-IP-adres voor de uitgaande configuratie van de front-end van de Load Balancer met [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
  - Een frontend-IP die het binnenkomende netwerk verkeer op de load balancer ontvangt.
  - Een back-end-groep waarbij het frontend-IP-netwerk verkeer met gelijke taak verdeling verzendt.
  - Een back-end-groep voor uitgaande verbindingen. 
  - Een status test die de status van de back-end-VM-exemplaren bepaalt.
  - Een load balancer binnenkomende regel waarmee wordt gedefinieerd hoe verkeer naar de virtuele machines wordt gedistribueerd.
  - Een load balancer uitgaande regel waarmee wordt gedefinieerd hoe verkeer van de virtuele machines wordt gedistribueerd.

### <a name="create-load-balancer"></a>Load Balancer maken

Maak een Load Balancer met het inkomende IP-adres met [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) named *lb* dat een inkomende frontend-IP-configuratie bevat en een back- *bepoolinbound* die is gekoppeld aan het open bare IP-adres *mypublicipinbound* dat u in de vorige stap hebt gemaakt.

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

### <a name="create-outbound-pool"></a>Uitgaande pool maken

Maak een extra back-end-adres groep om de uitgaande connectiviteit voor een groep Vm's te definiëren met [AZ Network lb address-pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) met de naam *bepooloutbound*.  Het maken van een afzonderlijke uitgaande groep biedt maximale flexibiliteit, maar u kunt deze stap overs Laan en ook alleen de inkomende *bepoolinbound* gebruiken.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Uitgaand frontend-IP maken
Maak de uitgaande frontend-IP-configuratie voor de Load Balancer met [AZ Network lb frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) dat en de uitgaande frontend-IP-configuratie met de naam *myfrontendoutbound* die is gekoppeld aan het open bare IP-adres *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Status test maken

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

### <a name="create-load-balancing-rule"></a>Taak verdelings regel maken

Een load balancer regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-groep om het verkeer te ontvangen, samen met de vereiste bron-en doel poort. Maak een load balancer regel *myinboundlbrule* met [AZ Network lb regel Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) voor het Luis teren naar poort 80 in de front- *myfrontendinbound* en verzend netwerk verkeer met gelijke taak verdeling naar de back-end-adres groep *bepool* ook poort 80. 

>[!NOTE]
>Met deze taakverdelings regel worden de Automatic outbound (S) NAT uitgeschakeld als gevolg van deze regel met de para meter--Disable-outbound-SNAT. Uitgaande NAT wordt alleen door de uitgaande regel verschaft.

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

Een uitgaande regel definieert het open bare frontend-IP-adres dat wordt vertegenwoordigd door de front- *myfrontendoutbound*, die wordt gebruikt voor al het uitgaande NAT-verkeer en de back-endadresgroep waarop deze regel van toepassing is.  Maak een uitgaande regel *myoutboundrule* voor uitgaande netwerk vertaling van alle virtuele machines (NIC IP-configuraties) in *bepool* back-end-pool.  Met de onderstaande opdracht wordt ook de uitgaande time-out voor inactiviteit van 4 tot 15 minuten gewijzigd en worden er 10000 SNAT-poorten toegewezen in plaats van 1024.  Lees de [regels voor uitgaande verbindingen](https://aka.ms/lboutboundrules) voor meer informatie.

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

Als u geen afzonderlijke uitgaande pool wilt gebruiken, kunt u in plaats daarvan het argument adres groep wijzigen in de voor gaande opdracht om *bepoolinbound* op te geven.  We raden u aan om afzonderlijke groepen te gebruiken voor flexibiliteit en lees baarheid van de resulterende configuratie.

U kunt nu door gaan met het toevoegen van uw VM aan de back- *bepoolinbound* __en__ *bepooloutbound* door de IP-configuratie van de betreffende NIC-bronnen bij te werken met [AZ Network NIC IP-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u Standard Load Balancer gemaakt en hebt u de regels voor binnenkomend load balancer-verkeer, geconfigureerd en de status test voor de virtuele machines in de back-endadresgroep geconfigureerd. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
