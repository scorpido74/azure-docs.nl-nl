---
title: Modelleringsfase van de levenscyclus van het Team Data Science Process
description: De doelen, taken en deliverables voor de modelleringsfase van uw data-science projecten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720466"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelleringsfase van de levenscyclus van het Team Data Science Process

In dit artikel worden de doelen, taken en deliverables beschreven die zijn gekoppeld aan de modelleringsfase van het Team Data Science Process (TDSP). Dit proces biedt een aanbevolen levenscyclus die u gebruiken om uw data-science-projecten te structureren. De levenscyclus schetst de belangrijkste fasen die projecten doorgaans uitvoeren, vaak iteratief:

   1. **Inzicht in het bedrijf**
   2. **Gegevens verzamelen en begrijpen**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier is een visuele weergave van de TDSP-levenscyclus:

![TDSP-levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Bepaal de optimale gegevensfuncties voor het machine learning-model.
* Maak een informatief machine learning-model dat het doel het nauwkeurigst voorspelt.
* Maak een machine learning-model dat geschikt is voor productie.

## <a name="how-to-do-it"></a>Hoe doe je dat?
In deze fase worden drie hoofdtaken behandeld:

  * **Feature engineering**: Maak gegevensfuncties uit de ruwe gegevens om modeltraining te vergemakkelijken.
  * **Modeltraining:** Vind het model dat de vraag het meest nauwkeurig beantwoordt door hun successtatistieken te vergelijken.
  * Bepaal of uw model geschikt is **voor productie.**

### <a name="feature-engineering"></a>Functie-engineering
Feature engineering omvat de opname, aggregatie en transformatie van ruwe variabelen om de functies te maken die in de analyse worden gebruikt. Als u inzicht wilt in wat een model drijft, moet u begrijpen hoe de functies zich tot elkaar verhouden en hoe de machine learning-algoritmen deze functies moeten gebruiken. 

Deze stap vereist een creatieve combinatie van domeinexpertise en de inzichten verkregen uit de data exploration stap. Feature engineering is een evenwichtsoefening van het vinden en opnemen van informatieve variabelen, maar tegelijkertijd proberen om te veel niet-gerelateerde variabelen te vermijden. Informatieve variabelen verbeteren uw resultaat; niet-gerelateerde variabelen introduceren onnodige ruis in het model. U moet deze functies ook genereren voor nieuwe gegevens die tijdens het scoren zijn verkregen. Als gevolg hiervan kan het genereren van deze functies alleen afhangen van gegevens die beschikbaar zijn op het moment van scoren. 

Zie [Functieengineering in het data science-proces voor](create-features.md)technische richtlijnen voor functieengineering wanneer u gebruik maakt van verschillende Azure-gegevenstechnologieën. 

### <a name="model-training"></a>Modeltraining
Afhankelijk van het type vraag dat u probeert te beantwoorden, zijn er veel modelleringsalgoritmen beschikbaar. Zie Hoe u [algoritmen voor Microsoft Azure Machine Learning](../studio/algorithm-choice.md)kiest voor richtlijnen voor het kiezen van de algoritmen. Hoewel dit artikel Azure Machine Learning gebruikt, is de richtlijnen die het biedt nuttig voor alle machine learning-projecten. 

Het proces voor modeltraining omvat de volgende stappen: 

   * **Splits de invoergegevens** willekeurig op voor modellering in een trainingsgegevensset en een testgegevensset.
   * **Bouw de modellen** met behulp van de trainingsgegevensset.
   * **Evalueer** de training en de testgegevensset. Gebruik een reeks concurrerende machine learning-algoritmen, samen met de verschillende bijbehorende tuningparameters (bekend als een *parametersweep)* die zijn gericht op het beantwoorden van de vraag van belang met de huidige gegevens.
   * **Bepaal de "beste" oplossing** om de vraag te beantwoorden door de successtatistieken tussen alternatieve methoden te vergelijken.

> [!NOTE]
> **Lekkage voorkomen**: U gegevenslekken veroorzaken als u gegevens van buiten de trainingsgegevensset opneemt waarmee een model of machine learning-algoritme onrealistisch goede voorspellingen kan doen. Lekkage is een veel voorkomende reden waarom data wetenschappers nerveus als ze voorspellende resultaten die lijken te mooi om waar te zijn. Deze afhankelijkheden kunnen moeilijk te detecteren zijn. Om lekkage te voorkomen moet vaak worden geëvalueerd tussen het bouwen van een analysegegevensset, het maken van een model en het evalueren van de nauwkeurigheid van de resultaten. 
> 
> 

We bieden een [geautomatiseerde modellerings- en rapportagetool](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) met TDSP die meerdere algoritmen en parametersweeps kan uitvoeren om een basislijnmodel te produceren. Het produceert ook een basislijn modellering rapport dat de prestaties van elk model en parameter combinatie inclusief variabel belang samenvat. Dit proces is ook iteratief omdat het verdere functie-engineering kan aansturen. 

## <a name="artifacts"></a>Artefacten
De artefacten geproduceerd in deze fase zijn:

   * [Functiesets:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md)de functies die zijn ontwikkeld voor de modellering worden beschreven in het gedeelte **Functiesets** van het rapport **Gegevensdefinitie.** Het bevat verwijzingen naar de code om de functies te genereren en een beschrijving van hoe de functie is gegenereerd.
   * [Modelrapport:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)Voor elk model dat wordt geprobeerd, wordt een standaardrapport op basis van sjablonen geproduceerd met details over elk experiment.
   * **Controlepuntbesluit**: Beoordelen of het model voldoende presteert voor productie. Enkele belangrijke vragen om te stellen zijn:
     * Beantwoordt het model de vraag met voldoende vertrouwen gezien de testgegevens? 
     * Moet je proberen een alternatieve aanpak? Moet u extra gegevens verzamelen, meer functieengineering doen of experimenteren met andere algoritmen?

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levenscyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verzamelen en begrijpen](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige end-to-end walkthroughs die alle stappen in het proces voor specifieke scenario's demonstreren. Het artikel [Voorbeeld walkthroughs](walkthroughs.md) bevat een lijst met de scenario's met koppelingen en miniatuurbeschrijvingen. De walkthroughs illustreren hoe u cloud-, on-premises tools en services combineren in een workflow of pijplijn om een intelligente toepassing te maken. 

Zie [De TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)gebruiken met Azure Machine Learning voor voorbeelden van het uitvoeren van stappen in TDSP's die Azure Machine Learning Studio gebruiken. 
