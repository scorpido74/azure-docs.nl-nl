---
title: Invoeg toepassing voor de ingangs controller inschakelen voor een nieuw AKS-cluster met een nieuwe Azure-toepassing gateway
description: Gebruik deze zelf studie om de invoeg toepassing ingangs controller voor uw nieuwe AKS-cluster in te scha kelen met een nieuwe Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670934"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Zelf studie: invoeg toepassing Application Gateway ingangs controller inschakelen voor een nieuw AKS-cluster met een nieuwe Application Gateway via Azure CLI (preview)

U kunt Azure CLI gebruiken voor het inschakelen van de invoeg toepassing [Application Gateway ingangs controller (AGIC)](ingress-controller-overview.md) , die momenteel beschikbaar is als preview, voor uw [Azure Kubernetes Services-cluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . In deze zelf studie maakt u een AKS-cluster waarvoor de AGIC-invoeg toepassing is ingeschakeld, waarmee automatisch een Application Gateway wordt gemaakt die moet worden gebruikt. Vervolgens implementeert u een voorbeeld toepassing, die gebruikmaakt van de add-on van de AGIC om de toepassing via de Application Gateway beschikbaar te maken. De invoeg toepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [voorheen via helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) en biedt ook een volledig beheerde ervaring.    

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een resourcegroep maken 
> * Een nieuw AKS-cluster maken waarbij AGIC-invoeg toepassing is ingeschakeld 
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

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Een nieuw AKS-cluster implementeren met AGIC-invoeg toepassing ingeschakeld

U implementeert nu een nieuw AKS-cluster waarvoor de AGIC-invoeg toepassing is ingeschakeld. Wanneer u een nieuw AKS-cluster implementeert waarop de AGIC-invoeg toepassing is ingeschakeld en geen bestaande Application Gateway biedt, worden er automatisch een nieuwe Application Gateway gemaakt en ingesteld voor het verzenden van verkeer naar het AKS-cluster.  

> [!NOTE]
> De invoeg toepassing Application Gateway ingangs controller (AGIC) ondersteunt **alleen** Application Gateway v2 Sku's (Standard en WAF) en **niet** de Application Gateway v1-sku's. Wanneer u een nieuwe Application Gateway implementeert via de AGIC-invoeg toepassing, kunt u alleen een Application Gateway Standard_v2 SKU implementeren. Als u de AGIC-invoeg toepassing voor een Application Gateway WAF_v2 SKU wilt inschakelen, schakelt u WAF in op de Application Gateway via de portal of maakt u de WAF_v2 Application Gateway eerst en volgt u de instructies voor het [inschakelen van AGIC-invoeg toepassing met een bestaand AKS-cluster en bestaande Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

In het volgende voor beeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure cni](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) en [beheerde identiteiten](https://docs.microsoft.com/azure/aks/use-managed-identity) , waarbij de AGIC-invoeg toepassing is ingeschakeld in de resource groep die u hebt gemaakt, *myResourceGroup*. Omdat het implementeren van een nieuw AKS-cluster met de AGIC-invoeg toepassing is ingeschakeld zonder een bestaande Application Gateway op te geven, wordt het automatisch maken van een Standard_v2 SKU Application Gateway. u kunt ook de naam en het subnetadres van de Application Gateway opgeven. De naam van de Application Gateway wordt *myApplicationGateway* en de adres ruimte die we gebruiken, is 10.2.0.0/16. Zorg ervoor dat u de extensie AKS-preview aan het begin van deze zelf studie hebt toegevoegd/bijgewerkt. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Als u aanvullende para meters voor de opdracht wilt configureren `az aks create` , gaat u naar [hier](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Het AKS-cluster dat u hebt gemaakt, wordt weer gegeven in de resource groep die u hebt gemaakt, *myResourceGroup*. De automatisch gemaakte Application Gateway zullen echter wel Live in de resource groep node, waarbij de agent groepen zijn. De resource groep van het knoop punt wordt standaard aangeduid als *MC_resource-Group-name_cluster-name_location*, maar kan worden gewijzigd. 

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

