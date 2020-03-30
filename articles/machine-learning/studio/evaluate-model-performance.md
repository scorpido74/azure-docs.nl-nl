---
title: Modelprestaties evalueren
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het evalueren van de modelprestaties in Azure Machine Learning Studio (klassiek) en over de statistieken die beschikbaar zijn voor deze taak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218146"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Modelprestaties evalueren in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

In dit artikel wordt uitgelegd hoe u de prestaties van een model evalueren in Azure Machine Learning Studio (klassiek) en wordt een korte uitleg gegeven over de statistieken die voor deze taak beschikbaar zijn. Drie veelvoorkomende leerscenario's onder toezicht worden gepresenteerd: 

* Regressie
* binaire classificatie 
* classificatie van meerdere klassen



Het evalueren van de prestaties van een model is een van de kernfasen in het data science-proces. Het geeft aan hoe succesvol de score (voorspellingen) van een gegevensset is geweest door een getraind model. 

Azure Machine Learning Studio (klassiek) ondersteunt modelevaluatie via twee van de belangrijkste machine learning-modules: Model en [Cross-Validate Model][cross-validate-model] [evalueren.][evaluate-model] Met deze modules u zien hoe uw model presteert in termen van een aantal statistieken die vaak worden gebruikt in machine learning en statistieken.

## <a name="evaluation-vs-cross-validation"></a>Evaluatie vs. Cross Validatie
Evaluatie en cross validatie zijn standaardmanieren om de prestaties van uw model te meten. Ze genereren beide evaluatiestatistieken die u inspecteren of vergelijken met die van andere modellen.

[Evalueren Model][evaluate-model] verwacht een gescoorde dataset als input (of twee in het geval u de prestaties van twee verschillende modellen wilt vergelijken). Daarom moet u uw model trainen met behulp van de [treinmodelmodule][train-model] en voorspellingen doen over een of andere gegevensset met behulp van de module [Scoremodel][score-model] voordat u de resultaten evalueren. De evaluatie is gebaseerd op de gescoorde labels/waarschijnlijkheden, samen met de echte labels, die allemaal worden uitgevoerd door de [module Scoremodel.][score-model]

U ook cross validatie gebruiken om een aantal treinscore-evaluatiebewerkingen (10 vouwen) automatisch uit te voeren op verschillende subsets van de invoergegevens. De invoergegevens zijn opgesplitst in 10 delen, waar de ene is gereserveerd voor het testen, en de andere 9 voor training. Dit proces wordt 10 keer herhaald en de evaluatiestatistieken worden gemiddeld weergegeven. Dit helpt bij het bepalen hoe goed een model zou generaliseren naar nieuwe gegevenssets. De [module Cross-Validate Model][cross-validate-model] neemt een ongetraind model op en een aantal gelabelde gegevenssets en voert de evaluatieresultaten van elk van de 10 vouwen uit, naast de gemiddelde resultaten.

In de volgende secties bouwen we eenvoudige regressie- en classificatiemodellen en evalueren we hun prestaties, met behulp van zowel het [evaluatiemodel][evaluate-model] als de [modules van het cross-valideringsmodel.][cross-validate-model]

## <a name="evaluating-a-regression-model"></a>Een regressiemodel evalueren
Stel dat we de prijs van een auto willen voorspellen met behulp van functies zoals afmetingen, pk's, motorspecificaties, enzovoort. Dit is een typisch regressieprobleem, waarbij de doelvariabele *(prijs)* een continue numerieke waarde is. We kunnen een lineair regressiemodel plaatsen dat, gezien de functiewaarden van een bepaalde auto, de prijs van die auto kan voorspellen. Dit regressiemodel kan worden gebruikt om dezelfde gegevensset te scoren waarop we hebben getraind. Zodra we de voorspelde autoprijzen hebben, kunnen we de modelprestaties evalueren door te kijken hoeveel de voorspellingen gemiddeld afwijken van de werkelijke prijzen. Om dit te illustreren, gebruiken we de *Auto's-prijsgegevens (Raw) dataset* beschikbaar in de sectie **Opgeslagen datasets** in Machine Learning Studio (klassiek).

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werkruimte in Azure Machine Learning Studio (klassiek):

