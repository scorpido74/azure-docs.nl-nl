---
title: 'Prep-gegevens voor ML Studio (klassiek): team data Science process'
description: Voorverwerken en opschonen van gegevens naar voorbereiden voor het efficiënt worden gebruikt voor machine learning.
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
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720041"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Taken om gegevens voor te bereiden voor verbeterde Machine Learning
Gegevens voorafgaand aan verwerking en reiniging zijn belang rijke taken die moeten worden uitgevoerd voordat een gegevensset kan worden gebruikt voor model training. Onbewerkte gegevens is vaak veel ruis veroorzaken en onbetrouwbare en mogelijk ontbreken waarden. Met behulp van dergelijke gegevens voor modellen kan misleidend resultaten opleveren. Deze taken maken deel uit van het Team Data Science Process (TDSP) en een eerste onderzoek van een gegevensset die wordt gebruikt om te detecteren en plannen van het vooraf verwerken vereist doorgaans als volgt. Zie voor meer instructies voor de TDSP-proces gedetailleerde, de stappen die worden beschreven de [Team Data Science Process](overview.md).

Taken voorafgaand aan verwerking en opschoning, zoals de taak voor het verkennen van gegevens, kunnen worden uitgevoerd in een breed scala aan omgevingen, zoals SQL of Hive of Azure Machine Learning Studio (klassiek), en met verschillende hulpprogram ma's en talen, zoals R of python, afhankelijk van waar uw gegevens wordt opgeslagen en hoe deze wordt geformatteerd. Aangezien TDSP iteratieve van aard is, kunnen deze taken plaatsvinden op verschillende stappen in de werkstroom van het proces.

In dit artikel worden verschillende concepten en taken voor gegevens verwerking geïntroduceerd die kunnen worden uitgevoerd vóór of na het opnemen van gegevens in Azure Machine Learning Studio (klassiek).

