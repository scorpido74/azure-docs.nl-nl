---
title: Analytics migreren vanuit Excel
titleSuffix: ML Studio (classic) - Azure
description: Een vergelijking van lineaire regressiemodellen in Excel en in Azure Machine Learning Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217797"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Analytics migreren van Excel naar Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* en *Ben Boatman* zijn enterprise solution architects in Microsoft's Data Insights Center of Excellence. In dit artikel beschrijven ze hun ervaring met het migreren van een bestaande regressieanalysesuite naar een cloudgebaseerde oplossing met Azure Machine Learning Studio (klassiek).

## <a name="goal"></a>Doel

Ons project begon met twee doelen in gedachten: 

1. Gebruik voorspellende analyses om de nauwkeurigheid van de maandelijkse inkomstenprognoses van onze organisatie te verbeteren 
2. Gebruik Azure Machine Learning Studio (klassiek) om onze snelheid te bevestigen, optimaliseren, verhogen en schalen van onze resultaten. 

Net als veel andere bedrijven, onze organisatie gaat door middel van een maandelijkse inkomsten prognose proces. Ons kleine team van bedrijfsanalisten was belast met het gebruik van Azure Machine Learning Studio (klassiek) om het proces te ondersteunen en de nauwkeurigheid van de prognose te verbeteren. Het team heeft enkele maanden besteed aan het verzamelen van gegevens uit meerdere bronnen en het uitvoeren van de gegevenskenmerken door middel van statistische analyse die belangrijke kenmerken identificeerde die relevant zijn voor het voorspellen van de verkoop van services. De volgende stap was om te beginnen met het prototypen van statistische regressiemodellen op de gegevens in Excel. Binnen een paar weken hadden we een Excel-regressiemodel dat beter presteerde dan het huidige veld en de prognoseprocessen voor financiën. Dit werd het resultaat van de basislijnvoorspelling. 

Vervolgens hebben we de volgende stap gezet om onze predictive analytics over te heen naar Studio (klassiek) om erachter te komen hoe Studio (klassiek) de voorspellende prestaties kan verbeteren.

## <a name="achieving-predictive-performance-parity"></a>Het bereiken van voorspellende prestatiepariteit
Onze eerste prioriteit was het bereiken van pariteit tussen Studio (klassiek) en Excel regressiemodellen. Gezien dezelfde gegevens, en dezelfde splitsing voor trainings- en testgegevens, wilden we voorspellende prestatiepariteit tussen Excel en Studio (klassiek) bereiken. In eerste instantie hebben we gefaald. Het Excel-model presteerde beter dan studio (klassiek) model. De storing was te wijten aan een gebrek aan begrip van de basis tool instelling in Studio (klassiek). Na een synchronisatie met het Studio (classic) productteam kregen we een beter inzicht in de basisinstelling die nodig is voor onze datasets en bereikten we pariteit tussen de twee modellen. 

### <a name="create-regression-model-in-excel"></a>Regressiemodel maken in Excel
Onze Excel Regression maakte gebruik van het standaard lineaire regressiemodel in de Excel Analysis ToolPak. 

We *berekenden Gemiddelde Absolute % Fout* en gebruikten deze als prestatiemeting voor het model. Het duurde 3 maanden om te komen tot een werkend model met behulp van Excel. We brachten veel van het leren in Studio (klassieke) experiment die uiteindelijk gunstig was in het begrijpen van eisen.

### <a name="create-comparable-experiment-in-studio-classic"></a>Vergelijkbare experiment maken in Studio (klassiek)
We volgden deze stappen om ons experiment in Studio (klassiek) te maken: 

1. De gegevensset geüpload als csv-bestand naar Studio (klassiek) (zeer klein bestand)
2. Een nieuw experiment gemaakt en de module [Kolommen selecteren in gegevensset][select-columns] gebruiken om dezelfde gegevensfuncties te selecteren die in Excel worden gebruikt 
3. De module [Gesplitste gegevens][split] (met de modus *Relatieve expressie)* gebruikt om de gegevens te verdelen in dezelfde trainingsgegevenssets als in Excel 
4. Geëxperimenteerd met de [module Lineaire regressie][linear-regression] (alleen standaardopties), gedocumenteerd en de resultaten vergeleken met ons Excel-regressiemodel

### <a name="review-initial-results"></a>Bekijk de eerste resultaten
In eerste instantie presteerde het Excel-model duidelijk beter dan het Studio(klassieke) model: 

