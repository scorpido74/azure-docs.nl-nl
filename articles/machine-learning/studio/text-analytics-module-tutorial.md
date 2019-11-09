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
ms.openlocfilehash: 2e5cebb05549c2bd3cd810da58930efdae1422e4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838625"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Een sentiment-analyse model maken in Azure Machine Learning Studio (klassiek)

U kunt Azure Machine Learning Studio (klassiek) gebruiken om tekst analyse modellen te bouwen en te operationeel maken. Deze modellen kunnen u helpen bij het oplossen van bijvoorbeeld document classificatie-of sentiment analyse problemen.

In een tekst analyse-experiment zou u normaal gesp roken het volgende doen:

1. Tekst gegevensset voor Clean en preproces
2. Numerieke functie vectoren extra heren van vooraf verwerkte tekst
3. Classificatie of regressie model trainen
4. Het model beoordelen en valideren
5. Het model implementeren naar productie

In deze zelf studie leert u deze stappen te volgen tijdens het door lopen van een sentiment-analyse model met behulp van de Amazon Book revisies-gegevensset (zie dit Engelstalige document "biographs, Bollywood, walkie: Domain aanpassing for sentiment classificatie" door John Blitzer, Mark Dredze en Fernando Pereira; Koppeling van de reken kundige taal (ACL), 2007.) Deze gegevensset bestaat uit beoordelings scores (1-2 of 4-5) en een vrije-vorm tekst. Het doel is om de beoordelings score te voors pellen: laag (1-2) of hoog (4-5).

In deze zelf studie vindt u de experimenten die worden behandeld op Azure AI Gallery:

