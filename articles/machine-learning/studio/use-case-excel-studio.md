---
title: Analyses migreren vanuit Excel
titleSuffix: ML Studio (classic) - Azure
description: Een vergelijking van lineaire regressie modellen in Excel en in Azure Machine Learning Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 5831f83d8cd38cc07c64fdc398405c3a70935485
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169092"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Analytics van Excel migreren naar Azure Machine Learning Studio (klassiek)

> *Kate Baroni* en *ben Boatman* zijn Enter prise Solution Architects in het data Insights Center van micro soft. In dit artikel beschrijven ze hun ervaring met het migreren van een bestaande regressie analyse Suite naar een Cloud oplossing met behulp van Azure Machine Learning Studio (klassiek).

## <a name="goal"></a>Doel

Ons project is gestart met twee doel stellingen: 

1. Gebruik predictive analytics om de nauw keurigheid van de maandelijkse omzet prognoses van onze organisatie te verbeteren 
2. Gebruik Azure Machine Learning Studio (klassiek) om onze resultaten te bevestigen, te optimaliseren, te verhogen en te schalen. 

Net als bij veel bedrijven gaat onze organisatie door een maandelijks omzet prognose proces. Ons kleine team van bedrijfs analisten is gewerkt met het gebruik van Azure Machine Learning Studio (klassiek) ter ondersteuning van het proces en de nauw keurigheid van de prognose te verbeteren. Het team heeft een aantal maanden besteed aan het verzamelen van gegevens uit meerdere bronnen en het uitvoeren van de gegevens kenmerken via statistische analyses, waarmee belang rijke kenmerken van de verkoop prognoses van services worden geïdentificeerd. De volgende stap was het starten van het prototypen van statistische regressie modellen op de gegevens in Excel. Binnen een paar weken moesten we een Excel-regressie model maken dat het huidige veld heeft uitgevoerd en de prognoses voor het verwerken van de Financiën heeft uitgewerkt. Dit is het voor Spellings resultaat van de basis lijn. 

We hebben vervolgens de volgende stap gevolgd om onze predictive analytics over te zetten naar Studio (klassiek) om erachter te komen hoe Studio (klassiek) kan worden verbeterd voor voorspellende prestaties.

## <a name="achieving-predictive-performance-parity"></a>Vertraagde prestatie pariteit
Onze eerste prioriteit is om de pariteit te halen tussen studio-en Excel-regressie modellen. Gezien dezelfde gegevens, en dezelfde splitsing voor trainings-en test gegevens, wilden we anticiperen op voorspellende prestatie pariteit tussen Excel en Studio (klassiek). In eerste instantie mislukt. Het Excel-model van een uitvoerbaar Studio model (klassiek). De fout is veroorzaakt door het ontbreken van een uitleg van de instelling basis hulpprogramma in Studio (klassiek). Na een synchronisatie met het Studio-product team (klassiek), hebben we een beter inzicht in de basis instelling die is vereist voor onze gegevens sets, en is er pariteit tussen de twee modellen behaald. 

### <a name="create-regression-model-in-excel"></a>Regressie model maken in Excel
In onze Excel-regressie werd het standaard lineaire regressie model gebruikt dat is gevonden in het Analysis ToolPak van Excel. 

We hebben het *gemiddelde absolute% error* berekend en gebruiken als de prestatie meting voor het model. Het duurde drie maanden om aan een werkend model te komen met Excel. We hebben een groot deel van het leer proces in Studio (klassiek) ontwikkeld, wat uiteindelijk nuttig was bij het inzicht in vereisten.

### <a name="create-comparable-experiment-in-studio-classic"></a>Vergelijkbaar experiment maken in Studio (klassiek)
We hebben deze stappen gevolgd om ons experiment te maken in Studio (klassiek): 

1. De gegevensset is geüpload als een CSV-bestand naar Studio (klassiek) (zeer klein bestand)
2. Een nieuw experiment gemaakt en de module [select columns in dataset][select-columns] gebruiken om dezelfde gegevens functies te selecteren die worden gebruikt in Excel 
3. De module voor het [splitsen van gegevens][split] (met de *relatieve expressie* modus) gebruikt om de gegevens te verdelen in dezelfde trainings gegevens sets die zijn uitgevoerd in Excel 
4. Experimenteren met de [lineaire regressie][linear-regression] module (alleen de standaard opties), gedocumenteerd en vergeleken met de resultaten in het regressie model van Excel

