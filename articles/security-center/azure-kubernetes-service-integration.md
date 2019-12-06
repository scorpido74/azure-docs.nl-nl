---
title: Azure Security Center en Azure Kubernetes service | Microsoft Docs
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
ms.openlocfilehash: 2a5d01978d8a4883d760e7ecf84afa381dfd563d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868555"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integratie van Azure Kubernetes Services met Security Center (preview-versie)
Azure Kubernetes service (AKS) is de beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers. 

Gebruik AKS in combi natie met de standaardlaag van Azure Security Center (Zie [prijzen](security-center-pricing.md)) voor een diep gaande zicht baarheid van uw AKS knooppunten, Cloud verkeer en beveiligings controles.

Security Center zorgt voor een afdoende beveiliging voor uw AKS-clusters met behulp van gegevens die al zijn verzameld door het hoofd knooppunt AKS. 

![Overzicht op hoog niveau van Azure Security Center en Azure Kubernetes service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Deze twee hulp middelen vormen samen de best mogelijke Cloud-native Kubernetes-beveiligings aanbieding. 

## <a name="benefits-of-integration"></a>Voor delen van integratie

Het gebruik van de twee services biedt samen:

* **Aanbevelingen voor beveiliging** -Security Center identificeert uw AKS-resources en categoriseert deze: van clusters tot afzonderlijke virtuele machines. U kunt vervolgens beveiligings aanbevelingen per resource bekijken. Zie [beveiligings aanbevelingen implementeren](security-center-recommendations.md)voor meer informatie. 

    > [!NOTE]
    > Als de naam van een Security Center aanbeveling eindigt met een tag (preview), wordt verwezen naar de preview-aard van de aanbeveling; niet de functie.

* **Environment hardening** -Security Center bewaakt voortdurend de configuratie van uw Kubernetes-clusters en genereert beveiligings aanbevelingen die de industrie normen weer spie gelen.

* **Run-time beveiliging** : door doorlopende analyse van de volgende AKS bronnen, Security Center u op de hoogte van bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het cluster niveau host *en* AKS (Zie voor meer informatie [detectie van bedreigingen voor Azure-containers](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * Onbewerkte beveiligings gebeurtenissen, zoals netwerk gegevens en het maken van processen
    * Het Kubernetes-controle logboek

![Azure Security Center en Azure Kubernetes service (AKS) meer details](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Sommige van de gegevens die worden gescand door Azure Security Center in uw Kubernetes-omgeving kunnen gevoelige informatie bevatten.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Container Registry](azure-container-registry-integration.md)

* [Beveiliging van virtuele machines](security-center-virtual-machine-protection.md) -Hiermee worden de aanbevelingen van Security Center beschreven

* [Gegevens beheer bij micro soft](https://www.microsoft.com/trust-center/privacy/data-management) : beschrijft het gegevens beleid van micro soft-Services (inclusief Azure, intune en Office 365), Details van het gegevens beheer van micro soft en het Bewaar beleid dat van invloed is op uw gegevens