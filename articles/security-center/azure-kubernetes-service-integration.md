---
title: Azure Security Center en Azure Kubernetes-service
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
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436181"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Services-integratie met Security Center

Azure Kubernetes Service (AKS) is de beheerde service van Microsoft voor het ontwikkelen, implementeren en beheren van gecontaineriseerde toepassingen. 

Gebruik AKS samen met de standaardlaag van Azure Security Center (zie [prijzen)](security-center-pricing.md)om meer inzicht te krijgen in uw AKS-knooppunten, cloudverkeer en beveiligingsbesturingselementen.

Security Center biedt beveiligingsvoordelen voor uw AKS-clusters met behulp van gegevens die al zijn verzameld door het AKS-hoofdknooppunt. 

![Overzicht van Azure Security Center en Azure Kubernetes Service (AKS) op hoog niveau](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Samen vormen deze twee tools het beste cloud-native Kubernetes-beveiligingsaanbod. 

## <a name="benefits-of-integration"></a>Voordelen van integratie

Het gebruik van de twee diensten samen biedt:

* **Beveiligingsaanbevelingen** - Security Center identificeert uw AKS-resources en categoriseert ze: van clusters tot afzonderlijke virtuele machines. Vervolgens u beveiligingsaanbevelingen per resource bekijken. Zie voor meer informatie de aanbevelingen voor containers in de [referentielijst met aanbevelingen](recommendations-reference.md#recs-containers). 

* **Verharding van de omgeving** - Security Center bewaakt voortdurend de configuratie van uw Kubernetes-clusters en Docker-configuraties. Het genereert vervolgens beveiligingsaanbevelingen die de industrienormen weerspiegelen.

* **Run-time bescherming** - Door continue analyse van de volgende AKS-bronnen waarschuwt Security Center u voor bedreigingen en schadelijke activiteiten die worden gedetecteerd op host- *en* AKS-clusterniveau:
    * Ruwe beveiligingsgebeurtenissen, zoals netwerkgegevens en procescreatie
    * Het Kubernetes-controlelogboek

    Zie [bedreigingsbeveiliging voor Azure-containers voor](threat-protection.md#azure-containers) meer informatie

    Zie deze secties in de referentietabel waarschuwingen voor de lijst met mogelijke waarschuwingen: [waarschuwingen op clusterniveau](alerts-reference.md#alerts-akscluster) van AKS en [waarschuwingen op containerhostniveau](alerts-reference.md#alerts-containerhost).  

![Azure Security Center en Azure Kubernetes Service (AKS) in meer detail](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Sommige gegevens die door Azure Security Center zijn gescand vanuit uw Kubernetes-omgeving, kunnen gevoelige informatie bevatten.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de containerbeveiligingsfuncties van Security Center:

* [Azure Security Center en containerbeveiliging](container-security.md)

* [Integratie met Azure Container Registry](azure-container-registry-integration.md)

* [Gegevensbeheer bij Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - beschrijft het gegevensbeleid van Microsoft-services (waaronder Azure, Intune en Office 365), details over het gegevensbeheer van Microsoft en het bewaarbeleid dat van invloed is op uw gegevens
