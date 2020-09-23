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
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894932"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integratie van Azure Kubernetes Services met Security Center

Azure Kubernetes service (AKS) is de beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers. 

Schakel Azure **Defender voor Kubernetes** in om dieper inzicht te krijgen in uw AKS-knoop punten, Cloud verkeer en beveiligings controles.

Security Center en AKS vormen samen de beste Cloud-native Kubernetes-beveiligings aanbieding.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Wat zijn de onderdelen van de Kubernetes-beveiliging van Security Center?

Security Center beveiligingen voor Kubernetes worden gegeven door een combi natie van twee elementen:

- **Azure Security Center bedreigings beveiliging voor virtuele machines** : met behulp van dezelfde log Analytics agent die Security Center gebruikt op andere vm's, kan Security Center u beveiligings problemen laten zien die zich voordoen op uw AKS-knoop punten. De agent bewaakt ook voor container-specifieke analyses.

- Het **optionele Azure Defender for Kubernetes-abonnement van Azure Security Center** : het Kubernetes-abonnement ontvangt logboeken en informatie van het subsysteem Kubernetes via de AKS-service. Deze logboeken zijn al beschikbaar in azure via de AKS-service. Wanneer u Azure Defender voor Kubernetes inschakelt, verleent u Security Center toegang tot de logboeken. Security Center zorgt er daarom voor dat uw AKS-clusters worden beveiligd met gegevens die al zijn verzameld door het hoofd knooppunt AKS. Sommige van de gegevens die worden gescand door Azure Security Center in uw Kubernetes-omgeving kunnen gevoelige informatie bevatten.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Container Registry](azure-container-registry-integration.md)

* [Gegevens beheer bij micro soft](https://www.microsoft.com/trust-center/privacy/data-management) : beschrijft het gegevens beleid van micro soft-Services (inclusief Azure, intune en Microsoft 365), Details van het gegevens beheer van micro soft en het Bewaar beleid dat van invloed is op uw gegevens
