---
title: Gegevens voorbereiden voor ML Studio (klassiek) - Team Data Science Process
description: Pre-proces en schone gegevens voor te bereiden om effectief te worden gebruikt voor machine learning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720041"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Taken om gegevens voor te bereiden voor verbeterde Machine Learning
Voorverwerking en reiniging van gegevens zijn belangrijke taken die moeten worden uitgevoerd voordat een gegevensset kan worden gebruikt voor modeltraining. Ruwe gegevens zijn vaak luidruchtig en onbetrouwbaar en kunnen waarden missen. Het gebruik van dergelijke gegevens voor modellering kan misleidende resultaten opleveren. Deze taken maken deel uit van het Team Data Science Process (TDSP) en volgen meestal een eerste verkenning van een gegevensset die wordt gebruikt om de vereiste voorverwerking te ontdekken en te plannen. Zie de stappen die zijn beschreven in het Team [Data Science Process](overview.md)voor meer gedetailleerde instructies over het TDSP-proces.

Pre-processing en reinigingstaken, zoals de gegevensverkenningstaak, kunnen worden uitgevoerd in een breed scala aan omgevingen, zoals SQL of Hive of Azure Machine Learning Studio (klassiek), en met verschillende tools en talen, zoals R of Python, afhankelijk van waar uw gegevens wordt opgeslagen en hoe het is opgemaakt. Aangezien TDSP iteratief van aard is, kunnen deze taken op verschillende stappen in de workflow van het proces plaatsvinden.

In dit artikel worden verschillende concepten en taken voor gegevensverwerking geïntroduceerd die kunnen worden uitgevoerd voor of na het innemen van gegevens in Azure Machine Learning Studio (klassiek).

Zie de video [Voor het voorverwerken](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) van gegevens voor in Azure Machine Learning Studio (klassiek) voor een voorbeeld van gegevensverkenning en voorverwerking.

## <a name="why-pre-process-and-clean-data"></a>Waarom vooraf verwerken en schone gegevens?
Gegevens uit de echte wereld worden verzameld uit verschillende bronnen en processen en kunnen onregelmatigheden of corrupte gegevens bevatten die de kwaliteit van de gegevensset in gevaar brengen. De typische problemen met de gegevenskwaliteit die zich voordoen zijn:

* **Onvolledig**: Gegevens ontbreken kenmerken of ontbrekende waarden.
* **Lawaaierig**: Gegevens bevatten foutieve records of uitschieters.
* **Inconsistent:** gegevens bevatten conflicterende records of discrepanties.

Kwaliteitsgegevens zijn een voorwaarde voor kwaliteitsvoorspellende modellen. Om te voorkomen dat "garbage in, garbage out" en verbetering van de kwaliteit van de gegevens en dus model prestaties, is het noodzakelijk om een data health screen uit te voeren om gegevensproblemen vroegtijdig te herkennen en te beslissen over de bijbehorende gegevensverwerking en reiniging stappen.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Wat zijn enkele typische gegevensstatusschermen die worden gebruikt?
We kunnen de algemene kwaliteit van de gegevens controleren door te controleren:

* Het aantal **records**.
* Het aantal **kenmerken** (of **functies).**
* De **kenmerkgegevenstypen** (nominaal, ordinaal of continu).
* Het aantal **ontbrekende waarden**.
* **Goed gevormde** gegevens.
  * Als de gegevens zich in TSV of CSV begeven, controleert u of de kolomscheidingstekens en lijnscheidingstekens kolommen en lijnen altijd correct scheiden.
  * Als de gegevens in HTML- of XML-indeling zijn, controleert u of de gegevens goed zijn gevormd op basis van hun respectieve normen.
  * Parsing kan ook nodig zijn om gestructureerde informatie uit semi-gestructureerde of ongestructureerde gegevens te halen.
* **Inconsistente gegevensrecords**. Controleer of het waardenbereik is toegestaan. Als de gegevens bijvoorbeeld de GPA van studenten bevatten (cijferpuntgemiddeld), controleert u of de GPA zich in het aangewezen bereik bevindt, zeg 0~4.

Wanneer u problemen met gegevens vindt, zijn **verwerkingsstappen** nodig, waarbij vaak ontbrekende waarden, gegevensnormalisatie, discretisatie, tekstverwerking worden verwijderd en/of vervangen die van invloed kunnen zijn op de uitlijning van gegevens, gemengde gegevenstypen in algemene velden en anderen.

**Azure Machine Learning verbruikt goed gevormde tabelgegevens.**  Als de gegevens al in tabelvorm zijn, kunnen gegevens vooraf worden verwerkt rechtstreeks met Azure Machine Learning Studio (klassiek) in de Machine Learning.  Als gegevens niet in tabelvorm zijn, bijvoorbeeld in XML, kan ontwering nodig zijn om de gegevens om te zetten in tabelvorm.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Wat zijn enkele van de belangrijkste taken in de voorverwerking van gegevens?
* **Gegevensreiniging:** vul ontbrekende waarden in, detecteer en verwijder rumoerige gegevens en uitschieters.
* **Gegevenstransformatie**: Gegevens normaliseren om afmetingen en ruis te verminderen.
* **Gegevensreductie:** voorbeeldgegevensrecords of -kenmerken voor een eenvoudigere gegevensverwerking.
* **Gegevensdiscretisatie**: Continue kenmerken converteren naar categorische kenmerken voor gebruiksgemak met bepaalde machine learning-methoden.
* **Tekstreiniging:** verwijder ingesloten tekens die kunnen leiden tot foute uitlijning van gegevens, bijvoorbeeld ingesloten tabbladen in een gegevensbestand met tabbladen gescheiden, ingesloten nieuwe regels die records kunnen verbreken, bijvoorbeeld.

