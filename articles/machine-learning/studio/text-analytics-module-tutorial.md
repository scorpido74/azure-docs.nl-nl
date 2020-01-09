---
title: Use-case voor sentiment-analyse
titleSuffix: ML Studio (classic) - Azure
description: Tekst analyse modellen maken in Azure Machine Learning Studio (klassiek) met behulp van modules voor tekst voorverwerken, N-gram of functie-hashing
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 313b9c92b10d3170eb71bb8290a9388bb8dcc67c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427526"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Een sentiment-analyse model maken in Azure Machine Learning Studio (klassiek)

U kunt Azure Machine Learning Studio (klassiek) gebruiken om tekst analyse modellen te bouwen en te operationeel maken. Aan de hand van deze modellen kunnen u oplossen, bijvoorbeeld document classificatie of sentiment-analyse.

In een tekst analyse-experiment zou u meestal:

1. Opschonen en tekst gegevensset voorverwerken
2. Numerieke functie vectoren onttrekken aan vooraf verwerkte tekst
3. De classificatie- of regressiemodel model trainen
4. Beoordelen en valideren van het model
5. Het model implementeren voor productie

In deze zelf studie leert u deze stappen te volgen tijdens het door lopen van een sentiment-analyse model met behulp van de Amazon Book revisies-gegevensset (zie dit Engelstalige document "biographs, Bollywood, walkie: Domain aanpassing for sentiment classificatie" door John Blitzer, Mark Dredze en Fernando Pereira; Koppeling van de reken kundige taal (ACL), 2007.) Deze gegevensset bestaat uit beoordelings scores (1-2 of 4-5) en een vrije-vorm tekst. Het doel is om te voorspellen van de beoordeling-score: lage (1 - 2) of Hoog (4-5).

Hier vindt u experimenten in deze zelfstudie is besproken in Azure AI Gallery:

