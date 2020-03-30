---
title: Aks-knooppunten (Azure Kubernetes Service) automatisch herstellen
description: Meer informatie over de functionaliteit voor automatische reparatie van knooppunten en hoe AKS kapotte werknemersknooppunten oplost.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284837"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) knooppunt automatisch herstellen

AKS controleert continu de status van werknemersknooppunten en voert automatische reparatie van de knooppunten uit als ze ongezond worden. In deze documentatie wordt beschreven hoe Aks (Azure Kubernetes Service) werknemersknooppunten controleert en ongezonde werknemersknooppunten herstelt.  De documentatie is om AKS-operators te informeren over het gedrag van knooppuntreparatiefunctionaliteit. Het is ook belangrijk op te merken dat Azure-platform [onderhoud uitvoert op virtuele machines][vm-updates] die problemen ervaren. AKS en Azure werken samen om serviceonderbrekingen voor uw clusters te minimaliseren.

> [!Important]
> De functionaliteit voor automatische reparatie van knooppunten wordt momenteel niet ondersteund voor groepen voor windows server-knooppunt.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hoe AKS controleert op ongezonde knooppunten

> [!Note]
> AKS onderneemt herstelactie op knooppunten met het gebruikersaccount **aks-remediator**.

AKS gebruikt regels om te bepalen of een knooppunt een ongezonde status is en moet worden gerepareerd. AKS gebruikt de volgende regels om te bepalen of automatische reparatie nodig is.

* Het knooppunt rapporteert de status van **NotReady** bij opeenvolgende controles binnen een tijdsbestek van 10 minuten
* Het knooppunt meldt geen status binnen 10 minuten

U handmatig de status van uw knooppunten controleren met kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Hoe automatische reparatie werkt

> [!Note]
> AKS onderneemt herstelactie op knooppunten met het gebruikersaccount **aks-remediator**.

Dit gedrag is voor **virtuele machineschaalsets**.  Auto-repair neemt verschillende stappen om een kapot knooppunt te repareren.  Als wordt vastgesteld dat een knooppunt niet in orde is, probeert AKS verschillende herstelstappen.  De stappen worden in deze volgorde uitgevoerd:

1. Nadat de runtime van de container gedurende 10 minuten niet meer reageert, worden de falende runtime-services opnieuw gestart op het knooppunt.
2. Als het knooppunt niet binnen 10 minuten klaar is, wordt het knooppunt opnieuw opgestart.
3. Als het knooppunt niet binnen 30 minuten klaar is, wordt het knooppunt opnieuw weergegeven.

> [!Note]
> Als meerdere knooppunten niet in orde zijn, worden ze één voor één gerepareerd

## <a name="next-steps"></a>Volgende stappen

Gebruik [beschikbaarheidszones][availability-zones] om de hoge beschikbaarheid met uw AKS-clusterworkloads te verhogen.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
