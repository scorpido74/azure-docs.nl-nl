---
title: De invoegtoepassing voor inkomend verkeer inschakelen voor een nieuw AKS-cluster met een nieuwe Azure Application Gateway-instantie
description: Leer hoe u de Azure CLI kunt gebruiken om de invoegtoepassing voor inkomend verkeer voor uw nieuwe AKS-cluster in kunt schakelen met een nieuwe Application Gateway-instantie.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 620fe5851f9dbde01b2206595fa79b61f62deb66
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595917"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Zelfstudie: De invoegtoepassing voor inkomend verkeer (preview) inschakelen voor een nieuw AKS-cluster met een nieuwe Application Gateway-instantie

U kunt de Azure CLI gebruiken om de invoegtoepassing [voor inkomend verkeer van Application Gateway (AGIC)](ingress-controller-overview.md) in te schakelen voor een [AKS-cluster](https://azure.microsoft.com/services/kubernetes-service/) (Azure Kubernetes Services). Deze invoegtoepassing is momenteel in preview.

In deze zelfstudie maakt u een AKS-cluster waarvoor de AGIC-invoegtoepassing is ingeschakeld. Als u het cluster maakt, wordt automatisch een Azure Application Gateway-instantie gemaakt om te gebruiken. Vervolgens implementeert u een voorbeeldtoepassing die gebruikmaakt van de invoegtoepassing om via Application Gateway de toepassing beschikbaar te maken. 

De invoegtoepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [voorheen met behulp van helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Ze biedt ook een volledig beheerde ervaring.    

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een resourcegroep maken. 
> * Een nieuw AKS-cluster maken waarvoor de AGIC-invoegtoepassing is ingeschakeld. 
> * Een voorbeeldtoepassing implementeren met behulp van AGIC voor inkomend verkeer op het AKS-cluster.
> * Controleren of de toepassing bereikbaar is via Application Gateway.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie de Azure CLI (versie 2.0.4 of hoger) uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Registreer de functievlag *AKS-IngressApplicationGatewayAddon* door gebruik te maken van de opdracht [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register), zoals in het volgende voorbeeld wordt gedemonstreerd. Zolang de invoegtoepassing nog in preview is, hoeft u dit slechts één keer per abonnement te doen.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status `Registered` wordt weergegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider Microsoft.ContainerService met behulp van de opdracht [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register):
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Voor deze zelfstudie dient u de uitbreiding aks-preview te installeren of bij te werken. Gebruik de volgende Azure CLI-opdrachten:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie (regio) *canadacentral*: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Een AKS-cluster implementeren waarvoor de invoegtoepassing is ingeschakeld

U gaat nu een nieuw AKS-cluster implementeren waarvoor de AGIC-invoegtoepassing is ingeschakeld. Als u geen bestaande Application Gateway-instantie opgeeft om in dit proces te worden gebruikt, wordt er automatisch een nieuwe Application Gateway-instantie gemaakt en ingesteld om het verkeer naar het AKS-cluster af te handelen.  

> [!NOTE]
> De invoegtoepassing voor inkomend verkeer van Application Gateway ondersteunt *alleen* Application Gateway v2-SKU's (Standard en WAF) en *niet* de Application Gateway v1-SKU's. Wanneer u een nieuwe Application Gateway-instantie implementeert met behulp van de AGIC-invoegtoepassing, kunt u alleen een Application Gateway Standard_v2-SKU implementeren. Als u de invoegtoepassing voor een Application Gateway WAF_v2-SKU wilt inschakelen, moet u een van de volgende methoden gebruiken:
>
> - Schakel WAF in op Application Gateway via de portal. 
> - Maak eerst de Application Gateway-instantie WAF_v2 en volg daarna de instructies voor het [inschakelen van de AGIC-invoegtoepassing met een bestaand AKS-cluster en een bestaande Application Gateway-instantie](tutorial-ingress-controller-add-on-existing.md). 

In het volgende voorbeeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) en [beheerde identiteiten](https://docs.microsoft.com/azure/aks/use-managed-identity). De AGIC-invoegtoepassing wordt ingeschakeld in de resourcegroep *myResourceGroup* die u hebt gemaakt. 

Als u een nieuw AKS-cluster implementeert terwijl de AGIC-invoegtoepassing is ingeschakeld zonder dat u een bestaande Application Gateway-instantie opgeeft, wordt er automatisch een instantie van de Application Gateway Standard_v2-SKU gemaakt. U geeft dus ook de naam en de adresruimte van het subnet op van de Application Gateway-instantie. De naam van de Application Gateway-instantie wordt *myApplicationGateway* en de adresruimte van het subnet dat we gebruiken is 10.2.0.0/16. Zorg ervoor dat u aan het begin van deze zelfstudie de uitbreiding aks-preview hebt toegevoegd of bijgewerkt. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Zie [dit referentiemateriaal](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create) voor meer informatie over het configureren van aanvullende parameters voor de opdracht `az aks create`. 

> [!NOTE]
> Het AKS-cluster dat u hebt gemaakt, wordt weergegeven in de resourcegroep *myResourceGroup* die u hebt gemaakt. De automatisch gemaakte Application Gateway-instantie bevindt zich echter in het knooppunt van de resourcegroep, waar zich de agentpools bevinden. De resourcegroep van het knooppunt heet standaard *MC_resource-group-name_cluster-name_location*, maar kan worden gewijzigd. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Een voorbeeldtoepassing implementeren met behulp van AGIC

U gaat nu een voorbeeldtoepassing implementeren naar het AKS-cluster dat u hebt gemaakt. De toepassing maakt gebruik van de AGIC-invoegtoepassing voor inkomend verkeer en verbindt de Application Gateway-instantie met het AKS-cluster. 

Haal eerst referenties op voor het AKS-cluster door de opdracht `az aks get-credentials` uit te voeren: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Nu u referenties hebt, voert u de volgende opdracht uit om een voorbeeldtoepassing in te stellen die gebruikmaakt van AGIC voor inkomend verkeer naar het cluster. Met AGIC wordt de Application Gateway-instantie die u eerder hebt ingesteld met de bijbehorende regels voor doorsturen, bijgewerkt naar de nieuwe voorbeeldtoepassing die u hebt geïmplementeerd.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Controleer of de toepassing bereikbaar is

Nu de Application Gateway-instantie is ingesteld om het verkeer naar het AKS-cluster af te handelen, gaan we controleren of uw toepassing bereikbaar is. Haal eerst het IP-adres van het inkomende verkeer op: 

```azurecli-interactive
kubectl get ingress
```

Controleer of de voorbeeldtoepassing die u hebt gemaakt wordt uitgevoerd door een van de volgende handelingen uit te voeren:

- Ga naar het IP-adres van de Application Gateway-instantie dat u hebt verkregen door de voorgaande opdracht uit te voeren.
- Gebruik daarvoor `curl`. 

Het kan even duren voordat Application Gateway de update heeft ontvangen. Als Application Gateway op de portal nog steeds de status **Bijwerken** heeft, wacht dan tot het proces is voltooid voordat u naar het IP-adres gaat. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de Application Gateway-instantie en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het uitschakelen van de AGIC-invoegtoepassing](./ingress-controller-disable-addon.md)
* [Meer informatie over welke aantekeningen worden ondersteund door AGIC](./ingress-controller-annotations.md)
* [Problemen met AGIC oplossen](./ingress-controller-troubleshoot.md)