Voor een voor beeld van het verkennen van gegevens en vooraf verwerkingen binnen Azure Machine Learning Studio (klassiek), raadpleegt u de video [preprocessing data](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Waarom voorverwerken en opschonen van gegevens?
Werkelijk bestaande gegevens uit diverse bronnen worden verzameld en processen en onregelmatigheden of beschadigde gegevens in gevaar brengt de kwaliteit van de gegevensset kunnen bevatten. De gebruikelijke data quality-problemen die zich voordoen zijn:

* **Onvolledige**: gegevens beschikt niet over kenmerken of met ontbrekende waarden.
* **Ruis**: gegevens bevat onjuiste records of uitbijters.
* **Inconsistente**: gegevens bevat conflicterende records of afwijkingen.

Kwaliteitsgegevens is een vereiste voor kwaliteit voorspellende modellen. Om te voorkomen 'garbagecollection in garbagecollection out' en de gegevenskwaliteit te verbeteren en daarom model prestaties, is het van cruciaal belang voor het uitvoeren van een scherm van de status gegevens om problemen met gegevens vroeg signaleren en te beslissen over de bijbehorende gegevens verwerken en opschonen stappen.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Wat zijn enkele typische gegevens health schermen die worden gebruikt?
We kunt de algemene kwaliteit van de gegevens controleren door te controleren:

* Het aantal **records**.
* Het aantal **kenmerken** (of **functies**).
* Het kenmerk **gegevenstypen** (nominale, rangtelwoord of continue).
* Het aantal **ontbrekende waarden**.
* **Juist opgemaakte** gegevens.
  * Als de gegevens zich bevinden in TSV- of CSV, controleert u dat de kolom scheidingstekens en regel scheidingstekens altijd goed scheiden van kolommen en regels.
  * Als de gegevens zich bevinden in HTML of XML-indeling, controleert u of de gegevens is goed samengesteld op basis van hun respectieve standaarden.
  * Parseren kan ook nodig zijn om gestructureerde informatie onttrekken aan semi-gestructureerde of ongestructureerde gegevens.
* **Inconsistente gegevensrecords**. Controleer het bereik van waarden zijn toegestaan. Als de gegevens bijvoorbeeld student GPA bevatten (gemiddelde van cijfers), controleert u of de GPA zich in het aangewezen bereik bevindt, bijvoorbeeld 0 ~ 4.

Wanneer u problemen met gegevens hebt gevonden, zijn de **verwerkings stappen** nood zakelijk, wat vaak nodig is voor het opschonen van ontbrekende waarden, gegevens normalisatie, onderscheidings, tekst verwerking voor het verwijderen en/of vervangen van Inge sloten tekens die van invloed kunnen zijn op de uitlijning van gegevens, gemengde gegevens typen in algemene velden en andere.

**Azure Machine Learning verbruikt opgemaakte tabelgegevens**.  Als de gegevens zich al in tabel vorm bevinden, kunnen gegevens vooraf worden verwerkt met Azure Machine Learning Studio (klassiek) in de Machine Learning.  Als gegevens niet in tabelvorm, zeg dat deel uitmaakt van XML-parsering mogelijk zijn vereist om te kunnen omzetten van de gegevens in tabelvorm.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Wat zijn enkele van de belangrijkste taken in vooraf verwerken van gegevens?
* **Gegevens reiniging**: Vul ontbrekende waarden in, detecteer en Verwijder ruis gegevens en uitschieters.
* **Gegevenstransformatie**: normaliseren gegevens om dimensies en ruis te reduceren.
* **Gegevensreductie**: voorbeeld gegevensrecords of kenmerken voor eenvoudiger verwerking van gegevens.
* **Gegevens onderscheidende**: converteren naar de doorlopende kenmerken categorische kenmerken voor gebruiksgemak gebruikt met bepaalde methoden van machine learning.
* **Tekst opschonen**: Verwijder Inge sloten tekens die mogelijk een onjuiste uitlijning van gegevens veroorzaken, bijvoorbeeld Inge sloten tabbladen in een door tabs gescheiden gegevens bestand, Inge sloten nieuwe regels die records kunnen verstoren, bijvoorbeeld.

De volgende secties bevatten informatie over enkele van deze stappen gegevensverwerking.

## <a name="how-to-deal-with-missing-values"></a>Het omgaan met ontbrekende waarden?
Als u wilt omgaan met ontbrekende waarden, is het beste eerst het probleem bepalen de reden voor de ontbrekende waarden beter wordt verwerkt. Typische ontbrekende waarde verwerking methoden zijn:

* **Verwijdering**: records met ontbrekende waarden te verwijderen
* **Testupdate worden uitgevoerd van vervanging**: ontbrekende waarden vervangen door een dummy-waarde: bijvoorbeeld *onbekende* voor categorische of 0 voor numerieke waarden.
* **Vervanging betekenen**: als de ontbrekende gegevens numeriek is, vervangt u de ontbrekende waarden door het gemiddelde.
* **Vervanging frequente**: als de ontbrekende gegevens categorische is, vervangt u de ontbrekende waarden met de meest voorkomende item
* **Regressie vervanging**: een regressiemethode ontbrekende waarden te vervangen door verminderde waarden gebruiken.  

## <a name="how-to-normalize-data"></a>Hoe kan ik gegevens normaliseren?
Met gegevens normalisatie worden numerieke waarden opnieuw geschaald naar een opgegeven bereik. Populaire normalisering methoden zijn onder andere:

* **Min-Max normalisering**: lineair worden de gegevens transformeren naar een bereik, zeg tussen 0 en 1, waarbij de minimumwaarde is geschaald naar 0 en maximale waarde in 1.
* **Z-score normalisering**: gegevens op basis van gemiddelde en standaarddeviatie schalen: het verschil tussen de gegevens en het gemiddelde delen door de standaarddeviatie.
* **Decimale schalen**: schalen van de gegevens door te verplaatsen van het decimaalteken van de waarde van het kenmerk.  

## <a name="how-to-discretize-data"></a>Hoe kunt u voor het onderscheiden van gegevens?
Gegevens kunnen worden onderscheiden door doorlopende waarden converteren naar de nominale kenmerken of intervallen. Er zijn enkele manieren waarop u dit doet:

* **Gelijke breedte Binning**: het bereik van alle mogelijke waarden van een kenmerk onderverdelen in N groepen van dezelfde grootte en de waarden die kunnen worden onderverdeeld in een opslaglocatie toewijzen met het opslaglocatienummer van de.
* **Gelijk hoogte Binning**: het bereik van alle mogelijke waarden van een kenmerk onderverdelen in N groepen, elk met hetzelfde aantal instanties en wijs vervolgens de waarden die kunnen worden onderverdeeld in een opslaglocatie met het opslaglocatienummer van de.  

## <a name="how-to-reduce-data"></a>Hoe kunt u het om gegevens te reduceren?
Er zijn verschillende methoden om gegevensgrootte voor de afhandeling van gegevens gemakkelijker te beperken. Afhankelijk van de grootte en het domein, kunnen de volgende methoden worden toegepast:

* **Vastleggen van steekproeven**: voorbeeld van de records met gegevens en kiest u alleen de representatieve subset van de gegevens.
* **Steekproeven van het kenmerk**: selecteert u alleen een subset van de belangrijkste kenmerken van de gegevens.  
* **Aggregatie**: de gegevens verdelen in groepen en op te slaan van de getallen voor elke groep. De dagelijkse omzet-nummers van een restaurantketen in de afgelopen 20 jaar kan bijvoorbeeld worden geaggregeerd naar maandelijkse omzet om de grootte van de gegevens te beperken.  

## <a name="how-to-clean-text-data"></a>Hoe kunt u voor het opschonen van gegevens?
**Tekst velden in tabellaire gegevens** bevatten mogelijk tekens die van invloed zijn op kolommen uitlijning en/of record grenzen. Inge sloten tabbladen in een bestand met door tabs gescheiden waarden veroorzaken bijvoorbeeld kolom verkeerd uitlijnen en Inge sloten nieuwe regel tekens record regels. Ongeldige verwerking van tekst codering tijdens het schrijven of lezen van tekst leidt tot verlies van gegevens, onbedoelde introductie van onleesbare tekens (zoals null-waarden) en kan ook van invloed zijn op het parseren van tekst. Zorg ervoor dat bij het parseren en het bewerken van mogelijk nodig om u te reinigen tekstvelden voor de juiste uitlijning en/of extract gestructureerde gegevens uit ongestructureerde of semi-gestructureerde tekstgegevens.

**Gegevens verkennen** biedt een vroege weergeven in de gegevens. Een aantal problemen met gegevens kan worden gezien tijdens deze stap en de bijbehorende methoden kunnen worden toegepast om die problemen op te lossen.  Het is belangrijk om te vragen, zoals wat is de oorzaak van het probleem en hoe het probleem kan zijn geïntroduceerd. Dit proces helpt u ook bij het bepalen van de gegevens verwerkings stappen die moeten worden uitgevoerd om ze op te lossen. Het identificeren van de eind use cases en personen kunnen ook worden gebruikt voor het bepalen van de gegevens verwerkings inspanning.

## <a name="references"></a>Naslaginformatie
> *Gegevensanalyse: De concepten en technieken voor*, derde Edition Morgan Kaufmann, 2011, Jiawei Han Micheline Kamber en Jian Pei
> 
> 