|  | Excel | Studio (klassiek) |
| --- |:---:|:---:|
| Prestaties | | |
| <ul style="list-style-type: none;"><li>Aangepast R-vierkant</li></ul> |0.96 |N.v.t. |
| <ul style="list-style-type: none;"><li>Coëfficiënt van <br />Bepaling</li></ul> |N.v.t. |0,78<br />(lage nauwkeurigheid) |
| Gemiddelde absolute fout |$ 9,5 miljoen |$ 19,4 Miljoen |
| Gemiddelde absolute fout (%) |6.03% |12.2% |

Toen we ons proces en de resultaten van de ontwikkelaars en data scientists van het Machine Learning-team hebben uitgevoerd, gaven ze al snel een aantal nuttige tips. 

* Wanneer u de [module Lineaire Regressie][linear-regression] in Studio (klassiek) gebruikt, worden twee methoden meegeleverd:
  * Online Gradiënt Afdaling: Kan meer geschikt zijn voor grotere problemen
  * Gewone minste vierkanten: Dit is de methode die de meeste mensen denken als ze lineaire regressie horen. Voor kleine datasets kunnen gewone minimumvierkantjes een meer optimale keuze zijn.
* Overweeg het aanpassen van de parameter L2 Regularization Weight om de prestaties te verbeteren. Het is standaard ingesteld op 0,001, maar voor onze kleine gegevensset stellen we het in op 0,005 om de prestaties te verbeteren. 

### <a name="mystery-solved"></a>Mysterie opgelost!
Toen we de aanbevelingen toepasten, bereikten we dezelfde basislijnprestaties in Studio (klassiek) als in Excel: 

