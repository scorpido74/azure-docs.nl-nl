---
title: 'Compute-doelen: voor het trainen en implementeren van modellen'
titleSuffix: Azure Machine Learning service
description: Bepaal waar u uw model wilt trainen of implementeren met Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: b2e4c9160ccad66b04dd30979f0568da68836d76
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860584"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Wat zijn Compute-doelen in Azure Machine Learning service? 

Een **Compute-doel** is een aangewezen reken resource/omgeving waar u uw trainings script uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine of een cloud-gebaseerde reken resource zijn. Met Compute-doelen kunt u op een later tijdstip uw berekenings omgeving wijzigen zonder dat u de code hoeft te wijzigen.  

In een typische levens cyclus voor model ontwikkeling kunt u het volgende doen:
1. Begin met het ontwikkelen en experimenteren van een kleine hoeveelheid gegevens. In deze fase raden wij uw lokale omgeving (lokale computer of Cloud-VM) aan als uw reken doel. 
2. Schaal omhoog naar grotere gegevens of voer gedistribueerde training uit met behulp van een van deze [trainings Compute-doelen](#train).  
3. Zodra uw model klaar is, implementeert u het in een webhost of IoT-apparaat met een van deze doelen voor de [implementatie](#deploy).

De reken resources die u voor uw reken doelen gebruikt, worden aan een [werk ruimte](concept-workspace.md)gekoppeld. Andere reken bronnen dan de lokale computer worden gedeeld door gebruikers van de werk ruimte.

## <a name="train"></a>Doelen van de trainings compute

Azure Machine Learning-service heeft verschillende ondersteunings bronnen.  U kunt ook uw eigen reken resource toevoegen, maar ondersteuning voor verschillende scenario's kan variëren.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Meer informatie over [het instellen en gebruiken van een berekenings doel voor model training](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Implementatie doelen

De volgende reken bronnen kunnen worden gebruikt voor het hosten van uw model implementatie.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Meer informatie over [waar en hoe u uw model implementeert in een compute-doel](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (beheerd)

Een beheerde Compute-resource wordt gemaakt en beheerd door Azure Machine Learning service. Deze berekening is geoptimaliseerd voor machine learning workloads. Azure Machine Learning Compute is de enige beheerde reken kracht vanaf 30 mei 2019. Extra beheerde reken bronnen kunnen in de toekomst worden toegevoegd.

U kunt Azure machine learning Compute for training en voor batch-(preview-versie) gebruiken.  Met deze reken resource hebt u het volgende:

* Cluster met één of meerdere knoop punten
* Automatisch schalen elke keer dat u een uitvoering verzendt 
* Automatische Cluster beheer en taak planning 
* Ondersteuning voor zowel CPU-als GPU-resources

U kunt Azure Machine Learning Compute-instanties maken in Azure Portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com), met de SDK of met de cli. Wanneer het wordt gemaakt, maakt het automatisch deel uit van uw werk ruimte, in tegens telling tot andere soorten Compute-doelen.

## <a name="unmanaged-compute"></a>Onbeheerde compute

Een niet-beheerd reken doel wordt *niet* beheerd door Azure machine learning service. U maakt dit type reken doel buiten Azure Machine Learning en koppelt het vervolgens aan uw werk ruimte. Onbeheerde reken resources kunnen extra stappen vereisen om de prestaties van machine learning werk belastingen te hand haven of te verbeteren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Een reken doel instellen om uw model te trainen](how-to-set-up-training-targets.md)
* [Uw model implementeren naar een berekenings doel](how-to-deploy-and-where.md)