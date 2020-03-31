---
title: 'Kolommen transformeren selecteren: naslagverwijzing naar de module'
titleSuffix: Azure Machine Learning
description: Lees hoe u de module Kolommentransformeren selecteren in Azure Machine Learning gebruikt om een transformatie te maken die dezelfde subset van kolommen selecteert als in de opgegeven gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455907"
---
# <a name="select-columns-transform"></a>Kolomtransformatie selecteren

In dit artikel wordt beschreven hoe u de module Kolommen transformeren selecteren gebruiken in azure machine learning-ontwerper (voorbeeld). Het doel van de module Transformatie van kolommen selecteren is ervoor te zorgen dat een voorspelbare, consistente set kolommen wordt gebruikt in downstream machine learning-bewerkingen.

Deze module is handig voor taken zoals scoren, waarvoor specifieke kolommen nodig zijn. Wijzigingen in de beschikbare kolommen kunnen de pijplijn verbreken of de resultaten wijzigen.

U gebruikt Kolommen transformeren selecteren om een reeks kolommen te maken en op te slaan. Gebruik vervolgens de module [Transformatie toepassen](apply-transformation.md) om deze selecties toe te passen op nieuwe gegevens.

## <a name="how-to-use-select-columns-transform"></a>Kolommen transformeren selecteren gebruiken

In dit scenario wordt ervan uitgegaan dat u functieselectie wilt gebruiken om een dynamische set kolommen te genereren die worden gebruikt voor het trainen van een model. Als u ervoor wilt zorgen dat kolomselecties hetzelfde zijn voor het scoringsproces, gebruikt u de module Kolommen transformeren selecteren om de kolomselecties vast te leggen en deze elders in de pijplijn toe te passen.

1. Voeg een invoergegevensset toe aan uw pijplijn in de ontwerper.

2. Een instantie van [filtergebaseerde functieselectie](filter-based-feature-selection.md)toevoegen .

3. Sluit de modules aan en configureer de functieselectiemodule om automatisch een aantal beste functies in de invoergegevensset te vinden.

4. Voeg een exemplaar van [Train Model toe](train-model.md) en gebruik de uitvoer van [Filtergebaseerde functieselectie](filter-based-feature-selection.md) als invoer voor training.

    > [!IMPORTANT]
    > Omdat functiebelang is gebaseerd op de waarden in de kolom, u niet van tevoren weten welke kolommen mogelijk beschikbaar zijn voor invoer naar [Train Model.](train-model.md)  
5. Voeg een instantie van de module Kolommen transformeren selecteren toe. 

    Met deze stap wordt een kolomselectie gegenereerd als een transformatie die kan worden opgeslagen of toegepast op andere gegevenssets. Deze stap zorgt ervoor dat de kolommen die in functieselectie zijn geïdentificeerd, worden opgeslagen voor het hergebruik van andere modules.

6. Voeg de module [Scoremodel](score-model.md) toe. 

   *Sluit de invoergegevensset niet aan.* Voeg in plaats daarvan de module [Transformatie toepassen](apply-transformation.md) toe en sluit de uitvoer van de functieselectietransformatie aan.

   > [!IMPORTANT]
   > U niet verwachten dat [u filtergebaseerde functieselectie](filter-based-feature-selection.md) toepast op de gegevensset voor het scoren en dezelfde resultaten krijgt. Omdat functieselectie is gebaseerd op waarden, kan deze een andere set kolommen kiezen, waardoor de scoringsbewerking mislukt.
7. Verzend de pijplijn.

Dit proces van het opslaan en vervolgens toepassen van een kolomselectie zorgt ervoor dat hetzelfde gegevensschema beschikbaar is voor training en scoren.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
