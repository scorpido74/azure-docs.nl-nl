---
title: ML-modellen bouwen met Designer
titleSuffix: Azure Machine Learning
description: Meer informatie over de termen, concepten en werk stromen die samen de ontwerper vormen voor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517863"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Wat is Azure Machine Learning Designer (preview)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Met de ontwerp functie voor Azure Machine Learning kunt u gegevens voorbereiden, machine learning modellen trainen, testen, implementeren, beheren en bijhouden zonder code te schrijven.

Er is geen programmering vereist. u kunt [gegevens sets](#datasets) en [modules](#module) visueel verbinden om uw model samen te stellen.

De ontwerp functie maakt gebruik van uw Azure Machine Learning- [werk ruimte](concept-workspace.md) voor het volgende:

+ [Pijp lijnen](#pipeline) maken, bewerken en uitvoeren in de werk ruimte.
+ Access- [gegevens sets](#datasets).
+ Gebruik de [reken resources](#compute) in de werk ruimte om de pijp lijn uit te voeren. 
+ [Modellen](concept-azure-machine-learning-architecture.md#models)registreren.
+ [Publiceer](#publish) pijp lijnen als rest-eind punten.
+ [Implementeer](#deployment) modellen als pijplijn eindpunten (voor batch deinterferentie) of realtime-eind punten op reken resources in de werk ruimte.

![Overzicht van de ontwerp functie](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Werkstroom

De Designer biedt u een interactief, visueel canvas om snel een model te bouwen, te testen en te herhalen. 

+ U kunt [gegevens sets](#datasets) en [modules](#module) slepen en neerzetten op het canvas.
+ Verbind de modules samen om een [pijp lijn](#pipeline)te vormen.
+ Voer de pijp lijn uit met behulp van de reken resource van de werk ruimte Machine Learning service.
+ Herhaal uw model ontwerp door de pijp lijn te bewerken en opnieuw uit te voeren.
+ Wanneer u klaar bent, kunt u uw trainings pijplijn converteren naar een **pijp lijn**voor een **detrainer** .
+ [Publiceer](#publish) uw pijp lijn als een rest-eind punt als u deze opnieuw wilt verzenden zonder de python-code hebt gebouwd.
+ [Implementeer](#deployment) de pijp lijn voor het afnemen van een pipeline-eind punt of realtime-eind punt zodat uw model kan worden geopend door anderen.

## <a name="pipeline"></a>Pijplijn

Maak een geheel nieuwe ML [pijp lijn](concept-azure-machine-learning-architecture.md#ml-pipelines) of gebruik een bestaande voorbeeld pijplijn als sjabloon. Telkens wanneer u een pijp lijn uitvoert, worden artefacten opgeslagen in uw werk ruimte. Pijplijn uitvoeringen worden gegroepeerd in [experimenten](concept-azure-machine-learning-architecture.md#experiments).

Een pijp lijn bestaat uit gegevens sets en analytische modules, waarmee u verbinding maakt om een model samen te stellen. Met name een geldige pijp lijn heeft deze kenmerken:

* Gegevens sets kunnen alleen worden verbonden met modules.
* Modules kunnen worden verbonden met gegevens sets of andere modules.
* Alle invoer poorten voor modules moeten een verbinding met de gegevens stroom hebben.
* Alle vereiste para meters voor elke module moeten worden ingesteld.


Voor meer informatie over hoe u aan de slag kunt gaan met de ontwerp functie, Zie [zelf studie: prijs van auto Mobile met de ontwerp functie](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Gegevenssets

Met een machine learning-gegevensset kunt u eenvoudig toegang krijgen tot uw gegevens en deze gebruiken. De ontwerper bevat een aantal voorbeeld gegevens sets waarmee u kunt experimenteren. U kunt meer gegevens sets [registreren](./how-to-create-register-datasets.md) wanneer u ze nodig hebt.

## <a name="module"></a>Module

Een module is een algoritme dat u met uw gegevens kunt uitvoeren. De ontwerper heeft een aantal modules die variëren van de functies voor het inkomen van gegevens in trainings processen voor training, waardering en validatie.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de para meters van de module weer gegeven in het deel venster Eigenschappen rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen.

![Eigenschappen van module](media/ui-concept-visual-interface/properties.png)

Zie voor meer informatie over de beschik bare bibliotheek met machine learning algoritmen [overzicht van algoritme & module](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Reken resources

Gebruik reken resources van uw werk ruimte om uw pijp lijn uit te voeren en uw geïmplementeerde modellen als realtime-eind punten of pijplijn eindpunten te hosten (voor batch-deinterferentie). De ondersteunde Compute-doelen zijn:

| Rekendoel | Training | Implementatie |
| ---- |:----:|:----:|
| Azure Machine Learning compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Reken doelen zijn gekoppeld aan uw Machine Learning- [werk ruimte](concept-workspace.md). U beheert uw reken doelen in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).

## <a name="publish"></a>Publiceren

Zodra u een pijp lijn gereed hebt, kunt u deze publiceren als een REST-eind punt. Een [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) kan worden verzonden zonder de python-code die het heeft opgebouwd.

Daarnaast kan een PublishedPipeline worden gebruikt om een pijp lijn opnieuw in te dienen met verschillende PipelineParameter-waarden en-invoer.

## <a name="deployment"></a>Implementatie

Als uw voorspellende model klaar is, implementeert u het als een pijplijn eindpunt of een real-time eind punt van de ontwerp functie.

Het pipeline-eind punt is een PublishedPipeline. u kunt een pijp lijn uitvoeren met verschillende PipelineParameter-waarden en invoer voor batch-de.

Het real-time eind punt biedt een interface tussen een toepassing en uw score model. Een externe toepassing kan in real-time communiceren met het score model. Een aanroep van een real-time eind punt retourneert Voorspellings resultaten naar een externe toepassing. Als u een real-time eind punt wilt aanroepen, geeft u een API-sleutel door die is gemaakt tijdens het implementeren van het eind punt. Het eind punt is gebaseerd op REST, een populaire architectuur keuze voor webprogrammeer projecten.

Zie [zelf studie: een machine learning model implementeren met de ontwerp functie](tutorial-designer-automobile-price-deploy.md)voor meer informatie over het implementeren van uw model.

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Overstappen van de visuele interface naar de ontwerp functie

De visuele interface (preview) is bijgewerkt en is nu Azure Machine Learning Designer (preview). De ontwerp functie is opnieuw ontworpen om een op een pijp lijn gebaseerde backend te gebruiken die volledig is geïntegreerd met de andere functies van Azure Machine Learning. 

Als gevolg van deze updates zijn sommige concepten en voor waarden voor de visuele interface gewijzigd of is de naam van de weer gegeven. Zie de onderstaande tabel voor de belangrijkste concept wijzigingen. 

| Concept in de ontwerp functie | Eerder in de visuele interface |
| ---- |:----:|
| Pijp lijn concept | Probeer |
| Real-time eind punt | Webservice |

### <a name="migrating-to-the-designer"></a>Migreren naar de ontwerp functie

U kunt bestaande visuele interface experimenten en webservices omzetten in pijp lijnen en real-time eind punten in de ontwerp functie. Gebruik de volgende stappen om uw Visual Interface-assets te migreren:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Volgende stappen

* Leer de basis beginselen van predictive analytics en machine learning met [de zelf studie: prijs van auto Mobile met de ontwerper](tutorial-designer-automobile-price-train-score.md)
* Gebruik een van de voor beelden en wijzig uw behoeften:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)

