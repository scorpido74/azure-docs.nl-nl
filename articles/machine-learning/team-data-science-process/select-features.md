---
title: Functieselectie in het Team Data Science-proces
description: Legt het doel van functieselectie uit en geeft voorbeelden van hun rol in het proces voor gegevensverbetering van machine learning.
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
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716677"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Functies selecteren in Team Data Science Process (TDSP)
In dit artikel worden de doeleinden van functieselectie uitgelegd en worden voorbeelden gegeven van zijn rol in het proces voor gegevensverbetering van machine learning. Deze voorbeelden zijn afkomstig van Azure Machine Learning Studio.

De engineering en selectie van functies is een onderdeel van het Team Data Science Process (TDSP) beschreven in het artikel [Wat is het Team Data Science Process?](overview.md). Feature engineering en selectie zijn onderdelen van de **ontwikkelfuncties** stap van de TDSP.

* **feature engineering**: Dit proces probeert extra relevante functies te maken van de bestaande ruwe functies in de gegevens, en om de voorspellende kracht van het leeralgoritme te vergroten.
* **selectie van functies:** Dit proces selecteert de belangrijkste subset van oorspronkelijke gegevensfuncties in een poging om de dimensionaliteit van het trainingsprobleem te verminderen.

Normaal gesproken wordt **feature engineering** eerst toegepast om extra functies te genereren, en vervolgens wordt de **functieselectiestap** uitgevoerd om irrelevante, redundante of sterk gecorreleerde functies te elimineren.

## <a name="filter-features-from-your-data---feature-selection"></a>Functies filteren op uw gegevens - selectie van functies
Functieselectie kan worden gebruikt voor classificatie- of regressietaken. Het doel is om een subset van de functies uit de oorspronkelijke gegevensset te selecteren die de afmetingen ervan verminderen door een minimale set functies te gebruiken om de maximale hoeveelheid variantie in de gegevens weer te geven. Deze subset van functies wordt gebruikt om het model te trainen. Functieselectie dient twee belangrijke doeleinden.

* Ten eerste verhoogt functieselectie vaak de nauwkeurigheid van de classificatie door irrelevante, redundante of sterk gecorreleerde functies te elimineren.
* Ten tweede vermindert het aantal functies, waardoor het modeltrainingsproces efficiënter wordt. Efficiëntie is belangrijk voor leerlingen die duur zijn om te trainen, zoals ondersteuningsvectormachines.

Hoewel functieselectie het aantal functies in de gegevensset die wordt gebruikt om het model te trainen, te verminderen, wordt het niet aangeduid met de term "dimensionaliteitsreductie". Functieselectiemethoden extraheren een subset van oorspronkelijke functies in de gegevens zonder deze te wijzigen.  Dimensionaliteitsreductiemethoden maken gebruik van ontworpen functies die de oorspronkelijke functies kunnen transformeren en zo kunnen wijzigen. Voorbeelden van methoden voor dimensionaliteitsreductie zijn Principal Component Analysis, canonieke correlatieanalyse en Singular Value Decomposition.

Een van de meest toegepaste categorie van functieselectiemethoden in een gecontroleerde context wordt onder andere "filtergebaseerde functieselectie" genoemd. Door de correlatie tussen elke functie en het doelkenmerk te evalueren, passen deze methoden een statistische meting toe om een score aan elke functie toe te wijzen. De functies worden vervolgens gerangschikt op basis van de score, die kan worden gebruikt om de drempel voor het behouden of elimineren van een specifieke functie in te stellen. Voorbeelden van de statistische metingen die in deze methoden worden gebruikt, zijn person-correlatie, wederzijdse informatie en de Chi-kwadraattest.

In Azure Machine Learning Studio zijn er modules beschikbaar voor functieselectie. Zoals blijkt uit de volgende figuur, deze modules omvatten [Filter-Based Feature Selection][filter-based-feature-selection] en [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis].

![Modules voor functieselectie](./media/select-features/feature-Selection.png)

Denk bijvoorbeeld aan het gebruik van de [filtergebaseerde functieselectiemodule.][filter-based-feature-selection] Voor het gemak, blijven gebruiken van de tekst mining voorbeeld. Stel dat u een regressiemodel wilt bouwen nadat een set van 256 functies is gemaakt via de [module Feature Hashing][feature-hashing] en dat de responsvariabele de "Col1" is die boekbeoordelingen bevat variërend van 1 tot 5. Door "Feature scoring method" in te stellen als "Pearson Correlatie", de "Doelkolom" als "Col1" en het "Aantal gewenste functies" op 50. Vervolgens produceert de module [Filtergebaseerde functieselectie][filter-based-feature-selection] een gegevensset met 50 functies samen met het doelkenmerk "Col1". De volgende afbeelding toont de stroom van dit experiment en de invoerparameters:

![Eigenschappen van filtergebaseerde functieselectiemodule](./media/select-features/feature-Selection1.png)

De volgende afbeelding toont de resulterende gegevenssets:

![Resulterende gegevensset voor module Filtergebaseerde functieselectie](./media/select-features/feature-Selection2.png)

Elke functie wordt beoordeeld op basis van de Pearson Correlatie tussen zichzelf en het doelkenmerk "Col1". De functies met topscores worden bewaard.

De overeenkomstige scores van de geselecteerde functies worden weergegeven in de volgende afbeelding:

![Scores voor module Filtergebaseerde functieselectie](./media/select-features/feature-Selection3.png)

Door deze [filtergebaseerde functieselectiemodule][filter-based-feature-selection] toe te passen, worden 50 van de 256 functies geselecteerd omdat ze de meest gecorreleerde functies hebben met de doelvariabele "Col1", gebaseerd op de scoringsmethode "Pearson-correlatie".

## <a name="conclusion"></a>Conclusie
Feature engineering en functie selectie zijn twee algemeen ontworpen en geselecteerde functies verhogen de efficiëntie van het trainingsproces dat probeert om de belangrijkste informatie in de gegevens te extraheren. Ze verbeteren ook de kracht van deze modellen om de invoergegevens nauwkeurig te classificeren en om de resultaten van belang robuuster te voorspellen. Feature engineering en selectie kunnen ook combineren om het leren meer computationeel te maken. Het doet dit door het verbeteren en vervolgens verminderen van het aantal functies die nodig zijn om te kalibreren of trainen van een model. Wiskundig gezien zijn de functies die zijn geselecteerd om het model te trainen een minimale set onafhankelijke variabelen die de patronen in de gegevens verklaren en vervolgens resultaten met succes voorspellen.

Het is niet altijd noodzakelijk om functie-engineering of functie selectie uit te voeren. Of het nodig is of niet, hangt af van de verzamelde gegevens, het geselecteerde algoritme en het doel van het experiment.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

