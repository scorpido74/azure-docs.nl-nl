---
title: Wat zijn rekendoelen
titleSuffix: Azure Machine Learning
description: Definieer waar u uw model wilt trainen of implementeren met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270418"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Wat zijn rekendoelen in Azure Machine Learning? 

Een **compute target** is een aangewezen compute resource/omgeving waar u uw trainingsscript uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine zijn of een cloudgebaseerde compute resource. Door rekendoelen te gebruiken, u later uw compute-omgeving wijzigen zonder dat u uw code hoeft te wijzigen.  

In een typische levenscyclus van modelontwikkeling u het:
1. Begin met het ontwikkelen en experimenteren op een kleine hoeveelheid data. In dit stadium raden we uw lokale omgeving (lokale computer of cloud-gebaseerde VM) aan als uw rekendoel. 
2. Schaal op naar grotere gegevens of doe gedistribueerde training met behulp van een van deze [trainingscomputetargets.](#train)  
3. Zodra uw model klaar is, implementeert u het naar een webhostingomgeving of IoT-apparaat met een van deze [compute-doelen voor implementatie.](#deploy)

De rekenresources die u voor uw rekendoelen gebruikt, zijn gekoppeld aan een [werkruimte.](concept-workspace.md) Andere rekenbronnen dan de lokale machine worden gedeeld door gebruikers van de werkruimte.

## <a name="training-compute-targets"></a><a name="train"></a>Rekendoelen trainen

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen.  U ook uw eigen rekenbron toevoegen, hoewel de ondersteuning voor verschillende scenario's kan variëren.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Meer informatie over [het instellen en gebruiken van een rekendoel voor modeltraining](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Implementatiedoelen

De volgende compute resources kunnen worden gebruikt om uw modelimplementatie te hosten.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Meer informatie over [waar en hoe u uw model implementeren in een rekendoel.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (beheerd)

Een beheerde compute resource wordt gemaakt en beheerd door Azure Machine Learning. Deze compute is geoptimaliseerd voor machine learning workloads. Azure Machine Learning compute clusters en [compute instances](concept-compute-instance.md) zijn de enige beheerde computes. In de toekomst kunnen extra beheerde rekenresources worden toegevoegd.

U Azure Machine Learning compute instances (preview) of compute clusters maken in:

| | Azure Machine Learning Studio | Azure Portal | SDK | Resource Manager-sjabloon | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Rekenproces | ja | ja | ja | ja |  |
| Compute-cluster | ja | ja | ja | ja | ja |

Wanneer deze compute resources worden gemaakt, maken ze automatisch deel uit van uw werkruimte in tegenstelling tot andere soorten rekendoelen.

### <a name="compute-clusters"></a>Compute clusters

U Azure Machine Learning-compute clusters gebruiken voor training en voor batch-inferencing (preview).  Met deze compute resource hebt u het:

* Cluster met één of meerdere node
* Automatisch schalen elke keer dat u een run indient 
* Automatisch clusterbeheer en taakplanning 
* Ondersteuning voor zowel CPU- als GPU-bronnen



## <a name="unmanaged-compute"></a>Onbeheerde gegevens

Een onbeheerd compute-doel wordt *niet* beheerd door Azure Machine Learning. U maakt dit type compute-doel buiten Azure Machine Learning en koppelt het vervolgens aan uw werkruimte. Onbeheerde rekenresources kunnen extra stappen vereisen om de prestaties voor machine learning-workloads te behouden of te verbeteren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Een rekendoel instellen om uw model te trainen](how-to-set-up-training-targets.md)
* [Uw model implementeren op een rekendoel](how-to-deploy-and-where.md)