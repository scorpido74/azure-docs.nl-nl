---
title: Model voorbereiden op implementatie
titleSuffix: ML Studio (classic) - Azure
description: Hoe u uw getrainde model voorbereidt op implementatie als webservice door uw Machine Learning Studio (klassieke) trainingsexperiment om te zetten in een voorspellend experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204508"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Uw model voorbereiden op implementatie in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassiek) biedt u de tools die u nodig hebt om een voorspellend analysemodel te ontwikkelen en vervolgens te operationaliseren door het te implementeren als een Azure-webservice.

Hiervoor gebruik je Studio (klassiek) om een experiment te maken - een *trainingsexperiment* genoemd - waarbij je je model traint, scoort en bewerkt. Zodra u tevreden bent, krijgt u uw model klaar om te implementeren door uw trainingsexperiment om te zetten in een *voorspellend experiment* dat is geconfigureerd om gebruikersgegevens te scoren.

U een voorbeeld van dit proces zien in [Zelfstudie 1: Kredietrisico voorspellen](tutorial-part1-credit-risk.md).

Dit artikel neemt een diepe duik in de details van hoe een trainingsexperiment wordt omgezet in een voorspellend experiment en hoe dat voorspellende experiment wordt geïmplementeerd. Door deze details te begrijpen, u leren hoe u uw geïmplementeerde model configureren om het effectiever te maken.



## <a name="overview"></a>Overzicht 

Het proces van het converteren van een trainingsexperiment naar een voorspellend experiment omvat drie stappen:

1. Vervang de machine learning algoritme modules door uw getrainde model.
2. Trim het experiment alleen door die modules die nodig zijn om te scoren. Een trainingsexperiment omvat een aantal modules die nodig zijn voor de opleiding, maar die niet nodig zijn zodra het model is opgeleid.
3. Definieer hoe uw model gegevens van de webservicegebruiker accepteert en welke gegevens worden geretourneerd.

> [!TIP]
> In uw trainingsexperiment houdt u zich bezig met het trainen en scoren van uw model met behulp van uw eigen gegevens. Maar eenmaal geïmplementeerd, zullen gebruikers nieuwe gegevens naar uw model sturen en het zal voorspellingsresultaten retourneren. Dus, als u uw trainingsexperiment omte zetten in een voorspellend experiment om het klaar te maken voor implementatie, houd er dan rekening mee hoe het model door anderen zal worden gebruikt.
> 
> 

## <a name="set-up-web-service-button"></a>Knop Webservice instellen
Nadat u het experiment hebt uitgevoerd (klik op **UITVOEREN** onder aan het experimentcanvas) klikt u op de knop **Webservice instellen** (selecteer de optie **Voorspellende webservice).** **Webservice instellen** voert de drie stappen uit om uw trainingsexperiment om te zetten in een voorspellend experiment:

1. Het slaat uw getrainde model op in het gedeelte **Getrainde modellen** van het modulepalet (links van het experimentcanvas). Het vervangt dan de machine learning algoritme en [Train Model][train-model] modules met de opgeslagen getrainde model.
2. Het analyseert uw experiment en verwijdert modules die duidelijk alleen voor training zijn gebruikt en niet meer nodig zijn.
3. Hiermee worden invoer- en _uitvoermodules van_ _webservices_ ingevoegd in standaardlocaties in uw experiment (deze modules accepteren en retourneren gebruikersgegevens).

Het volgende experiment leidt bijvoorbeeld een tweeklassenmodel met een gebooste beslissingsstructuur op met behulp van gegevens over de telling van monsters:

![Trainingsexperiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

De modules in dit experiment voeren in principe vier verschillende functies uit:

![Modulefuncties](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Wanneer u dit trainingsexperiment omzet in een voorspellend experiment, zijn sommige van deze modules niet meer nodig of dienen ze nu een ander doel:

* **Gegevens** - De gegevens in deze voorbeeldgegevensset worden niet gebruikt tijdens het scoren - de gebruiker van de webservice verstrekt de te scoren gegevens. De metagegevens uit deze gegevensset, zoals gegevenstypen, worden echter gebruikt door het getrainde model. U moet de gegevensset dus in het voorspellende experiment bewaren, zodat deze metagegevens kan leveren.

* **Prep** - Afhankelijk van de gebruikersgegevens die worden ingediend om te scoren, kunnen deze modules al dan niet nodig zijn om de binnenkomende gegevens te verwerken. De knop **Webservice instellen** raakt deze niet aan - u moet beslissen hoe u ze wilt hanteren.
  
    In dit voorbeeld kan de voorbeeldgegevensset bijvoorbeeld ontbrekende waarden hebben, dus er is een clean [missing data-module][clean-missing-data] opgenomen om ze te behandelen. De voorbeeldgegevensset bevat ook kolommen die niet nodig zijn om het model te trainen. Er is dus een [module Kolommen selecteren in gegevensset][select-columns] opgenomen om die extra kolommen uit te sluiten van de gegevensstroom. Als u weet dat de gegevens die worden ingediend om te scoren via de webservice geen ontbrekende waarden hebben, u de module [Ontbrekende gegevens opschonen.][clean-missing-data] Aangezien de module [Kolommen selecteren in gegevensset][select-columns] echter helpt bij het definiëren van de kolommen met gegevens die het getrainde model verwacht, moet die module blijven.

* **Trein** - Deze modules worden gebruikt om het model te trainen. Wanneer u op **Webservice instellen**klikt, worden deze modules vervangen door één module met het model dat u hebt getraind. Deze nieuwe module wordt opgeslagen in het gedeelte **Getrainde modellen** van het modulepalet.

* **Score** - In dit voorbeeld wordt de module [Gesplitste gegevens][split] gebruikt om de gegevensstroom te verdelen in testgegevens en trainingsgegevens. In het voorspellende experiment trainen we niet meer, zodat [Split Data][split] kan worden verwijderd. Op dezelfde manier worden de tweede [scoremodelmodule][score-model] en de module [Model evalueren][evaluate-model] gebruikt om de resultaten van de testgegevens te vergelijken, zodat deze modules niet nodig zijn in het voorspellende experiment. De resterende [scoremodelmodule][score-model] is echter nodig om een scoreresultaat via de webservice terug te geven.

Zo ziet ons voorbeeld eruit nadat we op **WebService instellen**hebben geklikt:

![Geconverteerd voorspellend experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Het werk dat door **Set Up Web Service** wordt uitgevoerd, kan voldoende zijn om uw experiment voor te bereiden om als webservice te worden geïmplementeerd. U echter wat extra werk doen dat specifiek is voor uw experiment.

### <a name="adjust-input-and-output-modules"></a>Invoer- en uitvoermodules aanpassen
In uw trainingsexperiment hebt u een set trainingsgegevens gebruikt en vervolgens wat verwerking en verwerking gedaan om de gegevens te krijgen in een vorm die het machine learning-algoritme nodig had. Als de gegevens die u verwacht te ontvangen via de webservice deze verwerking niet nodig hebben, u deze omzeilen: sluit de uitvoer van de invoermodule van de **webservice** aan een andere module in uw experiment. De gegevens van de gebruiker komen nu in het model op deze locatie.

Als u bijvoorbeeld standaard **Webservice instellen instellen,** wordt de **invoermodule webservice** boven aan uw gegevensstroom geplaatst, zoals in de bovenstaande afbeelding wordt weergegeven. Maar we kunnen de **webserviceinvoer** handmatig voorbij de gegevensverwerkingsmodules plaatsen:

![De webservice-invoer verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De invoergegevens die via de webservice worden verstrekt, worden nu rechtstreeks naar de module Scoremodel overgegaan zonder enige voorbewerking.

Op dezelfde manier plaatst **Webservice instellen** standaard de webserviceuitvoermodule onder aan uw gegevensstroom. In dit voorbeeld geeft de webservice de uitvoer van de module [Scoremodel][score-model] aan de gebruiker terug, inclusief de volledige invoergegevensvector plus de scoreresultaten.
Als u echter liever iets anders wilt retourneren, u extra modules toevoegen voordat de **webserviceuitvoermodule wordt** uitgevoerd. 

Als u bijvoorbeeld alleen de scoreresultaten wilt retourneren en niet de hele vector van invoergegevens, voegt u een module [Kolommen selecteren in gegevensset][select-columns] toe om alle kolommen uit te sluiten, behalve de scoreresultaten. Verplaats de module **Webservice-uitvoer** vervolgens naar de uitvoer van de module Kolommen selecteren in de module [Gegevensset.][select-columns] Het experiment ziet er als volgt uit:

![De uitvoer van de webservice verplaatsen](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Extra gegevensverwerkingsmodules toevoegen of verwijderen
Als er meer modules in uw experiment zijn waarvan u weet dat ze niet nodig zijn tijdens het scoren, kunnen deze worden verwijderd. Omdat we bijvoorbeeld de **invoermodule voor webservice** hebben verplaatst naar een punt na de gegevensverwerkingsmodules, kunnen we de module [Schone ontbrekende gegevens][clean-missing-data] uit het voorspellende experiment verwijderen.

Ons voorspellend experiment ziet er nu als volgt uit:

![Extra module verwijderen](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Optionele webserviceparameters toevoegen
In sommige gevallen u de gebruiker van uw webservice toestaan het gedrag van modules te wijzigen wanneer de service wordt geopend. *Met WebService Parameters* u dit doen.

Een veelvoorkomend voorbeeld is het instellen van een [module Gegevens importeren,][import-data] zodat de gebruiker van de geïmplementeerde webservice een andere gegevensbron kan opgeven wanneer de webservice wordt geopend. Of het configureren van een [exportgegevensmodule][export-data] zodat een andere bestemming kan worden opgegeven.

U Webserviceparameters definiëren en deze koppelen aan een of meer moduleparameters en u opgeven of deze vereist of optioneel zijn. De gebruiker van de webservice geeft waarden voor deze parameters wanneer de service wordt geopend en de moduleacties dienovereenkomstig worden gewijzigd.

Zie [Azure Machine Learning Web Service Parameters gebruiken][webserviceparameters]voor meer informatie over wat webserviceparameters zijn en hoe u deze gebruiken.

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Het voorspellende experiment implementeren als webservice
Nu het voorspellende experiment voldoende is voorbereid, u het implementeren als een Azure-webservice. Met behulp van de webservice kunnen gebruikers gegevens naar uw model verzenden en het model geeft zijn voorspellingen terug.

Zie [Een Webservice azure Machine Learning implementeren][deploy] voor meer informatie over het volledige implementatieproces

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
