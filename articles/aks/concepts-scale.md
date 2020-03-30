---
title: Concepten - Toepassingen schalen in Azure Kubernetes Services (AKS)
description: Meer informatie over schalen in Azure Kubernetes Service (AKS), inclusief horizontale pod autoscaler, clusterautoscaler en de Azure Container Instances-connector.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595855"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Schaalopties voor toepassingen in Azure Kubernetes Service (AKS)

Wanneer u toepassingen uitvoert in Azure Kubernetes Service (AKS), moet u mogelijk de hoeveelheid rekenresources verhogen of verlagen. Aangezien het aantal toepassingsexemplaren dat u moet wijzigen, moet mogelijk ook het aantal onderliggende Kubernetes-knooppunten worden gewijzigd. Mogelijk moet u ook snel een groot aantal extra toepassingsinstanties inrichten.

In dit artikel worden de kernconcepten geïntroduceerd die u helpen toepassingen in AKS te schalen:

- [Handmatig schalen](#manually-scale-pods-or-nodes)
- [Horizontale pod autoscaler (HPA)](#horizontal-pod-autoscaler)
- [Clusterautoscaler](#cluster-autoscaler)
- [Aci-integratie (Azure Container Instance) met AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Pods of knooppunten handmatig schalen

U replica's (pods) en knooppunten handmatig schalen om te testen hoe uw toepassing reageert op een wijziging in de beschikbare resources en status. Met handmatigschalen u ook een ingestelde hoeveelheid resources definiëren die u wilt gebruiken om vaste kosten te behouden, zoals het aantal knooppunten. Als u handmatig wilt schalen, definieert u het aantal replica's of knooppunts. De Kubernetes API plant vervolgens het maken van extra pods of drainerende knooppunten op basis van dat aantal replica's of knooppunten.

Bij het verkleinen van knooppunten roept de Kubernetes API de relevante Azure Compute API aan die is gekoppeld aan het rekentype dat door uw cluster wordt gebruikt. Voor clusters die zijn gebouwd op VM-schaal stelt u bijvoorbeeld de logica in voor het selecteren van welke knooppunten u wilt verwijderen, wordt bepaald door de API vm-schaalsets. Zie de VEELgestelde vragen van [VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)voor meer informatie over hoe knooppunten worden geselecteerd voor verwijdering op schaal naar beneden.

Zie [Toepassingen schalen in AKS][aks-scale]voor aan de slag met het handmatig schalen van pods en knooppunten.

## <a name="horizontal-pod-autoscaler"></a>Horizontale pod autoscaler

Kubernetes gebruikt de horizontale pod autoscaler (HPA) om de vraag naar resources te controleren en het aantal replica's automatisch te schalen. Standaard controleert de horizontale podautoscaler de Metrics API elke 30 seconden op alle vereiste wijzigingen in het aantal replica's. Wanneer wijzigingen nodig zijn, wordt het aantal replica's dienovereenkomstig verhoogd of verlaagd. Horizontale pod autoscaler werkt met AKS-clusters die de Metrics Server voor Kubernetes 1.8+ hebben geïmplementeerd.

![Kubernetes horizontale pod automatisch schalen](media/concepts-scale/horizontal-pod-autoscaling.png)

Wanneer u de horizontale podautoscaler configureert voor een bepaalde implementatie, definieert u het minimum en het maximum aantal replica's dat kan worden uitgevoerd. U definieert ook de statistiek om eventuele schalingsbeslissingen te controleren en op te baseren, zoals CPU-gebruik.

Zie [Autoscale pods in AKS][aks-hpa]om aan de slag te gaan met de horizontale pod autoscaler in AKS.

### <a name="cooldown-of-scaling-events"></a>Cooldown van schaalvergroting gebeurtenissen

Aangezien de horizontale pod autoscaler de Metrics API elke 30 seconden controleert, zijn eerdere schaalgebeurtenissen mogelijk niet voltooid voordat er opnieuw een controle wordt uitgevoerd. Dit gedrag kan ertoe leiden dat de horizontale pod autoscaler om het aantal replica's te wijzigen voordat de vorige schaal gebeurtenis kan ontvangen toepassing werkbelasting en de resource eisen dienovereenkomstig aan te passen.

Om deze racegebeurtenissen tot een minimum te beperken, worden cooldown- of vertragingswaarden ingesteld. Deze waarden bepalen hoe lang de horizontale podautoscaler moet wachten na een schaalgebeurtenis voordat een andere schaalgebeurtenis kan worden geactiveerd. Met dit gedrag kan het aantal nieuwe replica's van kracht worden en kan de API voor metrische gegevens de gedistribueerde werkbelasting weergeven. Standaard is de vertraging bij het opschalen van gebeurtenissen 3 minuten en is de vertraging bij scale down-gebeurtenissen 5 minuten

Momenteel u deze cooldown-waarden niet afstemmen vanaf de standaardinstelling.

## <a name="cluster-autoscaler"></a>Clusterautoscaler

Om te reageren op veranderende podvereisten heeft Kubernetes een clusterautoscaler, die het aantal knooppunten aanpast op basis van de gevraagde compute resources in de knooppuntgroep. Standaard controleert de clusterautoscaler de API-server met statistieken elke 10 seconden op alle vereiste wijzigingen in het aantal knooppunten. Als de clusterautomatischeschaal bepaalt dat een wijziging vereist is, wordt het aantal knooppunten in uw AKS-cluster dienovereenkomstig verhoogd of verlaagd. De clusterautoscaler werkt met Aks-clusters met RBAC-functie die Kubernetes 1.10.x of hoger uitvoeren.

![Kubernetes cluster autoscaler](media/concepts-scale/cluster-autoscaler.png)

Clusterautoscaler wordt meestal gebruikt naast de horizontale pod autoscaler. In combinatie verhoogt of verlaagt de horizontale pod autoscaler het aantal pods op basis van de aanvraagvraag en past de clusterautoscaler het aantal knooppunten aan als dat nodig is om deze extra pods dienovereenkomstig uit te voeren.

Zie [Cluster Autoscaler op AKS][aks-cluster-autoscaler]om aan de slag te gaan met de clusterautoscaler in AKS.

### <a name="scale-up-events"></a>Gebeurtenissen opschalen

Als een knooppunt niet voldoende rekenbronnen heeft om een aangevraagde pod uit te voeren, kan die pod niet verder gaan in het planningsproces. De pod kan niet worden gestart, tenzij er extra rekenbronnen beschikbaar zijn in de knooppuntgroep.

Wanneer de clusterautoscaler pods opmerkt die niet kunnen worden gepland vanwege beperkingen van de knooppuntengroep, wordt het aantal knooppunten binnen de knooppuntgroep verhoogd om de extra rekenbronnen te bieden. Wanneer deze extra knooppunten zijn geïmplementeerd en beschikbaar zijn voor gebruik binnen de knooppuntgroep, worden de pods vervolgens gepland om erop te worden uitgevoerd.

Als uw toepassing snel moet worden geschaald, kunnen sommige pods in een status blijven wachten om te worden gepland totdat de extra knooppunten die door de clusterautoscaler zijn geïmplementeerd, de geplande pods kunnen accepteren. Voor toepassingen met hoge burst-eisen u schalen met virtuele knooppunten en Azure Container Instances.

### <a name="scale-down-events"></a>Gebeurtenissen beperken

De clusterautoscaler controleert ook de status van de podplanning voor knooppunten die onlangs geen nieuwe planningsaanvragen hebben ontvangen. Dit scenario geeft aan dat de knooppuntgroep meer rekenresources heeft dan nodig is en dat het aantal knooppunten kan worden verminderd.

Een knooppunt dat een drempelwaarde passeert omdat deze standaard 10 minuten nodig is, wordt verwijderd. Wanneer deze situatie zich voordoet, worden pods gepland om te worden uitgevoerd op andere knooppunten binnen de knooppuntgroep en vermindert de clusterautoscaler het aantal knooppunten.

Uw toepassingen kunnen enige onderbrekingen ondervinden omdat pods zijn gepland op verschillende knooppunten wanneer de clusterautoscaler het aantal knooppunten vermindert. Als u onderbrekingen minimaliseert, vermijdt u toepassingen die één pod-instantie gebruiken.

## <a name="burst-to-azure-container-instances"></a>Burst naar Azure Container Instances

Als u uw AKS-cluster snel wilt schalen, u integreren met Azure Container Instances (ACI). Kubernetes heeft ingebouwde componenten om het aantal replica's en node te schalen. Als uw toepassing echter snel moet worden geschaald, kan de horizontale pod-autoscaler meer pods plannen dan kan worden geleverd door de bestaande rekenbronnen in de knooppuntgroep. Als dit is geconfigureerd, wordt de clusterautoscaler vervolgens geactiveerd om extra knooppunten in de knooppuntgroep te implementeren, maar het kan enkele minuten duren voordat deze knooppunten met succes worden ingericht en de Kubernetes-planner toestaan er pods op uit te voeren.

![Kubernetes burst scaling naar ACI](media/concepts-scale/burst-scaling.png)

Met ACI u snel containerexemplaren implementeren zonder extra infrastructuuroverhead. Wanneer u verbinding maakt met AKS, wordt ACI een beveiligde, logische uitbreiding van uw AKS-cluster. De [component virtuele knooppunten,][virtual-nodes-cli] die is gebaseerd op [Virtual Kubelet,][virtual-kubelet]is geïnstalleerd in uw AKS-cluster dat ACI presenteert als een virtueel Kubernetes-knooppunt. Kubernetes kan vervolgens pods plannen die als ACI-exemplaren worden uitgevoerd via virtuele knooppunten, niet als pods op VM-knooppunten rechtstreeks in uw AKS-cluster. Virtuele knooppunten zijn momenteel in preview in AKS.

Uw toepassing vereist geen wijziging om virtuele knooppunten te gebruiken. Implementaties kunnen worden geschaald in AKS en ACI en zonder vertraging, omdat clusterautoscaler nieuwe knooppunten implementeert in uw AKS-cluster.

Virtuele knooppunten worden geïmplementeerd in een extra subnet in hetzelfde virtuele netwerk als uw AKS-cluster. Met deze virtuele netwerkconfiguratie kan het verkeer tussen ACI en AKS worden beveiligd. Net als een AKS-cluster is een ACI-instantie een veilige, logische compute-bron die is geïsoleerd van andere gebruikers.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met het schalen van toepassingen, volgt u eerst de [snelle start om een AKS-cluster te maken met de Azure CLI.][aks-quickstart] U dan beginnen met het handmatig of automatisch schalen van toepassingen in uw AKS-cluster:

- Pods [of][aks-manually-scale-pods] [knooppunten][aks-manually-scale-nodes] handmatig schalen
- De [horizontale podautoscaler gebruiken][aks-hpa]
- De [clusterautoscaler gebruiken][aks-cluster-autoscaler]

Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS-clusters en workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS toegang en identiteit][aks-concepts-identity]
- [Kubernetes / AKS-beveiliging][aks-concepts-security]
- [Kubernetes / AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes / AKS-opslag][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md