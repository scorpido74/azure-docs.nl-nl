---
title: Een persoonlijk Azure Kubernetes service-cluster maken
description: Meer informatie over het maken van een AKS-cluster (private Azure Kubernetes service)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: a477c2011ff3c6cf1987ed80ef5c19c26abc40f0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713320"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Een persoonlijk Azure Kubernetes service-cluster maken (preview)

In een particulier cluster heeft het besturings vlak of de API-server interne IP-adressen die zijn gedefinieerd in het [RFC1918-adres toewijzing voor privé-Internets](https://tools.ietf.org/html/rfc1918) . Door gebruik te maken van een persoonlijk cluster, kunt u ervoor zorgen dat het netwerk verkeer tussen uw API-server en uw knooppunt groepen alleen op het particuliere netwerk blijft.

Het besturings vlak of de API-server bevindt zich in een door Azure Kubernetes service (AKS) beheerd Azure-abonnement. Het cluster of de knooppunt groep van een klant bevindt zich in het abonnement van de klant. De server en het cluster of de knooppunt groep kunnen met elkaar communiceren via de [Azure Private Link-service][private-link-service] in het virtuele netwerk van de API-server en een persoonlijk eind punt dat wordt weer gegeven in het subnet van het AKS-cluster van de klant.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service en worden op een opt-basis aangeboden. Previews worden aangeboden *als is* en *als beschikbaar* en zijn uitgesloten van de Service Level Agreement (Sla) en beperkte garantie. AKS-previews worden gedeeltelijk gedekt *door de klant* ondersteuning. De functies zijn daarom niet bedoeld voor productie gebruik. Zie de volgende ondersteunings artikelen voor meer informatie:
>
> * [AKS-ondersteunings beleid](support-policies.md)
> * [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="prerequisites"></a>Vereisten

* De Azure CLI-versie 2.0.77 of hoger en de Azure CLI AKS preview-extensie versie 0.4.18

## <a name="currently-supported-regions"></a>Momenteel ondersteunde regio's
* US - west
* US - west 2
* VS - oost 2
* Canada-Midden
* Europa - noord
* Europa - west
* Australië - oost

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>De nieuwste Azure CLI AKS preview-extensie installeren

Als u persoonlijke clusters wilt gebruiken, hebt u de Azure CLI AKS preview-extensie versie 0.4.18 of hoger nodig. Installeer de Azure CLI AKS preview-extensie met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de volgende opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunt u standaard instellingen gebruiken voor alle AKS-clusters die in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de registratie status als *geregistreerd*wordt weer gegeven. U kunt de status controleren met behulp van de volgende opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Wanneer de status is geregistreerd, vernieuwt u de registratie van de resource provider *micro soft. container service* met de volgende opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Een persoonlijk AKS-cluster maken

### <a name="default-basic-networking"></a>Standaard netwerken 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Waarbij *--Enable-Private-cluster* is een verplichte vlag voor een persoonlijk cluster. 

### <a name="advanced-networking"></a>Geavanceerde netwerken  

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
Waarbij *--Enable-Private-cluster* is een verplichte vlag voor een persoonlijk cluster. 

> [!NOTE]
> Als de docker Bridge-adres CIDR (172.17.0.1/16) in conflict is met de CIDR van het subnet, wijzigt u het docker Bridge-adres op de juiste manier.

## <a name="connect-to-the-private-cluster"></a>Verbinding maken met het persoonlijke cluster
Het API-server eindpunt heeft geen openbaar IP-adres. Daarom moet u een virtuele Azure-machine (VM) in een virtueel netwerk maken en verbinding maken met de API-server. Ga hiervoor als volgt te werk:

1. Referenties ophalen om verbinding te maken met het cluster.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Voer een van de volgende bewerkingen uit:
   * Maak een virtuele machine in hetzelfde virtuele netwerk als het AKS-cluster.  
   * Maak een virtuele machine in een ander virtueel netwerk en vergelijkt dit virtuele netwerk met het virtuele netwerk van het AKS-cluster.

     Als u een virtuele machine in een ander virtueel netwerk maakt, stelt u een koppeling in tussen dit virtuele netwerk en de privé-DNS-zone. Dit doet u als volgt:
    
     a. Ga naar de resource groep MC_ * in de Azure Portal.  
     b. Selecteer de privé-DNS-zone.   
     c. Selecteer de koppeling **virtueel netwerk** in het linkerdeel venster.  
     d. Maak een nieuwe koppeling om het virtuele netwerk van de VM toe te voegen aan de privé-DNS-zone. Het duurt enkele minuten voordat de koppeling van de DNS-zone beschikbaar wordt.  
     e. Ga terug naar de resource groep MC_ * in de Azure Portal.  
     f. Selecteer het virtuele netwerk in het rechterdeel venster. De naam van het virtuele netwerk bevindt zich in de vorm *AKS-vnet-\** .  
     g. Selecteer **peerings**in het linkerdeel venster.  
     h. Selecteer **toevoegen**, voeg het virtuele netwerk van de VM toe en maak de peering.  
     i. Ga naar het virtuele netwerk waar u de virtuele machine hebt, selecteer **peerings**, selecteer het virtuele netwerk AKS en maak de peering. Als de adresbereiken in het virtuele netwerk van AKS en het virtuele netwerk van de VM conflicteren, mislukt de peering. Zie [peering van virtuele netwerken][virtual-network-peering]voor meer informatie.

1. Toegang tot de virtuele machine via Secure Shell (SSH).
1. Installeer het hulp programma Kubectl en voer de Kubectl-opdrachten uit.


## <a name="dependencies"></a>Afhankelijkheden  
* De service private link wordt alleen ondersteund op standaard Azure Load Balancer. Basis Azure Load Balancer wordt niet ondersteund.  
* Als u een aangepaste DNS-server wilt gebruiken, implementeert u een AD-server met DNS om door te sturen naar deze IP-168.63.129.16

## <a name="limitations"></a>Beperkingen 
* Beschikbaarheidszones worden momenteel niet ondersteund
* De beperkingen van de [Azure Private Link-service][private-link-service] zijn van toepassing op persoonlijke clusters, Azure-eind punten en service-eind punten van virtuele netwerken, die momenteel niet worden ondersteund in hetzelfde virtuele netwerk.
* Geen ondersteuning voor virtuele knoop punten in een persoonlijk cluster om persoonlijke Azure Container Instances (ACI) in te draaien in een particulier Azure Virtual Network
* Geen ondersteuning voor Azure DevOps-integratie uit het vak met privé clusters
* Voor klanten die Azure Container Registry kunnen gebruiken met persoonlijke AKS, moet het virtuele netwerk Container Registry worden gekoppeld aan het virtuele netwerk van het agent cluster.
* Geen huidige ondersteuning voor Azure dev Spaces
* Geen ondersteuning voor het converteren van bestaande AKS-clusters naar particuliere clusters
* Als u het persoonlijke eind punt in het subnet van de klant verwijdert of wijzigt, werkt het cluster niet meer. 
* Azure Monitor voor containers Live-gegevens wordt momenteel niet ondersteund.
* *Uw eigen DNS-server* wordt momenteel niet ondersteund.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

