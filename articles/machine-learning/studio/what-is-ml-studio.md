---
title: Wat is ML Studio (klassiek)
titleSuffix: Azure
description: Azure Machine Learning Studio (klassiek) is een hulp programma voor slepen en neerzetten waarmee u snel modellen kunt bouwen op basis van een kant-en-klare bibliotheek met algoritmen en modules.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: 8179e36342f4f3544a1ecd503d7f136ea437d374
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217809"
---
# <a name="what-is-machine-learning-studio-classic"></a>Wat is Machine Learning Studio (klassiek)?

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klassiek) is een hulp programma voor het slepen en neerzetten dat u kunt gebruiken voor het bouwen, testen en implementeren van predictive analytics oplossingen voor uw gegevens. Azure Machine Learning Studio (klassiek) publiceert modellen als webservices die eenvoudig kunnen worden gebruikt door aangepaste apps of BI-hulpprogram ma's zoals Excel.

Machine Learning Studio (klassiek) is waar data Science, predictive analytics, cloud resources en uw gegevens aan voldoen.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>De interactieve werk ruimte Machine Learning Studio (klassiek)
Voor het ontwikkelen van een voorspellend analyse model gebruikt u doorgaans gegevens uit een of meer bronnen, transformeert en analyseert u die gegevens via verschillende gegevens manipulatie en statistische functies en genereert u een set resultaten. Het ontwikkelen van een model als dit is een iteratief proces. Terwijl u de verschillende functies en de bijbehorende parameters aanpast, worden de resultaten geconvergeerd tot u een afdoende getraind en doeltreffend model hebt.

Azure Machine Learning Studio (klassiek) biedt een interactieve, visuele werk ruimte om eenvoudig een voorspellend analyse model te bouwen, te testen en te herhalen. U sleept ***gegevens sets*** en analyse ***modules*** naar een interactief canvas en verbindt deze met elkaar om een ***experiment***te vormen, dat u in machine learning Studio (klassiek) uitvoert. Als u het modelontwerp wilt herhalen, kunt u het experiment bewerken, desgewenst een kopie ervan opslaan en het opnieuw uitvoeren. Wanneer u klaar bent, kunt u het ***trainingsexperiment*** converteren naar een ***voorspellend experiment*** en dit vervolgens ***publiceren*** als webservice, zodat het model ook voor anderen toegankelijk is.

Er is geen programmering vereist. u kunt gegevens sets en modules visueel verbinden om uw voorspellende analyse model samen te stellen.

