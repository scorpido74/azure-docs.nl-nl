---
title: Feature engineering in data science - Team Data Science Process
description: Legt de doeleinden van feature engineering en geeft voorbeelden van zijn rol in de data enhancement proces van machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721129"
---
# <a name="feature-engineering-in-data-science"></a>Feature engineering in data science
In dit artikel worden de doeleinden van feature engineering uitgelegd en worden voorbeelden gegeven van zijn rol in het proces voor gegevensverbetering van machine learning. De voorbeelden die worden gebruikt om dit proces te illustreren, zijn afkomstig van Azure Machine Learning Studio. 

Deze taak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

Feature engineering probeert de voorspellende kracht van leeralgoritmen te vergroten door functies te maken van ruwe gegevens die het leerproces vergemakkelijken. De engineering en selectie van functies is een onderdeel van de TDSP beschreven in de [Levenscyclus van het Team Data Science Process?](overview.md) Feature engineering en selectie zijn onderdelen van de **ontwikkelfuncties** stap van de TDSP. 

* **feature engineering**: Dit proces probeert extra relevante functies te maken van de bestaande ruwe functies in de gegevens, en om de voorspellende kracht van het leeralgoritme te vergroten.
* **selectie van functies:** Dit proces selecteert de belangrijkste subset van oorspronkelijke gegevensfuncties in een poging om de dimensionaliteit van het trainingsprobleem te verminderen.

Normaal gesproken wordt **feature engineering** eerst toegepast om extra functies te genereren, en vervolgens wordt de **functieselectiestap** uitgevoerd om irrelevante, redundante of sterk gecorreleerde functies te elimineren.

De trainingsgegevens die worden gebruikt in machine learning kunnen vaak worden verbeterd door het extraheren van functies uit de verzamelde ruwe gegevens. Een voorbeeld van een engineered functie in de context van het leren hoe de beelden van handgeschreven tekens te classificeren is het creëren van een bit dichtheid kaart opgebouwd uit de ruwe bit distributie gegevens. Deze kaart kan helpen de randen van de tekens efficiënter te vinden dan alleen de ruwe distributie direct te gebruiken.

Zie de volgende artikelen om functies voor gegevens in specifieke omgevingen te maken:

