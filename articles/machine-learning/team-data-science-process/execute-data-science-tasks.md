---
title: Data science taken uitvoeren - Team Data Science Process
description: Hoe een datascientist een data science-project kan uitvoeren op een traceerbare, versiegestuurde en collaboratieve manier.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477150"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Gegevenswetenschapstaken uitvoeren: verkenning, modellering en implementatie

Typische gegevenswetenschapstaken zijn gegevensverkenning, modellering en implementatie. In dit artikel ziet u hoe u de **hulpprogramma's Voor verkenning, analyse en rapportage (Interactive Data Exploration, And Reporting)** en **Automated Modeling and Reporting (AMAR)** gebruiken om verschillende veelvoorkomende gegevenswetenschappelijke taken uit te voeren, zoals interactieve gegevensverkenning, gegevensanalyse, rapportage en het maken van modellen. Opties voor het implementeren van een model in een productieomgeving kunnen bestaan uit:

- [Azure Machine Learning](../index.yml)
- [SQL-Server met ML-services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Verkenning 

Een datascientist kan verkenning en rapportage op verschillende manieren uitvoeren: door bibliotheken en pakketten te gebruiken die beschikbaar zijn voor Python (matplotlib bijvoorbeeld) of met R (bijvoorbeeld ggplot of rooster). Gegevenswetenschappers kunnen dergelijke code aanpassen aan de behoeften van gegevensverkenning voor specifieke scenario's. De behoeften voor het omgaan met gestructureerde gegevens zijn verschillend dan voor ongestructureerde gegevens zoals tekst of afbeeldingen. 

Producten zoals Azure Machine Learning bieden ook [geavanceerde gegevensvoorbereiding](../how-to-create-register-datasets.md) voor het verzamelen en verkennen van gegevens, inclusief het maken van functies. De gebruiker moet beslissen over de tools, bibliotheken en pakketten die het beste passen bij hun behoeften. 

De deliverable aan het einde van deze fase is een data exploration report. Het rapport moet een vrij uitgebreid overzicht geven van de gegevens die moeten worden gebruikt voor het modelleren en een beoordeling van de vraag of de gegevens geschikt zijn om door te gaan naar de modelleringsstap. De Hulpprogramma's van het Team Data Science Process (TDSP) die in de volgende secties worden besproken voor semi-geautomatiseerde exploratie- en modellerings- en rapportagerapporten bieden ook gestandaardiseerde rapporten over gegevensverkenning en modellering. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interactieve gegevensverkenning, -analyse en -rapportage met behulp van het IDEAR-hulpprogramma

Deze R-markdown- of Python-notebook-gebaseerde hulpprogramma biedt een flexibel en interactief hulpmiddel om gegevenssets te evalueren en te verkennen. Gebruikers kunnen snel rapporten genereren van de gegevensset met minimale codering. Gebruikers kunnen op knoppen klikken om de verkenningsresultaten in de interactieve tool te exporteren naar een eindrapport, dat aan clients kan worden geleverd of kan worden gebruikt om beslissingen te nemen over welke variabelen moeten worden opgenomen in de volgende modelleringsstap.

Op dit moment werkt de tool alleen op gegevensframes in het geheugen. Er is een YAML-bestand nodig om de parameters van de te verkennen gegevensset op te geven. Zie [IDEAR in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)voor meer informatie.


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modellenwerk

Er zijn tal van toolkits en pakketten voor trainingsmodellen in verschillende talen. Gegevenswetenschappers moeten zich vrij voelen om te gebruiken met welke ze zich ook comfortabel voelen, zolang prestatieoverwegingen met betrekking tot nauwkeurigheid en latentie worden voldaan voor de relevante business use cases en productiescenario's.

In de volgende sectie ziet u hoe u een R-gebaseerd TDSP-hulpprogramma gebruiken voor semi-geautomatiseerde modellering. Deze AMAR utility kan worden gebruikt om snel basislijnmodellen te genereren, evenals de parameters die moeten worden afgestemd om een beter presterend model te bieden.
In de volgende sectie modelbeheer ziet u hoe u een systeem hebben voor het registreren en beheren van meerdere modellen.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modeltraining: modelleren en rapporteren met behulp van het AMAR-hulpprogramma

De [Automated Modeling and Reporting (AMAR) Utility](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) biedt een aanpasbare, semi-geautomatiseerde tool om modelcreatie uit te voeren met hyper-parameter vegen en om de nauwkeurigheid van die modellen te vergelijken. 

Het hulpprogramma voor het maken van het model is een R Markdown-bestand dat kan worden uitgevoerd om zelfstandige HTML-uitvoer te produceren met een inhoudsopgave voor eenvoudige navigatie door de verschillende secties. Drie algoritmen worden uitgevoerd wanneer het Markdown-bestand wordt uitgevoerd (gebreid): geregulariseerde regressie met behulp van het glmnet-pakket, willekeurig forest met behulp van het randomForest-pakket en het stimuleren van bomen met behulp van het xgboost-pakket). Elk van deze algoritmen produceert een getraind model. De nauwkeurigheid van deze modellen wordt vervolgens vergeleken en de relatieve functie belang percelen worden gemeld. Momenteel zijn er twee hulpprogramma's: een is voor een binaire classificatie taak en een is voor een regressie taak. De belangrijkste verschillen tussen deze zijn de manier waarop controleparameters en nauwkeurigheidsstatistieken worden opgegeven voor deze leertaken. 

