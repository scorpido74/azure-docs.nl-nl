---
title: Gebruikscase voor sentimentanalyse
titleSuffix: ML Studio (classic) - Azure
description: Tekstanalysemodellen maken in Azure Machine Learning Studio (klassiek) met behulp van modules voor tekstvoorbewerking, N-grammen of feature hashing
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217884"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Een sentimentanalysemodel maken in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U Azure Machine Learning Studio (klassiek) gebruiken om tekstanalysemodellen te bouwen en te operationaliseren. Deze modellen kunnen u helpen bij het oplossen van bijvoorbeeld problemen met documentclassificatie of sentimentanalyse.

In een experiment met tekstanalyse zou u doorgaans het als gevolg van:

1. Gegevensset Tekst maken en vooraf verwerken
2. Numerieke functievectoren uit vooraf verwerkte tekst extraheren
3. Treinclassificatie of regressiemodel
4. Het model scoren en valideren
5. Het model in productie implementeren

In deze tutorial leert u deze stappen terwijl we door een sentimentanalysemodel lopen met behulp van de Amazon Book Reviews-gegevensset (zie dit onderzoeksartikel "Biografieën, Bollywood, Boom-boxes en Blenders: Domain Adaptation for Sentiment Classification" van John Blitzer, Mark Dredze en Fernando Pereira; Association of Computational Linguistics (ACL), 2007.) Deze dataset bestaat uit review scores (1-2 of 4-5) en een vrije tekst. Het doel is om de review score te voorspellen: laag (1-2) of hoog (4-5).

In deze zelfstudie vindt u experimenten in Azure AI Gallery:

