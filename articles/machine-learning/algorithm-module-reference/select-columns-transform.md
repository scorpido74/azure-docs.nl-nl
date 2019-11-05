---
title: 'Kolom transformatie selecteren: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over hoe u de transformatie module select columns in Azure Machine Learning service kunt gebruiken om een trans formatie te maken waarmee dezelfde subset van kolommen wordt geselecteerd als in de opgegeven gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: def15b3c28b2278f884b36acc39fa75982fcf8c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516199"
---
# <a name="select-columns-transform"></a>Kolom transformatie selecteren

In dit artikel wordt beschreven hoe u de module **Column Transform Select** in azure machine learning Designer (preview) gebruikt. Het doel van de **transformatie module select columns** is ervoor te zorgen dat een voorspel bare, consistente set kolommen altijd wordt gebruikt in downstream-machine learning bewerkingen.

Deze module is handig voor taken zoals scores, waarvoor specifieke kolommen zijn vereist. Wijzigingen in de beschik bare kolommen kunnen de pijp lijn verstoren of de resultaten wijzigen.

U gebruikt de **trans formatie select columns** om een set kolommen te maken en op te slaan. Vervolgens gebruikt u de module [trans formatie Toep](apply-transformation.md) assen om deze selecties toe te passen op nieuwe gegevens.

## <a name="how-to-use-select-columns-transform"></a>De optie kolommen transformeren gebruiken

In dit scenario wordt ervan uitgegaan dat u de functie selectie wilt gebruiken om een dynamische set kolommen te genereren die wordt gebruikt voor het trainen van een model. Om ervoor te zorgen dat de kolom selecties hetzelfde zijn voor het Score proces, gebruikt u de **transformatie module select columns** om de kolom selecties vast te leggen en ze elders in de pijp lijn toe te passen.

1. Voeg een invoer-gegevensset toe aan uw pijp lijn in de ontwerp functie.

2. Een exemplaar van de [functie selectie op basis van filters](filter-based-feature-selection.md)toevoegen.

3. Verbind de modules en configureer de module functie selectie om automatisch een aantal beste functies in de invoer gegevensset te vinden.

4. Een exemplaar van [Train model](train-model.md) toevoegen en de uitvoer van [functie selectie op basis van filters](filter-based-feature-selection.md) gebruiken als invoer voor de training.

    > [!IMPORTANT]
    > Omdat het belang van de functie wordt bepaald op basis van de waarden in de kolom, kunt u niet van tevoren weten welke kolommen beschikbaar zijn voor invoer naar [Train model](train-model.md).  
5. Koppel nu een exemplaar van de module **select columns Transform** . 

    Hiermee wordt een kolom selectie gegenereerd als een trans formatie die kan worden opgeslagen of toegepast op andere gegevens sets. Met deze stap zorgt u ervoor dat de kolommen die worden geïdentificeerd door de functie selectie, worden opgeslagen voor hergebruik door andere modules.

6. Voeg de module [score model](score-model.md) toe. 

    **Verbind de invoer gegevensset niet.**

    Voeg in plaats daarvan de module [trans formatie Toep assen](apply-transformation.md) toe en verbind de uitvoer van de functie selectie transformatie.

   > [!IMPORTANT]
   > U kunt niet verwachten dat u de [functie selectie op basis van filters](filter-based-feature-selection.md) toepast op de Score gegevensset en dezelfde resultaten krijgt. Omdat de functie selectie is gebaseerd op waarden, kan er een andere set kolommen worden gekozen, waardoor de Score bewerking mislukt.
7. Voer de pijplijn uit.

Dit proces voor het opslaan en vervolgens Toep assen van een kolom selectie zorgt ervoor dat hetzelfde gegevens schema beschikbaar is voor training en scores.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