[Boek beoordelingen voorspellen](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Boek beoordelingen - Voorspellend Experiment voorspellen](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Stap 1: Opschonen en tekst gegevensset voorverwerken
We beginnen met het experiment door te verdelen van de beoordeling-scores in categorische lage en hoge buckets te formuleren van het probleem als twee klassen classificatie. We gebruiken [metagegevens bewerken](https://msdn.microsoft.com/library/azure/dn905986.aspx) en [groep Categorische waarden](https://msdn.microsoft.com/library/azure/dn906014.aspx) modules.

![Label maken](./media/text-analytics-module-tutorial/create-label.png)

Vervolgens schonen we de tekst met behulp [voorverwerken tekst](https://msdn.microsoft.com/library/azure/mt762915.aspx) module. Het opschonen van vermindert de ruis in de gegevensset, kunt u informatie over de belangrijkste functies en de nauwkeurigheid van het uiteindelijke model. We verwijderen stopwoorden - veelvoorkomende woorden, zoals "het" of "a" - en getallen, speciale tekens, dubbele tekens, e-mailadressen en URL's. We ook de tekst naar kleine letters, de woorden lemmatize converteren en detecteren zin grenzen die vervolgens worden aangeduid met ' ||| "symbool in vooraf verwerkte tekst.

![Tekst voorverwerken](./media/text-analytics-module-tutorial/preprocess-text.png)

Wat gebeurt er als u wilt gebruiken een aangepaste lijst stopwoorden? U kunt in doorgeven als optionele invoer. U kunt ook aangepaste C# syntaxis van de reguliere expressie subtekenreeksen vervangen en verwijderen van woorden door spraak: woorden of termen bijvoeglijke naamwoorden.

Nadat de voorverwerking voltooid is, we de gegevens splitsen in trainen en testen sets.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Stap 2: Numerieke functie vectoren uit vooraf verwerkte tekst extraheren
Voor het bouwen van een model voor tekstgegevens, moet u doorgaans vrije tekst converteren naar numerieke functie vectoren. In dit voorbeeld gebruiken we [N-Gram-functies extraheren uit tekst](https://msdn.microsoft.com/library/azure/mt762916.aspx) module voor het transformeren van de gegevens naar deze indeling. Deze module wordt een kolom met spaties gescheiden woorden en berekent een woordenlijst met woorden of N-grammen van woorden die worden weergegeven in uw gegevensset. Vervolgens telt deze het aantal keren dat elk woord of N-gram, wordt weergegeven in elke record en vectoren functie maakt die wordt geteld. In deze zelfstudie, we N-gram grootte instellen op 2, zodat onze vectoren functie enkele woorden en combinaties van de twee volgende woorden bevatten.

![N-grammen extraheren](./media/text-analytics-module-tutorial/extract-ngrams.png)

We TF toepassen * IDF (Term frequentie Inverse Document frequentie) weging van N-gram telt. Deze methode wordt het gewicht van woorden die vaak in één record worden weergegeven, maar zijn zeldzaam in de hele gegevensset toegevoegd. Andere opties zijn binary, TF, en levert het afwegen van een grafiek.

Dergelijke tekstfuncties hebben vaak hoge dimensionaliteit. Bijvoorbeeld, als uw corpus 100.000 unieke woorden heeft, zou de adresruimte van uw functie hebben 100.000 dimensies of meer als N-grammen worden gebruikt. De N-Gram-functies ophalen-module biedt u een set van opties voor het verminderen van de dimensionaliteit. U kunt kiezen om uit te sluiten van woorden die korte of lange, of te vaak of te vaak aanzienlijke voorspellende waarde hebben. In deze zelfstudie sluiten wij N-grammen die in minder dan 5 records of in meer dan 80% van de records worden weergegeven.

Bovendien kunt u functies selecteren alleen de functies die het meest gecorreleerde met het doel-voorspelling selecteren. We gebruiken chi-kwadraatverdeling Functieselectie om 1000 onderdelen te selecteren. U kunt het vocabulaire van de geselecteerde woorden of N-grammen weergeven door te klikken op de juiste uitvoer van Extract N-gram-module.

Als een alternatieve methode voor het gebruik van de N-Gram-functies ophalen, kunt u hash-functies-module. Merk echter op dat [hash-functies](https://msdn.microsoft.com/library/azure/dn906018.aspx) heeft geen ingebouwde functie selectie mogelijkheden of TF * IDF wegen.

## <a name="step-3-train-classification-or-regression-model"></a>Stap 3: De classificatie- of regressiemodel model trainen
Nu is de tekst is omgezet naar numerieke parameterkolommen. De gegevensset bevat nog steeds tekenreekskolommen van vorige fasen, we Select Columns in Dataset gebruiken uitsluit.

Vervolgens gebruiken we [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) om te voorspellen ons doel: hoog of laag revisie score. De text analytics-probleem is op dit moment is omgezet in een reguliere regressieprobleem. U kunt de hulpprogram ma's die beschikbaar zijn in Azure Machine Learning Studio (klassiek) gebruiken om het model te verbeteren. Bijvoorbeeld, kunt u experimenteren met verschillende classificaties om erachter te komen hoe nauwkeurige resultaten ze geven of gebruik hyperparameter afstemmen om de nauwkeurigheid te verbeteren.

![Train en Score](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Stap 4: Beoordelen en valideren van het model
Hoe zou u het getrainde model valideren? We hebben deze score op basis van de testgegevensset en evalueren van de nauwkeurigheid. Echter, in het model het vocabulaire van de N-grammen en het gewicht van de gegevensset training hebt geleerd. Daarom moeten we die vocabulaire en deze gewicht gebruiken bij het uitpakken van de functies van testgegevens, in plaats van deze opnieuw maken van het vocabulaire. Dus we N-Gram-functies extraheren module toevoegen aan de scoring-vertakking van het experiment, verbinding maken met het vocabulaire van de uitvoer van training-vertakking en de woordenlijst-modus instellen op alleen-lezen. We ook het filteren van N-grammen frequentie door het minimum instellen op 1 exemplaar en de maximale tot 100% uitschakelen en de Functieselectie uitschakelen.

Nadat de tekstkolom in testgegevens heeft zijn omgezet naar numerieke parameterkolommen, uitsluiten we de tekenreekskolommen van vorige fasen, zoals in de vertakking training. Vervolgens gebruiken we de module Score Model om voorspellingen te doen en de module Evaluate Model om te evalueren van de nauwkeurigheid.

## <a name="step-5-deploy-the-model-to-production"></a>Stap 5: Het model implementeren voor productie
Het model is bijna klaar om te worden geïmplementeerd naar productie. Bij de implementatie als webservice, het vrije tekst tekenreeks als invoer en retourneren een voorspelling 'hoog' of 'laag'. De geleerde N-gram-woordenlijst wordt gebruikt om te zetten van de tekst die moet worden functies en getraind logistieke regressiemodel om een voorspelling van deze functies te maken. 

Als u de Voorspellend experiment instelt, opslaan we eerst het vocabulaire van de N-gram als gegevensset en het getrainde logistieke regressiemodel van de training-vertakking van het experiment. Vervolgens slaat we het experiment met behulp van 'OpslaanAls' een experimentgrafiek voor Voorspellend experiment maken. We verwijderen de module Split Data en de vertakking training van het experiment. We vervolgens verbinden met de eerder opgeslagen N-gram vocabulaire en het model N-Gram-functies ophalen en Score Model modules, respectievelijk. We ook de module Evaluate Model te verwijderen.

We invoegen Select Columns in Dataset module voordat tekst voorverwerken module de labelkolom wilt verwijderen, en hef de selectie van de optie 'Score kolom toevoegen aan de gegevensset' in de Module Score. Op die manier kunnen de webservice vraagt niet om het label die deze probeert te voorspellen en werkt niet echo de invoer functies in de reactie.

![Voorspellend Experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu hebben we een experiment die kan worden gepubliceerd als een webservice en aangeroepen met behulp van de uitvoering van aanvraag-reactie of batch API's.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over tekstanalysemodules van [MSDN-documentatie](https://msdn.microsoft.com/library/azure/dn905886.aspx).

