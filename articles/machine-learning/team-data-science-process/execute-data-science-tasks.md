---
title: Uitvoeren van datatechnologietaken - Team Data Science Process
description: Hoe een gegevenswetenschapper een data science-project kunt uitvoeren in een herleidbare versie beheerd en gezamenlijke manier.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: eb792034e49b0f8f663eb9ee968ea6fac1372373
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278610"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Datatechnologietaken uitvoeren: gegevensonderzoek, modelleren en implementatie

Datatechnologietaken doorgaans bevatten gegevens gegevensonderzoek, modelleren en implementatie. In dit artikel ziet u hoe u de **interactieve Gegevensverkenning, analyse en rapportage (IDEAR)** en **geautomatiseerde modelleren en rapportage (AMAR)** hulpprogramma's voor verschillende algemene datatechnologietaken uitvoeren zoals interactieve gegevensverkenning, gegevensanalyse, rapportage en model maken. Ook biedt opties voor het implementeren van een model in een productieomgeving met behulp van een aantal gegevens en toolkits platformen, zoals het volgende:

- [Azure Machine Learning](../index.yml)
- [SQL-Server met ML-services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Verkennen 

Een gegevenswetenschapper kunt verkennen en rapportage in tal van manieren uitvoeren: met behulp van bibliotheken en pakketten beschikbaar voor Python (matplotlib bijvoorbeeld) of met R (ggplot of andere voorbeeld). Gegevenswetenschappers kunnen aanpassen van dergelijke code aan de behoeften van de gegevens verkennen voor specifieke scenario's aan. De behoeften voor het omgaan met gestructureerde gegevens zijn verschillend voor ongestructureerde gegevens zoals tekst of afbeeldingen. 

Producten, zoals Azure Machine Learning-service bieden ook [geavanceerde gegevensvoorbereiding](../service/how-to-transform-data.md) voor data wrangling en onderzoek, met inbegrip van de functie maken. De gebruiker beslissen over de hulpprogramma's, bibliotheken en pakketten die het best suite hun behoeften. 

Het product aan het einde van deze fase is een rapport verkennen. Het rapport moet bieden een redelijk uitgebreide weergave van de gegevens moet worden gebruikt voor het maken van modellering en een evaluatie van of de gegevens zijn geschikt om door te gaan naar de stap modelleren. Het Team Data Science Process (TDSP) hulpprogramma's beschreven in de volgende secties voor de semi-automatische verkennen, modelleren en rapportage bieden ook gestandaardiseerde gegevens verkennen en modelleren van rapporten. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interactieve gegevensverkenning, analyse en rapportage met het hulpprogramma IDEAR

Deze markdown op basis van R of Python-notebook-hulpprogramma biedt een flexibele en interactieve hulpprogramma om te evalueren en gegevenssets te verkennen. Gebruikers kunnen snel rapporten genereren van de gegevensset met minimale code schrijven. Gebruikers kunnen klikken op knoppen waarmee u kunt de resultaten verkennen in de interactieve hulpprogramma exporteren naar een uiteindelijke rapport, die kan worden bezorgd bij clients of gebruikt om beslissingen over welke variabelen om op te nemen in de modellering van de volgende stap.

Op dit moment werkt het hulpprogramma alleen op gegevensframes in het geheugen. Een YAML-bestand is nodig om op te geven van de parameters van de gegevensset worden onderzocht. Zie voor meer informatie, [IDEAR in TDSP Data Science-hulpprogramma's](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Model maken

Er zijn talrijke toolkits en -pakketten voor modellen voor training in diverse talen. Gegevenswetenschappers moeten gerust gebruiken die uitgevoerde die ze vertrouwd met, bent zolang de prestatie-overwegingen met betrekking tot de nauwkeurigheid en latentie is voldaan voor de desbetreffende bedrijven use cases en productiescenario's.

De volgende sectie ziet u hoe u een TDSP op basis van R-hulpprogramma voor de semi-automatische modelleren. Dit hulpprogramma AMAR kan worden gebruikt om de basislijn modellen snel, evenals de parameters die worden afgestemd moeten voor een beter uitvoeren model genereren.
De volgende sectie van de model-management laat zien hoe een systeem voor het registreren en beheren van meerdere modellen.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model voor training: gegevensmodellering en -rapportage met het hulpprogramma AMAR

De [geautomatiseerde modelleren en hulpprogramma voor rapportage (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) voorziet in een aanpasbare, semi-automatische hulpprogramma voor het uitvoeren van model maken met hyper-parameter sweeping en vergelijken van de nauwkeurigheid van deze modellen. 

Het model maken van het hulpprogramma is een R Markdown-bestand dat kan worden uitgevoerd om ingesloten HTML-uitvoer met een tabel met inhoud voor eenvoudige navigatie door de verschillende secties produceren. Drie algoritmen worden uitgevoerd wanneer het Markdown-bestand (knit) wordt uitgevoerd: overgegaan regression uit met behulp van de glmnet inpakken, willekeurige forest met behulp van het pakket randomForest en structuren met behulp van het pakket xgboost versterking). Elk van deze algoritmen produceert een getraind model. De nauwkeurigheid van deze modellen wordt vervolgens vergeleken en de functie voor relatieve belang grafieken worden gerapporteerd. Er zijn momenteel twee hulpprogramma's: één voor een taak binaire classificatie en één voor een taak regressie. De belangrijkste verschillen tussen deze is de parameters voor de manier en nauwkeurigheid metrische gegevens voor deze taken zijn opgegeven. 

Een YAML-bestand wordt gebruikt om op te geven:

- de gegevensinvoer (een SQL-bron of een R-bestand) 
- welk gedeelte van de gegevens wordt gebruikt voor training en welk gedeelte voor het testen
- welke algoritmen om uit te voeren 
- de keuze van de parameters voor voor de optimalisatie van het model:
    - kruisvalidatie 
    - uitvoeren van de bootstrap
    - vouwen van kruisvalidatie
- Hiermee stelt u de hyper-parameter voor elk algoritme. 

Het nummer van algoritmen, het aantal vouwen voor optimalisatie, de hyper-parameters en het aantal hyper-parameter opgegeven voor zwaaihoek via kan ook worden aangepast in het Yaml-bestand in de modellen snel worden uitgevoerd. Ze kunnen bijvoorbeeld worden uitgevoerd met een kleiner aantal vouwen CV, een kleiner aantal parametersets. Als het gerechtvaardigd is, kunnen ze ook worden uitgevoerd meer uitvoerig met een hoger aantal vouwen CV of een groter aantal parametersets.

Zie voor meer informatie, [geautomatiseerde modelleren en hulpprogramma voor rapportage in TDSP Data Science-hulpprogramma's](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modelbeheer
Nadat u meerdere modellen zijn gebouwd, moet u doorgaans een systeem voor het registreren en beheren van de modellen. Normaal gesproken moet u een combinatie van scripts of API's en een back-end-database of het versiebeheer systeem. Er zijn een aantal opties die u voor deze beheertaken overwegen kunt:

1. [Azure Machine Learning - Modelbeheer-service](../index.yml)
2. [ModelDB van MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-server als een model management-systeem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Implementatie

Productie-implementatie kunt een model voor het een actieve rol spelen in een bedrijf. Voorspellingen op basis van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen te nemen.

### <a name="production-platforms"></a>Productieplatforms
Er zijn verschillende benaderingen en platforms modellen in productie te plaatsen. Hier zijn enkele opties:


- [Modelimplementatie in Azure Machine Learning-service](../service/how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Voorafgaand aan de implementatie heeft een zorgen dat de latentie van het model scoren laag genoeg is voor gebruik in productie is.
>
>

Meer voorbeelden zijn beschikbaar in scenario's die laten zien van alle de stappen in het proces voor het **specifieke scenario's**. Ze worden weergegeven en die is gekoppeld met miniaturen beschrijvingen in de [voorbeeld walkthroughs](walkthroughs.md) artikel. Ze laten zien hoe u naar de cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen.

> [!NOTE]
> Zie voor een implementatie met behulp van Azure Machine Learning Studio, [een Azure Machine Learning-webservice implementeren](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B-tests
Als meerdere modellen in productie, kan het nuttig zijn om uit te voeren [A / B-tests](https://en.wikipedia.org/wiki/A/B_testing) kunnen de prestaties van de modellen. 

 
## <a name="next-steps"></a>Volgende stappen

[Voortgang van de data science-projecten volgen](track-progress.md) laat zien hoe een gegevenswetenschapper de voortgang van een data science-project kunt volgen.

[Model voor bewerking en CI/CD](ci-cd-flask.md) laat zien hoe CI/CD met ontwikkelde modellen kunnen worden uitgevoerd.


