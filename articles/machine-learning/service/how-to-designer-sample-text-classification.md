---
title: 'Voor beeld van Designer #7: rapport beoordelingen classificeren'
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van een machine learning model beoordelingen over boek classificaties in verschillende categorieën.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 6f9a2327953177f29ba7a1f5948d24c7df428c09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515445"
---
# <a name="sample-7---text-classification-predict-company-category"></a>Voor beeld 7-tekst classificatie: bedrijfs categorie voors pellen 

Dit voor beeld laat zien hoe u met behulp van tekst analyse modules een pijp lijn met tekst classificatie kunt bouwen in Azure Machine Learning Designer (preview).

Het doel van tekst classificatie is het toewijzen van een stuk tekst aan een of meer vooraf gedefinieerde klassen of categorieën. Het stuk tekst kan een document, nieuws artikel, zoek query, e-mail, Tweet, ondersteunings tickets, feedback van klanten, beoordeling van gebruikers producten, enzovoort zijn. Toepassingen van tekst classificatie zijn onder andere het categoriseren van kranten artikelen en de inhoud van nieuws draden in onderwerpen, het organiseren van webpagina's in hiërarchische categorieën, het filteren van ongewenste e-mail, sentiment analyse, het voors pellen van de gebruikers intentie van zoek query's, route ring ondersteunings tickets en analyse van feedback van klanten. 

Deze pijp lijn traint een classificatie van een **logistiek-regressie** met een meer klasse om de bedrijfs categorie te voors pellen met wikipedia SP 500-gegevensset die is afgeleid van Wikipedia.  

De belangrijkste stappen van een cursus machine learning model met tekst gegevens zijn:

1. De gegevens ophalen

1. De tekst gegevens vooraf verwerken

1. Functie techniek

   Tekst functie converteren naar de numerieke functie met de functie voor het uitpakken van functies, zoals functie-hashing, extra heren n-gram-functie uit de tekst gegevens.

1. Het model trainen

1. Gegevensset voor Score

1. Het model evalueren

Hier volgt de uiteindelijke, voltooide grafiek van de pijp lijn waaraan we werken. We bieden de motivering voor alle modules, zodat u op uw eigen wijze vergelijk bare beslissingen kunt nemen.

