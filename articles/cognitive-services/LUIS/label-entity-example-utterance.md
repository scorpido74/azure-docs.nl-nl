---
title: Voorbeeldutterance van de labelentiteit
titleSuffix: Azure Cognitive Services
description: Meer informatie over het labelen van een door machines geleerde entiteit met subcomponenten in een voorbeeldutterance op een pagina met intentiedetails van de LUIS-portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898380"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Door de machine geleerde entiteit labelen in een voorbeeldutterance

Als u een entiteit labelt in een voorbeeldutterance, geeft LUIS een voorbeeld van wat de entiteit is en waar de entiteit in de utterance kan worden weergegeven.

## <a name="labeling-machine-learned-entity"></a>Labelen machine-geleerde entiteit

Denk aan `hi, please I want a cheese pizza in 20 minutes`de zin, .

1. Selecteer de meest linkse tekst, selecteer vervolgens de meest rechtse tekst van de entiteit en kies vervolgens de entiteit waarmee u wilt labelen, in dit geval Complete Order. De _volledige bestelling_ wordt in de volgende afbeelding vermeld.

    > [!div class="mx-imgBorder"]
    > ![Label volledige machine-geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecteer de entiteit in het pop-upvenster. De gelabelde complete pizzaorderentiteit bevat alle woorden (van links naar rechts in het Engels) die zijn gelabeld.

## <a name="review-labeled-text"></a>Gelabelde tekst controleren

Bekijk na het labelen de voorbeeldutterance en zorg ervoor dat de geselecteerde reeks tekst is onderstreept met de gekozen entiteit. De vaste regel geeft aan dat de tekst is gelabeld.

> [!div class="mx-imgBorder"]
> ![Gelabeldvolledige machine-geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Voorspelde entiteit bevestigen

Als er een vak met stippelen rond de reeks tekst is en de naam van de entiteit boven de utterance staat, geeft dit aan dat de tekst wordt voorspeld, maar _nog niet is gelabeld._ Als u de voorspelling wilt omzetten in een label, selecteert u de utterancerij en selecteert u **Entiteitsvoorspellingen bevestigen**.

> [!div class="mx-imgBorder"]
> ![Volledige machine-geleerde entiteit voorspellen](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

U ook de naam van de entiteit boven de tekst selecteren en **Vervolgens Voorspelling bevestigen** selecteren in het menu dat wordt weergegeven.

> [!div class="mx-imgBorder"]
> ![Volledige machine-geleerde entiteit met menu voorspellen](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Entiteit labelen door te schilderen met de cursor van het entiteitspalet

Het entiteitspalet biedt een alternatief voor de vorige labelervaring. Hiermee u tekst overpoetsen om deze direct te labelen met een entiteit.

1. Open het entiteitspalet door te selecteren op het pictogram Highlighter rechtsboven in de utterancetabel.

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selecteer de entiteitscomponent. Deze actie wordt visueel aangegeven met een nieuwe cursor. De cursor volgt de muis terwijl u in de portal beweegt.

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. _Schilder_ in de voorbeeldutterance de entiteit met de cursor.

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Subcomponenten van een door de machine geleerde entiteit labelen

Subcomponenten in entiteiten worden op precies dezelfde manier gelabeld als entiteiten op het hoogste niveau. Bij het selecteren van tekst zijn de entiteiten die beschikbaar zijn in het pop-upvenster relatief ten opzichte van de context waarin de tekst wordt weergegeven. Als u bijvoorbeeld een door de 5-niveau machine geleerde entiteit hebt en u tekst selecteert die is gelabeld met de 1e en 2e niveaus (aangegeven door een gelabelde entiteitsnaam onder de voorbeeldutterance), zijn de entiteiten die beschikbaar zijn in het pop-upvenster beperkt tot de context van componenten van het 3e niveau. Als u de tekst wilt labelen met andere entiteiten, selecteert u **Label als een andere entiteitsoptie.**

> [!div class="mx-imgBorder"]
> ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Subcomponenten kunnen alleen worden gelabeld als de bovenliggende component ook is gelabeld.

## <a name="labeling-entity-roles"></a>Entiteitrollen labelen

Entiteitsrollen worden gelabeld met behulp van het entiteitspalet.

1. Selecteer op de pagina Intent-detail het **palet Entiteit** op de contextwerkbalk.
1. Nadat het palet Entiteit is geopend, selecteert u de entiteit in de lijst met entiteiten.
1. Ga naar de **entiteitscontrole**en selecteer een bestaande rol of maak een nieuwe rol.
1. Label de tekst in het voorbeeld utterance met de entiteitsrol.

## <a name="unlabel-entities"></a>Entiteiten zonder label

Als u het label van een entiteit wilt ontzeggen, selecteert u de entiteitsnaam onder de tekst en selecteert **u Label opheffen**. Als de entiteit die u probeert te ontlabelen subcomponenten heeft gelabeld, moeten de subcomponenten eerst worden ongelabeld.

## <a name="editing-labels-using-the-entity-palette"></a>Labels bewerken met het entiteitspalet

Als u een fout maakt tijdens het labelen, is het entiteitspalet een eenvoudig hulpmiddel waarmee u snel worden bewerkt. Als een entiteitslabel bijvoorbeeld per ongeluk een extra woord omspant en subcomponenten al heeft gelabeld, u het entiteitspalet gebruiken om de vereiste kortere woordwijdte te poetsen.

Bijvoorbeeld:

1. Pizza Type subcomponent overspant "kaas pizza met" die een extra onjuist woord bevat - "met"

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/edit-label-with-palette-1.png)

2. Gebruik het entiteitspalet om PizzaType te kiezen en over "kaaspizza" te borstelen. Het resultaat is dat alleen kaas pizza is gelabeld als Pizza Type nu.

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Labels voor entiteiten met overeenkomende tekst

Entiteiten met overeenkomende tekst omvatten vooraf gebouwde entiteiten, entiteiten met reguliere expressies, lijstentiteiten en patroonentiteiten. Deze worden automatisch gelabeld door LUIS, zodat ze niet handmatig worden gelabeld door gebruikers.

## <a name="entity-prediction-errors"></a>Entiteitsvoorspellingsfouten

Entiteitsvoorspellingsfouten geven aan dat de voorspelde entiteit niet overeenkomt met de gelabelde entiteit. Dit wordt gevisualiseerd met een waarschuwingsindicator naast de utterance.

> [!div class="mx-imgBorder"]
> ![Entiteitspalet voor door machines geleerde entiteit](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik het [dashboard](luis-how-to-use-dashboard.md) en bekijk eindpuntuitingen om de [voorspellingskwaliteit](luis-how-to-review-endpoint-utterances.md) van uw app te verbeteren.