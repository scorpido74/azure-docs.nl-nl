---
title: Azure Kubernetes service (AKS) hoge Beschik baarheid met SLA voor uptime
description: Meer informatie over de optionele SLA-aanbieding voor hoge Beschik baarheid voor de Azure Kubernetes service (AKS)-API-server.
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125720"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA voor uptime van Azure Kubernetes service (AKS)

SLA voor uptime is een optionele functie voor het inschakelen van een financieel back-up met een hogere SLA voor een cluster. SLA voor uptime garandeert 99,95% Beschik baarheid van het Kubernetes API-server eindpunt voor clusters die gebruikmaken van de [beschikbaarheids zone][availability-zones] en 99,9% van de beschik baarheid voor clusters die geen beschikbaarheids zones gebruiken. AKS maakt gebruik van hoofd knooppunt replica's in update-en fout domeinen om te controleren of aan de SLA-vereisten wordt voldaan.

Klanten die een SLA moeten gebruiken om nalevings redenen of het uitbreiden van SLA naar hun klanten, moeten deze functie inschakelen. Klanten met kritieke werk belastingen die hogere Beschik baarheid nodig hebben met een optie voor het voor deel van SLA, kunnen deze functie niet inschakelen. Schakel de functie in met Beschikbaarheidszones om een hogere Beschik baarheid van de Kubernetes API-server te verkrijgen.  

Klanten kunnen onbeperkt gratis clusters maken met een serviceniveau doelstelling (SLO) van 99,5%.

> [!Important]
> Zie voor clusters met uitgaand vergren delen [beperken](limit-egress-traffic.md) dat de juiste poorten voor de sla voor uptime worden geopend.

## <a name="sla-terms-and-conditions"></a>SLA-voor waarden

SLA voor uptime is een betaalde functie en ingeschakeld per cluster. De prijzen voor de SLA voor uptime worden bepaald door het aantal clusters en niet door de grootte van de clusters. U kunt de [prijs informatie](https://azure.microsoft.com/pricing/details/kubernetes-service/) voor de sla voor uptime bekijken voor meer informatie.

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

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

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
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Beperkingen

* U kunt momenteel geen SLA voor uptime aan bestaande clusters toevoegen.
* Er is momenteel geen manier om de SLA voor uptime uit een AKS-cluster te verwijderen.  

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

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