* [Functies maken voor gegevens in SQL Server](create-features-sql-server.md)
* [Functies maken voor gegevens in een Hadoop-cluster met Hive-query's](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Functies maken op basis van uw gegevens - feature engineering
De trainingsgegevens bestaan uit een matrix die bestaat uit voorbeelden (records of waarnemingen die in rijen zijn opgeslagen), die elk een reeks kenmerken hebben (variabelen of velden die in kolommen zijn opgeslagen). De kenmerken die in het experimentele ontwerp worden gespecificeerd, zullen naar verwachting de patronen in de gegevens karakteriseren. Hoewel veel van de ruwe gegevensvelden direct kunnen worden opgenomen in de geselecteerde functieset die wordt gebruikt om een model te trainen, is het vaak het geval dat extra (engineered) functies moeten worden opgebouwd uit de functies in de ruwe gegevens om een verbeterde trainingsgegevensset te genereren.

Welke functies moeten worden gemaakt om de gegevensset te verbeteren bij het trainen van een model? Ontworpen functies die de training verbeteren, bieden informatie die de patronen in de gegevens beter onderscheidt. De nieuwe functies zullen naar verwachting aanvullende informatie bieden die niet duidelijk is vastgelegd of gemakkelijk zichtbaar is in de oorspronkelijke of bestaande functieset. Maar dit proces is iets van een kunst. Goede en productieve beslissingen vereisen vaak enige domeinexpertise.

Wanneer u begint met Azure Machine Learning, is het het gemakkelijkst om dit proces concreet te begrijpen met behulp van voorbeelden die in de studio worden geleverd. Twee voorbeelden worden hier gepresenteerd:

* Een regressievoorbeeld [Voorspelling van het aantal fietsverhuur](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in een begeleid experiment waarbij de streefwaarden bekend zijn
* Een voorbeeld van tekstminingclassificatie met [Functie Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Voorbeeld 1: Tijdelijke functies toevoegen voor een regressiemodel
Laten we het experiment 'Vraagprognose van fietsen' in Azure Machine Learning Studio (klassiek) gebruiken om te laten zien hoe u functies ontwerpen voor een regressietaak. Het doel van dit experiment is om de vraag naar de fietsen te voorspellen, dat wil zeggen het aantal fietsverhuur binnen een bepaalde maand/dag/uur. De dataset "Bike Rental UCI dataset" wordt gebruikt als de ruwe invoergegevens. Deze dataset is gebaseerd op echte gegevens van het Capital Bikeshare bedrijf dat een fietsverhuurnetwerk onderhoudt in Washington DC in de Verenigde Staten. De dataset vertegenwoordigt het aantal fietsverhuur binnen een bepaald uur van een dag in de jaren 2011 en het jaar 2012 en bevat 17379 rijen en 17 kolommen. De raw feature set bevat weersomstandigheden (temperatuur/vochtigheid/windsnelheid) en het type dag (vakantie/weekdag). Het te voorspellen veld is de 'cnt'-telling, die de fietsverhuur binnen een bepaald uur vertegenwoordigt en varieert van 1 tot 977.

Met als doel het bouwen van effectieve functies in de trainingsgegevens, worden vier regressiemodellen gebouwd met behulp van hetzelfde algoritme, maar met vier verschillende trainingsgegevenssets. De vier gegevenssets vertegenwoordigen dezelfde ruwe invoergegevens, maar met een toenemend aantal functies ingesteld. Deze functies zijn gegroepeerd in vier categorieën:

1. A = weer + vakantie + doordeweeks + weekend kenmerken voor de voorspelde dag
2. B = aantal fietsen dat in elk van de voorgaande 12 uur werd gehuurd
3. C = aantal fietsen dat in elk van de voorgaande 12 dagen op hetzelfde uur werd gehuurd
4. D = aantal fietsen dat in elk van de voorgaande 12 weken op hetzelfde uur en dezelfde dag werd gehuurd

Naast functieset A, die al bestaat in de oorspronkelijke ruwe gegevens, worden de andere drie sets functies gemaakt via het feature engineering proces. Feature set B vangt de recente vraag naar de fietsen. Feature set C vangt de vraag naar fietsen op een bepaald uur. Feature set D vangt de vraag naar fietsen op bepaalde uur en bepaalde dag van de week. De vier trainingsgegevenssets bevatten elk functieset A, A+B, A+B+C en A+B+C+D.

In het Azure Machine Learning-experiment worden deze vier trainingsgegevenssets gevormd via vier branches uit de vooraf verwerkte invoergegevensset. Behalve de meest linkse vertakking bevat elk van deze branches een [Execute R Script-module,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) waarin de afgeleide functies (functieset B, C en D) respectievelijk zijn opgebouwd en toegevoegd aan de geïmporteerde gegevensset. De volgende figuur toont het R-script dat wordt gebruikt om functieset B te maken in de tweede linkervertakking.

![functies maken](./media/create-features/addFeature-Rscripts.png)

Een vergelijking van de prestatieresultaten van de vier modellen wordt samengevat in de volgende tabel: 

![resultaatvergelijking](./media/create-features/result1.png)

De beste resultaten worden weergegeven door functies A+B+C. Het foutenpercentage neemt af wanneer extra functieset is opgenomen in de trainingsgegevens. Het controleert het vermoeden dat de functieset B, C aanvullende relevante informatie voor de regressietaak bevat. Maar het toevoegen van de D-functie lijkt geen extra vermindering van het foutenpercentage te bieden.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Voorbeeld 2: Functies maken in tekstmining
Feature engineering wordt op grote schaal toegepast in taken met betrekking tot tekstmining, zoals documentclassificatie en sentimentanalyse. Wanneer u bijvoorbeeld documenten in verschillende categorieën wilt classificeren, is een typische aanname dat het woord/de woordgroepen die in één documentcategorie zijn opgenomen, minder waarschijnlijk voorkomen in een andere documentcategorie. Met andere woorden, de frequentie van de verdeling van woorden/zinnen is in staat om verschillende documentcategorieën te karakteriseren. In tekst mining toepassingen, omdat individuele stukken van tekst-inhoud meestal dienen als de input gegevens, de functie engineering proces is nodig om de functies met betrekking tot woord / zin frequenties te creëren.

Om deze taak te bereiken, wordt een techniek genaamd **feature hashing** toegepast om willekeurige tekstfuncties efficiënt om te zetten in indexen. In plaats van elke tekstfunctie (woorden/zinnen) aan een bepaalde index te koppelen, functioneert deze methode door een hashfunctie toe te passen op de functies en hun hashwaarden rechtstreeks als indexten te gebruiken.

In Azure Machine Learning is er een [Feature Hashing-module](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) die handig functies voor woord/woordgroep maakt. Onderstaande afbeelding toont een voorbeeld van het gebruik van deze module. De invoergegevensset bevat twee kolommen: de boekclassificatie variërend van 1 tot 5 en de werkelijke recensie-inhoud. Het doel van deze [Feature Hashing-module](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) is om een aantal nieuwe functies op te halen die de gebeurtenisfrequentie van het overeenkomstige woord(en)/zin(en) in de specifieke boekbespreking weergeven. Voer de volgende stappen uit om deze module te gebruiken:

* Selecteer eerst de kolom die de invoertekst bevat ('Col2' in dit voorbeeld).
* Ten tweede stelt u de "Hashing bitsize" in op 8, wat betekent dat 2^8=256 functies worden gemaakt. Het woord/fase in alle tekst wordt gehasht tot 256 indexen. De parameter "Hashing bitsize" varieert van 1 tot 31. Het woord(en)/zin(en) wordt minder snel in dezelfde index gehasht als het een groter getal wordt ingesteld.
* Ten derde stelt u de parameter "N-grammen" in op 2. Deze waarde krijgt de voorkomen frequentie van unigrammen (een functie voor elk woord) en bigrammen (een functie voor elk paar aangrenzende woorden) uit de invoertekst. De parameter "N-grammen" varieert van 0 tot 10, wat het maximum aantal opeenvolgende woorden aangeeft dat in een functie moet worden opgenomen.  

![Module "Feature Hashing"](./media/create-features/feature-Hashing1.png)

De volgende figuur laat zien hoe deze nieuwe functie eruit ziet.

![Voorbeeld "Feature Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusie
Ontworpen en geselecteerde functies verhogen de efficiëntie van het trainingsproces, dat probeert de belangrijkste informatie in de gegevens te extraheren. Ze verbeteren ook de kracht van deze modellen om de invoergegevens nauwkeurig te classificeren en om de resultaten van belang robuuster te voorspellen. Feature engineering en selectie kunnen ook combineren om het leren meer computationeel te maken. Het doet dit door het verbeteren en vervolgens verminderen van het aantal functies die nodig zijn om te kalibreren of trainen van een model. Wiskundig gezien zijn de functies die zijn geselecteerd om het model te trainen een minimale set onafhankelijke variabelen die de patronen in de gegevens verklaren en vervolgens resultaten met succes voorspellen.

Het is niet altijd noodzakelijk om functie-engineering of functie selectie uit te voeren. Of het nodig is of niet hangt af van de gegevens bij de hand of verzameld, het algoritme geselecteerd, en het doel van het experiment.

