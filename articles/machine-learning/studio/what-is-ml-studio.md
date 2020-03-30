---
title: Wat is ML Studio (klassiek)
titleSuffix: Azure
description: Azure Machine Learning Studio (klassiek) is een drag-and-drop tool voor het snel bouwen van modellen uit een kant-en-klare bibliotheek van algoritmen en modules.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371979"
---
# <a name="what-is-machine-learning-studio-classic"></a>Wat is Machine Learning Studio (klassiek)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klassiek) is een samenwerkingstool voor slepen en neerzetten die u gebruiken om voorspellende analyseoplossingen op uw gegevens te bouwen, testen en implementeren. Azure Machine Learning Studio (klassiek) publiceert modellen als webservices die eenvoudig kunnen worden gebruikt door aangepaste apps of BI-hulpprogramma's zoals Excel.

Machine Learning Studio (klassiek) is waar data science, predictive analytics, cloud resources en uw data elkaar ontmoeten.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>De Machine Learning Studio (klassieke) interactieve werkruimte

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Om een voorspellend analysemodel te ontwikkelen, gebruikt u meestal gegevens uit een of meer bronnen, transformeert en analyseert u die gegevens via verschillende gegevensmanipulatie en statistische functies en genereert u een reeks resultaten. Het ontwikkelen van een model als dit is een iteratief proces. Terwijl u de verschillende functies en de bijbehorende parameters aanpast, worden de resultaten geconvergeerd tot u een afdoende getraind en doeltreffend model hebt.

Azure Machine Learning Studio (klassiek) biedt u een interactieve, visuele werkruimte om eenvoudig een voorspellend analysemodel te bouwen, te testen en te herhalen. Je sleept ***datasets*** en ***analysemodules*** naar een interactief canvas en verbindt ze om een ***experiment***te vormen , dat je uitvoert in Machine Learning Studio (klassiek). Als u het modelontwerp wilt herhalen, kunt u het experiment bewerken, desgewenst een kopie ervan opslaan en het opnieuw uitvoeren. Wanneer u klaar bent, kunt u het ***trainingsexperiment*** converteren naar een ***voorspellend experiment*** en dit vervolgens publish als ***webservice***, zodat het model ook voor anderen toegankelijk is.

Er is geen programmering vereist, visueel verbinden datasets en modules om uw voorspellende analyse model te construeren.

![Azure Machine Learning Studio (klassiek) diagram: Maak experimenten, lees gegevens voor veel bronnen, schrijf gescoorde gegevens, schrijf modellen.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Download het overzichtsdiagram van Machine Learning Studio (klassiek)
Download het **overzichtsdiagram (Microsoft Azure Machine Learning Studio) (klassieke) mogelijkheden** en krijg een overzicht op hoog niveau van de mogelijkheden van Machine Learning Studio (klassiek). Als u het diagram altijd bij de hand wilt hebben, kunt u het in A3-formaat afdrukken.

**Download het diagram hier: [Overzicht van Microsoft Azure Machine Learning Studio (klassieke) mogelijkheden](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio (klassiek) Overzicht van mogelijkheden](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Onderdelen van een Studio (klassiek) experiment
Een experiment bestaat uit gegevenssets die gegevens leveren aan analytische modules. Deze kunt u met elkaar verbinden in een predictive analytics-model. Een geldig experiment heeft de volgende kenmerken:

* Het experiment heeft minimaal één gegevensset en één module
* Gegevenssets mogen alleen worden verbonden met modules
* Modules mogen worden verbonden met gegevenssets of andere modules
* Alle ingangspoorten voor modules moeten een verbinding hebben met de gegevensstroom
* Voor elke module moeten alle vereiste parameters zijn ingesteld

U kunt een geheel nieuw experiment maken, maar u kunt ook een bestaand voorbeeldexperiment als sjabloon gebruiken. Zie [Voorbeeldexperimenten kopiëren om nieuwe experimenten voor Machine Learning te maken](sample-experiments.md) voor meer informatie.

Zie [Een eenvoudig experiment maken in Azure Machine Learning Studio (klassiek)](create-experiment.md)voor een voorbeeld van het maken van een experiment.

Zie [Een voorspellende oplossing ontwikkelen met Azure Machine Learning Studio (klassiek)](tutorial-part1-credit-risk.md)voor een completere walkthrough van het maken van een predictive analytics-oplossing.

### <a name="datasets"></a>Gegevenssets
Een gegevensset is gegevens die zijn geüpload naar Machine Learning Studio (klassiek), zodat deze kunnen worden gebruikt in het modelleringsproces. Een aantal voorbeeldgegevenssets zijn opgenomen bij Machine Learning Studio (klassiek) waarmee u experimenteren en u meer gegevenssets uploaden wanneer u ze nodig hebt. Hier volgen enkele voorbeelden van opgenomen gegevenssets:

* **MPG-gegevens voor verschillende auto's**: MPG-waarden (mijl per gallon) voor auto's, geïdentificeerd met het aantal cilinders, paardenkracht, enzovoort.
* **Borstkankergegevens**: gegevens voor borstkankerdiagnose.
* **Bosbrandgegevens**: omvang van bosbranden in het noordoosten van Portugal.

Terwijl u een experiment bouwt, u kiezen uit de lijst met gegevenssets die beschikbaar zijn aan de linkerkant van het canvas.

Zie [De voorbeeldgegevenssets gebruiken in Azure Machine Learning Studio (klassiek)](use-sample-datasets.md)voor een lijst met voorbeeldgegevenssets in Machine Learning Studio (klassiek).

### <a name="modules"></a>Modules
Een module is een algoritme dat u met uw gegevens kunt uitvoeren. Azure Machine Learning Studio (klassiek) heeft een aantal modules, variërend van gegevensingress-functies tot trainings-, scorings- en validatieprocessen. Hier volgen enkele voorbeelden van opgenomen modules:

* [Convert to ARFF][convert-to-arff]: converteert een geserialiseerde .NET-gegevensset naar Attribute-Relation File Format (ARFF).
* [Compute Elementary Statistics][elementary-statistics]: berekent elementaire statistieken, zoals het gemiddelde, de standaardafwijking, enzovoort.
* [Linear Regression][linear-regression]: maakt een online lineair regressiemodel met daalgradiënt.
* [Score Model][score-model]: beoordeelt een getraind classificatie- of regressiemodel.

Terwijl u een experiment bouwt, u kiezen uit de lijst met modules die links van het canvas beschikbaar zijn.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de parameters van de module weergegeven in het deelvenster **Properties**, rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen.

Zie [Algoritmes kiezen voor Microsoft Azure Machine Learning Studio (klassiek)](algorithm-choice.md)voor sommige hulp bij het navigeren door de grote bibliotheek met machine learning-algoritmen.

## <a name="deploying-a-predictive-analytics-web-service"></a>Een predictive analytics-web service implementeren
Zodra uw predictive analytics-model klaar is, u het implementeren als een webservice vanuit Machine Learning Studio (klassiek). Zie [Een Webservice Azure Machine Learning implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over dit proces.

## <a name="next-steps"></a>Volgende stappen
U kunt zich de basiskennis van predictive analytics en machine learning eigen maken aan de hand van een [Stapsgewijze quickstart](create-experiment.md) en [door voorbeelden verder uit te werken](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