[Boekbesprekingen voorspellen](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Boekbesprekingen voorspellen - Voorspellend experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Stap 1: Tekstgegevensset schoonmaken en vooraf verwerken
We beginnen het experiment door de reviewscores te verdelen in categorischlage en hoge buckets om het probleem te formuleren als classificatie van twee klassen. We gebruiken modules [Metagegevens bewerken](https://msdn.microsoft.com/library/azure/dn905986.aspx) en [Categorische waarden groeperen.](https://msdn.microsoft.com/library/azure/dn906014.aspx)

![Label maken](./media/text-analytics-module-tutorial/create-label.png)

Vervolgens reinigen we de tekst met de module [Preprocess Text.](https://msdn.microsoft.com/library/azure/mt762915.aspx) De reiniging vermindert het geluid in de gegevensset, helpt u de belangrijkste functies te vinden en de nauwkeurigheid van het uiteindelijke model te verbeteren. We verwijderen stopwoorden - veelvoorkomende woorden zoals 'de' of 'a' en getallen, speciale tekens, dubbele tekens, e-mailadressen en URL's. We zetten de tekst ook om in kleine letters, lemmatize de woorden, en detecteren zin grenzen die vervolgens worden aangegeven door "|||" symbool in vooraf verwerkte tekst.

![Tekst voorverwerken](./media/text-analytics-module-tutorial/preprocess-text.png)

Wat als u een aangepaste lijst met stopwoorden wilt gebruiken? U het doorgeven als optionele invoer. U ook de reguliere expressie van de aangepaste C#-syntaxis gebruiken om subtekenreeksen te vervangen en woorden te verwijderen door een deel van spraak: zelfstandige naamwoorden, werkwoorden of bijvoeglijke naamwoorden.

Nadat de voorbewerking is voltooid, splitsen we de gegevens op in trein- en testsets.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Stap 2: Numerieke functievectoren extraheren uit vooraf verwerkte tekst
Als u een model wilt maken voor tekstgegevens, moet u doorgaans tekst in vrije vorm omzetten in numerieke functievectoren. In dit voorbeeld gebruiken we [Extraheern-Gram-functies uit de tekstmodule](https://msdn.microsoft.com/library/azure/mt762916.aspx) om de tekstgegevens om te zetten in een dergelijke indeling. Deze module neemt een kolom van witruimte-gescheiden woorden en berekent een woordenboek van woorden, of N-gram woorden, die in uw gegevensset verschijnen. Vervolgens telt het hoe vaak elk woord, of N-gram, in elke record wordt weergegeven en maakt het functievectoren van die tellingen. In deze zelfstudie stellen we de grootte van de N-gram in op 2, zodat onze functievectoren enkele woorden en combinaties van twee volgende woorden bevatten.

![Extract N-grammen](./media/text-analytics-module-tutorial/extract-ngrams.png)

We passen TF*IDF (Term Frequency Inverse Document Frequency) weging toe op N-gramtellingen. Deze benadering voegt het gewicht van woorden toe die vaak in één record worden weergegeven, maar zeldzaam zijn in de hele gegevensset. Andere opties zijn binaire, TF, en grafiek wegen.

Dergelijke tekstfuncties hebben vaak een hoge dimensionaliteit. Als uw corpus bijvoorbeeld 100.000 unieke woorden heeft, heeft uw functieruimte 100.000 dimensies of meer als N-grammen worden gebruikt. De module N-Gram extra's biedt u een reeks opties om de dimensionaliteit te verminderen. U ervoor kiezen om woorden uit te sluiten die kort of lang zijn, of te ongewoon of te vaak om een significante voorspellende waarde te hebben. In deze zelfstudie sluiten we N-grammen uit die in minder dan 5 records of in meer dan 80% van de records worden weergegeven.

U ook functieselectie gebruiken om alleen die functies te selecteren die het meest gecorreleerd zijn met uw voorspellingsdoel. We gebruiken Chi-Squared feature selection om 1000 functies te selecteren. U de woordenschat van geselecteerde woorden of N-grammen bekijken door op de juiste uitvoer van extract N-grammodule te klikken.

Als alternatieve benadering van het gebruik van Extract N-Gram-functies u de Feature Hashing-module gebruiken. Houd er echter rekening mee dat [Feature Hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) geen ingebouwde functieselectiemogelijkheden heeft of tf*IDF-weegkracht.

## <a name="step-3-train-classification-or-regression-model"></a>Stap 3: Classificatie- of regressiemodel trainen
Nu is de tekst omgezet in numerieke functiekolommen. De gegevensset bevat nog steeds tekenreekskolommen uit eerdere fasen, dus we gebruiken Kolommen selecteren in gegevensset om ze uit te sluiten.

Vervolgens gebruiken we [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) om ons doel te voorspellen: hoge of lage reviewscore. Op dit moment is het probleem met tekstanalyse omgezet in een regelmatig classificatieprobleem. U de tools die beschikbaar zijn in Azure Machine Learning Studio (klassiek) gebruiken om het model te verbeteren. U bijvoorbeeld experimenteren met verschillende classificaties om erachter te komen hoe nauwkeurige resultaten ze geven, of hyperparametertuning gebruiken om de nauwkeurigheid te verbeteren.

![Trainen en scoren](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Stap 4: Score en valideer het model
Hoe zou u het getrainde model valideren? We scoren deze aan de hand van de testdataset en evalueren de nauwkeurigheid. Echter, het model leerde de woordenschat van N-grammen en hun gewichten uit de training dataset. Daarom moeten we die woordenschat en die gewichten gebruiken bij het extraheren van functies uit testgegevens, in tegenstelling tot het opnieuw creëren van de woordenschat. Daarom voegen we de module Extract N-Gram Features toe aan de scoringstak van het experiment, verbinden we de uitvoerwoordenschat van de trainingstak en stellen we de woordenschatmodus in op alleen-lezen. We schakelen ook het filteren van N-grammen op frequentie uit door het minimum in te stellen op 1 instantie en maximaal op 100%, en schakel de functieselectie uit.

Nadat de tekstkolom in testgegevens is omgezet in numerieke functiekolommen, sluiten we de tekenreekskolommen uit van eerdere fasen, zoals in de trainingsbranch. Vervolgens gebruiken we de scoremodelmodule om voorspellingen te doen en de modelmodule te evalueren om de nauwkeurigheid te evalueren.

## <a name="step-5-deploy-the-model-to-production"></a>Stap 5: Het model in productie implementeren
Het model is bijna klaar om te worden ingezet voor productie. Wanneer deze wordt geïmplementeerd als webservice, wordt de teksttekenreeks in vrije vorm als invoer beschouwd en wordt een voorspelling 'hoog' of 'laag' weergegeven. Het maakt gebruik van de geleerde N-gram woordenschat om de tekst te transformeren naar functies, en getraind logistieke regressie model om een voorspelling van deze functies te maken. 

Om het voorspellende experiment op te zetten, slaan we eerst de N-gram woordenschat op als gegevensset en het getrainde logistieke regressiemodel van de trainingstak van het experiment. Vervolgens slaan we het experiment op met 'Opslaan als' om een experimentgrafiek voor voorspellend experiment te maken. We verwijderen de module Split Data en de trainingstak uit het experiment. Vervolgens verbinden we de eerder opgeslagen N-gram woordenschat en het model met respectievelijk N-Gram Features en Score Model modules. We verwijderen ook de module Model evalueren.

We voegen De module Kolommen selecteren in de module Gegevensset in voordat de module Tekst vooraf wordt verwerkt om de labelkolom te verwijderen en de optie 'Puntkolom toevoegen aan gegevensset' in de scoremodule uit te schakelen. Op die manier vraagt de webservice niet om het label dat het probeert te voorspellen en sluit de invoerfuncties als reactie niet door.

![Voorspellend experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu hebben we een experiment dat kan worden gepubliceerd als een webservice en wordt aangeroepen met behulp van request-response of batch execution API's.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over tekstanalysemodules uit [MSDN-documentatie](https://msdn.microsoft.com/library/azure/dn905886.aspx).