Een YAML-bestand wordt gebruikt om op te geven:

- de gegevensinvoer (een SQL-bron of een R-Data-bestand) 
- welk deel van de gegevens wordt gebruikt voor training en welk deel voor het testen
- welke algoritmen moeten worden uitgevoerd 
- de keuze van de controleparameters voor modeloptimalisatie:
    - cross-validatie 
    - Bootstrapping
    - plooien van cross-validatie
- de hyperparametersets voor elk algoritme. 

Het aantal algoritmen, het aantal plooien voor optimalisatie, de hyperparameters en het aantal hyperparametersets dat moet worden overgenomen, kunnen ook worden gewijzigd in het Yaml-bestand om de modellen snel uit te voeren. Ze kunnen bijvoorbeeld worden uitgevoerd met een lager aantal CV-vouwen, een lager aantal parametersets. Als dit gerechtvaardigd is, kunnen ze ook uitgebreider worden uitgevoerd met een hoger aantal CV-plooien of een groter aantal parametersets.

Zie [Automated Modeling and Reporting Utility in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)voor meer informatie.

### <a name="model-management"></a>Modelbeheer
Nadat er meerdere modellen zijn gebouwd, moet u meestal een systeem hebben voor het registreren en beheren van de modellen. Meestal hebt u een combinatie van scripts of API's en een backend-database of versiesysteem nodig. Een paar opties die u overwegen voor deze beheertaken zijn:

1. [Azure Machine Learning - modelbeheerservice](../index.yml)
2. [ModelDB van MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server als modelbeheersysteem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Implementatie

Met de productie-implementatie kan een model een actieve rol spelen in een bedrijf. Voorspellingen van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen.

### <a name="production-platforms"></a>Productieplatforms
Er zijn verschillende benaderingen en platforms om modellen in productie te nemen. Hier zijn een paar opties:


- [Implementatie modelleren in Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Voorafgaand aan de implementatie moet men ervoor zorgen dat de latentie van modelscores laag genoeg is om te gebruiken in de productie.
>
>

Verdere voorbeelden zijn beschikbaar in walkthroughs die alle stappen in het proces voor **specifieke scenario's**demonstreren . Ze worden vermeld en gekoppeld aan miniatuurbeschrijvingen in het artikel [Voorbeeld walkthroughs.](walkthroughs.md) Ze illustreren hoe cloud- en on-premises tools en services kunnen worden gecombineerd in een workflow of pijplijn om een intelligente toepassing te maken.

> [!NOTE]
> Zie [Een Webservice Azure Machine Learning implementeren](../studio/deploy-a-machine-learning-web-service.md)voor implementatie met Azure Machine Learning Studio.
>
>

### <a name="ab-testing"></a>A/B-testen
Wanneer er meerdere modellen in productie zijn, kan het handig zijn om [A/B-tests](https://en.wikipedia.org/wiki/A/B_testing) uit te voeren om de prestaties van de modellen te vergelijken. 

 
## <a name="next-steps"></a>Volgende stappen

[De voortgang van data science-projecten](track-progress.md) bijhouden, laat zien hoe een datascientist de voortgang van een data science-project kan volgen.

[Modelbediening en CI/CD](ci-cd-flask.md) laten zien hoe CI/CD kan worden uitgevoerd met ontwikkelde modellen.


