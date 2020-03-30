---
title: Een privé-Azure Kubernetes Service-cluster maken
description: Meer informatie over het maken van een privé-AKS-cluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499302"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Een privé-Azure Kubernetes Service-cluster maken

In een privécluster heeft het besturingsvlak of de API-server interne IP-adressen die zijn gedefinieerd in het [document RFC1918 - Adrestoewijzing voor privéinternet.](https://tools.ietf.org/html/rfc1918) Door een privécluster te gebruiken, u ervoor zorgen dat het netwerkverkeer tussen uw API-server en uw knooppuntpools alleen op het privénetwerk blijft.

Het besturingsvlak of api-server bevindt zich in een Door Azure Kubernetes Service (AKS)-beheerde Azure-abonnement. De cluster- of knooppuntgroep van een klant bevindt zich in het abonnement van de klant. De server en de cluster- of knooppuntgroep kunnen met elkaar communiceren via de [Azure Private Link-service][private-link-service] in het virtuele netwerk van de API-server en een privéeindpunt dat wordt weergegeven in het subnet van het AKS-cluster van de klant.

## <a name="prerequisites"></a>Vereisten

* De Azure CLI-versie 2.2.0 of hoger

## <a name="create-a-private-aks-cluster"></a>Een privé-AKS-cluster maken

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep of gebruik een bestaande resourcegroep voor uw AKS-cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standaardbasisnetwerken 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Waar *--enable-private-cluster* een verplichte vlag is voor een privécluster. 

### <a name="advanced-networking"></a>Geavanceerd netwerken  

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
Waar *--enable-private-cluster* een verplichte vlag is voor een privécluster. 

> [!NOTE]
> Als het Docker-brugadres CIDR (172.17.0.1/16) botst met het subnet CIDR, wijzigt u het adres van de Docker-brug op de juiste manier.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opties voor verbinding maken met het privécluster

Het eindpunt van de API-server heeft geen openbaar IP-adres. Als u de API-server wilt beheren, moet u een VM gebruiken die toegang heeft tot het Azure Virtual Network (VNet) van het AKS-cluster. Er zijn verschillende opties voor het tot stand brengen van netwerkconnectiviteit met het privécluster.

* Maak een VM in hetzelfde Azure Virtual Network (VNet) als het AKS-cluster.
* Gebruik een VM in een apart netwerk en stel [virtueel netwerkpeering][virtual-network-peering]in.  Zie het gedeelte hieronder voor meer informatie over deze optie.
* Gebruik een [Express Route of VPN-verbinding.][express-route-or-VPN]

Het maken van een VM in dezelfde VNET als het AKS-cluster is de eenvoudigste optie.  Express Route en VPN's voegen kosten toe en vereisen extra complexiteit van netwerken.  Voor virtuele netwerkpeering moet u uw CIDR-bereiken voor netwerken plannen om ervoor te zorgen dat er geen overlappende bereiken zijn.

## <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Zoals gezegd is VNet-peering een manier om toegang te krijgen tot uw privécluster. Als u VNet-peering wilt gebruiken, moet u een koppeling tussen virtueel netwerk en de privé-DNS-zone instellen.
    
1. Ga naar de MC_*-brongroep in de Azure-portal.  
2. Selecteer de privé-DNS-zone.   
3. Selecteer in het linkerdeelvenster de **koppeling Virtueel netwerk.**  
4. Maak een nieuwe koppeling om het virtuele netwerk van de VM toe te voegen aan de privé-DNS-zone. Het duurt een paar minuten voordat de DNS-zonekoppeling beschikbaar is.  
5. Ga terug naar de MC_* brongroep in de Azure-portal.  
6. Selecteer in het rechterdeelvenster het virtuele netwerk. De naam van het virtuele netwerk is in de vorm *aks-vnet-\**.  
7. Selecteer **Peerings**in het linkerdeelvenster .  
8. Selecteer **Toevoegen,** voeg het virtuele netwerk van de vm toe en maak vervolgens de peering.  
9. Ga naar het virtuele netwerk waar u de VM hebt, selecteer **Peerings,** selecteer het virtuele AKS-netwerk en maak vervolgens peering. Als het adres bereik op het virtuele AKS-netwerk en het virtuele netwerk van de VM botsen, mislukt peering. Zie [Virtueel netwerkpeeren][virtual-network-peering]voor meer informatie.

## <a name="dependencies"></a>Afhankelijkheden  

* De Private Link-service wordt alleen ondersteund op Standaard Azure Load Balancer. Basic Azure Load Balancer wordt niet ondersteund.  
* Als u een aangepaste DNS-server wilt gebruiken, voegt u het Azure DNS IP 168.63.129.16 toe als de upstream DNS-server in de aangepaste DNS-server.

## <a name="limitations"></a>Beperkingen 
* IP-geautoriseerde bereiken kunnen niet worden toegepast op het eindpunt van de private api-server, ze zijn alleen van toepassing op de openbare API-server
* Beschikbaarheidszones worden momenteel ondersteund voor bepaalde regio's, zie het begin van dit document 
* [Azure Private Link-servicebeperkingen][private-link-service] zijn van toepassing op privéclusters, Azure private endpoints en eindpunten voor virtuele netwerkservices, die momenteel niet worden ondersteund in hetzelfde virtuele netwerk.
* Geen ondersteuning voor virtuele knooppunten in een privécluster om private Azure Container Instances (ACI) uit te draaien in een privé-Extern Azure-virtueel netwerk
* Geen ondersteuning voor Azure DevOps-integratie out-of-the-box met privéclusters
* Voor klanten die Azure Container Registry moeten inschakelen om met privé-AKS te werken, moet het virtuele netwerk containerregister worden gekoppeld aan het virtuele netwerk van het agentcluster.
* Geen huidige ondersteuning voor Azure Dev Spaces
* Geen ondersteuning voor het omzetten van bestaande AKS-clusters in private clusters
* Als u het privéeindpunt in het subnet van de klant verwijderde of wijzigt, werkt het cluster niet meer. 
* Azure Monitor voor containers Live Data wordt momenteel niet ondersteund.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