* Auto prijsgegevens (Raw)
* [Lineaire regressie][linear-regression]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Sluit de poorten zoals hieronder in figuur 1 aan en stel de kolom Label van de module [Treinmodel][train-model] op *prijs*.

![Een regressiemodel evalueren](./media/evaluate-model-performance/1.png)

Afbeelding 1. Een regressiemodel evalueren.

### <a name="inspecting-the-evaluation-results"></a>De evaluatieresultaten inspecteren
Nadat u het experiment hebt uitgevoerd, u op de uitvoerpoort van de module [Model evalueren][evaluate-model] klikken en *Visualiseren* selecteren om de evaluatieresultaten te bekijken. De evaluatiestatistieken die beschikbaar zijn voor regressiemodellen zijn: *Mean Absolute Error*, Root Mean Absolute *Error*, Relative *Absolute Error*, *Relative Squared Error*, en de *Bepalingscoëfficiënt*.

De term "fout" hier vertegenwoordigt het verschil tussen de voorspelde waarde en de werkelijke waarde. De absolute waarde of het kwadraat van dit verschil wordt meestal berekend om de totale omvang van de fout in alle gevallen vast te leggen, omdat het verschil tussen de voorspelde en werkelijke waarde in sommige gevallen negatief kan zijn. De foutstatistieken meten de voorspellende prestaties van een regressiemodel in termen van de gemiddelde afwijking van de voorspellingen van de werkelijke waarden. Lagere foutwaarden betekenen dat het model nauwkeuriger is in het maken van voorspellingen. Een algemene foutstatistiek van nul betekent dat het model perfect bij de gegevens past.

De bepalingscoëfficiënt, die ook bekend staat als R-kwadraat, is ook een standaardmanier om te meten hoe goed het model bij de gegevens past. Het kan worden geïnterpreteerd als de verhouding van de variatie uitgelegd door het model. Een hoger aandeel is beter in dit geval, waar 1 geeft een perfecte pasvorm.

![Evaluatiestatistieken voor lineaire regressie](./media/evaluate-model-performance/2.png)

Afbeelding 2. Statistieken voor lineaire regressieevaluatie.

### <a name="using-cross-validation"></a>Kruisvalidatie gebruiken
Zoals eerder vermeld, u automatisch herhaalde training, scores en evaluaties uitvoeren met behulp van de module [Model voor crossvalideren.][cross-validate-model] Alles wat je in dit geval nodig hebt is een dataset, een ongetraind model en een [module Cross-Validate Model][cross-validate-model] (zie figuur hieronder). U moet de labelkolom inde- *of prijs* instellen in de eigenschappen van de module Model [voor crossvalideren.][cross-validate-model]

![Een regressiemodel kruisgeldig maken](./media/evaluate-model-performance/3.png)

Afbeelding 3. Een regressiemodel kruisgeldigmaken.

Na het uitvoeren van het experiment u de evaluatieresultaten inspecteren door op de juiste uitvoerpoort van de module [Model voor kruisvalidatie te][cross-validate-model] klikken. Dit geeft een gedetailleerd overzicht van de statistieken voor elke iteratie (fold) en de gemiddelde resultaten van elk van de statistieken (figuur 4).

![Resultaten van een regressiemodel voor crossvalidatie](./media/evaluate-model-performance/4.png)

Afbeelding 4: Resultaten van een regressiemodel voor crossvalidatie.