### <a name="review-initial-results"></a>Initiële resultaten controleren
In eerste instantie heeft het Excel-model duidelijk het model studio (klassiek) uitgevoerd: 

|  | Excel | Studio (klassiek) |
| --- |:---:|:---:|
| Prestaties | | |
| <ul style="list-style-type: none;"><li>Aangepast R-kwadraat</li></ul> |0.96 |N.v.t. |
| <ul style="list-style-type: none;"><li>Coëfficiënt van <br />Analyses</li></ul> |N.v.t. |0.78<br />(lage nauw keurigheid) |
| Gemiddelde absolute fout |$9.5 m |$19.4 m |
| Gemiddelde absolute fout (%) |6.03% |12.2% |

Wanneer we het proces en de resultaten door de ontwikkel aars en gegevens wetenschappers op het Machine Learning team heeft uitgevoerd, hebben ze snel enkele nuttige tips gegeven. 

* Wanneer u de [lineaire regressie][linear-regression] module in Studio (klassiek) gebruikt, zijn er twee methoden aanwezig:
  * Online verloop Daal: is mogelijk beter geschikt voor grotere problemen
  * Normale minimale kwadraten: dit is de methode waarvan de meeste gebruikers denken wanneer ze lineaire regressie horen. Voor kleine gegevens sets is het mogelijk dat gewone, minimale kwadraten een meer optimale keuze zijn.
* U kunt de para meter voor het regularisatie gewicht van L2 aanpassen om de prestaties te verbeteren. De instelling is standaard ingesteld op 0,001, maar voor de kleine gegevensset is deze ingesteld op 0,005 om de prestaties te verbeteren. 

### <a name="mystery-solved"></a>Mysterie opgelost.
Toen we de aanbevelingen hebben toegepast, hebben we dezelfde basislijn prestaties in Studio (klassiek) bereikt als bij Excel: 

|  | Excel | Studio (klassiek) (oorspronkelijk) | Studio (klassiek) met minste kwadraten |
| --- |:---:|:---:|:---:|
| Label waarde |Werkelijke waarden (numeriek) |dezelfde |dezelfde |
| Learner |Excel-> gegevens analyse-> regressie |Lineaire regressie. |Lineaire regressie |
| Opties voor meer informatie |N.v.t. |Standaard |normale minimale kwadraten<br />L2 = 0,005 |
| Gegevensset |26 rijen, 3 kenmerken, 1 label. Alle getallen. |dezelfde |dezelfde |
| Splitsen: trainen |Excel getraind op de eerste 18 rijen, getest op de laatste 8 rijen. |dezelfde |dezelfde |
| Splitsen: testen |Formule van Excel-regressie toegepast op de laatste 8 rijen |dezelfde |dezelfde |
| **Prestaties** | | | |
| Aangepast R-kwadraat |0.96 |N.v.t. | |
| Determinatie coëfficiënt |N.v.t. |0.78 |0.952049 |
| Gemiddelde absolute fout |$9.5 m |$19.4 m |$9.5 m |
| Gemiddelde absolute fout (%) |<span style="background-color: 00FF00;">6,03%</span> |12.2% |<span style="background-color: 00FF00;">6,03%</span> |

Daarnaast worden de Excel-coëfficiënten vergeleken met het onderdeel gewichten in het door Azure getrainde model:

|  | Excel-coëfficiënten | Gewichten van Azure-functies |
| --- |:---:|:---:|
| Interceptie/afwijking |19470209.88 |19328500 |
| Functie A |0.832653063 |0.834156 |
| Functie B |11071967.08 |11007300 |
| Functie C |25383318.09 |25140800 |

## <a name="next-steps"></a>Volgende stappen
We wilden de Machine Learning-webservice in Excel gebruiken. Onze bedrijfs analisten vertrouwen op Excel en we hebben een manier nodig om de Machine Learning-webservice aan te roepen met een rij Excel-gegevens en de voorspelde waarde te retour neren naar Excel. 

We wilden ons model ook optimaliseren met behulp van de opties en algoritmen die beschikbaar zijn in Studio (klassiek).

### <a name="integration-with-excel"></a>Integratie met Excel
Onze oplossing was om ons Machine Learning regressie model te operationeel maken door een webservice te maken op basis van het getrainde model. Binnen een paar minuten is de webservice gemaakt en kunnen we deze rechtstreeks vanuit Excel aanroepen om een voorspelde omzet waarde te retour neren. 

