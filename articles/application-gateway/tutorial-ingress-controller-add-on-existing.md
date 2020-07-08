---
title: Invoeg toepassing voor ingangs controller inschakelen voor een bestaand AKS-cluster met bestaande Azure-toepassing gateway
description: Gebruik deze zelf studie om de invoeg toepassing ingangs controller in te scha kelen voor uw bestaande AKS-cluster met een bestaande Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 6cbfac4794a685e5858e689c20d6603807edcedf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987991"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Zelf studie: invoeg toepassing Application Gateway ingangs controller inschakelen voor een bestaand AKS-cluster met een bestaande Application Gateway via Azure CLI (preview)

U kunt Azure CLI gebruiken voor het inschakelen van de invoeg toepassing [Application Gateway ingangs controller (AGIC)](ingress-controller-overview.md) , die momenteel beschikbaar is als preview, voor uw [Azure Kubernetes Services-cluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . In deze zelf studie leert u hoe u AGIC add-on kunt gebruiken om uw Kubernetes-toepassing beschikbaar te maken in een bestaand AKS-cluster via een bestaande Application Gateway geïmplementeerd in afzonderlijke virtuele netwerken. U begint met het maken van een AKS-cluster in één virtueel netwerk en een Application Gateway in een apart virtueel netwerk om bestaande resources te simuleren. Vervolgens schakelt u de AGIC-invoeg toepassing in, vergelijkt u de twee virtuele netwerken met elkaar en implementeert u een voorbeeld toepassing die via de Application Gateway wordt weer gegeven met behulp van de invoeg toepassing AGIC. Als u de invoeg toepassing AGIC inschakelt voor een bestaand Application Gateway en een bestaand AKS-cluster in hetzelfde virtuele netwerk, kunt u de onderstaande stappen voor peering overs Laan. De invoeg toepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [voorheen via helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) en biedt ook een volledig beheerde ervaring.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een resourcegroep maken 
> * Een nieuw AKS-cluster maken 
> * Een nieuwe Application Gateway maken 
> * Schakel de invoeg toepassing AGIC in het bestaande AKS-cluster in met behulp van de bestaande Application Gateway 
> * Het virtuele netwerk van Application Gateway koppelen aan het virtuele netwerk van het AKS-cluster
> * Een voorbeeld toepassing implementeren met AGIC voor binnenkomend verkeer op het AKS-cluster
> * Controleer of de toepassing bereikbaar is via Application Gateway

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie de Azure CLI (versie 2.0.4 of hoger) uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Registreer de functie vlag *AKS-IngressApplicationGatewayAddon* met behulp van de opdracht [AZ feature REGI ster](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , zoals weer gegeven in het volgende voor beeld. u hoeft dit slechts één keer per abonnement te doen terwijl de invoeg toepassing nog in de preview-versie is:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Het kan een paar minuten duren voordat de status geregistreerd wordt weer gegeven. U kunt de registratie status controleren met de opdracht [AZ Feature List](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider micro soft. container service met de opdracht [AZ provider REGI ster](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Zorg ervoor dat u de uitbrei ding AKS-Preview voor deze zelf studie installeert/bijwerkt. de volgende Azure CLI-opdrachten gebruiken
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *canadacentral* (regio). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Een nieuw AKS-cluster implementeren

U gaat nu een nieuw AKS-cluster implementeren om te simuleren dat een bestaand AKS-cluster is waarvoor u de AGIC-invoeg toepassing wilt inschakelen voor.  

In het volgende voor beeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure cni](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) en [beheerde identiteiten](https://docs.microsoft.com/azure/aks/use-managed-identity) in de resource groep die u hebt gemaakt, *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Als u aanvullende para meters voor de opdracht wilt configureren `az aks create` , gaat u naar [hier](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Een nieuwe Application Gateway implementeren 

U implementeert nu een nieuwe Application Gateway om te simuleren dat een bestaande Application Gateway die u wilt gebruiken om verkeer te verdelen naar uw AKS-cluster, *myCluster*. De naam van de Application Gateway wordt *myApplicationGateway*, maar u moet eerst een open bare IP-bron maken, met de naam *myPublicIp*en een nieuw virtueel netwerk genaamd *myVnet* met adres ruimte 11.0.0.0/8, en een subnet met adres ruimte 11.1.0.0/16 met de naam *mySubnet*, en uw Application Gateway in *mySubnet* implementeren met behulp van *myPublicIp*. 

Wanneer u een AKS-cluster gebruikt en Application Gateway in afzonderlijke virtuele netwerken, mogen de adres ruimten van de twee virtuele netwerken elkaar niet overlappen. De standaard adres ruimte die een AKS-cluster implementeert in is 10.0.0.0/8. Daarom stellen we het Application Gateway virtuele netwerk adres voorvoegsel in op 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> De invoeg toepassing Application Gateway ingangs controller (AGIC) ondersteunt **alleen** Application Gateway v2 Sku's (Standard en WAF) en **niet** de Application Gateway v1-sku's. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>De invoeg toepassing AGIC in een bestaand AKS-cluster inschakelen met bestaande Application Gateway 

Nu schakelt u de invoeg toepassing AGIC in het AKS-cluster in dat u hebt gemaakt, *myCluster*en geeft u de AGIC-invoeg toepassing op om de bestaande Application Gateway te gebruiken die u hebt gemaakt, *myApplicationGateway*. Zorg ervoor dat u de extensie AKS-preview aan het begin van deze zelf studie hebt toegevoegd/bijgewerkt. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Peer de twee virtuele netwerken samen

Omdat we het AKS-cluster in een eigen virtueel netwerk hebben geïmplementeerd en de Application Gateway in een ander virtueel netwerk, moet u de twee virtuele netwerken met elkaar verbinden, zodat het verkeer van de Application Gateway naar het Peul in het cluster kan stromen. Voor de peering van de twee virtuele netwerken moet de Azure CLI-opdracht twee afzonderlijke keer worden uitgevoerd om ervoor te zorgen dat de verbinding bi-directioneel is. Met de eerste opdracht maakt u een peering-verbinding van het Application Gateway virtuele netwerk naar het virtuele netwerk AKS. met de tweede opdracht wordt een peering-verbinding in de andere richting gemaakt. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Een voorbeeld toepassing implementeren met behulp van AGIC 

U gaat nu een voorbeeld toepassing implementeren naar het AKS-cluster dat u hebt gemaakt en die de AGIC-add-on gebruikt voor inkomend verkeer en de Application Gateway verbinden met het AKS-cluster. Eerst voert u de referenties voor het AKS-cluster uit dat u hebt geïmplementeerd door de opdracht uit te voeren `az aks get-credentials` . 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Wanneer u de referenties hebt van het cluster dat u hebt gemaakt, voert u de volgende opdracht uit om een voorbeeld toepassing in te stellen die gebruikmaakt van AGIC voor binnenkomend verkeer naar het cluster. Met AGIC worden de Application Gateway die u eerder hebt ingesteld met de bijbehorende routerings regels, bijgewerkt naar de nieuwe voorbeeld toepassing die u hebt geïmplementeerd.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Controleren of de toepassing bereikbaar is

Nu de Application Gateway zo is ingesteld dat verkeer naar het AKS-cluster wordt verzonden, gaan we controleren of uw toepassing bereikbaar is. U krijgt eerst het IP-adres van de ingang. 

```azurecli-interactive
kubectl get ingress
```

Controleer of de voorbeeld toepassing die u hebt gemaakt, actief is door het IP-adres van de Application Gateway te bezoeken dat u hebt gekregen om de bovenstaande opdracht uit te voeren of neem contact op met `curl` . Het kan Application Gateway een minuut duren voordat de update is opgehaald, dus als de Application Gateway nog steeds in de status ' update ' op de portal staat, laat u deze volt ooien voordat u het IP-adres probeert te bereiken. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het uitschakelen van de AGIC-invoeg toepassing](./ingress-controller-disable-addon.md)
* [Meer informatie over welke aantekeningen worden ondersteund met AGIC](./ingress-controller-annotations.md)
* [Problemen met AGIC oplossen](./ingress-controller-troubleshoot.md)