[Voors pellen Boek Recensies](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Revisies boeken-voor speld experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Stap 1: tekst gegevensset Clean en preprocess
We beginnen met het experiment door de beoordelings scores in categorische lage en hoge buckets te delen om het probleem te formuleren als een classificatie met twee klassen. We gebruiken de modules [meta gegevens bewerken](https://msdn.microsoft.com/library/azure/dn905986.aspx) en [groeps categorische waarden](https://msdn.microsoft.com/library/azure/dn906014.aspx) .

![Label maken](./media/text-analytics-module-tutorial/create-label.png)

Vervolgens reinigt u de tekst met de [tekst module preprocess](https://msdn.microsoft.com/library/azure/mt762915.aspx) . De reiniging vermindert de ruis in de gegevensset, helpt u de belangrijkste functies te vinden en de nauw keurigheid van het uiteindelijke model te verbeteren. We verwijderen stopwords-veelvoorkomende woorden, zoals "de" of "a", en cijfers, speciale tekens, dubbele tekens, e-mail adressen en Url's. De tekst wordt ook geconverteerd naar kleine letters, lemmatize de woorden en detectie van zinnen die vervolgens worden aangeduid met het symbool | | | in vooraf verwerkte tekst.

![Tekst voorverwerken](./media/text-analytics-module-tutorial/preprocess-text.png)

Wat gebeurt er als u een aangepaste lijst met stopwords wilt gebruiken? U kunt het door geven als optionele invoer. U kunt ook een reguliere C# expressie met aangepaste syntaxis gebruiken om subtekenreeksen te vervangen en woorden te verwijderen door een deel van de spraak: zelfstandig naam woorden, werk woorden of bijvoegingen.

Nadat de voor verwerking is voltooid, worden de gegevens in de Train-en test sets gesplitst.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Stap 2: numerieke functie vectoren extra heren van vooraf verwerkte tekst
Als u een model voor tekst gegevens wilt maken, moet u doorgaans vrije-vorm tekst omzetten in numerieke functie vectoren. In dit voor beeld gebruiken we [N-gram-functies uit](https://msdn.microsoft.com/library/azure/mt762916.aspx) de module tekst om de tekst gegevens om te zetten in deze indeling. In deze module wordt een kolom met door spaties gescheiden woorden gebruikt en wordt een woorden boek met woorden of N-g woorden, die worden weer gegeven in uw gegevensset, berekend. Vervolgens wordt het aantal keer dat elk woord, of N-gram, in elke record wordt weer gegeven en worden er functie vectoren van die aantallen gemaakt. In deze zelf studie stellen we N-gram grootte in op 2. Daarom bevatten onze functie vectoren enkele woorden en combi Naties van twee volgende woorden.

![Uitpakken N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

We Toep assen TF * IDF (term frequentie inverse document frequentie) weging van het aantal N-gram-tellingen. Met deze benadering wordt het gewicht van woorden die regel matig worden weer gegeven in een enkele record toegevoegd, maar zeldzame gegevens over de hele gegevensset. Andere opties zijn binaire, TF en grafiek weging.

Dergelijke tekst functies hebben vaak een hoge mate van dimensionaliteit. Als uw verzameling bijvoorbeeld 100.000 unieke woorden bevat, heeft uw functie ruimte 100.000 dimensies, of meer als N-gram wordt gebruikt. De module N-gram functies uitpakken biedt een set opties om de dimensionaliteit te verminderen. U kunt ervoor kiezen woorden uit te sluiten die korter of lang zijn, of te vaak niet of niet gebruikelijk zijn, maar ook een aanzienlijke voorspellende waarde hebben. In deze zelf studie worden N-gram uitgesloten van minder dan 5 records of in meer dan 80% van de records.

U kunt ook functie selectie gebruiken om alleen de functies te selecteren die het meest overeenkomen met uw Voorspellings doel. We gebruiken Chi-kwadraat functie selectie om 1000-functies te selecteren. U kunt de woorden lijst met geselecteerde woorden of N-gram weer geven door te klikken op de juiste uitvoer van de module N-gram uitpakken.

Als alternatief voor het gebruik van de functie voor het extra heren van N-gram-functies kunt u gebruikmaken van de module hashing. Houd er rekening mee dat [hashing van functies](https://msdn.microsoft.com/library/azure/dn906018.aspx) geen functies voor het selecteren van ingebouwde functies heeft, of TF * IDF weegt.

## <a name="step-3-train-classification-or-regression-model"></a>Stap 3: de classificatie of het regressie model trainen
De tekst is nu omgezet naar numerieke functie kolommen. De gegevensset bevat nog steeds teken reeks kolommen uit eerdere fasen. Daarom gebruiken we select columns in dataset om ze uit te sluiten.

Vervolgens gebruiken we een [logistiek regressie met twee klassen](https://msdn.microsoft.com/library/azure/dn905994.aspx) om ons doel te voors pellen: hoge of lage beoordelings Score. Op dit moment is het probleem met tekst analyse omgezet in een probleem met een normale classificatie. U kunt de hulpprogram ma's die beschikbaar zijn in de klassieke versie van Azure Machine Learning Studio gebruiken om het model te verbeteren. U kunt bijvoorbeeld experimenteren met verschillende classificaties om erachter te komen hoe nauw keurige resultaten worden verstrekt, of door afstemming-afstemming te gebruiken om de nauw keurigheid te verbeteren.

![Training en Score](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Stap 4: het model beoordelen en valideren
Hoe zou u het getrainde model valideren? We beoordelen het aan de hand van de test gegevensset en evalueren de nauw keurigheid. Het model heeft echter de woorden lijst van N-gram en hun gewicht van de trainings gegevensset geleerd. Daarom moeten we deze vocabulaire en de gewichten gebruiken bij het uitpakken van de functies uit de test gegevens, in plaats van de woorden lijst opnieuw te maken. Daarom voegen we de module N-gram-onderdelen toe aan het Score-gedeelte van het experiment, Verbind de uitvoer woordenlijst van de trainings vertakking en stel de woordenlijst modus in op alleen-lezen. We scha kelen ook het filteren van N-gram op frequentie uit door het minimum op 1 en Maxi maal op 100% in te stellen en de functie selectie uit te scha kelen.

Nadat de tekst kolom in test gegevens is getransformeerd naar numerieke functie kolommen, worden de teken reeks kolommen uit eerdere fasen, zoals in de opleidings vertakking, uitgesloten. Vervolgens gebruiken we de module score model om voor spellingen te maken en model module te evalueren om de nauw keurigheid te evalueren.

## <a name="step-5-deploy-the-model-to-production"></a>Stap 5: het model implementeren naar productie
Het model is bijna klaar om te worden geïmplementeerd voor productie. Wanneer de service wordt geïmplementeerd als webservice, krijgt deze een vrije-vorm teken reeks als invoer en retour neren ze een voor spelling van ' hoog ' of ' laag '. Hierbij wordt gebruikgemaakt van de geleerde N-gram vocabulaire om de tekst naar functies en het getrainde logistiek regressie model te transformeren om een voor spelling van deze functies te maken. 

Om het voorspellende experiment in te stellen, slaan we de N-gram vocabulaire als gegevensset op en het regressie model van de getrainde logistiek van het opleidings filiaal van het experiment. Vervolgens slaan we het experiment op met behulp van ' opslaan als ' om een experiment diagram te maken voor voorspellend experiment. We verwijderen de module voor het splitsen van gegevens en de vertakking van de training uit het experiment. Vervolgens verbindt u de eerder opgeslagen woorden lijst N-gram en het model om respectievelijk N-gram-functies en score model modules te extra heren. We verwijderen ook de module evaluate model.

We voegen select columns in dataset module voordat u de tekst module preprocess verwijdert om de kolom label te verwijderen, en de optie Score kolom aan gegevensset toevoegen in Score module te deselecteren. Op die manier vraagt de webservice niet het label aan dat wordt gebruikt om te voors pellen, en worden de invoer functies niet in antwoord echo's.

![Voorspellend experiment](./media/text-analytics-module-tutorial/predictive-text.png)

We hebben nu een experiment dat kan worden gepubliceerd als een webservice en wordt aangeroepen met behulp van Api's voor aanvraag-Response of batch-uitvoering.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over modules voor tekst analyse van [MSDN-documentatie](https://msdn.microsoft.com/library/azure/dn905886.aspx).

