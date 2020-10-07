---
title: Overzicht van Kubernetes met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: In dit artikel vindt u een overzicht van Kubernetes met Azure Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.custom: references_regions
ms.openlocfilehash: 61317f7f5f2bf17c88fc019294574993c1854e59
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540639"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Wat is Kubernetes met Azure Arc Preview?

U kunt Kubernetes-clusters binnen of buiten Azure koppelen en configureren met behulp van Kubernetes met Azure Arc Preview. Wanneer een Kubernetes-cluster is gekoppeld aan Azure Arc, wordt het weergegeven in de Azure-portal. Het heeft dan een Azure Resource Manager-id en een beheerde identiteit. Clusters zijn gekoppeld aan standaardabonnementen in Azure, bevinden zich in een resourcegroep, en kunnen labels ontvangen zoals elke andere Azure-resource. 

Voor het verbinden van een Kubernetes-cluster met Azure moet de clusterbeheerder agents implementeren. Deze agents worden uitgevoerd in een Kubernetes-naamruimte met de naam `azure-arc`, en zijn standaardimplementaties van Kubernetes. De agents zijn verantwoordelijk voor de connectiviteit met Azure, het verzamelen van logboeken en metrische gegevens van Azure Arc, en het detecteren van configuratieaanvragen. 

Kubernetes met Azure Arc biedt ondersteuning voor industriestandaard SSL om gegevens te beveiligen tijdens de overdracht. Ook worden inactieve gegevens versleuteld opgeslagen in een Azure Cosmos DB-database om de vertrouwelijkheid van gegevens te garanderen.
 
> [!NOTE]
> Kubernetes met Azure Arc is beschikbaar als preview-versie. Dit wordt niet aanbevolen voor productieworkloads.

## <a name="supported-kubernetes-distributions"></a>Ondersteunde Kubernetes-distributies

Kubernetes met Azure Arc werkt met elk CNCF-gecertificeerd (Cloud Native Computing Foundation) Kubernetes-cluster zoals AKS-engine op Azure, AKS-engine op Azure Stack Hub, GKE, EKS en VMware vSphere.

De functies van Kubernetes met Azure Arc zijn door het Arc-team getest op de volgende distributies:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS-engine
* AKS-engine op Azure Stack Hub
* Cluster API Provider Azure

## <a name="supported-scenarios"></a>Ondersteunde scenario's 

Kubernetes met Azure Arc biedt ondersteuning voor deze scenario’s: 

* Verbinding maken met Kubernetes-clusters die buiten Azure worden uitgevoerd, voor inventariseren, groeperen en taggen.

* Toepassingen implementeren en configuratie toepassen met behulp van GitOps-configuratiebeheer. 

* Azure Monitor gebruiken voor containers om uw clusters te bekijken en te bewaken. 

* Beleid toepassen met behulp van Azure Policy voor Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Ondersteunde regio’s 

Kubernetes met Azure Arc wordt momenteel ondersteund in deze regio’s: 

* VS - oost 
* Europa -west

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* Wat is het verschil tussen Kubernetes met Azure Arc en Azure Kubernetes Service (AKS)?

    Azure Kubernetes Service (AKS) is de beheerde Kubernetes-service van Azure. Met AKS kunt u eenvoudig een ​​beheerd Kubernetes-cluster in Azure implementeren. AKS verkleint de complexiteit en de operationele overhead die gepaard gaan met het beheer van Kubernetes door veel van deze taken naar Azure over te hevelen. De Kubernetes-modellen worden beheerd door Azure. U beheert en onderhoudt alleen de agentknooppunten.

    Met Kubernetes met Azure Arc kunt u Kubernetes-clusters verbinden met Azure om de beheerfunctionaliteit van Azure uit te breiden met bijvoorbeeld Azure Monitor en Azure Policy. Het onderhoud van het onderliggende Kubernetes-cluster voert u zelf uit.

* Moet ik mijn Azure Kubernetes Service-clusters die worden uitgevoerd op Azure, verbinden met Azure Arc?

    Nee. Alle functies van Kubernetes met Azure Arc, zoals Azure Monitor en Azure Policy (Gatekeeper), zijn standaard beschikbaar met AKS, die al een resourceweergave in Azure heeft. Clusterconfiguratie (GitOps) is ook standaard beschikbaar op AKS, momenteel als beperkte preview. Gebruik dit [registratieformulier](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5acO18Lmx5Bk_qao2CrOcFUQ0UyRllDR1BEV1BPNENYRERYN1pFWTQ4WC4u) om toegang te krijgen tot deze functie.
    
* Moet ik mijn AKS-cluster op Azure Stack HCI verbinden met Azure Arc? En hoe zit het met Kubernetes-clusters die worden uitgevoerd op Azure Stack Hub of Azure Stack Engine?

    Ja, het verbinden van deze clusters met Azure Arc heeft voordelen. U beschikt hiermee over een resourceweergave voor deze Kubernetes-clusters in Azure Resource Manager. Met deze resourceweergave kunnen functies zoals Clusterconfiguratie, Azure Monitor, Azure Policy (Gatekeeper) worden uitgebreid naar deze Kubernetes-clusters

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met een cluster](./connect-cluster.md)
