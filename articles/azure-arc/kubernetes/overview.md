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
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309931"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Wat is Kubernetes met Azure Arc Preview?

U kunt Kubernetes-clusters binnen of buiten Azure koppelen en configureren met behulp van Kubernetes met Azure Arc Preview. Wanneer een Kubernetes-cluster is gekoppeld aan Azure Arc, wordt het weergegeven in de Azure-portal. Het heeft dan een Azure Resource Manager-id en een beheerde identiteit. Clusters zijn gekoppeld aan standaardabonnementen in Azure, bevinden zich in een resourcegroep, en kunnen labels ontvangen zoals elke andere Azure-resource. 

Voor het verbinden van een Kubernetes-cluster met Azure moet de clusterbeheerder agents implementeren. Deze agents worden uitgevoerd in een Kubernetes-naamruimte met de naam `azure-arc`, en zijn standaardimplementaties van Kubernetes. De agents zijn verantwoordelijk voor de connectiviteit met Azure, het verzamelen van logboeken en metrische gegevens van Azure Arc, en het detecteren van configuratieaanvragen. 

Kubernetes met Azure Arc biedt ondersteuning voor industriestandaard SSL om gegevens te beveiligen tijdens de overdracht. Ook worden inactieve gegevens versleuteld opgeslagen in een Azure Cosmos DB-database om de vertrouwelijkheid van gegevens te garanderen.
 
 > [!NOTE]
> Kubernetes met Azure Arc bevindt zich in de preview-fase. Dit wordt niet aanbevolen voor productieworkloads. 


## <a name="supported-scenarios"></a>Ondersteunde scenario's 

Kubernetes met Azure Arc biedt ondersteuning voor deze scenario’s: 

* Verbinding maken met Kubernetes-clusters die buiten Azure worden uitgevoerd, voor inventariseren, groeperen en taggen.

* Toepassingen implementeren en configuratie toepassen met behulp van GitOps-configuratiebeheer. 

* Azure Monitor gebruiken voor containers om uw clusters te bekijken en te bewaken. 

* Beleid toepassen met behulp van Azure Policy voor Kubernetes. 

 
## <a name="supported-regions"></a>Ondersteunde regio’s 

Kubernetes met Azure Arc wordt momenteel ondersteund in deze regio’s: 

* VS - oost 
* Europa -west 

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met een cluster](./connect-cluster.md)
