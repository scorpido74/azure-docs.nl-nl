---
title: Modellering van fase van de levenscyclus van het Team Data Science Process
description: De doelen, taken en producten voor de fase van het modelleren van uw data-science-projecten
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
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720466"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modellering van fase van de levenscyclus van het Team Data Science Process

In dit artikel bevat een overzicht van de doelen, taken en producten die zijn gekoppeld aan de model-fase van het Team Data Science Process (TDSP). Deze procedure biedt de levensduur van een aanbevolen die u gebruiken kunt voor het structureren van uw data-science-projecten. De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Model**
   4. **Implementatie**
   5. **Klant acceptatie**

Hier volgt een visuele representatie van de TDSP-levenscyclus:

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Bepaalt de optimale data-functies voor de machine learning-model.
* Maak een informatieve machine learning-model waarmee het doel nauwkeurigst worden voorspeld.
* Maak een machine learning-model dat geschikt is voor productie.

## <a name="how-to-do-it"></a>Hoe voer ik
Er zijn drie belangrijkste taken in deze fase behandeld:

  * **Functie techniek**: Maak gegevens functies van de onbewerkte gegevens om model trainingen te vergemakkelijken.
  * **Model training**: Zoek het model dat de vraag het meest nauw keurig beantwoordt door de metrische gegevens over het slagen te vergelijken.
  * Bepaal of uw model **geschikt is voor productie.**

### <a name="feature-engineering"></a>Functie-engineering
Feature-engineering omvat de opname, aggregatie en transformatie van onbewerkte variabelen te maken van de functies die in de analyse worden gebruikt. Als u inzicht in wat vraagt om een model wilt, moet u begrijpen hoe de functies in verband met elkaar en hoe de machine learning-algoritmes zijn om deze functies te gebruiken. 

Deze stap moet een creatieve combinatie van expertise in verschillende domeinen en de inzichten die zijn verkregen van de gegevens verkennen stap. Feature-engineering is een evenwichtsoefening te vinden en informatieve variabelen inclusief, maar op hetzelfde moment probeert om te voorkomen dat te veel niet-gerelateerde variabelen. Informatieve variabelen verbetering van uw resultaat; niet-gerelateerde variabelen introduceren onnodige ' ruis ' in het model. Ook moet u deze functies voor alle nieuwe gegevens die zijn verkregen tijdens het scoring-genereren. Als gevolg hiervan kan de generatie van deze functies alleen afhankelijk van gegevens die beschikbaar is op het moment van scores. 

Zie [functie Engineering in het data Science proces](create-features.md)voor technische richt lijnen voor functie techniek bij het gebruik van verschillende Azure-gegevens technologieën. 

### <a name="model-training"></a>Modeltraining
Afhankelijk van het type vraag die u probeert te beantwoorden, zijn er veel modellen algoritmen beschikbaar. Zie [algoritmen voor Microsoft Azure machine learning kiezen](../studio/algorithm-choice.md)voor hulp bij het kiezen van algoritmen. Hoewel dit artikel wordt gebruikgemaakt van Azure Machine Learning, is de richtlijnen biedt handig voor de machine learning-projecten. 

Het proces voor het trainen van het model bevat de volgende stappen uit: 

   * **Splits de invoer gegevens** wille keurig splitsen voor model lering in een set met trainings gegevens en een test gegevensverzameling.
   * **Bouw de modellen** met behulp van de gegevensset voor training.
   * **Evalueer** de training en de test gegevensset. Gebruik een reeks concurrerende algoritmen voor machine learning samen met de verschillende bijbehorende afstemmings parameters (ook wel een *parameter sweep*genoemd) die zijn gericht op het beantwoorden van de vraag naar belang stelling met de huidige gegevens.
   * **Bepaal de ' beste ' oplossing** om de vraag te beantwoorden door de metrische gegevens over het succes te vergelijken tussen alternatieve methoden.

> [!NOTE]
> **Lekkage voor komen**: u kunt lekkage van gegevens veroorzaken als u gegevens opneemt van buiten de set met trainingen die een model of machine learning-algoritme mogelijk maakt om onrealistisch goede voor spellingen te maken. Lekken van gegevens is een veelvoorkomende reden waarom data scientists zenuwstelsel ontvangen wanneer ze voorspellende resultaten die ontvangt lijken te mooi om waar te zijn. Deze afhankelijkheden zijn moeilijk te detecteren. Om u te vaak te voorkomen dat lekken vereist digitaler tussen het bouwen van een gegevensset voor analyse, het maken van een model en de nauwkeurigheid van de resultaten evalueren. 
> 
> 

We bieden een [geautomatiseerd hulp programma voor model lering en rapportage](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) met TDSP dat kan worden uitgevoerd via meerdere algoritmen en parameter sweeps om een basis lijn model te maken. Het genereert ook een basislijn voor het modelleren van rapport met een overzicht van de prestaties van elke combinatie model en parameter, met inbegrip van de variabele belang is. Dit proces is ook iteratieve als het verdere feature-engineering kunt uitbreiden. 

## <a name="artifacts"></a>Artefacten
De artefacten die in deze fase worden geproduceerd, zijn onder andere:

   * [Functie sets](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): de functies die voor het model leren zijn ontwikkeld, worden beschreven in de sectie **functie sets** van het rapport **gegevens definitie** . Het bevat verwijzingen naar de code voor het genereren van de functies en een beschrijving van hoe de functie is gegenereerd.
   * [Model rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): voor elk model dat wordt geprobeerd, een standaard rapport op basis van een sjabloon dat details bevat over elk experiment, wordt er geproduceerd.
   * **Beslissing van het controle punt**: evalueren of het model voldoende voor productie wordt uitgevoerd. Er zijn een aantal belangrijke vragen stellen:
     * Het model de vraag worden beantwoord met voldoende vertrouwen gegeven van de testgegevens? 
     * Moet u een alternatieve methoden proberen? Moet u aanvullende gegevens verzamelen, doen meer feature-engineering of experimenteren met andere algoritmen?

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Zakelijke inzichten](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Model](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Klant acceptatie](lifecycle-acceptance.md)

We bieden volledige end-to-end-scenario's die laten zien van alle de stappen in het proces voor het specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. De scenario's laten zien hoe u cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 

Zie [de TDSP gebruiken met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio. 
