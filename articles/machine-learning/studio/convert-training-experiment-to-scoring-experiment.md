---
title: Model voorbereiden voor implementatie
titleSuffix: ML Studio (classic) - Azure
description: Het voorbereiden van uw getrainde model voor implementatie als een webservice door uw Machine Learning Studio-trainings experiment (klassiek) te converteren naar een voorspellend experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: fa9c5e5fa84a1ea6718bd82cd349f9248d283722
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168855"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Uw model voorbereiden voor implementatie in Azure Machine Learning Studio (klassiek)

Azure Machine Learning Studio (klassiek) biedt u de hulp middelen die u nodig hebt om een predictive analytics model te ontwikkelen en vervolgens te operationeel maken door het te implementeren als een Azure-webservice.

U kunt dit doen door Studio (klassiek) te gebruiken voor het maken van een experiment, een *trainings experiment* , waarin u uw model traint, scoreeert en bewerkt. Zodra u tevreden bent, kunt u uw model implementeren door uw trainings experiment te converteren naar een *voorspellend experiment* dat is geconfigureerd voor de Score gebruikers gegevens.

U kunt een voor beeld van dit proces bekijken in [zelf studie 1: krediet risico voors pellen](tutorial-part1-credit-risk.md).

In dit artikel wordt een dieper ingaan op de details van hoe een opleidingsexperiment wordt geconverteerd naar een Voorspellend experiment en hoe die Voorspellend experiment is geïmplementeerd. Door de volgende details, kunt u informatie over het configureren van uw geïmplementeerd model zodat het efficiëntere.



## <a name="overview"></a>Overzicht 

Het proces van het een opleidingsexperiment converteren naar een Voorspellend experiment bestaat uit drie stappen:

1. Vervang de machine learning-algoritme voor modules met het getrainde model.
2. Verwijder het experiment alleen de modules die nodig zijn voor het scoren. Een opleidingsexperiment omvat een aantal modules die nodig zijn voor de training, maar niet nodig zijn wanneer het model wordt getraind.
3. Definiëren hoe het model van uw gegevens van de gebruiker van de web-service worden geaccepteerd en welke gegevens worden geretourneerd.

> [!TIP]
> U hebt met training en scoren van uw model met behulp van uw eigen gegevens betrokken zijn in uw trainingsexperiment. Maar zodra geïmplementeerd, gebruikers nieuwe gegevens wordt verzonden aan uw model en voorspellingsresultaten wordt geretourneerd. Dus als u uw trainingsexperiment naar een Voorspellend experiment converteren zodat deze klaar is voor implementatie, houd er rekening mee hoe het model wordt gebruikt door anderen.
> 
> 

## <a name="set-up-web-service-button"></a>Knop Web Service instellen
Nadat u uw experiment hebt uitgevoerd (Klik onder aan het canvas op **uitvoeren** ), klikt u op de knop **webservice instellen** (Selecteer de optie **voorspellende webservice** ). Het instellen van de **webservice** voert de volgende drie stappen uit om uw trainings experiment te converteren naar een voorspellend experiment:

1. U kunt uw getrainde model opslaan in de sectie **getrainde modellen** van het module palet (links van het canvas op het experiment). Vervolgens wordt het algoritme voor machine learning en [Train model][train-model] modules vervangen door het opgeslagen getrainde model.
2. Deze analyseert uw experiment en verwijdert u modules die zijn duidelijk wordt alleen gebruikt voor training en niet langer nodig zijn.
3. Hiermee worden de modules _Web Service input_ en _output_ ingevoegd op de standaard locaties in uw experiment (deze modules accepteren en retour neren gebruikers gegevens).

Het volgende experiment traint bijvoorbeeld een tweeklasse boosted decision tree model met voorbeeldgegevens telling:

![Trainingsexperiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

De modules in dit experiment uitvoeren in feite vier verschillende functies:

![Module-functies](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Wanneer u deze trainingsexperiment naar een Voorspellend experiment converteren, sommige van deze modules zijn niet meer nodig hebt of ze nu een ander doel dienen:

* **Gegevens** -de gegevens in deze voorbeeld gegevensset worden niet gebruikt tijdens de score-de gebruiker van de webservice levert de gegevens die moeten worden beoordeeld. Echter, de metagegevens van deze gegevensset, zoals gegevenstypen, wordt gebruikt door het getrainde model. Daarom moet u de gegevensset in de Voorspellend experiment houden, zodat deze metagegevens kan bieden.

* **Prep** : afhankelijk van de gebruikers gegevens die worden verzonden voor de score, zijn deze modules mogelijk niet nodig om de binnenkomende gegevens te verwerken. Met de knop **webservice instellen** kunt u deze niet aanraken. u moet bepalen hoe u deze wilt verwerken.
  
    In dit voor beeld kan de gegevensset van de voor beelden bijvoorbeeld ontbrekende waarden bevatten, zodat er een [schone ontbrekende gegevens][clean-missing-data] module werd opgenomen om deze te verwerken. Daarnaast bevat de voorbeeldgegevensset van kolommen die niet nodig zijn voor het model te trainen. Daarom is een [select columns in dataset][select-columns] -module opgenomen om deze extra kolommen uit te sluiten van de gegevens stroom. Als u weet dat de gegevens die via de webservice worden verzonden, geen ontbrekende waarden bevatten, kunt u de module [clean Missing Data][clean-missing-data] verwijderen. Omdat in de module [select columns in dataset][select-columns] de kolommen met gegevens worden gedefinieerd die het getrainde model verwacht, moet die module echter behouden blijven.

* **Train** : deze modules worden gebruikt om het model te trainen. Wanneer u op **webservice instellen**klikt, worden deze modules vervangen door één module die het model bevat dat u hebt getraind. Deze nieuwe module wordt opgeslagen in de sectie **getrainde modellen** van het module palet.

* **Score** : in dit voor beeld wordt de module [Split data][split] gebruikt om de gegevens stroom te verdelen in test gegevens en trainings gegevens. In het voorspellende experiment worden we niet meer getraind, zodat [gesplitste gegevens][split] kunnen worden verwijderd. Op dezelfde manier worden de module voor het tweede [score model][score-model] en de [model module evalueren][evaluate-model] gebruikt voor het vergelijken van de resultaten van de test gegevens. deze modules zijn dus niet nodig in het voorspellende experiment. De resterende [score model][score-model] module is echter nodig om een score resultaat te retour neren via de webservice.

Hier ziet u hoe het voor beeld eruitziet nadat **u op webservice instellen**hebt geklikt:

![Geconverteerde Voorspellend experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Het werk dat is gedaan door de webservice in te **stellen** kan voldoende zijn om uw experiment voor te bereiden om te worden geïmplementeerd als een webservice. U wilt echter enkele extra werk die specifiek zijn voor uw experiment doet.

### <a name="adjust-input-and-output-modules"></a>Invoer- en -modules aanpassen
In uw trainingsexperiment gebruikt van een set trainingsgegevens en vervolgens heeft enkele verwerking om de gegevens in een formulier dat de machine learning-algoritme die nodig zijn. Als de gegevens die u verwacht via de webservice, deze verwerking niet nodig hebt, kunt u deze omzeilen: Verbind de uitvoer van de **module Web Service-invoer** naar een andere module in uw experiment. U ontvangt nu gegevens van de gebruiker in het model op deze locatie.

Zo wordt bijvoorbeeld standaard de webservice- **invoer** module aan de bovenkant van de gegevens stroom **ingesteld** , zoals wordt weer gegeven in de bovenstaande afbeelding. Maar we kunnen de webservice- **invoer** na de modules voor gegevens verwerking hand matig plaatsen:

![Het verplaatsen van de web service-invoer](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De ingevoerde gegevens opgegeven via de webservice geeft nu rechtstreeks in de module Score Model zonder eventuele voorverwerking.

Op dezelfde manier wordt de webservice-uitvoer module aan de onderkant van de gegevens stroom standaard **ingesteld** . In dit voor beeld keert de webservice terug naar de gebruiker de uitvoer van de module [score model][score-model] , die de volledige invoer gegevens vector bevat plus de resultaten van de score.
Als u echter liever iets anders retourneert, kunt u aanvullende modules toevoegen vóór de module **webservice-uitvoer** . 

Als u bijvoorbeeld alleen de Score resultaten en niet de volledige vector van invoer gegevens wilt retour neren, voegt u een [select columns in dataset][select-columns] -module toe om alle kolommen uit te sluiten, met uitzonde ring van de Score resultaten. Verplaats vervolgens de module **webservice-uitvoer** naar de uitvoer van de module [select columns in dataset][select-columns] . Het experiment ziet er als volgt:

![Het verplaatsen van de web service-uitvoer](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Toevoegen of verwijderen van extra gegevensverwerkende modules
Als er meer modules in uw experiment waarvan u weet niet meer nodig dat tijdens de score, kunnen deze worden verwijderd. Omdat we de module **webservice-invoer** bijvoorbeeld hebben verplaatst naar een punt na de modules voor gegevens verwerking, kunnen we de module [clean Missing Data][clean-missing-data] uit het voorspellende experiment verwijderen.

Onze Voorspellend experiment ziet er nu als volgt uit:

![Aanvullende module verwijderen](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Toevoegen van optionele Parameters van de Web Service
In sommige gevallen kunt u toestaan dat de gebruiker van uw web-service het gedrag van modules wijzigen wanneer de service wordt geopend. U kunt dit doen met de *para meters* van de webservice.

Een voor beeld hiervan is het instellen van een [import gegevens][import-data] module, zodat de gebruiker van de geïmplementeerde webservice een andere gegevens bron kan opgeven wanneer de webservice wordt geopend. Of configureer een module voor het [exporteren van gegevens][export-data] , zodat er een andere bestemming kan worden opgegeven.

U kunt Web Service Parameters definiëren en deze koppelen aan een of meer moduleparameters en kunt u opgeven of ze verplicht of optioneel zijn. De gebruiker van de webservice bevat waarden voor deze parameters wanneer de service kan worden geopend en de acties van de module worden gewijzigd.

Zie [Using Azure machine learning web service para meters][webserviceparameters]voor meer informatie over de para meters van de web-service en hoe u deze kunt gebruiken.

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Het voorspellende experiment implementeren als webservice
Nu dat de Voorspellend experiment voldoende is voorbereid, kunt u deze kunt implementeren als een Azure-web-service. Met behulp van de webservice, kunnen gebruikers gegevens verzenden naar uw model en het model de voorspellingen wordt geretourneerd.

Zie [een Azure machine learning-webservice implementeren][deploy] voor meer informatie over het volledige implementatie proces

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