[Grafiek van de pijp lijn ![](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Gegevens

In deze pijp lijn gebruiken we de **Wikipedia SP 500** -gegevensset. De gegevensset is afgeleid van Wikipedia (https://www.wikipedia.org/) op basis van artikelen van elke S & P 500-bedrijf. Voordat u naar Azure Machine Learning Designer uploadt, is de gegevensset als volgt verwerkt:

- Tekst inhoud voor elk specifiek bedrijf extra heren
- Wiki-opmaak verwijderen
- Niet-alfanumerieke tekens verwijderen
- Alle tekst naar kleine letters converteren
- Er zijn bekende bedrijfs categorieën toegevoegd

Er zijn geen artikelen voor sommige bedrijven gevonden. het aantal records is dus kleiner dan 500.

## <a name="pre-process-the-text-data"></a>De tekst gegevens vooraf verwerken

We gebruiken de **tekst module preprocess** om de tekst gegevens voor te verwerken, met inbegrip van de zinnen, Tokenize, enzovoort. U vindt alle ondersteunde opties in het artikel voor [**verwerking van tekst**](../algorithm-module-reference/preprocess-text.md) . Nadat de TeX-gegevens vooraf zijn verwerkt, gebruiken we de module voor het **splitsen van gegevens** om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 50% van de oorspronkelijke gegevens bevat en de test-gegevensset 50% van de oorspronkelijke gegevens bevat.

## <a name="feature-engineering"></a>Functie techniek
In dit voor beeld gebruiken we twee methoden voor het uitvoeren van functie techniek.

### <a name="feature-hashing"></a>Functie-hashing
We hebben de [**functie hashing**](../algorithm-module-reference/feature-hashing.md) module gebruikt om de onbewerkte tekst van de artikelen te converteren naar gehele getallen en de waarden van het gehele getal als invoer functies voor het model te gebruiken. 

De **functie hashing** module kan worden gebruikt om tekst documenten met een variabele lengte te converteren naar numerieke functie vectoren van gelijke lengte, met behulp van de 32-bits murmurhash v3-hash methode die wordt verschaft door de Vowpal Wabbit-bibliotheek. De doel stelling van het gebruik van functie-hashing is het beperken van de dimensionaliteit; ook functie-hashing maakt het opzoeken van functie-gewichten sneller op classificatie tijd, omdat de vergelijking van hash-waarden wordt gebruikt in plaats van teken reeks vergelijking.

In de voorbeeld pijplijn stellen we het aantal hash-bits in op 14 en stellen we het aantal n-gram in op 2. Met deze instellingen kan de hash-tabel 2 ^ 14 vermeldingen bevatten, waarbij elke hash-functie een of meer n-gram-functies vertegenwoordigt en de waarde ervan de frequentie van het exemplaar van die n-gram in het tekst exemplaar vertegenwoordigt. Een hash-tabel van deze omvang is voor veel problemen meer dan voldoende, maar in sommige gevallen is er mogelijk meer ruimte nodig om conflicten te voor komen. Evalueer de prestaties van uw machine learning oplossing met behulp van een ander aantal bits. 

### <a name="extract-n-gram-feature-from-text"></a>N-gram-functie uit tekst extra heren

Een n-gram is een aaneengesloten reeks n voor waarden van een bepaalde tekst reeks. Een n-gram van grootte 1 wordt een unigram genoemd. een n-gram van grootte 2 is een bigram. een n-gram van grootte 3 is een Trigram. N-g van grotere grootten wordt soms aangeduid met de waarde n, bijvoorbeeld ' vier-gram ', ' vijf-gram ', enzovoort.

We gebruiken de [**functie N-gram van de tekst module uitpakken**](../algorithm-module-reference/extract-n-gram-features-from-text.md)als een andere oplossing voor functie techniek. In deze module wordt eerst de set van n-gram geëxtraheerd, naast het n-gram nummer, het aantal documenten waarbij elke n-gram wordt weer gegeven in de tekst, geteld (DF). In dit voor beeld wordt TF-IDF metric gebruikt om onderdeel waarden te berekenen. Vervolgens worden ongestructureerde tekst gegevens geconverteerd naar numerieke functie vectoren met gelijke lengte, waarbij elke functie de TF-IDF van een n-gram in een tekst-exemplaar vertegenwoordigt.

Nadat u tekst gegevens hebt geconverteerd naar numerieke functie vectoren, wordt een **Select column** -module gebruikt om de tekst gegevens uit de gegevensset te verwijderen. 

## <a name="train-the-model"></a>Het model trainen

Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Omdat het doel van deze pijp lijn het voors pellen van de categorie van het bedrijf is, is een classificatie model met meerdere klassen een goede keuze. Als u overweegt dat het aantal functies groot is en deze functies verspreid zijn, gebruiken we een **logistiek regressie** model met multi klasse voor deze pijp lijn.

## <a name="test-evaluate-and-compare"></a>Testen, evalueren en vergelijken

 We splitsen de gegevensset en gebruiken verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, gebruiken we het **score model** en **evalueren we model** modules om voorspelde resultaten te genereren en de modellen te evalueren. Voordat u de module **score model** gebruikt, is het echter nodig om functie-engineering uit te voeren, wat we hebben gedaan tijdens de training. 

Voor de **functie hashing** -module is het eenvoudig om functie-Engineer uit te voeren op Score stroom als trainings stroom. Gebruik de **functie hashing** module rechtstreeks om de invoer tekst gegevens te verwerken.

Voor het **extra heren van de functie N-gram van de tekst** module, zouden we de **Resultaten woordenlijst uitvoer** van de trainings gegevensstroom koppelen aan de **invoer woordenlijst** op de score gegevensstroom, en de **woordenlijst modus** para meter instellen op **alleen-lezen** .
[![grafiek van n-gram-Score](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

Nadat de technische stap is voltooid, kan het **score model** worden gebruikt voor het genereren van voor spellingen voor de test gegevensset met behulp van het getrainde model. Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **score model** en selecteert u vervolgens **visualiseren**.

Vervolgens worden de scores door gegeven aan de module **Evaluate model** voor het genereren van metrische gegevens voor de evaluatie. Het **Evalueer model** heeft twee invoer poorten, zodat we gescoorde gegevens sets kunnen evalueren en vergelijken die met verschillende methoden worden gegenereerd. In dit voor beeld vergelijken we de prestaties van het resultaat dat is gegenereerd met de functie hashing methode en n-gram-methode.
Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **Evalueer model** en selecteert u vervolgens **visualiseren**.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:
- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
