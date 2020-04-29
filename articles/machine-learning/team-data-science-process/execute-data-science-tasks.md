---
title: Data science tasks uitvoeren-team data Science process
description: Hoe een Data wetenschapper een Data Science-project kan uitvoeren in een trackable, gecontroleerde versie en op samenwerkings wijze.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477150"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Data science tasks uitvoeren: verkennen, model leren en implementeren

Typische data science tasks omvatten het verkennen, model leren en implementeren van gegevens. In dit artikel wordt beschreven hoe u de Program ma's voor het **verkennen, analyseren en rapporteren van interactieve gegevens (ideeën)** en **geautomatiseerde modellen en rapporten (AMAR)** gebruikt voor het uitvoeren van verschillende algemene gegevens Science-taken, zoals het verkennen van interactieve gegevens, het analyseren van gegevens en het maken van een model. De opties voor het implementeren van een model in een productie omgeving kunnen het volgende omvatten:

- [Azure Machine Learning](../index.yml)
- [SQL-Server met ML-Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> verkennen 

Een gegevens wetenschapper kan op verschillende manieren onderzoek en rapportage uitvoeren: door gebruik te maken van bibliotheken en pakketten die beschikbaar zijn voor python (matplotlib bijvoorbeeld) of met R (ggplot of raster bijvoorbeeld). Gegevens wetenschappers kunnen dergelijke code aanpassen aan de behoeften van het verkennen van gegevens voor specifieke scenario's. De behoeften voor het verwerken van gestructureerde gegevens zijn anders dan voor ongestructureerde gegevens, zoals tekst of afbeeldingen. 

Producten zoals Azure Machine Learning bieden ook [geavanceerde gegevens voorbereiding](../how-to-create-register-datasets.md) voor het wrangling en verkennen van gegevens, inclusief het maken van functies. De gebruiker moet beslissen over de hulpprogram ma's, Bibliotheken en pakketten die het beste aan hun behoeften voldoen. 

Het product aan het einde van deze fase is een rapport voor gegevens onderzoek. Het rapport moet een redelijk uitgebreid overzicht geven van de gegevens die moeten worden gebruikt voor model lering en een evaluatie van de vraag of de gegevens geschikt zijn om door te gaan naar de model stap. De TDSP-hulpprogram ma's (team data Science process) die in de volgende secties worden besproken voor semi-Automated verkennen, model leren en rapporteren, bieden ook gestandaardiseerde rapporten voor gegevens exploratie en-modellering. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interactief verkennen, analyseren en rapporteren met behulp van het hulp programma voor idee

Dit hulp programma voor op prijs verlaging of python gebaseerd op een notebook biedt een flexibel en interactief hulp programma voor het evalueren en verkennen van gegevens sets. Gebruikers kunnen snel rapporten genereren op basis van de gegevensset met minimale code ring. Gebruikers kunnen klikken op knoppen om de resultaten van het onderzoek in het interactieve hulp programma naar een eind rapport te exporteren. Dit kan worden geleverd aan clients of worden gebruikt om beslissingen te nemen over de variabelen die in de volgende model stap moeten worden opgenomen.

Op dit moment werkt het hulp programma alleen op Data-frames in het geheugen. Er is een YAML-bestand nodig om de para meters op te geven van de gegevensset die moet worden verkend. Zie voor meer informatie [ideeën in TDSP data Science-Hulpprogram ma's](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> model lering

Er zijn talloze tool kits en pakketten voor trainings modellen in diverse talen. Gegevens wetenschappers kunnen gemoeds rust gebruik te maken van de ooit die ze kunnen gebruiken, zolang de prestatie overwegingen met betrekking tot nauw keurigheid en latentie voldoen aan de relevante zakelijke gebruiks situaties en productie scenario's.

In de volgende sectie ziet u hoe u een R-gebaseerd TDSP-hulp programma gebruikt voor semi-geautomatiseerd model leren. Dit AMAR-hulp programma kan worden gebruikt voor het snel genereren van basis lijn modellen en de para meters die moeten worden afgestemd om een beter model voor het uitvoeren van modellen te bieden.
In de volgende sectie voor model beheer ziet u hoe u een systeem hebt voor het registreren en beheren van meerdere modellen.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model training: modellen en rapporten met behulp van het hulp programma AMAR

Het [hulp programma voor geautomatiseerd model leren en rapporteren (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) biedt een aanpasbaar, semi-geautomatiseerd hulp programma voor het maken van modellen met Hyper-para meters sweep en om de nauw keurigheid van deze modellen te vergelijken. 

Het hulp programma voor het maken van modellen is een R-bestand voor korting dat kan worden uitgevoerd voor het produceren van zelf-opgenomen HTML-uitvoer met een inhouds opgave voor eenvoudige navigatie door de verschillende secties. Er worden drie algoritmen uitgevoerd wanneer het afkortings bestand wordt uitgevoerd (Knit): er is een reguliere regressie met het glmnet-pakket, een wille keurig forest met het randomForest-pakket en het verhogen van structuren met het xgboost-pakket). Met elk van deze algoritmen wordt een getraind model gegenereerd. De nauw keurigheid van deze modellen wordt vervolgens vergeleken en de belangrijkste waarnemings punten van de functie worden gerapporteerd. Er zijn momenteel twee hulpprogram ma's: een is voor een binaire classificatie taak en één is voor een regressie taak. De belangrijkste verschillen ertussen zijn de manier waarop controle parameters en nauw keurigheid meet waarden worden opgegeven voor deze trainings taken. 

Er wordt een YAML-bestand gebruikt om het volgende op te geven:

- de gegevens invoer (een SQL-bron of een R-gegevens bestand) 
- welk gedeelte van de gegevens wordt gebruikt voor training en welk gedeelte voor testen
- welke algoritmen moeten worden uitgevoerd 
- de keuze van besturings parameters voor model optimalisatie:
    - Kruis validatie 
    - oftewel opnieuw opstarten
    - vouwen van kruis validatie
- de Hyper-parameters sets voor elk algoritme. 

Het aantal algoritmen, het aantal vouwen voor optimalisatie, de Hyper-para meters en het aantal Hyper-para meter sets voor het opruimen van items kunnen ook worden gewijzigd in het yaml-bestand om de modellen snel uit te voeren. Ze kunnen bijvoorbeeld worden uitgevoerd met een lager aantal AVK vouwen, een lager aantal parameter sets. Als deze is gerechtvaardigd, kunnen ze ook uitgebreider worden uitgevoerd met een hoger aantal AVK vouwen of een groter aantal para meters.

Zie het [hulp programma voor geautomatiseerd model leren en rapporteren in TDSP data Science-Hulpprogram ma's](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)voor meer informatie.

### <a name="model-management"></a>Modelbeheer
Nadat er meerdere modellen zijn gebouwd, hebt u doorgaans een systeem nodig voor het registreren en beheren van de modellen. Normaal gesp roken hebt u een combi natie van scripts of Api's en een back-end-data base of versie systeem nodig. Hier volgen enkele opties die u kunt overwegen voor deze beheer taken:

1. [Azure Machine Learning-model beheer service](../index.yml)
2. [ModelDB van MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-Server als model beheersysteem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> implementatie

Bij de productie-implementatie kan een model een actieve rol spelen in een bedrijf. Voor spellingen van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen.

### <a name="production-platforms"></a>Productie platforms
Er zijn verschillende benaderingen en platformen om modellen in productie te brengen. Hier volgen enkele opties:


- [Model implementatie in Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Voorafgaand aan de implementatie moet u er zeker van zijn dat de latentie van de model Score laag genoeg is voor gebruik in de productie omgeving.
>
>

Meer voor beelden zijn beschikbaar in een scenario waarin alle stappen in het proces voor **specifieke scenario's**worden getoond. Ze worden weer gegeven en gekoppeld aan miniatuur beschrijvingen in het artikel [voorbeeld scenario's](walkthroughs.md) . Ze illustreren het combi neren van Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn om een intelligente toepassing te maken.

> [!NOTE]
> Zie [Deploy a Azure machine learning web service](../studio/deploy-a-machine-learning-web-service.md)(Engelstalig) voor implementatie met behulp van Azure machine learning Studio.
>
>

### <a name="ab-testing"></a>A/B testen
Wanneer er meerdere modellen in productie zijn, kan het nuttig zijn om [een/B-test](https://en.wikipedia.org/wiki/A/B_testing) uit te voeren om de prestaties van de modellen te vergelijken. 

 
## <a name="next-steps"></a>Volgende stappen

[Houd de voortgang bij van data Science-projecten](track-progress.md) om te zien hoe een gegevens wetenschapper de voortgang van een Data Science-project kan volgen.

[Model bewerking en CI/cd](ci-cd-flask.md) laat zien hoe CI/cd kan worden uitgevoerd met ontwikkelde modellen.