In de onderstaande secties worden enkele van deze stappen voor gegevensverwerking beschreven.

## <a name="how-to-deal-with-missing-values"></a>Hoe om te gaan met ontbrekende waarden?
Om te gaan met ontbrekende waarden, is het het beste om eerst de reden voor de ontbrekende waarden te identificeren om het probleem beter te behandelen. Typische methoden voor het verwerken van ontbrekende waarden zijn:

* **Verwijdering:** Records verwijderen met ontbrekende waarden
* **Dummy substitutie**: Vervang ontbrekende waarden door een dummy waarde: bijvoorbeeld *onbekend* voor categorisch of 0 voor numerieke waarden.
* **Gemiddelde vervanging**: Als de ontbrekende gegevens numeriek zijn, vervangt u de ontbrekende waarden door het gemiddelde.
* **Frequente vervanging**: Als de ontbrekende gegevens categorisch zijn, vervangt u de ontbrekende waarden door het meest voorkomende item
* **Regressiesubstitutie:** gebruik een regressiemethode om ontbrekende waarden te vervangen door terugvallende waarden.  

## <a name="how-to-normalize-data"></a>Hoe kunnen gegevens worden genormalt?
Bij gegevensnormalisatie worden numerieke waarden opnieuw schalen naar een opgegeven bereik. Populaire methoden voor gegevensnormalisatie zijn:

* **Min-Max Normalisatie**: Transformeer de gegevens lineair naar een bereik, bijvoorbeeld tussen 0 en 1, waarbij de minwaarde wordt geschaald naar 0 en de maximale waarde op 1.
* **Z-score Normalisatie**: Schaalgegevens op basis van gemiddelde en standaarddeviatie: verdeel het verschil tussen de gegevens en het gemiddelde door de standaarddeviatie.
* **Decimale schaling:** schaal de gegevens door het decimale punt van de kenmerkwaarde te verplaatsen.  

## <a name="how-to-discretize-data"></a>Hoe gegevens te discretizen?
Gegevens kunnen worden gedescretiseerd door continue waarden om te zetten in nominale kenmerken of intervallen. Enkele manieren om dit te doen zijn:

* **Binning met gelijke breedte**: Verdeel het bereik van alle mogelijke waarden van een kenmerk in N-groepen van dezelfde grootte en wijs de waarden toe die in een opslaglocatie met het opslaglocatienummer vallen.
* **Gelijk-hoogte Binning**: Verdeel het bereik van alle mogelijke waarden van een kenmerk in N-groepen, elk met hetzelfde aantal instanties, en wijs vervolgens de waarden toe die in een opslaglocatie met het opslagpuntnummer vallen.  

## <a name="how-to-reduce-data"></a>Hoe verminder je data?
Er zijn verschillende methoden om de gegevensgrootte te verkleinen voor een eenvoudigere gegevensverwerking. Afhankelijk van de grootte van de gegevens en het domein kunnen de volgende methoden worden toegepast:

* **Recordsampling:** sample de gegevensrecords en kies alleen de representatieve subset van de gegevens.
* **Kenmerkbemonstering:** selecteer alleen een subset van de belangrijkste kenmerken uit de gegevens.  
* **Aggregatie**: Verdeel de gegevens in groepen en sla de getallen voor elke groep op. De dagelijkse inkomstenaantallen van een restaurantketen in de afgelopen 20 jaar kunnen bijvoorbeeld worden samengevoegd tot maandelijkse inkomsten om de grootte van de gegevens te verkleinen.  

## <a name="how-to-clean-text-data"></a>Hoe maak je tekstgegevens schoon?
**Tekstvelden in tabelgegevens** kunnen tekens bevatten die van invloed zijn op de uitlijning en/of recordgrenzen van kolommen. Ingesloten tabbladen in een met tabbladen gescheiden bestand veroorzaken bijvoorbeeld een verkeerde uitlijning van kolommen en ingesloten nieuwe regeltekens breken recordlijnen. Onjuiste verwerking van tekstcodering tijdens het schrijven of lezen van tekst leidt tot informatieverlies, onbedoelde introductie van onleesbare tekens (zoals nullen) en kan ook van invloed zijn op tekstparsing. Zorgvuldig ontleed en bewerken kan nodig zijn om tekstvelden schoon te maken voor een goede uitlijning en/of om gestructureerde gegevens te extraheren uit ongestructureerde of semi-gestructureerde tekstgegevens.

**Data-exploratie** biedt een vroege weergave van de gegevens. Een aantal gegevensproblemen kunnen tijdens deze stap aan het licht worden gebracht en overeenkomstige methoden kunnen worden toegepast om deze problemen aan te pakken.  Het is belangrijk om vragen te stellen, zoals wat de bron van het probleem is en hoe het probleem kan zijn ingevoerd. Dit proces helpt u ook bij het bepalen van de stappen voor gegevensverwerking die moeten worden genomen om deze op te lossen. Het identificeren van de uiteindelijke use cases en persona's kan ook worden gebruikt om prioriteit te geven aan de gegevensverwerkingsinspanning.

## <a name="references"></a>Verwijzingen
> *Data Mining: Concepten en technieken*, Derde editie, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber en Jian Pei
> 
> 

