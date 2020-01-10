---
title: Persoonlijk Azure Kubernetes service-cluster
description: Meer informatie over het maken van een AKS-cluster (private Azure Kubernetes service)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830051"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Open bare preview-privé Azure Kubernetes service-cluster

In een particulier cluster worden in het besturings vlak/de API-server interne IP-adressen gedefinieerd in [RFC1918](https://tools.ietf.org/html/rfc1918).  Door gebruik te maken van een persoonlijk cluster, kunt u ervoor zorgen dat het netwerk verkeer tussen uw API-server en de knooppunt groepen alleen in het particuliere netwerk blijven.

De communicatie tussen de Control-en API-server, die zich in een door AKS beheerd Azure-abonnement bevindt, en de cluster-of knooppunt groep van klanten, die zich in een klant abonnement bevindt, kunnen met elkaar communiceren via de [service voor persoonlijke koppelingen][private-link-service] in het API-server VNET en een persoonlijk eind punt dat wordt weer gegeven in het subnet van de klant AKS cluster.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid](support-policies.md)
> * [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

* U hebt de Azure CLI-versie 2.0.77 of hoger nodig en de AKS-Preview 0.4.18-extensie

## <a name="current-supported-regions"></a>Huidige ondersteunde regio's
* VS - west
* VS - west 2
* VS - oost 2
* Canada-Midden
* Europa - noord
* Europa - west
* Australië Oost

## <a name="install-latest-aks-cli-preview-extension"></a>De meest recente AKS CLI-preview-extensie installeren

Als u persoonlijke clusters wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.18 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status *geregistreerd*wordt weer gegeven. U kunt de registratie status controleren met de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Wanneer de status is geregistreerd, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Een persoonlijk AKS-cluster maken

#### <a name="default-basic-networking"></a>Standaard netwerken 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Waar--Enable-Private-cluster is een verplichte vlag voor een persoonlijk cluster 

#### <a name="advanced-networking"></a>Geavanceerde netwerken  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Waar--Enable-Private-cluster is een verplichte vlag voor een persoonlijk cluster 

## <a name="steps-to-connect-to-the-private-cluster"></a>Stappen om verbinding te maken met het privé cluster
Het eind punt van de API-server heeft geen openbaar IP-adres. Als gevolg hiervan moeten gebruikers een virtuele Azure-machine maken in een virtueel netwerk en verbinding met de API-server hebben. De stappen in

* Referenties ophalen om verbinding te maken met het cluster

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Maak een virtuele machine in hetzelfde VNET als het AKS-cluster of maak een virtuele machine in een ander VNET en vergelijkt dit VNET met het AKS-cluster VNET
* Als u een virtuele machine in een ander VNET maakt, moet u een koppeling instellen tussen dit VNET en de Privé-DNS zone
    * Ga naar de resource groep MC_ * in de portal 
    * Klik op de zone Privé-DNS 
    * Virtuele netwerk koppeling in het linkerdeel venster selecteren
    * Maak een nieuwe koppeling om het VNET van de virtuele machine toe te voegen aan de Privé-DNS zone *(het duurt enkele minuten voordat de koppeling van de DNS-zone beschikbaar is)*
    * Ga terug naar de resource groep MC_ * in de portal
    * Selecteer het virtuele netwerk in het rechterdeel venster. De naam van het virtuele netwerk heeft de indeling AKS-vnet-*.
    * Selecteer peerings in het linkerdeel venster
    * Klik op toevoegen en voeg het virtuele netwerk van de VM toe en maak de peering.
    * Ga naar het Vnet waar u de virtuele machine hebt, klik op Peerings en selecteer het virtuele netwerk AKS en maak de peering. Als de adresbereiken in het virtuele netwerk van AKS en het virtuele netwerk van de VM conflicteren, mislukt de peering. Raadpleeg dit [document][virtual-network-peering] voor meer informatie over peering van virtuele netwerken.
* SSH naar de virtuele machine
* Kubectl-hulp programma installeren en Kubectl-opdrachten uitvoeren

## <a name="dependencies"></a>Afhankelijkheden  
* Alleen standaard LB: geen ondersteuning voor basis load balancer  

## <a name="limitations"></a>Beperkingen 
* Dezelfde beperkingen voor de [Azure Private Link-service][private-link-service] zijn van toepassing op particuliere clusters, Azure private endpoints en Virtual Network Service-eind punten worden momenteel niet ondersteund in hetzelfde VNET
* Geen ondersteuning voor virtuele knoop punten in een persoonlijk cluster om persoonlijke ACI-exemplaren in een persoonlijk Azure VNET te draaien
* Geen ondersteuning voor Azure DevOps-integratie uit het vak met privé clusters
* Als klanten ACR in staat stellen om met persoonlijke AKS te werken, moet het VNET van de ACR worden gekoppeld aan het agent cluster VNET
* Geen huidige ondersteuning voor Azure dev Spaces
* Geen ondersteuning voor het converteren van bestaande AKS-clusters naar particuliere clusters  
* Als u het persoonlijke eind punt in het subnet van de klant verwijdert of wijzigt, werkt het cluster niet meer. 
* Azure Monitor voor containers Live-gegevens wordt momenteel niet ondersteund
* Uw eigen DNS-server wordt momenteel niet ondersteund


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

