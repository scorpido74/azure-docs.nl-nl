---
title: De invoeg toepassing voor de ingangs controller inschakelen voor een nieuw AKS-cluster met een nieuw Azure-toepassing gateway-exemplaar
description: Meer informatie over het gebruik van de Azure CLI om de invoeg toepassing ingangs controller voor uw nieuwe AKS-cluster in te scha kelen met een nieuw Application Gateway-exemplaar.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 439313f0f42adf0513ce490ab6569171cce7934b
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037899"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Zelf studie: de invoeg toepassing ingangs controller inschakelen (preview) voor een nieuw AKS-cluster met een nieuw Application Gateway-exemplaar

U kunt de Azure CLI gebruiken om de invoeg toepassing [Application Gateway ingangs controller (AGIC)](ingress-controller-overview.md) in te scha kelen voor een [AKS-cluster (Azure Kubernetes Services)](https://azure.microsoft.com/services/kubernetes-service/) . De invoeg toepassing is momenteel beschikbaar als preview-versie.

In deze zelf studie maakt u een AKS-cluster waarvoor de AGIC-invoeg toepassing is ingeschakeld. Als u het cluster maakt, wordt automatisch een Azure-toepassing gateway-exemplaar gemaakt dat moet worden gebruikt. Vervolgens implementeert u een voor beeld van een toepassing die gebruikmaakt van de add-in om de toepassing via Application Gateway beschikbaar te maken. 

De invoeg toepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [voorheen via helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Het biedt ook een volledig beheerde ervaring.    

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een resourcegroep. 
> * Maak een nieuw AKS-cluster waarvoor de AGIC-invoeg toepassing is ingeschakeld. 
> * Implementeer een voorbeeld toepassing met behulp van AGIC voor binnenkomend verkeer op het AKS-cluster.
> * Controleer of de toepassing bereikbaar is via Application Gateway.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie Azure CLI versie 2.0.4 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Registreer de functie vlag *AKS-IngressApplicationGatewayAddon* met behulp van de opdracht [AZ feature REGI ster](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , zoals weer gegeven in het volgende voor beeld. U hoeft dit slechts één keer per abonnement te doen terwijl de invoeg toepassing nog in de preview-versie is.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Het kan enkele minuten duren voordat de status wordt weer gegeven `Registered` . U kunt de registratie status controleren met behulp van de opdracht [AZ Feature List](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resource provider micro soft. container service met de opdracht [AZ provider REGI ster](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

De uitbrei ding voor AKS-Preview installeren of bijwerken voor deze zelf studie. Gebruik de volgende Azure CLI-opdrachten:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *canadacentral* (regio): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Een AKS-cluster implementeren waarbij de invoeg toepassing is ingeschakeld

U implementeert nu een nieuw AKS-cluster waarvoor de AGIC-invoeg toepassing is ingeschakeld. Als u geen bestaand Application Gateway-exemplaar voor gebruik in dit proces opgeeft, wordt er automatisch een nieuw Application Gateway-exemplaar gemaakt en ingesteld zodat het verkeer naar het AKS-cluster kan worden verzonden.  

> [!NOTE]
> De invoeg toepassing Application Gateway ingangs controller ondersteunt *alleen* Application Gateway v2 Sku's (Standard en WAF) en *niet* de Application Gateway v1-sku's. Wanneer u een nieuw Application Gateway-exemplaar implementeert via de AGIC-invoeg toepassing, kunt u alleen een Application Gateway Standard_v2 SKU implementeren. Als u de invoeg toepassing voor een Application Gateway WAF_v2 SKU wilt inschakelen, gebruikt u een van de volgende methoden:
>
> - Schakel WAF in op Application Gateway via de portal. 
> - Maak eerst de WAF_v2 Application Gateway instantie en volg vervolgens de instructies voor [het inschakelen van de AGIC-invoeg toepassing met een bestaand AKS-cluster en bestaande Application Gateway-instantie](tutorial-ingress-controller-add-on-existing.md). 

In het volgende voor beeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure cni](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) en [beheerde identiteiten](https://docs.microsoft.com/azure/aks/use-managed-identity). De AGIC-invoeg toepassing wordt ingeschakeld in de resource groep die u hebt gemaakt, *myResourceGroup*. 

Als u een nieuw AKS-cluster implementeert terwijl de AGIC-invoeg toepassing is ingeschakeld zonder dat u een bestaand Application Gateway exemplaar opgeeft, wordt er automatisch een Standard_v2 SKU Application Gateway-exemplaar gemaakt. U geeft dus ook de naam en het adres van het subnet op van het Application Gateway exemplaar. De naam van de Application Gateway-instantie wordt *myApplicationGateway*en de gebruikte is 10.2.0.0/16. Zorg ervoor dat u de uitbrei ding AKS-preview aan het begin van deze zelf studie hebt toegevoegd of bijgewerkt. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Zie deze verwijzingen voor meer informatie over het configureren van aanvullende para meters voor de `az aks create` opdracht. [these references](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create) 

> [!NOTE]
> Het AKS-cluster dat u hebt gemaakt, wordt weer gegeven in de resource groep die u hebt gemaakt, *myResourceGroup*. Het automatisch gemaakte Application Gateway-exemplaar bevindt zich echter in de knooppunt resource groep, waarbij de agent groepen zijn. De resource groep van het knoop punt met de naam *MC_resource-Group-name_cluster-name_location* standaard, maar kan worden gewijzigd. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Een voorbeeld toepassing implementeren met behulp van AGIC

U gaat nu een voorbeeld toepassing implementeren naar het AKS-cluster dat u hebt gemaakt. De toepassing maakt gebruik van de AGIC-invoeg toepassing voor inkomend verkeer en verbindt het Application Gateway-exemplaar met het AKS-cluster. 

Haal eerst referenties op voor het AKS-cluster door de opdracht uit te voeren `az aks get-credentials` : 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Nu u referenties hebt, voert u de volgende opdracht uit om een voorbeeld toepassing in te stellen die gebruikmaakt van AGIC voor binnenkomend verkeer naar het cluster. Met AGIC wordt het Application Gateway exemplaar dat u eerder hebt ingesteld met de bijbehorende routerings regels, bijgewerkt naar de nieuwe voorbeeld toepassing die u hebt geïmplementeerd.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Controleren of de toepassing bereikbaar is

Nu het Application Gateway-exemplaar zo is ingesteld dat het verkeer naar het AKS-cluster wordt verzonden, gaan we controleren of uw toepassing bereikbaar is. Haal eerst het IP-adres van de ingang op: 

```azurecli-interactive
kubectl get ingress
```

Controleer of de voorbeeld toepassing die u hebt gemaakt wordt uitgevoerd door een van de volgende handelingen uit te voeren:

- Ga naar het IP-adres van het Application Gateway exemplaar dat u hebt ontvangen van het uitvoeren van de voor gaande opdracht.
- Gebruiken `curl` . 

Het kan even duren voordat Application Gateway de update heeft ontvangen. Als Application Gateway nog steeds een **Update** status in de portal hebt, laat u dit dan volt ooien voordat u het IP-adres probeert te bereiken. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resource groep, het Application Gateway-exemplaar en alle gerelateerde resources:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het uitschakelen van de AGIC-invoeg toepassing](./ingress-controller-disable-addon.md)
* [Meer informatie over welke aantekeningen worden ondersteund met AGIC](./ingress-controller-annotations.md)
* [Problemen met AGIC oplossen](./ingress-controller-troubleshoot.md)

