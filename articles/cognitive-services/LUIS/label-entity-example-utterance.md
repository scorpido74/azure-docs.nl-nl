---
title: Voor beeld van een label entiteit utterance
titleSuffix: Azure Cognitive Services
description: Meer informatie over het labelen van een door een machine geleerde entiteit met subonderdelen in een voor beeld van een utterance op een detail pagina van de LUIS-Portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135108"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Door de machine geleerde entiteit in een voor beeld-utterance

Het labelen van een entiteit in een voor beeld utterance toont LUIS een voor beeld van de entiteit is en waar de entiteit kan worden weer gegeven in de utterance. 

## <a name="labeling-machine-learned-entity"></a>Door de machine geleerde entiteit labelen

Bekijk de zin `hi, please I want a cheese pizza in 20 minutes`. 

1. Selecteer de meest linkse tekst en selecteer vervolgens de juiste tekst van de entiteit. De _volledige order_ is gelabeld in de volgende afbeelding.

    > [!div class="mx-imgBorder"]
    > ![label volledige, door de machine geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecteer de entiteit in het pop-upvenster. De entiteit volledige pizza order bevat alle woorden (van links naar rechts in het Engels) die een label hebben. 

> [!TIP]
> De entiteiten die beschikbaar zijn in het pop-upvenster zijn relatief ten opzichte van de context waarin de tekst wordt weer gegeven. Als u bijvoorbeeld een door een computer geleerde op 5 niveaus hebt en u tekst op het derde niveau selecteert (aangeduid met een entiteits naam met een label onder het voor beeld utterance), zijn de entiteiten die beschikbaar zijn in het pop-upvenster beperkt tot de context van subonderdelen van de derde niveau (subonderdelen van het vierde niveau). 

## <a name="review-labeled-text"></a>Gelabelde tekst controleren

Bekijk na het labelen het voor beeld utterance. LUIS past het huidige model toe op het voor beeld utterance na labelen. De ononderbroken lijn geeft aan dat de tekst is gelabeld. 

> [!div class="mx-imgBorder"]
> ![gelabelde volledige, door de machine geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Wanneer trainen

Als het huidige model de entiteit met een label moet ondersteunen, maar het voor beeld utterance blijft de tekst weer geven als voor speld, maar niet gelabeld, Train uw app.  

## <a name="confirm-predicted-entity"></a>Voorspelde entiteit bevestigen

Als de indicator voor het visuele element boven het utterance wordt weer gegeven, wordt aangegeven dat de tekst wordt voor speld, maar _nog niet is gelabeld_. Als u de voor spelling in een label wilt omzetten, selecteert u de utterance en selecteert u vervolgens de voor **spellingen van de entiteit bevestigen**.

> [!div class="mx-imgBorder"]
> ![voor speling volledige, door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Entiteit van subcomponent labelen door te schilderen met de entiteit palet cursor

1. Als u de voor spellingen wilt corrigeren (entiteiten die worden weer gegeven boven het voor beeld utterance), opent u het palet entity. 

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Selecteer het subonderdeel entiteit. Deze actie wordt visueel aangeduid met een nieuwe cursor. De cursor volgt de muis tijdens het verplaatsen van de portal. 

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. In het voor beeld utterance _tekent_ u de entiteit met de cursor. 

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Labelen van overeenkomende-tekst entiteiten aan een door de machine geleerde entiteit

Overeenkomende-tekst entiteiten bevatten vooraf gemaakte entiteiten, reguliere expressie-entiteiten en lijst entiteiten. U voegt deze toe aan een door een machine geleerde entiteit, als beperkingen voor een subonderdeel, wanneer u de entiteit die door de machine is geleerd maakt of bewerkt. 

**Zodra deze beperkingen zijn toegevoegd, hoeft u geen label te maken voor de overeenkomende tekst in het voor beeld utterance.**

## <a name="entity-prediction-errors"></a>Voorspellings fouten van entiteit

Bij de voor spelling van de entiteit wordt een waarschuwings indicator weer gegeven. Dit geeft aan dat de voorspelde entiteit niet overeenkomt met de gelabelde entiteit. 

> [!div class="mx-imgBorder"]
> het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik het [dash board](luis-how-to-use-dashboard.md) en [Bekijk het eind punt uitingen](luis-how-to-review-endpoint-utterances.md) om de kwaliteit van de voor spelling van uw app te verbeteren.