De sectie *Web Services-dash board* bevat een Excel-werkmap die kan worden gedownload. De werkmap wordt vooraf opgemaakt met de webservice-API en de Inge sloten schema-informatie. Wanneer u op *Excel-werkmap downloaden*klikt, wordt de werkmap geopend en kunt u deze opslaan op de lokale computer. 

![Excel-werkmap downloaden via het dash board van webservices](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Als de werkmap is geopend, kopieert u de vooraf gedefinieerde para meters naar de blauwe para meter sectie, zoals hieronder wordt weer gegeven. Als de para meters zijn ingevoerd, wordt Excel aangeroepen naar de Machine Learning-webservice en worden de voorspelde labels weer gegeven in de sectie voor waarden voor voor spellingen. De werkmap blijft voor spellingen maken voor para meters op basis van uw getrainde model voor alle rij-items die onder para meters zijn ingevoerd. Zie voor meer informatie over het gebruik van deze functie [een Azure machine learning-webservice gebruiken vanuit Excel](consuming-from-excel.md). 

![Sjabloon Excel-werkmap die verbinding maakt met de geïmplementeerde webservice](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalisatie en verdere experimenten
Nu we een basis lijn hadden met ons Excel-model, zijn we eerder verhuisd om het lineaire regressie model van Machine Learning te optimaliseren. We hebben de functie voor het [filteren op basis][filter-based-feature-selection] van module filters gebruikt voor het verbeteren van de selectie van de eerste gegevens elementen en het heeft ons geholpen een prestatie verbetering van 4,6% te bereiken met een absolute fout. Voor toekomstige projecten gebruiken we deze functie die in de toekomst weken kan besparen bij het door lopen van gegevens kenmerken om de juiste set functies te vinden die moeten worden gebruikt voor het model leren. 

Daarna plannen we extra algoritmen zoals [Bayesiaanse][bayesian-linear-regression] of [versterkte beslissings structuren][boosted-decision-tree-regression] in ons experiment om de prestaties te vergelijken. 

Als u wilt experimenteren met regressie, is een goede gegevensset om te proberen de gegevensset energie-efficiëntie regressie te testen, die veel numerieke kenmerken heeft. De gegevensset wordt gegeven als onderdeel van de voorbeeld gegevens sets in Studio (klassiek). U kunt verschillende leer modules gebruiken om te voors pellen van de belasting of de koel belasting. In de onderstaande grafiek ziet u een vergelijking van de prestaties van verschillende regressies voor het voors pellen van de gegevensset voor energie-efficiëntie voor de berekenings variabele van de doel variabelen: 

| Model | Gemiddelde absolute fout | Wortel fout in kwadraat | Relatieve absolute fout | Relatief gekwadrateerde fout | Determinatie coëfficiënt |
| --- | --- | --- | --- | --- | --- |
| Versterkte beslissings structuur |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineaire regressie (kleur overgang Daal) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regressie neuraal netwerk |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineaire regressie (gewone, minimale kwadraten) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Key Takeaways
We hebben een hoop geleerd van het uitvoeren van Excel-experimenten met regressie en Studio (klassiek) parallel. Het basis model maken in Excel en vergelijken met modellen met behulp van Machine Learning [lineaire regressie][linear-regression] heeft ons geholpen bij het leren van Studio (klassiek) en we hebben mogelijkheden gedetecteerd om de gegevens selectie en de prestaties van het model te verbeteren. 

We hebben ook geadviseerd om op [filter gebaseerde functie selectie][filter-based-feature-selection] te gebruiken om toekomstige Voorspellings projecten te versnellen. Door functie selectie toe te passen op uw gegevens, kunt u een verbeterd model maken in Studio (klassiek) met betere algehele prestaties. 

De mogelijkheid om de voorspellende analytische prognose van Studio (klassiek) over te dragen naar Excel System root, maakt een aanzienlijke toename van de mogelijkheid om resultaten te bieden aan een brede zakelijke gebruikers publiek. 

## <a name="resources"></a>Bronnen
Hier volgen enkele bronnen voor hulp bij het werken met regressie: 

* Regressie in Excel. Als u nog niet hebt geprobeerd regressie in Excel te maken, kunt u met deze zelf studie het volgende doen: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressie versus prognoses. Tyler Schaakman schreef een blog artikel waarin wordt uitgelegd hoe u een time series-prognose in Excel kunt maken. dit document bevat een goede beschrijving van lineaire regressie. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Normale minimale kwadraten lineaire regressie: fouten, problemen en Valk uilen. Voor een inleiding en bespreking van regressie: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

