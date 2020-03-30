---
title: Modelresultaten interpreteren
titleSuffix: ML Studio (classic) - Azure
description: Hoe de optimale parameterset voor een algoritme te kiezen met behulp van en visualiseren van scoremodeluitgangen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218072"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Modelresultaten interpreteren in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
In dit onderwerp wordt uitgelegd hoe u voorspellingsresultaten visualiseert en interpreteert in Azure Machine Learning Studio (klassiek). Nadat u een model hebt getraind en er voorspellingen bovenop hebt gedaan ("het model gescoord"), moet u het voorspellingsresultaat begrijpen en interpreteren.



Er zijn vier belangrijke soorten machine learning-modellen in Azure Machine Learning Studio (klassiek):

* Classificatie
* Clustering
* Regressie
* Recommendersystemen

De modules die worden gebruikt voor voorspelling op de top van deze modellen zijn:

* [Scoremodelmodule][score-model] voor classificatie en regressie
* [Module Clusters toewijzen][assign-to-clusters] voor clustering
* [Score Matchbox Recommender][score-matchbox-recommender] voor aanbevelingssystemen

In dit document wordt uitgelegd hoe voorspellingsresultaten voor elk van deze modules kunnen worden geïnterpreteerd. Zie Parameters kiezen om [uw algoritmen te optimaliseren in Azure Machine Learning Studio (klassiek)](algorithm-parameters-optimize.md)voor een overzicht van deze modules.

Dit onderwerp richt zich op voorspelling interpretatie, maar niet model evaluatie. Zie De prestaties van het [model evalueren in Azure Machine Learning Studio (klassiek)](evaluate-model-performance.md)voor meer informatie over het evalueren van uw model.

Zie [Een eenvoudig experiment maken in Azure Machine Learning Studio (klassiek)](create-experiment.md)als u nieuw bent in Azure Machine Learning Studio (klassiek) en hulp nodig hebt bij het maken van een eenvoudig experiment om aan de slag te gaan.

## <a name="classification"></a>Classificatie
Er zijn twee subcategorieën van classificatieproblemen:

* Problemen met slechts twee klassen (twee-klasse of binaire classificatie)
* Problemen met meer dan twee klassen (meerklasseclassificatie)

Azure Machine Learning Studio (klassiek) heeft verschillende modules om te gaan met elk van deze soorten classificatie, maar de methoden voor het interpreteren van hun voorspellingresultaten zijn vergelijkbaar.

### <a name="two-class-classification"></a>Classificatie van twee klassen
**Voorbeeldexperiment**

