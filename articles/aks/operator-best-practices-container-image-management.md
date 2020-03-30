---
title: Aanbevolen procedures voor operatoren - Beheer van containerafbeeldingen in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor de clusteroperator voor het beheren en beveiligen van containerafbeeldingen in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594739"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor beheer en beveiliging van containerafbeeldingen in Azure Kubernetes Service (AKS)

Bij het ontwikkelen en uitvoeren van toepassingen in Azure Kubernetes Service (AKS) is de beveiliging van uw containers en containerafbeeldingen een belangrijke overweging. Containers die verouderde basisafbeeldingen of ongepatchte toepassingskeer bevatten, brengen een beveiligingsrisico en mogelijke aanvalsvector met zich mee. Om deze risico's te minimaliseren, moet u tools integreren die problemen in uw containers scannen en oplossen tijdens het bouwen en uitvoeren. Hoe eerder in het proces de kwetsbaarheid of verouderde basisafbeelding wordt gevangen, hoe veiliger het cluster. In dit artikel worden *onder containers* zowel de containerafbeeldingen die zijn opgeslagen in een containerregister als de lopende containers.

Dit artikel richt zich op het beveiligen van uw containers in AKS. Procedures voor:

> [!div class="checklist"]
> * Beeldkwetsbaarheden scannen en herstellen
> * Containerafbeeldingen automatisch activeren en opnieuw implementeren wanneer een basisafbeelding wordt bijgewerkt

U ook de aanbevolen procedures voor [clusterbeveiliging][best-practices-cluster-security] en [podbeveiliging][best-practices-pod-security]lezen.

U [containerbeveiliging][security-center-containers] ook gebruiken in Security Center om uw containers te helpen scannen op kwetsbaarheden.  Er is ook [Azure Container Registry-integratie][security-center-acr] met Security Center om uw afbeeldingen en registratie te beschermen tegen kwetsbaarheden.

## <a name="secure-the-images-and-run-time"></a>Beveilig de afbeeldingen en de looptijd

**Richtlijnen voor aanbevolen procedures** : Scan uw containerafbeeldingen op kwetsbaarheden en implementeer alleen afbeeldingen die zijn gevalideerd. Werk regelmatig de basisafbeeldingen en de runtime van de toepassing bij en implementeer workloads vervolgens opnieuw in het AKS-cluster.

Een punt van zorg met de goedkeuring van container-gebaseerde workloads is het verifiëren van de veiligheid van beelden en runtime gebruikt om uw eigen toepassingen te bouwen. Hoe zorg je ervoor dat je geen beveiligingsproblemen in je implementaties introduceert? Uw implementatieworkflow moet een proces bevatten om containerafbeeldingen te scannen met behulp van hulpprogramma's zoals [Twistlock][twistlock] of [Aqua,][aqua]en vervolgens alleen geverifieerde afbeeldingen kunnen implementeren.

![Containerafbeeldingen scannen en herstellen, valideren en implementeren](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In een voorbeeld in de praktijk u een CI/CD-pijplijn (continuous integration and continuous deployment) gebruiken om de beeldscans, verificatie en implementaties te automatiseren. Azure Container Registry bevat deze beveiligingsmogelijkheden voor het scannen van kwetsbaarheden.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisch nieuwe afbeeldingen bouwen op basisafbeeldingsupdate

**Richtlijnen voor aanbevolen procedures** - Terwijl u basisafbeeldingen gebruikt voor toepassingsafbeeldingen, gebruikt u automatisering om nieuwe afbeeldingen te maken wanneer de basisafbeelding wordt bijgewerkt. Aangezien deze basisafbeeldingen doorgaans beveiligingsoplossingen bevatten, werkt u eventuele downstream-toepassingscontainerafbeeldingen bij.

Elke keer dat een basisafbeelding wordt bijgewerkt, moeten eventuele downstreamcontainerafbeeldingen ook worden bijgewerkt. Dit bouwproces moet worden geïntegreerd in validatie- en implementatiepijplijnen zoals [Azure Pipelines][azure-pipelines] of Jenkins. Deze pijplijnen zorgen ervoor dat uw toepassingen blijven draaien op de bijgewerkte afbeeldingen. Zodra de afbeeldingen van uw toepassingscontainer zijn gevalideerd, kunnen de AKS-implementaties worden bijgewerkt om de nieuwste, veilige afbeeldingen uit te voeren.

Azure Container Registry Tasks kunnen ook automatisch containerafbeeldingen bijwerken wanneer de basisafbeelding wordt bijgewerkt. Met deze functie u een klein aantal basisafbeeldingen bouwen en deze regelmatig up-to-date houden met bug- en beveiligingsoplossingen.

Zie [Image builds automatiseren op basisimage-update met Azure Container Registry Tasks][acr-base-image-update]voor meer informatie over basisafbeeldingsupdates.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op het beveiligen van uw containers. Zie de volgende artikelen om een aantal van deze gebieden te implementeren:

* [Beeldbuilds automatiseren op basisimage-update met Azure Container Registry Tasks][acr-base-image-update]

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