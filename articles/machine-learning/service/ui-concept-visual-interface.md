---
title: Visuele interface
titleSuffix: Azure Machine Learning
description: Meer informatie over de voor waarden, concepten en werk stromen waaruit de visuele interface (preview) van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128809"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Wat is de visuele interface voor Azure Machine Learning? 

Met de Visual Interface (preview) voor Azure Machine Learning kunt u gegevens voorbereiden, machine learning modellen trainen, testen, implementeren, beheren en bijhouden zonder code te schrijven.

Er is geen programmering vereist. u kunt [gegevens sets](#dataset) en [modules](#module) visueel verbinden om uw model samen te stellen.

De visuele interface gebruikt uw Azure Machine Learning- [werk ruimte](concept-workspace.md) om het volgende te doen:

+ Schrijf artefacten van [experiment](#experiment) -uitvoeringen in de werk ruimte.
+ Access- [gegevens sets](#dataset).
+ Gebruik de [reken resources](#compute) in de werk ruimte om het experiment uit te voeren. 
+ [Modellen](concept-azure-machine-learning-architecture.md#models)registreren.
+ [Implementeer](#deployment) modellen als webservices op reken resources in de werk ruimte.

![Overzicht van de visuele interface](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Werkstroom

De visuele interface biedt u een interactief, visueel canvas om snel een model te bouwen, te testen en te herhalen. 

+ U kunt [modules](#module) slepen en neerzetten op het canvas.
+ Verbind de modules samen om een [experiment](#experiment)te vormen.
+ Voer het experiment uit met behulp van de reken resource van de werk ruimte Machine Learning service.
+ Herhaal het ontwerp van het model door het experiment te bewerken en het opnieuw uit te voeren.
+ Wanneer u klaar bent, kunt u uw **trainings experiment** converteren naar een **voorspellend experiment**.
+ [Implementeer](#deployment) het voorspellende experiment als een webservice, zodat het model door anderen kan worden geopend.

## <a name="experiment"></a>Experiment

Maak een volledig experiment of gebruik een bestaand voorbeeld experiment als sjabloon.  Telkens wanneer u een experiment uitvoert, worden artefacten opgeslagen in uw werk ruimte.

Een experiment bestaat uit gegevens sets en analytische modules, waarmee u samen verbinding maakt om een model te maken. Een geldig experiment heeft de volgende kenmerken:

* Gegevens sets kunnen alleen worden verbonden met modules.
* Modules kunnen worden verbonden met gegevens sets of andere modules.
* Alle invoer poorten voor modules moeten een verbinding met de gegevens stroom hebben.
* Alle vereiste para meters voor elke module moeten worden ingesteld.


Zie [zelf studie voor meer informatie over hoe u aan de slag kunt gaan met de visuele interface: Prijs voor auto Mobile voor speld met](ui-tutorial-automobile-price-train-score.md)de visuele interface.

## <a name="dataset"></a>Gegevensset

Een gegevensset is gegevens die zijn geüpload naar de visuele interface voor gebruik in het model proces. Er zijn een aantal voorbeeld gegevens sets voor u opgenomen waarmee u kunt experimenteren met, en het uploaden van meer gegevens sets wanneer u ze nodig hebt.

## <a name="module"></a>Module

Een module is een algoritme dat u met uw gegevens kunt uitvoeren. De visuele interface bevat een aantal modules, variërend van de functies voor het inkomen van gegevens tot trainings-, Score-en validatie processen.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de para meters van de module weer gegeven in het deel venster Eigenschappen rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen.

![Eigenschappen van module](media/ui-concept-visual-interface/properties.png)

Zie voor meer informatie over de beschik bare bibliotheek met machine learning algoritmen [overzicht van algoritme & module](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Reken resources

Gebruik reken resources van uw werk ruimte om uw experiment uit te voeren of uw geïmplementeerde modellen als webservices te hosten. De ondersteunde compute-doelen zijn:


| COMPUTE-doel | Training | Implementatie |
| ---- |:----:|:----:|
| Azure Machine Learning-Computing | ✓ | |
| Azure Kubernetes Service | | ✓ |

Reken doelen zijn gekoppeld aan uw Machine Learning- [werk ruimte](concept-workspace.md). U beheert uw reken doelen in uw werk ruimte in de [Azure Portal](https://portal.azure.com) of in de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com).

## <a name="deployment"></a>Implementatie

Zodra uw predictive analytics model klaar is, implementeert u dit als een webservice rechtstreeks vanuit de visuele interface.

De webservices bieden een interface tussen een toepassing en uw score model. Een externe toepassing kan in real-time communiceren met het score model. Een aanroep van een webservice retourneert Voorspellings resultaten naar een externe toepassing. Tijdens een aanroep aan een webservice wordt een API-sleutel doorgegeven die is gemaakt tijdens de implementatie van de webservice. De webservice is gebaseerd op REST, een populaire architectuur keuze voor webprogrammeer projecten.

Zie [zelf studie voor meer informatie over het implementeren van uw model: Implementeer een machine learning model met de visuele interface](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Volgende stappen

* Leer de basis beginselen van Predictive Analytics en machine learning [met de zelf studie: Prijs voor auto Mobile voors pellen met de visuele interface](ui-tutorial-automobile-price-train-score.md)
* Gebruik een van de voor beelden en wijzig uw behoeften:
    * [Voor beeld 1-regressie: Prijs voors pellen](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Voor beeld 2-regressie: Prijs voors pellen en algoritmen vergelijken](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Voor beeld 3-classificatie: Krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Voor beeld 5-classificatie: Verloop, verlangen en up-sell voors pellen](how-to-ui-sample-classification-predict-churn.md)