Een voorbeeld van een classificatieprobleem van twee klassen is de classificatie van irisbloemen. De taak is om irisbloemen te classificeren op basis van hun kenmerken. De Iris-gegevensset in Azure Machine Learning Studio (klassiek) is een subset van de populaire [Iris-gegevensset](https://en.wikipedia.org/wiki/Iris_flower_data_set) met exemplaren van slechts twee bloemsoorten (klassen 0 en 1). Er zijn vier kenmerken voor elke bloem (sepal lengte, sepal breedte, bloemblaadjeslengte, en bloemblaadje breedte).

![Schermafbeelding van het irisexperiment](./media/interpret-model-results/1.png)

Afbeelding 1. Iris twee-klasse classificatie probleem experiment

Er is een experiment uitgevoerd om dit probleem op te lossen, zoals in figuur 1 wordt weergegeven. Een twee-klasse gestimuleerd beslissingboom model is opgeleid en scoorde. Nu u de voorspellingsresultaten van de module [Scoremodel][score-model] visualiseren door op de uitvoerpoort van de module [Scoremodel][score-model] te klikken en vervolgens op **Visualiseren**te klikken.

![Scoremodelmodule](./media/interpret-model-results/1_1.png)

Dit brengt de scoreresultaten zoals weergegeven in figuur 2.

![Resultaten van iris tweeklassenclassificatie-experiment](./media/interpret-model-results/2.png)

Afbeelding 2. Een scoremodelresultaat visualiseren in classificatie in twee klassen

**Resultaatinterpretatie**

Er zijn zes kolommen in de resultatentabel. De linker vier kolommen zijn de vier kenmerken. De juiste twee kolommen, Scored Labels en Scored Probabilities, zijn de voorspellingresultaten. De kolom Gescoorde waarschijnlijkheden toont de waarschijnlijkheid dat een bloem tot de positieve klasse behoort (klasse 1). Het eerste getal in de kolom (0,028571) betekent bijvoorbeeld dat de kans 0,028571 is dat de eerste bloem tot klasse 1 behoort. In de kolom Gescoorde labels wordt de voorspelde klasse voor elke bloem weergegeven. Dit is gebaseerd op de kolom Gescoorde waarschijnlijkheden. Als de gescoorde kans op een bloem groter is dan 0,5, wordt deze voorspeld als klasse 1. Anders wordt het voorspeld als klasse 0.

**Publicatie van webservice**

Nadat de voorspellingsresultaten zijn begrepen en beoordeeld geluid, kan het experiment worden gepubliceerd als een webservice, zodat u het implementeren in verschillende toepassingen en het bellen om klassevoorspellingen te verkrijgen over een nieuwe irisbloem. Zie [Leerplan 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md)voor meer informatie over het wijzigen van een trainingsexperiment in een scoreexperiment en het publiceren als een webservice. Deze procedure biedt u een score-experiment zoals weergegeven in figuur 3.

![Schermafbeelding van het score-experiment](./media/interpret-model-results/3.png)

Afbeelding 3. Het classificatieprobleemexperiment met iris tweeklassen scoren

Nu moet u de invoer en uitvoer voor de webservice instellen. De input is de juiste invoerpoort van [Score Model][score-model], dat is de Iris bloem functies input. De keuze van de uitvoer hangt af van de vraag of u geïnteresseerd bent in de voorspelde klasse (gescoord label), de gescoorde waarschijnlijkheid, of beide. In dit voorbeeld wordt aangenomen dat u geïnteresseerd bent in beide. Als u de gewenste uitvoerkolommen wilt selecteren, gebruikt u een module [Kolommen selecteren in gegevensset.][select-columns] Klik [op Kolommen selecteren in gegevensset,][select-columns]klik op **Kolomkiezer starten**en selecteer **Gescoorde labels** en **gescoorde waarschijnlijkheden**. Nadat u de uitvoerpoort van [Kolommen selecteren in gegevensset][select-columns] hebt ingesteld en opnieuw hebt uitgevoerd, moet u klaar zijn om het scoreexperiment als webservice te publiceren door op **WEBSERVICE PUBLICEREN**te klikken. Het laatste experiment lijkt op figuur 4.

![Het classificatie-experiment met iris twee klassen](./media/interpret-model-results/4.png)

Afbeelding 4: Eindscore-experiment van een iris tweeklassenclassificatieprobleem

Nadat u de webservice hebt uitgevoerd en een aantal functiewaarden van een testinstantie hebt ingevoerd, retourneert het resultaat twee getallen. Het eerste getal is het gescoorde label, en het tweede is de gescoorde waarschijnlijkheid. Deze bloem wordt voorspeld als Klasse 1 met 0.9655 waarschijnlijkheid.

![Scoremodel voor het interpreteren van tests](./media/interpret-model-results/4_1.png)

![Testresultaten scoren](./media/interpret-model-results/5.png)

Afbeelding 5. Webservice resultaat van iris tweeklassenclassificatie

### <a name="multi-class-classification"></a>Classificatie van meerdere klassen
**Voorbeeldexperiment**

In dit experiment voert u een taak voor letterherkenning uit als voorbeeld van classificatie van meerdere klassen. De classificatie probeert een bepaalde letter (klasse) te voorspellen op basis van enkele handgeschreven attribuutwaarden die uit de handgeschreven afbeeldingen zijn gehaald.

![Voorbeeld van briefherkenning](./media/interpret-model-results/5_1.png)

In de trainingsgegevens zijn er 16 functies uit handgeschreven briefafbeeldingen. De 26 letters vormen onze 26 klassen. Figuur 6 toont een experiment dat een classificatiemodel voor meerdere klassen voor letterherkenning traint en voorspelt op dezelfde functieset op een testgegevensset.

![Experiment met meerklassenclassificatie van letterherkenning](./media/interpret-model-results/6.png)

Afbeelding 6. Experiment met probleemexperiment voor probleem van de letterherkenning met meerdere klassen

Visualiseren van de resultaten van de [module Scoremodel][score-model] door op de uitvoerpoort van de module [Scoremodel][score-model] te klikken en vervolgens op **Visualiseren**te klikken, moet u inhoud zien zoals weergegeven in figuur 7.

![Modelresultaten score](./media/interpret-model-results/7.png)

Afbeelding 7. Scoremodelresultaten visualiseren in een classificatie met meerdere klassen

**Resultaatinterpretatie**

De linker 16 kolommen vertegenwoordigen de functiewaarden van de testset. De kolommen met namen als Gescoorde waarschijnlijkheden voor klasse "XX" zijn net als de kolom Gescoorde waarschijnlijkheden in de tweeklassenkwestie. Ze tonen de waarschijnlijkheid dat de overeenkomstige vermelding valt in een bepaalde klasse. Bijvoorbeeld, voor de eerste vermelding, is er 0,003571 kans dat het een "A", 0,000451 kans dat het een "B", enzovoort. De laatste kolom (Scored Labels) is hetzelfde als Scored Labels in de tweeklassenkwestie. Het selecteert de klasse met de grootste gescoorde waarschijnlijkheid als de voorspelde klasse van de overeenkomstige vermelding. Voor de eerste vermelding is het label bijvoorbeeld 'F' omdat het de grootste kans heeft om een "F" te zijn (0,916995).

**Publicatie van webservice**

U ook het gescoorde label voor elk item en de waarschijnlijkheid van het gescoorde label krijgen. De basislogica is het vinden van de grootste waarschijnlijkheid onder alle gescoorde waarschijnlijkheden. Hiervoor moet u de Script-module [Uitvoeren][execute-r-script] gebruiken. De R-code wordt weergegeven in figuur 8 en het resultaat van het experiment wordt weergegeven in figuur 9.

![Voorbeeld van R-code](./media/interpret-model-results/8.png)

Afbeelding 8. R-code voor het extraheren van gescoorde labels en de bijbehorende waarschijnlijkheid van de labels

![Experimentresultaat](./media/interpret-model-results/9.png)

Figuur 9. Eindscore-experiment van het probleem van de meervoudige klasseclassificatie van de letterherkenning

Nadat u de webservice hebt gepubliceerd en uitgevoerd en enkele waarden van invoerfuncties hebt ingevoerd, lijkt het geretourneerde resultaat op figuur 10. Deze handgeschreven brief, met zijn geëxtraheerd 16 functies, wordt voorspeld dat een "T" met 0,9715 waarschijnlijkheid.

![Module voor het interpreteren van de score testen](./media/interpret-model-results/9_1.png)

![Testresultaat](./media/interpret-model-results/10.png)

Figuur 10. Resultaat van de webservice van classificatie met meerdere klassen

## <a name="regression"></a>Regressie
Regressieproblemen verschillen van classificatieproblemen. In een classificatieprobleem probeert u afzonderlijke klassen te voorspellen, zoals tot welke klasse een irisbloem behoort. Maar zoals u zien in het volgende voorbeeld van een regressieprobleem, probeert u een continue variabele te voorspellen, zoals de prijs van een auto.

**Voorbeeldexperiment**

Gebruik auto prijsvoorspelling als uw voorbeeld voor regressie. U probeert de prijs van een auto te voorspellen op basis van de functies, waaronder merk, brandstoftype, carrosserie en aandrijfwiel. Het experiment wordt weergegeven in figuur 11.

![Auto prijs regressie experiment](./media/interpret-model-results/11.png)

Figuur 11. Auto prijs regressie probleem experiment

Visualiseren van de [score model][score-model] module, het resultaat ziet eruit als figuur 12.

![Scoreresultaten voor autoprijs voorspelling probleem](./media/interpret-model-results/12.png)

Figuur 12. Scoreresultaat voor de auto prijs voorspelling probleem

**Resultaatinterpretatie**

Scored Labels is de resultaatkolom in dit scoreresultaat. De cijfers zijn de voorspelde prijs voor elke auto.

**Publicatie van webservice**

U het regressie-experiment publiceren in een webservice en het op dezelfde manier voor prijsvoorspelling van auto's aanroepen als in de use case voor classificatie van twee klassen.

![Score experiment voor auto prijs regressie probleem](./media/interpret-model-results/13.png)

Figuur 13. Score experiment van een auto prijs regressie probleem

Het geretourneerde resultaat, dat de webservice uitvoert, lijkt op figuur 14. De voorspelde prijs voor deze auto is $15.085.52.

![Module voor het interpreteren van scores testen](./media/interpret-model-results/13_1.png)

![Resultaten van de scoremodule](./media/interpret-model-results/14.png)

Figuur 14. Web service resultaat van een auto prijs regressie probleem

## <a name="clustering"></a>Clustering
**Voorbeeldexperiment**

Laten we de Iris-gegevensset opnieuw gebruiken om een clusterexperiment te maken. Hier u de klassenlabels in de gegevensset filteren, zodat deze alleen functies heeft en kan worden gebruikt voor clustering. Geef in deze gebruikscase het aantal clusters op dat tijdens het trainingsproces twee moet zijn, wat betekent dat u de bloemen in twee klassen zou clusteren. Het experiment wordt getoond in figuur 15.

![Iris clustering probleem experiment](./media/interpret-model-results/15.png)

Figuur 15. Iris clustering probleem experiment

Clustering verschilt van classificatie in die tijd dat de trainingsgegevensset op zichzelf geen ground-truth labels heeft. Clustering groepeert de trainingsgegevenssetinstanties in afzonderlijke clusters. Tijdens het trainingsproces labelt het model de vermeldingen door de verschillen tussen hun functies te leren. Daarna kan het getrainde model worden gebruikt om toekomstige vermeldingen verder te classificeren. Er zijn twee delen van het resultaat waarin we geïnteresseerd zijn binnen een clustering probleem. Het eerste deel is het labelen van de trainingsgegevensset, en het tweede is het classificeren van een nieuwe gegevensset met het getrainde model.

Het eerste deel van het resultaat kan worden gevisualiseerd door op de linkeruitvoerpoort van [Train Clustering Model te][train-clustering-model] klikken en vervolgens op **Visualiseren**te klikken. De visualisatie wordt weergegeven in figuur 16.

![Clusterresultaat](./media/interpret-model-results/16.png)

Figuur 16. Clusteringresultaat visualiseren voor de trainingsgegevensset

Het resultaat van het tweede deel, waarin nieuwe items worden geclusterd met het getrainde clustermodel, wordt weergegeven in figuur 17.

![Clusteringresultaat visualiseren](./media/interpret-model-results/17.png)

Figuur 17. Clusteringresultaat visualiseren op een nieuwe gegevensset

**Resultaatinterpretatie**

Hoewel de resultaten van de twee delen voortkomen uit verschillende experimentfasen, zien ze er hetzelfde uit en worden ze op dezelfde manier geïnterpreteerd. De eerste vier kolommen zijn kenmerken. De laatste kolom, Opdrachten, is het voorspellingsresultaat. De items toegewezen hetzelfde nummer worden voorspeld te zijn in hetzelfde cluster, dat wil zeggen, ze delen gelijkenissen op een bepaalde manier (dit experiment maakt gebruik van de standaard Euclidische afstand metrische). Omdat u het aantal clusters als 2 hebt opgegeven, worden de vermeldingen in toewijzingen aangeduid als 0 of 1.

**Publicatie van webservice**

U het clusterexperiment publiceren in een webservice en het voor het clusteren van voorspellingen op dezelfde manier aanroepen als in de use case voor classificatie met twee klassen.

![Scoreexperiment voor irisclusteringprobleem](./media/interpret-model-results/18.png)

Figuur 18. Scoreexperiment van een irisclusteringprobleem

Nadat u de webservice hebt uitgevoerd, lijkt het geretourneerde resultaat op figuur 19. Deze bloem is naar verwachting in cluster 0.

![Module Interpreteren van tolken testen](./media/interpret-model-results/18_1.png)

![Resultaat scoremodule](./media/interpret-model-results/19.png)

Figuur 19. Webservice resultaat van iris tweeklassenclassificatie

## <a name="recommender-system"></a>Recommendersysteem
**Voorbeeldexperiment**

Voor aanbevelingssystemen u het restaurantaanbevelingsprobleem als voorbeeld gebruiken: u restaurants aanbevelen voor klanten op basis van hun beoordelingsgeschiedenis. De invoergegevens bestaan uit drie delen:

* Restaurantbeoordelingen van klanten
* Gegevens over klantfuncties
* Gegevens over de restaurantfunctie

Er zijn verschillende dingen die we kunnen doen met de [Train Matchbox Recommender-module][train-matchbox-recommender] in Azure Machine Learning Studio (klassiek):

* Beoordelingen voor een bepaalde gebruiker en item voorspellen
* Items aanbevelen aan een bepaalde gebruiker
* Gebruikers zoeken die gerelateerd zijn aan een bepaalde gebruiker
* Objecten zoeken die betrekking hebben op een bepaald object

U kiezen wat u wilt doen door te selecteren uit de vier opties in het menu **Voorspellingstype recommender.** Hier u alle vier de scenario's doorlopen.

![Matchbox recommender](./media/interpret-model-results/19_1.png)

Een typisch Azure Machine Learning Studio (klassiek) experiment voor een recommender-systeem lijkt op figuur 20. Zie [Treinmatchbox recommender][train-matchbox-recommender] en [Score matchbox recommender][score-matchbox-recommender]voor informatie over het gebruik van deze systeemmodules.

![Systeemexperiment recommender](./media/interpret-model-results/20.png)

Figuur 20. Systeemexperiment recommender

**Resultaatinterpretatie**

**Beoordelingen voor een bepaalde gebruiker en item voorspellen**

Door **Rating Prediction** te selecteren onder **Recommender voorspelling soort**, vraagt u de recommender systeem om de rating voor een bepaalde gebruiker en item te voorspellen. De visualisatie van de [recommender-uitvoer van de scorematchbox][score-matchbox-recommender] ziet eruit als figuur 21.

![Score resultaat van het recommender systeem -- voorspelling van rating](./media/interpret-model-results/21.png)

Figuur 21. Visualiseer het scoreresultaat van het recommender-systeem - rating voorspelling

De eerste twee kolommen zijn de gebruikersitemparen die door de invoergegevens worden geleverd. De derde kolom is de voorspelde beoordeling van een gebruiker voor een bepaald item. In de eerste rij wordt bijvoorbeeld voorspeld dat de Klant U1048 restaurant 135026 als 2 zal beoordelen.

**Items aanbevelen aan een bepaalde gebruiker**

Door **Aanbeveling voor items** te selecteren onder **voorspellingstype recommender,** vraagt u het recommender-systeem om items aan te bevelen aan een bepaalde gebruiker. De laatste parameter die u in dit scenario moet kiezen, is *Aanbevolen itemselectie*. De optie **Van Beoordeelde Items (voor modelevaluatie)** is voornamelijk voor modelevaluatie tijdens het trainingsproces. Voor deze voorspellingsfase kiezen we **uit alle items.** De visualisatie van de [recommender-uitvoer van de scorematchbox][score-matchbox-recommender] ziet eruit als figuur 22.

![Scoreresultaat van recommendersysteem -- aanbeveling voor items](./media/interpret-model-results/22.png)

Figuur 22. Scoreresultaat van het recommender-systeem visualiseren - itemaanbeveling

De eerste van de zes kolommen vertegenwoordigt de opgegeven gebruikers-id's om items aan te bevelen voor, zoals die door de invoergegevens. De andere vijf kolommen vertegenwoordigen de items die aan de gebruiker worden aanbevolen in aflopende volgorde van relevantie. Op de eerste rij is het aanbevolen restaurant voor klant U1048 bijvoorbeeld 134986, gevolgd door 135018, 134975, 135021 en 132862.

**Gebruikers zoeken die gerelateerd zijn aan een bepaalde gebruiker**

Door **gerelateerde gebruikers** te selecteren onder **recommender voorspelling soort**, vraagt u de recommender systeem om gerelateerde gebruikers te vinden om een bepaalde gebruiker. Gerelateerde gebruikers zijn de gebruikers die vergelijkbare voorkeuren hebben. De laatste parameter die u in dit scenario moet kiezen, is *Gerelateerde gebruikersselectie*. De optie **Van Gebruikers die punten (voor modelevaluatie) beoordeelden** is hoofdzakelijk voor modelevaluatie tijdens het opleidingsproces. Kies **Uit alle gebruikers** voor deze voorspellingsfase. De visualisatie van de [recommender-uitvoer van de scorematchbox][score-matchbox-recommender] ziet eruit als figuur 23.

![Scoreresultaat van recommender systeem -gerelateerde gebruikers](./media/interpret-model-results/23.png)

Figuur 23. Scoreresultaten van het recommender-systeem visualiseren- gebruikers

De eerste van de zes kolommen toont de opgegeven gebruiker ID's die nodig zijn om gerelateerde gebruikers te vinden, zoals die door middel van input gegevens. De andere vijf kolommen slaan de voorspelde gerelateerde gebruikers van de gebruiker op in aflopende volgorde van relevantie. In de eerste rij is u1048 bijvoorbeeld de meest relevante klant voor de Klant U1051, gevolgd door U1066, U1044, U1017 en U1072.

**Objecten zoeken die betrekking hebben op een bepaald object**

Door **gerelateerde items** te selecteren onder **voorspellingstype Recommender,** vraagt u het recommender-systeem om gerelateerde items voor een bepaald item te zoeken. Gerelateerde items zijn de objecten die waarschijnlijk door dezelfde gebruiker worden geliked. De laatste parameter die u in dit scenario moet kiezen, is *Gerelateerde itemselectie*. De optie **Van Beoordeelde Items (voor modelevaluatie)** is voornamelijk voor modelevaluatie tijdens het trainingsproces. Wij kiezen **Uit Alle items** voor deze voorspellingsfase. De visualisatie van de [recommender-uitvoer van de scorematchbox][score-matchbox-recommender] ziet eruit als figuur 24.

![Scoreresultaat van recommender systeem -gerelateerde items](./media/interpret-model-results/24.png)

Figuur 24. Scoreresultaten van het recommender-systeem visualiseren- gerelateerde items

De eerste van de zes kolommen vertegenwoordigt het opgegeven item ID's die nodig zijn om gerelateerde items te vinden, zoals die door de invoergegevens. De andere vijf kolommen slaan de voorspelde gerelateerde items van het artikel in aflopende volgorde op in termen van relevantie. Op de eerste rij bedraagt het meest relevante artikel voor artikel 135026 bijvoorbeeld 135074, gevolgd door 135035, 132875, 135055 en 134992.

**Publicatie van webservice**

Het proces van het publiceren van deze experimenten als webservices om voorspellingen te krijgen is vergelijkbaar voor elk van de vier scenario's. Hier nemen we het tweede scenario (items aanbevelen aan een bepaalde gebruiker) als voorbeeld. U dezelfde procedure volgen met de andere drie.

Als u het getrainde recommender-systeem opslaat als een getraind model en de invoergegevens filtert naar één kolom met gebruikers-id's zoals gevraagd, u het experiment aansluiten zoals in figuur 25 en het als webservice publiceren.

![Scoreexperiment van het restaurant aanbevelingprobleem](./media/interpret-model-results/25.png)

Figuur 25. Scoreexperiment van het restaurant aanbevelingprobleem

Het geretourneerde resultaat, dat de webservice uitvoert, lijkt op figuur 26. De vijf aanbevolen restaurants voor gebruiker U1048 zijn 134986, 135018, 134975, 135021 en 132862.

![Voorbeeld van recommendersysteemservice](./media/interpret-model-results/25_1.png)

![Resultaten van voorbeeldexperimenten](./media/interpret-model-results/26.png)

Figuur 26. Webservice resultaat van restaurant aanbeveling probleem

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