|  | Excel | Studio (klassiek) (Initial) | Studio (klassiek) w/ Minste Vierkanten |
| --- |:---:|:---:|:---:|
| Gelabelde waarde |Werkelijke artikelen (numeriek) |Dezelfde |Dezelfde |
| Leerling |Excel -> gegevensanalyse -> regressie |Lineaire regressie. |Lineaire regressie |
| Leerlingopties |N.v.t. |Standaardwaarden |gewone minste vierkantjes<br />L2 = 0,005 |
| Gegevensset |26 rijen, 3 functies, 1 label. Allemaal numeriek. |Dezelfde |Dezelfde |
| Split: Trein |Excel trainde op de eerste 18 rijen, getest op de laatste 8 rijen. |Dezelfde |Dezelfde |
| Splitsen: Testen |Excel-regressieformule toegepast op de laatste 8 rijen |Dezelfde |Dezelfde |
| **Prestaties** | | | |
| Aangepast R-vierkant |0.96 |N.v.t. | |
| Bepalingscoëfficiënt |N.v.t. |0,78 |0.952049 |
| Gemiddelde absolute fout |$ 9,5 miljoen |$ 19,4 Miljoen |$ 9,5 miljoen |
| Gemiddelde absolute fout (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Bovendien zijn de Excel-coëfficiënten goed vergeleken met de functiegewichten in het door Azure opgeleide model:

|  | Excel-coëfficiënten | Azure-functiegewichten |
| --- |:---:|:---:|
| Onderschepping/bias |19470209.88 |19328500 |
| -functie A |0.832653063 |0.834156 |
| Functie B |11071967.08 |11007300 |
| Functie C |25383318.09 |25140800 |

## <a name="next-steps"></a>Volgende stappen
We wilden de Machine Learning-webservice binnen Excel gebruiken. Onze bedrijfsanalisten vertrouwen op Excel en we hadden een manier nodig om de Machine Learning-webservice te bellen met een rij Excel-gegevens en de voorspelde waarde terug te geven aan Excel. 

We wilden ook ons model optimaliseren, met behulp van de opties en algoritmen die beschikbaar zijn in Studio (klassiek).

### <a name="integration-with-excel"></a>Integratie met Excel
Onze oplossing was om ons Machine Learning regressiemodel te operationaliseren door een webservice te maken van het getrainde model. Binnen een paar minuten werd de webservice gemaakt en konden we deze rechtstreeks vanuit Excel bellen om een voorspelde omzetwaarde terug te geven. 

De sectie *Web Services Dashboard* bevat een downloadbare Excel-werkmap. De werkmap wordt vooraf opgemaakt met de webservice-API en schema-informatie die is ingesloten. Wanneer u op *Excel-werkmap downloaden*klikt, wordt de werkmap geopend en u deze opslaan op uw lokale computer. 

![Excel-werkmap downloaden vanuit het Dashboard WebServices](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Kopieer de vooraf gedefinieerde parameters naar de blauwe parametersectie zoals hieronder wordt weergegeven. Zodra de parameters zijn ingevoerd, roept Excel naar de webservice Machine Learning en worden de voorspelde punten weergegeven in de groene sectie Voorspelde waarden. De werkmap blijft voorspellingen maken voor parameters op basis van uw getrainde model voor alle rijitems die onder Parameters zijn ingevoerd. Zie [Een Azure Machine Learning Web Service](consuming-from-excel.md)gebruiken vanuit Excel voor meer informatie over het gebruik van deze functie. 

![Sjabloon Excel-werkmap die verbinding maakt met de geïmplementeerde webservice](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalisatie en verdere experimenten
Nu we een basislijn hadden met ons Excel-model, zijn we verder gegaan om ons Machine Learning Linear Regression Model te optimaliseren. We gebruikten de module [Filter-Based Feature Selection][filter-based-feature-selection] om onze selectie van de eerste gegevenselementen te verbeteren en het hielp ons een prestatieverbetering van 4,6% Mean Absolute Error te bereiken. Voor toekomstige projecten zullen we deze functie gebruiken die ons weken kan besparen door middel van gegevensattributen om de juiste set functies te vinden die we kunnen gebruiken voor het modelleren. 

Vervolgens zijn we van plan om extra algoritmen zoals [Bayesian][bayesian-linear-regression] of [Boosted Decision Trees][boosted-decision-tree-regression] op te nemen in ons experiment om prestaties te vergelijken. 

Als u wilt experimenteren met regressie, is een goede gegevensset om te proberen de voorbeeldgegevensset Energie-efficiëntie regressie, die veel numerieke kenmerken heeft. De gegevensset wordt geleverd als onderdeel van de voorbeeldgegevenssets in Studio (klassiek). U verschillende leermodules gebruiken om verwarmingsbelasting of koelbelasting te voorspellen. De onderstaande grafiek is een prestatievergelijking van verschillende regressieleert ten opzichte van de energie-efficiëntie gegevensset voorspellen voor de doelvariabele koelingsbelasting: 

| Model | Gemiddelde absolute fout | Hoofdgemiddelde kwadraatfout | Relatieve absolute fout | Relatieve kwadraatfout | Bepalingscoëfficiënt |
| --- | --- | --- | --- | --- | --- |
| Versterkte beslissingsstructuur |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineaire regressie (verloopafdaling) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regressie neuraal netwerk |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineaire regressie (gewone kleinste vierkanten) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Belangrijkste afhaalmaaltijden
We hebben veel geleerd door excel regressie en Studio (klassieke) experimenten parallel uit te voeren. Het maken van het basislijnmodel in Excel en het vergelijken met modellen met Machine Learning [Linear Regression][linear-regression] hielp ons Studio (klassiek) te leren en we ontdekten mogelijkheden om de gegevensselectie en modelprestaties te verbeteren. 

We vonden ook dat het raadzaam is om [filtergebaseerde functieselectie][filter-based-feature-selection] te gebruiken om toekomstige voorspellingsprojecten te versnellen. Door functieselectie toe te passen op uw gegevens, u een verbeterd model maken in Studio (klassiek) met betere algemene prestaties. 

De mogelijkheid om de voorspellende analytische prognoses systemisch over te zetten van Studio (klassiek) naar Excel maakt een aanzienlijke toename van de mogelijkheid om met succes resultaten te leveren aan een breed zakelijk gebruikerspubliek mogelijk. 

## <a name="resources"></a>Resources
Hier volgen enkele bronnen om u te helpen met regressie te werken: 

* Regressie in Excel. Als u regressie in Excel nog nooit hebt geprobeerd, maakt deze zelfstudie het eenvoudig:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressie versus prognoses. Tyler Chessman schreef een blog artikel uit te leggen hoe je tijd serie forecasting doen in Excel, die een goede beginner beschrijving van lineaire regressie bevat. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Gewone minst vierkanten lineaire regressie: gebreken, problemen en valkuilen. Voor een inleiding en bespreking van Regressie: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

