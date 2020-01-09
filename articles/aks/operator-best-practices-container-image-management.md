---
title: Operator aanbevolen procedures - beheer van installatiekopieën voor Container in Azure Kubernetes Services (AKS)
description: Meer over de best practices uit de cluster-operator voor informatie over het beheren en beveiligen van containerinstallatiekopieën in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: cd859a4009782ca39732ec004a3d3e05edd377b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442899"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor het beheer van container-installatiekopie en beveiliging in Azure Kubernetes Service (AKS)

Als u toepassingen ontwikkelen en in Azure Kubernetes Service (AKS uitvoeren), is de beveiliging van uw containers en containerinstallatiekopieën belangrijkste overweging. Containers die verouderd zijn basis afbeeldingen of niet-gepatchte toepassing runtimes introduceren een beveiligingsrisico en een mogelijke aanvalsvector. Om te beperken deze risico's, moet u de hulpprogramma's die zoeken en oplossen van problemen in uw containers op build-tijd, evenals runtime integreren. De eerder in het proces dat de kwetsbaarheid of verouderd basisinstallatiekopie wordt geblokkeerd, is het veiliger het cluster. In dit artikel *containers* betekent dat zowel de containerinstallatiekopieën worden opgeslagen in een container registry, en de actieve containers.

In dit artikel richt zich op het beveiligen van uw containers in AKS. Procedures voor:

> [!div class="checklist"]
> * Zoeken en afbeelding beveiligingsproblemen herstellen
> * Automatisch activeren en opnieuw implementeren van containerinstallatiekopieën wanneer een basisinstallatiekopie wordt bijgewerkt

U kunt ook de aanbevolen procedures voor [cluster beveiliging][best-practices-cluster-security] en voor [pod-beveiliging][best-practices-pod-security]lezen.

U kunt ook [container beveiliging in Security Center][security-center-containers] gebruiken om uw containers te helpen scannen op beveiligings problemen.  Er is ook [Azure container Registry integratie][security-center-acr] met Security Center om uw installatie kopieën en het REGI ster te beschermen tegen beveiligings problemen.

## <a name="secure-the-images-and-run-time"></a>Beveiligen van de installatiekopieën en uitvoeringstijd

**Aanbevolen procedurerichtlijn** - Scan van uw containerinstallatiekopieën op beveiligingsproblemen en alleen installatiekopieën die zijn geslaagd voor de validatie te implementeren. Regelmatig de basisinstallatiekopieën en runtime voor de toepassing bijwerken en opnieuw implementeren van workloads in de AKS-cluster.

Een probleem met de acceptatie van werkbelastingen op basis van een container met het controleren van de beveiliging van installatiekopieën en runtime die wordt gebruikt om uw eigen toepassingen te bouwen. Hoe zorgt u ervoor dat u niet leiden beveiligingsproblemen in uw implementaties tot? De implementatie werk stroom moet een proces bevatten voor het scannen van container installatie kopieën met behulp van hulpprogram ma's zoals [Twistlock][twistlock] of [zeeblauw][aqua]. vervolgens mogen alleen geverifieerde installatie kopieën worden geïmplementeerd.

![Scannen en herstellen van containerinstallatiekopieën, valideren en implementeren](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In het voorbeeld van een echte, kunt u een continue integratie en continue implementatie (CI/CD)-pijplijn kunt gebruiken voor het automatiseren van de installatiekopie-scans, verificatie en -implementaties. Azure Container Registry bevat deze beveiligingslekken scanmogelijkheden.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisch maken van nieuwe installatiekopieën op updates van basisinstallatiekopieën

**Aanbevolen procedurerichtlijn** - als voor het gebruik van de basisinstallatiekopieën voor toepassingsinstallatiekopieën van, automation gebruiken om te maken van nieuwe afbeeldingen wanneer de basisinstallatiekopie wordt bijgewerkt. Als de basisinstallatiekopieën doorgaans beveiligingsverbeteringen bevatten, de containerinstallatiekopieën voor alle downstream-toepassing bijwerken

Telkens wanneer die een basisinstallatiekopie wordt bijgewerkt, moeten eventuele downstream containerinstallatiekopieën ook worden bijgewerkt. Dit bouw proces moet worden geïntegreerd in de validatie-en implementatie pijplijnen, zoals [Azure-pijp lijnen][azure-pipelines] of Jenkins. Deze pipelines zorgt ervoor dat uw toepassingen worden uitgevoerd op de bijgewerkte op basis van installatiekopieën. Zodra de containerinstallatiekopieën van uw toepassing worden gevalideerd, kunnen de AKS-implementaties voor het uitvoeren van de meest recente, beveiligde installatiekopieën worden bijgewerkt.

Azure Container Registry taken kunt containerinstallatiekopieën ook automatisch laten bijwerken wanneer de basisinstallatiekopie wordt bijgewerkt. Deze functie kunt u voor het bouwen van een klein aantal basisinstallatiekopieën en regelmatig bewaring bijgewerkt met oplossingen voor bugs en beveiliging.

Zie voor meer informatie over updates van basis installatie [kopieën automatiseren samen stellen van de basis installatie kopie met Azure container Registry taken][acr-base-image-update].

## <a name="next-steps"></a>Volgende stappen

In dit artikel is gericht op over het beveiligen van uw containers. Voor het implementeren van sommige van deze gebieden, Zie de volgende artikelen:

* [Automatische installatie kopieën automatiseren met de update van de basis installatie kopie met Azure Container Registry taken][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration