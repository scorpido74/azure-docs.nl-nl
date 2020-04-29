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
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204508"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Uw model voorbereiden voor implementatie in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassiek) biedt u de hulp middelen die u nodig hebt om een predictive analytics model te ontwikkelen en vervolgens te operationeel maken door het te implementeren als een Azure-webservice.

U kunt dit doen door Studio (klassiek) te gebruiken voor het maken van een experiment, een *trainings experiment* , waarin u uw model traint, scoreeert en bewerkt. Zodra u tevreden bent, kunt u uw model implementeren door uw trainings experiment te converteren naar een *voorspellend experiment* dat is geconfigureerd voor de Score gebruikers gegevens.

U kunt een voor beeld van dit proces bekijken in [zelf studie 1: krediet risico voors pellen](tutorial-part1-credit-risk.md).

In dit artikel wordt uitgelegd hoe een trainings experiment kan worden omgezet in een voorspellend experiment en hoe het voorspellende experiment wordt geïmplementeerd. Als u deze details begrijpt, kunt u leren hoe u uw geïmplementeerde model zo configureert dat het effectiever maakt.



## <a name="overview"></a>Overzicht 

Het proces voor het converteren van een opleidings experiment naar een voorspellend experiment bestaat uit drie stappen:

1. Vervang de machine learning algoritme modules door uw getrainde model.
2. Knip het experiment alleen uit op de modules die nodig zijn om te scoren. Een trainings experiment bevat een aantal modules die nodig zijn voor de training, maar die niet nodig zijn wanneer het model is getraind.
3. Definiëren hoe uw model gegevens van de webservice-gebruiker accepteert en welke gegevens worden geretourneerd.

> [!TIP]
> In uw trainings experiment hebt u zich met uw eigen gegevens getraind om uw model te trainen en te scoren. Maar na de implementatie worden gebruikers nieuwe gegevens naar uw model verzonden en retour neren ze de Voorspellings resultaten. Wanneer u uw trainings experiment converteert naar een voorspellend experiment om het klaar te maken voor implementatie, houdt u er rekening mee dat het model door anderen wordt gebruikt.
> 
> 

## <a name="set-up-web-service-button"></a>Knop webservice instellen
Nadat u uw experiment hebt uitgevoerd (Klik onder aan het canvas op **uitvoeren** ), klikt u op de knop **webservice instellen** (Selecteer de optie **voorspellende webservice** ). Het instellen van de **webservice** voert de volgende drie stappen uit om uw trainings experiment te converteren naar een voorspellend experiment:

1. U kunt uw getrainde model opslaan in de sectie **getrainde modellen** van het module palet (links van het canvas op het experiment). Vervolgens wordt het algoritme voor machine learning en [Train model][train-model] modules vervangen door het opgeslagen getrainde model.
2. Het analyseert uw experiment en verwijdert modules die duidelijk waren gebruikt voor trainingen en die niet meer nodig zijn.
3. Hiermee worden de modules _Web Service input_ en _output_ ingevoegd op de standaard locaties in uw experiment (deze modules accepteren en retour neren gebruikers gegevens).

Het volgende experiment traint bijvoorbeeld een gestimuleerd beslissings structuur model met twee klassen met voorbeeld gegevens over de telling:

