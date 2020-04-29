---
title: Functie techniek in data wetenschappen-team data Science process
description: Hierin worden de doel einden van functie-engineering beschreven en vindt u voor beelden van de rol ervan in het proces voor gegevens verbetering van machine learning.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721129"
---
# <a name="feature-engineering-in-data-science"></a>Functie techniek in data wetenschappen
In dit artikel wordt uitgelegd wat het doel is van functie techniek en vindt u voor beelden van hun rol in het proces voor gegevens verbetering van machine learning. De voor beelden die worden gebruikt voor het illustreren van dit proces, worden getrokken van Azure Machine Learning Studio. 

Deze taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Feature engineering probeert de voorspellende kracht van leer algoritmen te verhogen door functies te maken op basis van onbewerkte gegevens die het leer proces helpen vergemakkelijken. De techniek en selectie van functies is een onderdeel van de TDSP die wordt beschreven in de [informatie over de levens cyclus van het team data Science process.](overview.md) Functie techniek en selectie zijn onderdelen van de stap **functies ontwikkelen** van de TDSP. 

* **functie techniek**: dit proces probeert extra relevante functies te maken op basis van de bestaande onbewerkte functies in de gegevens en om de voorspellende kracht van het leer algoritme te verg Roten.
* **functie selectie**: dit proces selecteert de sleutel subset van de oorspronkelijke gegevens functies in een poging om de dimensionaliteit van het trainings probleem te reduceren.

Normaal gesp roken **functie techniek** wordt eerst toegepast om extra functies te genereren, en vervolgens wordt de **functie selectie** stap uitgevoerd om irrelevante, redundante of zeer gecorreleerde functies te elimineren.

De trainings gegevens die in machine learning worden gebruikt, kunnen vaak worden verbeterd door het uitpakken van functies uit de verzamelde gegevens. Een voor beeld van een functie die is ontworpen in de context van het leren hoe u de afbeeldingen van handgeschreven tekens kunt classificeren, is het maken van een kaart met een beetje dichtheids die is samengesteld uit de gegevens van de ruwe bits distributie. Deze kaart kan helpen om de randen van de tekens efficiënter te vinden dan eenvoudigweg de onbewerkte distributie rechtstreeks te gebruiken.

Raadpleeg de volgende artikelen voor informatie over het maken van functies voor gegevens in specifieke omgevingen:

