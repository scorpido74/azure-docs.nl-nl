---
title: Model fase van de levens cyclus van het team data Science-proces
description: De doel stellingen, taken en producten voor de model fase van uw data Science-projecten
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d72d39a2a59e06954c36473083af2d2b4689a7b6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538230"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Model fase van de levens cyclus van het team data Science-proces

In dit artikel vindt u een overzicht van de doelen, taken en producten die zijn gekoppeld aan de modelleer fase van het team data Science process (TDSP). Dit proces biedt een aanbevolen levens cyclus die u kunt gebruiken om uw projecten met een gegevens wetenschap te structureren. De levens cyclus bevat een overzicht van de belangrijkste fasen die door projecten meestal worden uitgevoerd, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Model**
   4. **Implementatie**
   5. **Klant acceptatie**

Hier volgt een visuele representatie van de levens cyclus van TDSP:

![TDSP levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Bedoeling
* Bepaal de optimale gegevens functies voor het machine learning-model.
* Maak een informatieve machine learning-model waarmee het doel nauw keurig wordt voor speld.
* Maak een machine learning-model dat geschikt is voor productie.

## <a name="how-to-do-it"></a>Hoe u dit doet
Er zijn drie hoofd taken die in deze fase worden behandeld:

  * **Functie techniek**: Maak gegevens functies van de onbewerkte gegevens om model trainingen te vergemakkelijken.
  * **Model training**: Zoek het model dat de vraag het meest nauw keurig beantwoordt door de metrische gegevens over het slagen te vergelijken.
  * Bepaal of uw model **geschikt is voor productie.**

### <a name="feature-engineering"></a>Functie-engineering
Functie techniek omvat het opnemen, samen voegen en transformeren van onbewerkte variabelen voor het maken van de functies die worden gebruikt in de analyse. Als u inzicht wilt krijgen in wat een model aangaat, moet u weten hoe de functies zijn gerelateerd aan elkaar en hoe de algoritmen voor machine learning deze functies moeten gebruiken. 

Deze stap vereist een creatieve combi natie van domein expertise en de inzichten die zijn verkregen uit de stap voor het verkennen van gegevens. Functie techniek is een taak verdeling van het vinden en opnemen van informatieve variabelen, maar tegelijk probeert te veel niet-gerelateerde variabelen te vermijden. Informatieve variabelen verbeteren uw resultaat; bij niet-gerelateerde variabelen worden onnodige ruis in het model geïntroduceerd. U moet ook deze functies genereren voor alle nieuwe gegevens die tijdens de score worden verkregen. Als gevolg hiervan kan het genereren van deze functies alleen afhankelijk zijn van gegevens die beschikbaar zijn op het moment van de score. 

Zie [functie Engineering in het data Science proces](create-features.md)voor technische richt lijnen voor functie techniek bij het gebruik van verschillende Azure-gegevens technologieën. 

### <a name="model-training"></a>Modeltraining
Afhankelijk van het type vraag dat u probeert te beantwoorden, zijn er veel modelleer algoritmen beschikbaar. Zie [algoritmen voor Microsoft Azure machine learning kiezen](../studio/algorithm-choice.md)voor hulp bij het kiezen van algoritmen. Hoewel in dit artikel Azure Machine Learning wordt gebruikt, is de richt lijn die het biedt, nuttig voor machine learning-projecten. 

De training voor het model proces bestaat uit de volgende stappen: 

   * **Splits de invoer gegevens** wille keurig splitsen voor model lering in een set met trainings gegevens en een test gegevensverzameling.
   * **Bouw de modellen** met behulp van de gegevensset voor training.
   * **Evalueer** de training en de test gegevensset. Gebruik een reeks concurrerende algoritmen voor machine learning samen met de verschillende bijbehorende afstemmings parameters (ook wel een *parameter sweep*genoemd) die zijn gericht op het beantwoorden van de vraag naar belang stelling met de huidige gegevens.
   * **Bepaal de ' beste ' oplossing** om de vraag te beantwoorden door de metrische gegevens over het succes te vergelijken tussen alternatieve methoden.

> [!NOTE]
> **Lekkage voor komen**: u kunt lekkage van gegevens veroorzaken als u gegevens opneemt van buiten de set met trainingen die een model of machine learning-algoritme mogelijk maakt om onrealistisch goede voor spellingen te maken. Lekkage is een gemeen schappelijke reden voor het verkrijgen van zenuwen bij gegevens wetenschappers wanneer ze voorspellende resultaten ontvangen die te mooi lijken om waar te zijn. Deze afhankelijkheden kunnen moeilijk te detecteren zijn. Om te voor komen dat lekkage vaak een iteratie vereist tussen het bouwen van een analyse gegevensset, het maken van een model en het evalueren van de nauw keurigheid van de resultaten. 
> 
> 

We bieden een [geautomatiseerd hulp programma voor model lering en rapportage](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) met TDSP dat kan worden uitgevoerd via meerdere algoritmen en parameter sweeps om een basis lijn model te maken. Er wordt ook een basislijn model rapport gemaakt met een overzicht van de prestaties van elk model en de combi natie van para meters, inclusief de prioriteit van de variabele. Dit proces wordt ook herhaald, aangezien het verdere functie techniek kan hebben. 

## <a name="artifacts"></a>Artifacts
De artefacten die in deze fase worden geproduceerd, zijn onder andere:

   * [Functie sets](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): de functies die voor het model leren zijn ontwikkeld, worden beschreven in de sectie **functie sets** van het rapport **gegevens definitie** . Het bevat aanwijzers naar de code voor het genereren van de functies en een beschrijving van de manier waarop de functie is gegenereerd.
   * [Model rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): voor elk model dat wordt geprobeerd, een standaard rapport op basis van een sjabloon dat details bevat over elk experiment, wordt er geproduceerd.
   * **Beslissing van het controle punt**: Bepaal of het model voldoende presteert om het te implementeren in een productie systeem. Hier volgen enkele belang rijke vragen die u kunt stellen:
     * Beantwoordt het model de vraag met voldoende betrouw baarheid op basis van de test gegevens? 
     * Moet u andere benaderingen proberen? Kunt u extra gegevens verzamelen, meer functies uitvoeren of experimenteren met andere algoritmen?

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levens cyclus van de TDSP:

   1. [Zakelijke inzichten](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Model](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Klant acceptatie](lifecycle-acceptance.md)

We bieden volledige end-to-end-instructies die alle stappen in het proces voor specifieke scenario's demonstreren. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. In de scenario's wordt uitgelegd hoe u Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn kunt combi neren om een intelligente toepassing te maken. 

Zie [de TDSP gebruiken met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio. 