![Trainings experiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

De modules in dit experiment voeren vier verschillende functies uit:

![Module functies](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Wanneer u dit trainings experiment converteert naar een voorspellend experiment, zijn sommige van deze modules niet meer nodig of hebben ze nu een ander doel:

* **Gegevens** -de gegevens in deze voorbeeld gegevensset worden niet gebruikt tijdens de score-de gebruiker van de webservice levert de gegevens die moeten worden beoordeeld. De meta gegevens van deze gegevensset, zoals gegevens typen, worden echter gebruikt door het getrainde model. Daarom moet u de gegevensset in het voorspellende experiment blijven staan zodat deze meta gegevens kan bevatten.

* **Prep** : afhankelijk van de gebruikers gegevens die worden verzonden voor de score, zijn deze modules mogelijk niet nodig om de binnenkomende gegevens te verwerken. Met de knop **webservice instellen** kunt u deze niet aanraken. u moet bepalen hoe u deze wilt verwerken.
  
    In dit voor beeld kan de gegevensset van de voor beelden bijvoorbeeld ontbrekende waarden bevatten, zodat er een [schone ontbrekende gegevens][clean-missing-data] module werd opgenomen om deze te verwerken. De voorbeeld gegevensset bevat ook kolommen die niet nodig zijn voor het trainen van het model. Daarom is een [select columns in dataset][select-columns] -module opgenomen om deze extra kolommen uit te sluiten van de gegevens stroom. Als u weet dat de gegevens die via de webservice worden verzonden, geen ontbrekende waarden bevatten, kunt u de module [clean Missing Data][clean-missing-data] verwijderen. Omdat in de module [select columns in dataset][select-columns] de kolommen met gegevens worden gedefinieerd die het getrainde model verwacht, moet die module echter behouden blijven.

* **Train** : deze modules worden gebruikt om het model te trainen. Wanneer u op **webservice instellen**klikt, worden deze modules vervangen door één module die het model bevat dat u hebt getraind. Deze nieuwe module wordt opgeslagen in de sectie **getrainde modellen** van het module palet.

* **Score** : in dit voor beeld wordt de module [Split data][split] gebruikt om de gegevens stroom te verdelen in test gegevens en trainings gegevens. In het voorspellende experiment worden we niet meer getraind, zodat [gesplitste gegevens][split] kunnen worden verwijderd. Op dezelfde manier worden de module voor het tweede [score model][score-model] en de [model module evalueren][evaluate-model] gebruikt voor het vergelijken van de resultaten van de test gegevens. deze modules zijn dus niet nodig in het voorspellende experiment. De resterende [score model][score-model] module is echter nodig om een score resultaat te retour neren via de webservice.

Hier ziet u hoe het voor beeld eruitziet nadat **u op webservice instellen**hebt geklikt:

![Geconverteerd predictief experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Het werk dat is gedaan door de webservice in te **stellen** kan voldoende zijn om uw experiment voor te bereiden om te worden geïmplementeerd als een webservice. Het kan echter ook voor komen dat u meer specifiek werk wilt doen voor uw experiment.

### <a name="adjust-input-and-output-modules"></a>Invoer-en uitvoer modules aanpassen
In uw trainings experiment hebt u een set trainings gegevens gebruikt en hebt u vervolgens een aantal bewerkingen uitgevoerd om de gegevens op te halen in een formulier waarvan het machine learning algoritme nodig is. Als de gegevens die u verwacht via de webservice, deze verwerking niet nodig hebt, kunt u deze omzeilen: Verbind de uitvoer van de **module Web Service-invoer** naar een andere module in uw experiment. De gegevens van de gebruiker worden nu in het model op deze locatie bezorgd.

Zo wordt bijvoorbeeld standaard de webservice- **invoer** module aan de bovenkant van de gegevens stroom **ingesteld** , zoals wordt weer gegeven in de bovenstaande afbeelding. Maar we kunnen de webservice- **invoer** na de modules voor gegevens verwerking hand matig plaatsen:

![De webservice-invoer verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De invoer gegevens die via de webservice worden opgegeven, worden nu direct door gegeven in de module score model, zonder voor verwerking.

Op dezelfde manier wordt de webservice-uitvoer module aan de onderkant van de gegevens stroom standaard **ingesteld** . In dit voor beeld keert de webservice terug naar de gebruiker de uitvoer van de module [score model][score-model] , die de volledige invoer gegevens vector bevat plus de resultaten van de score.
Als u echter liever iets anders retourneert, kunt u aanvullende modules toevoegen vóór de module **webservice-uitvoer** . 

Als u bijvoorbeeld alleen de Score resultaten en niet de volledige vector van invoer gegevens wilt retour neren, voegt u een [select columns in dataset][select-columns] -module toe om alle kolommen uit te sluiten, met uitzonde ring van de Score resultaten. Verplaats vervolgens de module **webservice-uitvoer** naar de uitvoer van de module [select columns in dataset][select-columns] . Het experiment ziet er als volgt uit:

![De webservice-uitvoer verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Extra gegevens verwerkings modules toevoegen of verwijderen
Als uw experiment meer modules bevat waarvan u weet dat ze niet nodig zijn tijdens de score, kunnen deze worden verwijderd. Omdat we de module **webservice-invoer** bijvoorbeeld hebben verplaatst naar een punt na de modules voor gegevens verwerking, kunnen we de module [clean Missing Data][clean-missing-data] uit het voorspellende experiment verwijderen.

Ons voorspellende experiment ziet er nu als volgt uit:

![Extra module verwijderen](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Optionele webservice-para meters toevoegen
In sommige gevallen wilt u mogelijk de gebruiker van uw webservice toestaan het gedrag van modules te wijzigen wanneer de service wordt geopend. U kunt dit doen met de *para meters* van de webservice.

Een voor beeld hiervan is het instellen van een [import gegevens][import-data] module, zodat de gebruiker van de geïmplementeerde webservice een andere gegevens bron kan opgeven wanneer de webservice wordt geopend. Of configureer een module voor het [exporteren van gegevens][export-data] , zodat er een andere bestemming kan worden opgegeven.

U kunt para meters voor de webservice definiëren en deze koppelen aan een of meer module parameters, en u kunt opgeven of ze vereist of optioneel zijn. De gebruiker van de webservice biedt waarden voor deze para meters wanneer de service wordt geopend en de module acties dienovereenkomstig worden gewijzigd.

Zie [Using Azure machine learning web service para meters][webserviceparameters]voor meer informatie over de para meters van de web-service en hoe u deze kunt gebruiken.

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Het voorspellende experiment implementeren als webservice
Nu het voorspellende experiment voldoende is voor bereid, kunt u dit implementeren als een Azure-webservice. Met de webservice kunnen gebruikers gegevens naar uw model verzenden en worden de voor spellingen van het model geretourneerd.

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
