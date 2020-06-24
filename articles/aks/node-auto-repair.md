---
title: De knoop punten van de Azure Kubernetes-service (AKS) automatisch herstellen
description: Meer informatie over de functionaliteit voor automatisch herstel van knoop punten en hoe AKS de gebroken werk knooppunten herstelt.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735622"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatische reparatie van het knoop punt Azure Kubernetes service (AKS)

AKS controleert voortdurend de status van worker-knoop punten en voert automatisch herstel van de knoop punten uit als deze een slechte status hebben. In dit document worden Opera tors geïnformeerd over de werking van de automatische functionaliteit voor knooppunt reparatie. Naast de reparaties van AKS, voert het Azure VM-platform [onderhoud uit op virtual machines][vm-updates] dat ook problemen ondervindt. AKS en Azure-Vm's werken samen om service onderbrekingen voor clusters te minimaliseren.

> [!Important]
> De functionaliteit voor automatisch herstellen van knoop punten wordt momenteel niet ondersteund voor Windows Server-knooppunt groepen.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hoe AKS controleert op beschadigde knoop punten

> [!Note]
> AKS neemt reparatie actie op knoop punten met het gebruikers account **AKS-** herstel bewerking.

AKS gebruikt regels om te bepalen of een knoop punt slecht is en moet worden hersteld. AKS maakt gebruik van de volgende regels om te bepalen of automatisch herstel nodig is.

* Het knoop punt rapporteert de status van een **loopvlak** bij opeenvolgende controles binnen een periode van tien minuten
* Het knoop punt rapporteert geen status binnen 10 minuten

U kunt de status van uw knoop punten hand matig controleren met kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Hoe werkt automatisch herstellen?

> [!Note]
> AKS initieert herstel bewerkingen met het gebruikers account **AKS-** herstellen.

Automatisch herstellen wordt standaard ondersteund voor clusters met het type **Virtual Machine Scale sets**van een VM-set. Als een knoop punt wordt vastgesteld dat de status niet in orde is op basis van de bovenstaande regels, wordt het knoop punt door AKS opnieuw opgestart na 10 opeenvolgende slechte minuten. Als de knoop punten niet meer in orde zijn na de eerste herstel bewerking, worden extra herstel bewerkingen door AKS engineers onderzocht.
  
Als er tijdens een status controle meerdere knoop punten zijn beschadigd, wordt elk knoop punt afzonderlijk hersteld voordat een andere herstel bewerking wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