![Diagram van Azure Machine Learning Studio (klassiek): experimenten maken, gegevens lezen voor veel bronnen, gescoorde gegevens schrijven, modellen schrijven.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Wat is het verschil tussen Machine Learning Studio (klassiek) en Azure Machine Learning?

[Azure machine learning](../overview-what-is-azure-ml.md) biedt **zowel sdk's als** de Azure machine learning Designer (preview) om snel gegevens te kunnen prepen, machine learning modellen te trainen en te implementeren. De Designer biedt een vergelijk bare functionaliteit voor slepen en neerzetten naar Studio (klassiek). Maar in tegens telling tot het eigen reken platform van Studio (klassiek) maakt de ontwerper gebruik van uw eigen reken resources en is deze volledig geïntegreerd in Azure Machine Learning.

Hier volgt een snelle vergelijking:

|| Machine Learning Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface slepen en neerzetten | Ja | Ja, [Azure machine learning Designer (preview-versie)](../concept-designer.md) |
| Experiment | Schaalbaar (10-GB training-gegevens limiet) | Schalen met Compute target |
| Modules voor de interface voor slepen en neerzetten | Allerlei | Initiële set populaire [modules](../algorithm-module-reference/module-reference.md)|
|Doelen van de trainings compute| Eigen reken doel, alleen CPU-ondersteuning| Ondersteunt Azure Machine Learning Compute (GPU of CPU) en virtuele notebook-Vm's.<br/>([Andere reken processen die worden ondersteund in SDK](../concept-compute-target.md#train))|
|Berekenings doelen afstellen| De indeling van de oorspronkelijke webservice, niet aanpasbaar |  Azure Kubernetes-service en AML-compute <br/>([Andere reken processen die worden ondersteund in SDK](../how-to-deploy-and-where.md)) |
| ML-pijp lijn | Niet ondersteund | Ondersteunde [pijp lijnen](../concept-ml-pipelines.md) |
| MLOps | Basis model beheer en-implementatie | Configureer bare implementatie-model-en pijplijn versie beheer en tracering |
| Model indeling | Alleen een eigen indeling, Studio (klassiek) | Standaard indeling, afhankelijk van het type trainings taak |
|Automatische model training en afstemming tuning | Nee | Nog niet in de ontwerp functie <br/> ([Ondersteund op de pagina voor het land van de SDK en de werk ruimte](../concept-automated-ml.md)) | 

Probeer de ontwerp functie uit met [de zelf studie: prijs van auto Mobile voors pellen met de ontwerper](../tutorial-designer-automobile-price-train-score.md)

> [!NOTE]
> Modellen die zijn gemaakt in Studio (klassiek), kunnen niet worden geïmplementeerd of beheerd door Azure Machine Learning. Modellen die in de ontwerp functie zijn gemaakt en geïmplementeerd, kunnen echter worden beheerd via de werk ruimte Azure Machine Learning.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Het overzichts diagram van de Machine Learning Studio (klassiek) downloaden
Down load het overzichts diagram van de **mogelijkheden van Microsoft Azure machine learning Studio (klassiek)** en krijg een weer gave op hoog niveau van de mogelijkheden van machine learning Studio (klassiek). Als u het diagram altijd bij de hand wilt hebben, kunt u het in A3-formaat afdrukken.

**Down load het diagram hier: overzicht van de [mogelijkheden van Microsoft Azure machine learning Studio (klassiek)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
overzicht van de mogelijkheden van ![Microsoft Azure Machine Learning Studio (klassiek)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Onderdelen van een studio-experiment (klassiek)
Een experiment bestaat uit gegevenssets die gegevens leveren aan analytische modules. Deze kunt u met elkaar verbinden in een predictive analytics-model. Een geldig experiment heeft de volgende kenmerken:

* Het experiment heeft minimaal één gegevensset en één module
* Gegevenssets mogen alleen worden verbonden met modules
* Modules mogen worden verbonden met gegevenssets of andere modules
* Alle ingangspoorten voor modules moeten een verbinding hebben met de gegevensstroom
* Voor elke module moeten alle vereiste parameters zijn ingesteld

U kunt een geheel nieuw experiment maken, maar u kunt ook een bestaand voorbeeldexperiment als sjabloon gebruiken. Zie [Voorbeeldexperimenten kopiëren om nieuwe experimenten voor Machine Learning te maken](sample-experiments.md) voor meer informatie.

Zie [een eenvoudig experiment maken in azure machine learning Studio (klassiek)](create-experiment.md)voor een voor beeld van het maken van een experiment.

Zie [een voorspellende oplossing ontwikkelen met Azure machine learning Studio (klassiek)](tutorial-part1-credit-risk.md)voor een volledig overzicht van het maken van een Predictive Analytics oplossing.

### <a name="datasets"></a>Gegevenssets
Een gegevensset is gegevens die zijn geüpload naar Machine Learning Studio (klassiek), zodat deze kan worden gebruikt in het model proces. Er zijn een aantal voorbeeld gegevens sets opgenomen in de Machine Learning Studio (klassiek) waarmee u kunt experimenteren, en u hebt meer gegevens sets nodig als u deze wilt. Hier volgen enkele voorbeelden van opgenomen gegevenssets:

* **MPG-gegevens voor verschillende auto's**: MPG-waarden (mijl per gallon) voor auto's, geïdentificeerd met het aantal cilinders, paardenkracht, enzovoort.
* **Borstkankergegevens**: gegevens voor borstkankerdiagnose.
* **Bosbrandgegevens**: omvang van bosbranden in het noordoosten van Portugal.

Wanneer u een experiment bouwt, kunt u kiezen uit de lijst met gegevens sets die aan de linkerkant van het canvas beschikbaar zijn.

Zie voor een lijst met voor beelden van gegevens sets die zijn opgenomen in Machine Learning Studio (klassiek) [de voorbeeld gegevenssets gebruiken in azure machine learning Studio (klassiek)](use-sample-datasets.md).

### <a name="modules"></a>Modules
Een module is een algoritme dat u met uw gegevens kunt uitvoeren. Azure Machine Learning Studio (klassiek) heeft een aantal modules die variëren van de functies voor het inkomen van gegevens tot trainings-, Score-en validatie processen. Hier volgen enkele voorbeelden van opgenomen modules:

* [Converteren naar ARFF][convert-to-arff] : converteert een door .net geserialiseerde gegevensset naar kenmerk-relation File Format (ARFF).
* [Berekende elementaire statistieken][elementary-statistics] : berekent elementaire statistieken, zoals het gemiddelde, de standaard afwijking, enzovoort.
* [Lineaire regressie][linear-regression] : maakt een online, op Daal gebaseerd lineair regressie model op basis van kleur overgang.
* [Score model][score-model] : scoret een getrainde classificatie of een regressie model.

Wanneer u een experiment bouwt, kunt u kiezen uit de lijst met modules die aan de linkerkant van het canvas beschikbaar zijn.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de parameters van de module weergegeven in het deelvenster **Properties**, rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen.

Raadpleeg [How to choose algorithms for Microsoft Azure machine learning Studio (klassiek)](algorithm-choice.md)voor meer informatie over het navigeren door de grote bibliotheek met machine learning algoritmen.

## <a name="deploying-a-predictive-analytics-web-service"></a>Een predictive analytics-web service implementeren
Zodra uw predictive analytics model klaar is, kunt u het implementeren als een webservice-recht vanuit Machine Learning Studio (klassiek). Zie [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over dit proces.

## <a name="next-steps"></a>Volgende stappen
U kunt zich de basiskennis van predictive analytics en machine learning eigen maken aan de hand van een [Stapsgewijze quickstart](create-experiment.md) en [door voorbeelden verder uit te werken](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
