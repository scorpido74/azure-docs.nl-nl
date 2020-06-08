---
title: Overzicht van Kubernetes met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665273"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Wat is Kubernetes met Azure Arc (preview)?

U kunt Kubernetes-clusters binnen of buiten Azure koppelen en configureren met Kubernetes met Azure Arc (preview). Wanneer een Kubernetes-cluster is gekoppeld aan Azure Arc, wordt het weergegeven in de Azure-portal, en krijgt het een Azure Resource Manager-id en een beheerde identiteit. Clusters zijn gekoppeld aan standaardabonnementen in Azure, zijn live in een resourcegroep, en kunnen labels ontvangen zoals elke andere Azure-resource. 


Voor het verbinden van een Kubernetes-cluster met Azure moet een clusterbeheerder agents implementeren. Deze agents worden uitgevoerd in een Kubernetes-naamruimte met de naam `azure-arc`, en zijn standaardimplementaties van Kubernetes. De agents zijn verantwoordelijk voor de connectiviteit met Azure, het verzamelen van logboeken en metrische gegevens van Azure Arc, en het detecteren van configuratieaanvragen.  
 
 > [!NOTE]
> Kubernetes met Azure Arc bevindt zich in de preview-fase en wordt niet aanbevolen voor workloads in een productieomgeving. 


## <a name="supported-scenarios"></a>Ondersteunde scenario's 

Kubernetes met Azure Arc biedt ondersteuning voor de volgende scenario’s: 

* Verbinding maken met Kubernetes-clusters die buiten Azure worden uitgevoerd, voor inventariseren, groeperen en taggen 

* Toepassingen implementeren en configuratie toepassen met behulp van GitOps-configuratiebeheer 

* Azure Monitor gebruiken voor containers om uw clusters te bekijken en te bewaken 

* Beleid toepassen met behulp van Azure Policy voor Kubernetes 

 
## <a name="supported-regions"></a>Ondersteunde regio's 

Kubernetes met Azure Arc wordt momenteel ondersteund in de volgende regio’s: 

* VS - oost 
* Europa -west 


## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met een cluster](./connect-cluster.md)
