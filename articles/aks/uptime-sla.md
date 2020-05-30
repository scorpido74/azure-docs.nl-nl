---
title: Azure Kubernetes service (AKS) met SLA voor uptime
description: Meer informatie over de optionele SLA-aanbieding voor uptime voor de API-server van Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 986bb835f319cebf3006d7e1bd31bd7f04a1ae82
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193992"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA voor uptime van Azure Kubernetes service (AKS)

SLA voor uptime is een optionele functie voor het inschakelen van een financieel ondersteunde, hogere SLA voor een cluster. SLA voor uptime garandeert 99,95% Beschik baarheid van het Kubernetes API-server eindpunt voor clusters die gebruikmaken van [Beschikbaarheidszones][availability-zones] en 99,9% Beschik baarheid voor clusters die geen Beschikbaarheidszones gebruiken. AKS maakt gebruik van hoofd knooppunt replica's in update-en fout domeinen om te controleren of aan de SLA-vereisten wordt voldaan.

Klanten die een SLA nodig hebben om te voldoen aan de nalevings vereisten of een SLA naar hun eind gebruikers moeten uitbreiden, moeten deze functie inschakelen. Klanten met kritieke werk belastingen die profiteren van een hogere SLA, kunnen ook profiteren van de voor delen. Het gebruik van de SLA-functie voor uptime met Beschikbaarheidszones maakt een hogere Beschik baarheid mogelijk voor de uptime van de Kubernetes-API-server.  

Klanten kunnen nog steeds onbeperkte gratis clusters met een serviceniveau doelstelling (SLO) van 99,5% maken en ervoor kiezen de voorkeurs duur van SLO of SLA als nodig te hebben.

> [!Important]
> Zie limiet voor uitgaand [verkeer](limit-egress-traffic.md) voor het openen van de juiste poorten voor clusters met uitgaand vergren delen.

## <a name="sla-terms-and-conditions"></a>SLA-voor waarden

SLA voor uptime is een betaalde functie en ingeschakeld per cluster. De prijzen voor de SLA voor uptime worden bepaald door het aantal afzonderlijke clusters en niet op basis van de grootte van de afzonderlijke clusters. U kunt de [prijs informatie](https://azure.microsoft.com/pricing/details/kubernetes-service/) voor de sla voor uptime bekijken voor meer informatie.

## <a name="region-availability"></a>Beschik baarheid van regio

SLA voor uptime is beschikbaar in de volgende regio's:

* Australië - oost
* Canada - midden
* VS - oost
* VS - oost 2
* VS - zuid-centraal
* Azië - zuidoost
* VS - west 2

## <a name="before-you-begin"></a>Voordat u begint

* De Azure CLI-versie 2.7.0 of hoger

## <a name="creating-a-cluster-with-uptime-sla"></a>Een cluster maken met SLA voor uptime

Als u een nieuw cluster wilt maken met de SLA voor de uptime, gebruikt u de Azure CLI.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Azure Monitor voor containers kan ook worden ingeschakeld met behulp van de parameter *--enable-addons monitoring*.  Het volt ooien van deze bewerking duurt enkele minuten.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Het volgende JSON-fragment toont de betaalde laag voor de SKU, wat aangeeft dat uw cluster is ingeschakeld met SLA voor uptime.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Beperkingen

* Kan momenteel niet converteren als bestaand cluster om de SLA voor uptime in te scha kelen.
* Op dit moment is er geen manier om de SLA voor de uptime te verwijderen uit een AKS-cluster nadat het is gemaakt.  
* Persoonlijke clusters worden momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.
Configureer uw cluster om uitgaand [verkeer te beperken](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
