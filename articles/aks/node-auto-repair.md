---
title: De knoop punten van de Azure Kubernetes-service (AKS) automatisch herstellen
description: Meer informatie over de functionaliteit voor automatisch herstel van knoop punten en hoe AKS de gebroken werk knooppunten herstelt.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284837"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatische reparatie van het knoop punt Azure Kubernetes service (AKS)

AKS controleert voortdurend de status van worker-knoop punten en voert automatisch herstel van de knoop punten uit als deze een slechte status hebben. In deze documentatie wordt beschreven hoe u met Azure Kubernetes service (AKS) werk knooppunten bewaakt en beschadigde werk knooppunten herstelt.  De documentatie is om AKS-Opera tors te informeren over het gedrag van de functionaliteit voor het herstellen van knoop punten. Het is ook belang rijk te weten dat het Azure-platform [onderhoud uitvoert op virtual machines][vm-updates] die problemen ondervinden. AKS en Azure werken samen om service onderbrekingen voor uw clusters te minimaliseren.

> [!Important]
> De functionaliteit voor automatisch herstellen van knoop punten wordt momenteel niet ondersteund voor Windows Server-knooppunt groepen.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hoe AKS controleert op beschadigde knoop punten

> [!Note]
> AKS neemt reparatie actie op knoop punten met het gebruikers account **AKS-** herstel bewerking.

AKS gebruikt regels om te bepalen of een knoop punt een slechte status heeft en moet worden hersteld. AKS maakt gebruik van de volgende regels om te bepalen of automatisch herstel nodig is.

* Het knoop punt rapporteert de status van een **loopvlak** bij opeenvolgende controles binnen een periode van tien minuten
* Het knoop punt rapporteert geen status binnen 10 minuten

U kunt de status van uw knoop punten hand matig controleren met kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Hoe werkt automatisch herstellen?

> [!Note]
> AKS neemt reparatie actie op knoop punten met het gebruikers account **AKS-** herstel bewerking.

Dit gedrag is voor **Virtual Machine Scale sets**.  Automatisch herstel neemt verschillende stappen uit om een beschadigd knoop punt te herstellen.  Als een knoop punt wordt beschouwd als een slechte status, probeert AKS verschillende herbemiddelings stappen.  De stappen worden in deze volg orde uitgevoerd:

1. Nadat de container runtime gedurende tien minuten niet meer reageert, worden de mislukte runtime services opnieuw gestart op het knoop punt.
2. Als het knoop punt niet binnen tien minuten is voltooid, wordt het knoop punt opnieuw opgestart.
3. Als het knoop punt niet binnen 30 minuten klaar is, wordt de installatie kopie van het knoop punt hersteld.

> [!Note]
> Als meerdere knoop punten een slechte status hebben, worden ze één voor één gerepareerd

## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
