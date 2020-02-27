---
title: Azure Security Center-en Azure Kubernetes-service
description: Meer informatie over de integratie van Azure Security Center met Azure Kubernetes Services
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 0743499b019bd1c7b985636e886eee9352284a55
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616076"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integratie van Azure Kubernetes Services met Security Center (preview-versie)
Azure Kubernetes service (AKS) is de beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers. 

Gebruik AKS in combi natie met de standaardlaag van Azure Security Center (Zie [prijzen](security-center-pricing.md)) voor een diep gaande zicht baarheid van uw AKS knooppunten, Cloud verkeer en beveiligings controles.

Security Center zorgt voor een afdoende beveiliging voor uw AKS-clusters met behulp van gegevens die al zijn verzameld door het hoofd knooppunt AKS. 

![Overzicht op hoog niveau van Azure Security Center en Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Deze twee hulp middelen vormen samen de best mogelijke Cloud-native Kubernetes-beveiligings aanbieding. 

## <a name="benefits-of-integration"></a>Voor delen van integratie

Het gebruik van de twee services biedt samen:

* **Aanbevelingen voor beveiliging** -Security Center identificeert uw AKS-resources en categoriseert deze: van clusters tot afzonderlijke virtuele machines. U kunt vervolgens beveiligings aanbevelingen per resource bekijken. Zie aanbevelingen voor containers in de [lijst met aanbevelingen](recommendations-reference.md#recs-computeapp)voor meer informatie. 

    > [!NOTE]
    > Als de naam van een Security Center aanbeveling eindigt met een tag (preview), wordt deze verwezen naar de preview-aard van de aanbeveling, niet de functie.

* **Omgevings beveiliging** -Security Center continu de configuratie van uw Kubernetes-clusters en docker-configuraties bewaakt. Vervolgens worden beveiligings aanbevelingen gegenereerd die de industrie normen weer spie gelen.

* **Run-time beveiliging** : door doorlopende analyse van de volgende AKS bronnen, Security Center u op de hoogte van bedreigingen en schadelijke activiteiten die zijn gedetecteerd op de host *en* het AKS-cluster niveau:
    * Onbewerkte beveiligings gebeurtenissen, zoals netwerk gegevens en het maken van processen
    * Het Kubernetes-controle logboek

    Zie [Threat Protection for Azure containers](threat-protection.md#azure-containers) (Engelstalig) voor meer informatie.

    Zie voor de lijst met mogelijke waarschuwingen deze secties in de naslag tabel waarschuwingen: [waarschuwingen op AKS-niveau](alerts-reference.md#alerts-akscluster) en [waarschuwingen op hostniveau op container](alerts-reference.md#alerts-containerhost)niveau.  

![Azure Security Center en Azure Kubernetes service (AKS) meer details](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Sommige van de gegevens die worden gescand door Azure Security Center in uw Kubernetes-omgeving kunnen gevoelige informatie bevatten.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Container Registry](azure-container-registry-integration.md)

* [Gegevens beheer bij micro soft](https://www.microsoft.com/trust-center/privacy/data-management) : beschrijft het gegevens beleid van micro soft-Services (inclusief Azure, intune en Office 365), Details van het gegevens beheer van micro soft en het Bewaar beleid dat van invloed is op uw gegevens