* [Functies maken voor gegevens in SQL Server](create-features-sql-server.md)
* [Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query's](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Functies maken op basis van uw gegevens functie techniek
De trainings gegevens bestaan uit een matrix die bestaat uit voor beelden (records of waarnemingen die zijn opgeslagen in rijen), die elk een set functies hebben (variabelen of velden die in kolommen zijn opgeslagen). De functies die zijn opgegeven in het experimentele ontwerp, worden verwacht het karakteriseren van de patronen in de gegevens. Hoewel veel van de onbewerkte gegevens velden rechtstreeks kunnen worden opgenomen in de geselecteerde functieset voor het trainen van een model, is het vaak het geval dat extra (ontworpen) functies moeten worden samengesteld op basis van de functies in de onbewerkte gegevens om een verbeterde trainings gegevensset te genereren.

Wat voor soort functies moet worden gemaakt om de gegevensset te verbeteren bij het trainen van een model? Functies die zijn ontworpen om de training uit te breiden, bevatten informatie die de patronen in de gegevens beter onderscheidt. De nieuwe functies worden verwacht om aanvullende informatie te verstrekken die niet duidelijk is vastgelegd of gemakkelijk zichtbaar is in de oorspronkelijke of bestaande functieset. Maar dit proces is iets van een art. Geluids-en productiviteits beslissingen vereisen vaak enkele domein expertise.

Wanneer u begint met Azure Machine Learning, kunt u dit proces het beste beton begrijpt met behulp van de voor beelden in de Studio. Er worden twee voor beelden weer gegeven:

* Een voor beeld van een regressie voor voor [Spelling van het aantal fiets huren](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in een experiment met toezicht waarbij de doel waarden bekend zijn
* Een voor beeld van een classificatie van tekst analyse met behulp van [functie-hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Voor beeld 1: tijdelijke functies toevoegen voor een regressie model
We gebruiken het experiment ' vraag prognose van fietsen ' in Azure Machine Learning Studio (klassiek) om te laten zien hoe u functies voor een regressie taak kunt inbouwen. Het doel van dit experiment is het voors pellen van de vraag naar fietsen, dat wil zeggen het aantal fiets huren binnen een bepaalde maand/dag/uur. De gegevensset ' fiets huren ICB-gegevensset ' wordt gebruikt als de onbewerkte invoer gegevens. Deze gegevensset is gebaseerd op echte gegevens van het kapitaal Bike share-bedrijf dat een netwerk van een fiets verhuur in het Verenigde Staten onderhoudt in Washington DC. De gegevensset vertegenwoordigt het aantal fiets huren binnen een bepaald uur van een dag in de jaren 2011 en het jaar 2012 en bevat 17379 rijen en 17 kolommen. De onbewerkte functieset bevat weers omstandigheden (Tempe ratuur/vochtigheid/wind snelheid) en het type van de dag (feestdag/weekdag). Het veld om te voors pellen is het aantal ' cnt ' dat de fiets huren binnen een bepaald uur vertegenwoordigt en een bereik van 1 tot en met 977.

Met het doel van het bouwen van efficiënte functies in de trainings gegevens worden vier regressie modellen gebouwd op basis van dezelfde algoritme, maar met vier verschillende trainings gegevens sets. De vier gegevens sets vertegenwoordigen dezelfde onbewerkte invoer gegevens, maar er is een steeds groter aantal functies ingesteld. Deze functies zijn onderverdeeld in vier categorieën:

1. A = weer + feestdag + weekdag + weekend functies voor de voorspelde dag
2. B = aantal fietsen dat in elk van de afgelopen 12 uur is gehuurd
3. C = aantal fietsen dat in elk van de voor gaande 12 dagen op hetzelfde uur is gehuurd
4. D = aantal fietsen dat in elk van de afgelopen 12 weken op hetzelfde uur en dezelfde dag is gehuurd

Naast de functieset A, die al aanwezig is in de oorspronkelijke onbewerkte gegevens, worden de andere drie sets functies gemaakt via het technisch proces van feature. Met de functieset B worden recente vraag voor de fietsen vastgelegd. Met de functieset C worden de vraag naar fietsen op een bepaald uur vastgelegd. De functieset D legt de vraag naar fietsen op een bepaald uur en een bepaalde dag van de week vast. De vier trainings gegevens sets bevatten elk een functieset A, een + B, een + B + C en een + B + C + D.

In het Azure Machine Learning experiment worden deze vier trainings gegevens sets gevormd via vier vertakkingen van de vooraf verwerkte invoer gegevensset. Met uitzonde ring van de meest linkse vertakking bevat elk van deze vertakkingen een [Execute R-script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) module, waarin de afgeleide functies (functieset B, C en D) respectievelijk zijn gebouwd en worden toegevoegd aan de geïmporteerde gegevensset. In de volgende afbeelding ziet u het R-script dat wordt gebruikt om de functieset B in de tweede vertakking te maken.

![functies maken](./media/create-features/addFeature-Rscripts.png)

Een vergelijking van de prestatie resultaten van de vier modellen wordt in de volgende tabel samenvatten: 

![resultaat vergelijking](./media/create-features/result1.png)

De beste resultaten worden weer gegeven door functies A + B + C. De fout frequentie neemt af wanneer extra functie sets zijn opgenomen in de trainings gegevens. Hiermee wordt het vermoeden gecontroleerd dat de functie set B, C aanvullende relevante informatie biedt voor de regressie taak. Het toevoegen van de D-functie lijkt geen extra verlaging van het fout aantal te bieden.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Voor beeld 2: functies maken in tekst analyse
Functie techniek wordt veel toegepast in taken met betrekking tot tekst analyse, zoals document classificatie en sentiment analyse. Als u bijvoorbeeld documenten wilt classificeren in verschillende categorieën, is een typische veronderstelling dat het woord/de zinsdelen die zijn opgenomen in één document categorie minder waarschijnlijk in een andere document categorie voor komt. Met andere woorden, de frequentie voor de verdeling van woorden/zinsdelen kan verschillende document categorieën kenmerken. In toepassingen voor tekst analyse, omdat afzonderlijke delen van tekstinhoud doorgaans als invoer gegevens dienen, is het technisch proces van de functie nodig om de functies te maken die betrekking hebben op de frequentie van Word/uitdrukkingen.

Ter verkrijging van deze taak wordt een techniek met de naam **hashing** toegepast om op efficiënte wijze wille keurige tekst functies in indexen in te scha kelen. In plaats van elke tekst functie (woorden/zinsdelen) aan een bepaalde index te koppelen, werkt deze methode door een hash-functie toe te passen op de functies en de hash-waarden ervan te gebruiken als indexen.

In Azure Machine Learning is er een [functie-hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -module die de functies van Word/woordgroepen handig maakt. In de volgende afbeelding ziet u een voor beeld van het gebruik van deze module. De invoer gegevensset bevat twee kolommen: de classificatie van het boek van 1 tot 5 en de werkelijke beoordelings inhoud. Het doel van deze [functie hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -module is om een aantal nieuwe functies op te halen die de frequentie van het voorval van de corresponderende woord (en)/phrase (s) in de specifieke boek beoordeling tonen. Als u deze module wilt gebruiken, voert u de volgende stappen uit:

* Selecteer eerst de kolom die de invoer tekst bevat ("col2" in dit voor beeld).
* Stel vervolgens ' hashing bitsize ' in op 8, wat betekent dat 2 ^ 8 = 256 functies worden gemaakt. Het woord/de fase in alle tekst wordt gehasht naar 256 indices. De para meter "hashing bitsize" ligt tussen 1 en 31. De woord (en)/phrase (en) worden waarschijnlijk in dezelfde index gehasht als het een groter getal is.
* Ten derde stelt u de para meter "N-gram" in op 2. Met deze waarde wordt de frequentie van het aantal unigrams (een functie voor elk woord) en bigrams (een functie voor elk paar aaneengesloten woorden) opgehaald uit de invoer tekst. De para meter ' N-g ' variëren van 0 tot 10, waarmee het maximum aantal sequentiële woorden wordt aangegeven dat in een functie moet worden opgenomen.  

![De module ' hashing van functies '](./media/create-features/feature-Hashing1.png)

In de volgende afbeelding ziet u hoe deze nieuwe functie eruitziet.

![Voor beeld van functie-hashing](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusie
Ontworpen en geselecteerde functies verhogen de efficiëntie van het trainings proces, waardoor de belang rijke gegevens in de gegevens worden geëxtraheerd. Ze verbeteren ook de kracht van deze modellen om de invoer gegevens nauw keurig te classificeren en om de resultaten betrouwbaarder te voors pellen. Functie techniek en selectie kunnen ook worden gecombineerd om het leren van de reken kracht te maken. Dit doet u door het aantal functies te verhogen en te verminderen dat nodig is voor het kalibreren of trainen van een model. Mathematische spraak: de functies die zijn geselecteerd om het model te trainen, zijn een minimale set onafhankelijke variabelen waarmee de patronen in de gegevens worden uitgelegd en die vervolgens kunnen worden voor speld.

Het is niet altijd nood zakelijk om functie-engineering of functie selectie uit te voeren. Of het nu nodig is of niet afhankelijk is van de gegevens die moeten worden opgenomen of verzameld, het algoritme dat is geselecteerd en het doel van het experiment.

