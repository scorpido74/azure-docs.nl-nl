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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800171"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integratie van Azure Kubernetes Services met Security Center

Azure Kubernetes service (AKS) is de beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers. 

Als u zich in de Standard-laag van Azure Security Center bevindt, kunt u de AKS bundel ( [prijzen](security-center-pricing.md)bekijken) toevoegen om dieper inzicht te krijgen in uw AKS knooppunten, Cloud verkeer en beveiligings controles.

Security Center en AKS vormen samen de beste Cloud-native Kubernetes-beveiligings aanbieding.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Wat zijn de onderdelen van de Kubernetes-beveiliging van Security Center?

Security Center beveiligingen voor Kubernetes worden gegeven door een combi natie van twee elementen:

- **Azure Security Center bedreigings beveiliging voor virtuele machines** : met behulp van dezelfde log Analytics agent die Security Center gebruikt op andere vm's, kan Security Center u beveiligings problemen laten zien die zich voordoen op uw AKS-knoop punten. De agent bewaakt ook voor container-specifieke analyses.

- **Azure Security Center optionele Kubernetes bundel** : de Kubernetes-bundel ontvangt logboeken en gegevens van het subsysteem Kubernetes via de AKS-service. Deze logboeken zijn al beschikbaar in azure via de AKS-service. Wanneer u de Kubernetes-bundel van Security Center inschakelt, verleent u Security Center toegang tot de logboeken. Security Center zorgt er daarom voor dat uw AKS-clusters worden beveiligd met gegevens die al zijn verzameld door het hoofd knooppunt AKS. Sommige van de gegevens die worden gescand door Azure Security Center in uw Kubernetes-omgeving kunnen gevoelige informatie bevatten.

    ![Overzicht op hoog niveau van Azure Security Center en Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Welke beveiligingen worden er gegeven?

Het gebruik van de twee services biedt samen:

* **Aanbevelingen voor beveiliging** -Security Center identificeert uw AKS-resources en categoriseert deze: van clusters tot afzonderlijke virtuele machines. U kunt vervolgens beveiligings aanbevelingen per resource bekijken. Zie aanbevelingen voor containers in de [lijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie. 

* **Omgevings beveiliging** -Security Center continu de configuratie van uw Kubernetes-clusters en docker-configuraties bewaakt. Vervolgens worden beveiligings aanbevelingen gegenereerd die de industrie normen weer spie gelen.

* **Run-time beveiliging** : door doorlopende analyse van de volgende AKS-bronnen, Security Center u naar bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het cluster niveau host *en* AKS. Meer [informatie over bedreigings beveiliging voor containers](threat-protection.md#azure-containers).


     

![Azure Security Center en Azure Kubernetes service (AKS) meer details](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS met veelgestelde vragen over Security Center

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan ik nog steeds AKS-beveiligingen krijgen zonder de Log Analytics-agent?

Zoals hierboven vermeld, biedt de optionele Kubernetes bundel beveiligingen op cluster niveau, de Log Analytics agent van Azure Security Center Standard-laag uw knoop punten te beveiligen. 

We raden u aan beide te implementeren voor de meest volledige beveiliging.

Als u ervoor kiest om de agent niet op uw hosts te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Container Registry](azure-container-registry-integration.md)

* [Gegevens beheer bij micro soft](https://www.microsoft.com/trust-center/privacy/data-management) : beschrijft het gegevens beleid van micro soft-Services (inclusief Azure, intune en Microsoft 365), Details van het gegevens beheer van micro soft en het Bewaar beleid dat van invloed is op uw gegevens