## <a name="evaluating-a-binary-classification-model"></a>Een binair classificatiemodel evalueren
In een binair classificatiescenario heeft de doelvariabele slechts twee mogelijke uitkomsten, bijvoorbeeld: {0, 1} of {false, true}, {negative, positive}. Stel dat u een gegevensset krijgt van volwassen werknemers met een aantal demografische en werkgelegenheidsvariabelen, en dat u wordt gevraagd om het inkomensniveau te voorspellen, een binaire variabele met de waarden {"<=50 K", ">50 K"}. Met andere woorden, de negatieve klasse vertegenwoordigt de werknemers die minder dan of gelijk aan 50 K per jaar, en de positieve klasse vertegenwoordigt alle andere werknemers. Net als in het regressiescenario zouden we een model trainen, bepaalde gegevens scoren en de resultaten evalueren. Het belangrijkste verschil hier is de keuze van metrics Azure Machine Learning Studio (klassieke) computes en outputs. Om het voorspellingsscenario voor inkomensniveau te illustreren, gebruiken we de gegevensset [Voor volwassenen](https://archive.ics.uci.edu/ml/datasets/Adult) om een Studio-experiment (klassiek) te maken en de prestaties van een logistiek regressiemodel met twee klassen te evalueren, een veelgebruikte binaire classificatie.

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werkruimte in Azure Machine Learning Studio (klassiek):

* Gegevensgegevensgegevens van binaire classificatie van inkomsten voor volwassenen
* [Logistieke regressie met twee klassen][two-class-logistic-regression]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Sluit de poorten zoals hieronder in figuur 5 aan en stel de kolom Label van de module [Treinmodel][train-model] in op *inkomsten*.

![Een binair classificatiemodel evalueren](./media/evaluate-model-performance/5.png)

Afbeelding 5. Een binair classificatiemodel evalueren.

### <a name="inspecting-the-evaluation-results"></a>De evaluatieresultaten inspecteren
Na het uitvoeren van het experiment u op de uitvoerpoort van de module [Model evalueren][evaluate-model] klikken en *Visualiseren* selecteren om de evaluatieresultaten te bekijken (figuur 7). De evaluatiestatistieken die beschikbaar zijn voor binaire classificatiemodellen zijn: *Nauwkeurigheid*, *Precisie*, *Recall*, *F1-score*en *AUC*. Bovendien, de module uitgangen een verwarring matrix met het aantal echte positieven, valse negatieven, false positives, en echte negatieven, evenals *ROC*, *Precision / Recall*, en *Lift* curven.

Nauwkeurigheid is gewoon het aandeel van correct geclassificeerde exemplaren. Het is meestal de eerste statistiek die u bekijkt bij de evaluatie van een classificatie. Echter, wanneer de testgegevens uit balans zijn (waar de meeste exemplaren behoren tot een van de klassen), of u meer geïnteresseerd bent in de prestaties op een van de klassen, nauwkeurigheid niet echt vangen de effectiviteit van een classificatie. In het classificatiescenario voor inkomensniveau, gaat u ervan uit dat u test op bepaalde gegevens waarbij 99% van de exemplaren mensen vertegenwoordigt die minder dan of gelijk aan 50.000 per jaar verdienen. Het is mogelijk om een nauwkeurigheid van 0,99 te bereiken door de klasse "<=50K" voor alle instanties te voorspellen. De classificatie in dit geval lijkt te doen een goede baan in het algemeen, maar in werkelijkheid, het niet aan een van de hoge inkomens individuen (de 1%) classificeren Correct.

Om die reden is het handig om aanvullende statistieken te berekenen die meer specifieke aspecten van de evaluatie vastleggen. Alvorens in te gaan op de details van dergelijke statistieken, is het belangrijk om de verwarring matrix van een binaire classificatie evaluatie te begrijpen. De klassenlabels in de trainingsset kunnen slechts twee mogelijke waarden aannemen, die we meestal positief of negatief noemen. De positieve en negatieve exemplaren die een classificatie correct voorspelt, worden respectievelijk true positives (TP) en true negatives (TN) genoemd. Evenzo worden de onjuist geclassificeerde exemplaren valse positieven (FP) en false negatives (FN) genoemd. De verwarringsmatrix is gewoon een tabel met het aantal exemplaren dat onder elk van deze vier categorieën valt. Azure Machine Learning Studio (klassiek) bepaalt automatisch welke van de twee klassen in de gegevensset de positieve klasse is. Als de klassenlabels Booleaanse of gehele getallen zijn, krijgen de labelinstanties 'waar' of '1' de positieve klasse toegewezen. Als de labels tekenreeksen zijn, zoals bij de gegevensset inkomen, worden de labels alfabetisch gesorteerd en wordt het eerste niveau gekozen als de negatieve klasse, terwijl het tweede niveau de positieve klasse is.

![Binaire classificatie verwarring Matrix](./media/evaluate-model-performance/6a.png)

Afbeelding 6. Binaire classificatie verwarring matrix.

Terug te gaan naar de inkomensclassificatie probleem, zouden we willen een aantal evaluatie vragen die ons helpen begrijpen van de prestaties van de classificatie gebruikt. Een natuurlijke vraag is: 'Van de personen die het model voorspelde te verdienen >50 K (TP + FP), hoeveel werden correct ingedeeld (TP)?' Deze vraag kan worden beantwoord door te kijken naar de **precisie** van het model, dat is het aandeel van de positieven die correct zijn ingedeeld: TP / (TP + FP). Een andere veel voorkomende vraag is "Van alle hoogverdienende werknemers met een inkomen >50k (TP +FN), hoeveel heeft de classifier correct classificeren (TP)". Dit is eigenlijk de **Recall**, of de ware positieve tarief: TP / (TP + FN) van de classificatie. Je zou kunnen merken dat er een duidelijke afweging tussen precisie en terugroepen. Bijvoorbeeld, gezien een relatief evenwichtige dataset, een classificatie die meestal positieve gevallen voorspelt, zou een hoge recall hebben, maar een vrij lage precisie als veel van de negatieve gevallen zou worden verkeerd ingedeeld wat resulteert in een groot aantal valse positieven. Als u een plot wilt zien van de manier waarop deze twee statistieken variëren, u op de **curve PRECISION/RECALL** klikken op de uitvoerpagina voor evaluatieresultaten (linksboven deel van figuur 7).

![Resultaten van binaire classificatie-evaluatie](./media/evaluate-model-performance/7.png)

Afbeelding 7. Resultaten van binaire classificatie-evaluatie.

Een andere gerelateerde statistiek die vaak wordt gebruikt is de **F1 Score**, die zowel precisie en terugroepen in aanmerking neemt. Het is het harmonische gemiddelde van deze twee metrics en wordt als zodanig berekend: F1 = 2 (precision x recall) / (precisie + recall). De F1-score is een goede manier om de evaluatie samen te vatten in een enkel nummer, maar het is altijd een goede gewoonte om te kijken naar zowel precisie en samen herinneren om beter te begrijpen hoe een classificatie zich gedraagt.

Daarnaast kan men de werkelijke positieve snelheid versus de false positive rate in de **Receiver Operating Characteristic (ROC)** curve en de overeenkomstige **Area Under the Curve (AUC)** waarde te inspecteren. Hoe dichter deze curve is naar de linkerbovenhoek, hoe beter de classifier de prestaties is (dat is het maximaliseren van de echte positieve tarief, terwijl het minimaliseren van de vals-positieve tarief). Curven die dicht bij de diagonaal van het perceel liggen, zijn het resultaat van classificaties die de neiging hebben voorspellingen te doen die dicht bij willekeurig gissen zijn.

### <a name="using-cross-validation"></a>Kruisvalidatie gebruiken
Net als in het regressievoorbeeld kunnen we crossvalidatie uitvoeren om verschillende subsets van de gegevens automatisch te trainen, te scoren en te evalueren. Op dezelfde manier kunnen we de [cross-validate model][cross-validate-model] module, een ongetraind logistiek regressiemodel en een dataset gebruiken. De labelkolom moet worden ingesteld op *inkomsten* in de eigenschappen van de module [Model voor crossvalideren.][cross-validate-model] Na het uitvoeren van het experiment en het klikken op de juiste uitvoerpoort van de module [Cross-Validate Model,][cross-validate-model] kunnen we de binaire classificatiemetrische waarden voor elke vouw zien, naast de gemiddelde en standaarddeviatie van elk. 

![Een binair classificatiemodel kruisgeldig maken](./media/evaluate-model-performance/8.png)

Afbeelding 8. Een binair classificatiemodel kruisgeldig maken.

![Resultaten van een binaire classificatie voor crossvalidatie](./media/evaluate-model-performance/9.png)

Figuur 9. Resultaten van een binaire classificatie voor crossvalidatie.

## <a name="evaluating-a-multiclass-classification-model"></a>Een classificatiemodel voor meerdere klassen evalueren
In dit experiment gebruiken we [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") de populaire Iris-gegevensset, die exemplaren van drie verschillende typen (klassen) van de irisplant bevat. Er zijn vier functiewaarden (sepallengte/breedte en bloemblaadjeslengte/breedte) voor elke instantie. In de vorige experimenten hebben we de modellen getraind en getest met dezelfde datasets. Hier gebruiken we de [module Gesplitste gegevens][split] om twee subsets van de gegevens te maken, op de eerste te trainen en te scoren en te evalueren op de tweede. De Iris-gegevensset is openbaar beschikbaar op de [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html)en kan worden gedownload met behulp van een [importgegevensmodule.][import-data]

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werkruimte in Azure Machine Learning Studio (klassiek):

* [Gegevens importeren][import-data]
* [Beslissingsforest met meerdere klassen][multiclass-decision-forest]
* [Gesplitste gegevens][split]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Sluit de poorten aan zoals hieronder in figuur 10.

Stel de kolomindex Label van de module [Treinmodel][train-model] in op 5. De gegevensset heeft geen koptekstrij, maar we weten dat de klassenlabels zich in de vijfde kolom bevinden.

Klik op de module [Gegevens importeren][import-data] en stel de eigenschap *Gegevensbron* in *op web-URL via HTTP*en de *URL* in op http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Stel de fractie in van de instanties die moet worden gebruikt voor training in de module [Gesplitste gegevens][split] (0,7 bijvoorbeeld).

![Een klasseclassificatie van meerdere klassen evalueren](./media/evaluate-model-performance/10.png)

Figuur 10. Een klasseclassificatie van meerdere klassen evalueren

### <a name="inspecting-the-evaluation-results"></a>De evaluatieresultaten inspecteren
Voer het experiment uit en klik op de uitvoerpoort van [Model evalueren][evaluate-model]. De evaluatieresultaten worden gepresenteerd in de vorm van een verwarringsmatrix, in dit geval. De matrix toont de werkelijke vs. voorspelde exemplaren voor alle drie de klassen.

![Evaluatieresultaten van classificaties van meerdere klassen](./media/evaluate-model-performance/11.png)

Figuur 11. Evaluatieresultaten van classificaties met meerdere klassen.

### <a name="using-cross-validation"></a>Kruisvalidatie gebruiken
Zoals eerder vermeld, u automatisch herhaalde training, scores en evaluaties uitvoeren met behulp van de module [Model voor crossvalideren.][cross-validate-model] U hebt een gegevensset, een ongetraind model en een [module Voor kruisvalidatie nodig][cross-validate-model] (zie figuur hieronder). Opnieuw moet u de labelkolom van de module [Model voor crossvalideren][cross-validate-model] instellen (kolomindex 5 in dit geval). Nadat u het experiment hebt uitgevoerd en op de juiste uitvoerpoort van het [model voor crossvalideren][cross-validate-model]hebt geklikt, u de metrische waarden voor elke vouw en de gemiddelde en standaarddeviatie inspecteren. De statistieken die hier worden weergegeven zijn vergelijkbaar met die besproken in de binaire classificatie geval. Echter, in multiclass classificatie, het berekenen van de echte positieven / negatieven en false positives / negatieven wordt gedaan door te rekenen op een basis per klasse, want er is geen algemene positieve of negatieve klasse. Bijvoorbeeld, bij het berekenen van de precisie of recall van de klasse 'Iris-setosa', wordt aangenomen dat dit de positieve klasse en alle anderen als negatief.

![Het valideren van een classificatiemodel voor meerdere klassen](./media/evaluate-model-performance/12.png)

Figuur 12. Het valideren van een meerklassenclassificatiemodel.

![Resultaten van meerdere validatie smaken van een classificatiemodel voor meerdere klassen](./media/evaluate-model-performance/13.png)

Figuur 13. Resultaten van meerdere validatie smaken van een classificatiemodel voor meerdere klassen.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
