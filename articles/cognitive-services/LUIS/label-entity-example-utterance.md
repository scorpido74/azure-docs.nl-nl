---
title: Voor beeld van een label entiteit utterance
titleSuffix: Azure Cognitive Services
description: Meer informatie over het labelen van een door een machine geleerde entiteit met subonderdelen in een voor beeld van een utterance op een detail pagina van de LUIS-Portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898380"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Door de machine geleerde entiteit in een voor beeld-utterance

Het labelen van een entiteit in een voor beeld utterance geeft LUIS een voor beeld van wat de entiteit is en waar de entiteit kan worden weer gegeven in de utterance.

## <a name="labeling-machine-learned-entity"></a>Door de machine geleerde entiteit labelen

Bekijk de zin `hi, please I want a cheese pizza in 20 minutes`.

1. Selecteer de meest linkse tekst en selecteer vervolgens de juiste tekst van de entiteit en kies vervolgens de entiteit waaraan u een label wilt toe. in dit geval is de volg orde voltooid. De _volledige order_ is gelabeld in de volgende afbeelding.

    > [!div class="mx-imgBorder"]
    > ![label volledige, door de machine geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecteer de entiteit in het pop-upvenster. De entiteit volledige pizza order bevat alle woorden (van links naar rechts in het Engels) die een label hebben.

## <a name="review-labeled-text"></a>Gelabelde tekst controleren

Bekijk na het labelen het voor beeld utterance en zorg ervoor dat de geselecteerde hoeveelheid tekst is onderstreept met de gekozen entiteit. De ononderbroken lijn geeft aan dat de tekst is gelabeld.

> [!div class="mx-imgBorder"]
> ![gelabelde volledige, door de machine geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Voorspelde entiteit bevestigen

Als er een vak met een stippel lijn rond de hoeveelheid tekst en de naam van de entiteit boven aan de utterance staat, geeft deze aan dat de tekst wordt voor speld, maar _nog niet is gelabeld_. Als u de voor spelling in een label wilt omzetten, selecteert u de rij utterance en selecteert u voor **spellingen van entiteiten bevestigen**.

> [!div class="mx-imgBorder"]
> ![voor speling volledige, door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

U kunt ook de naam van de entiteit selecteren boven de tekst en vervolgens voor **spelling bevestigen** selecteren in het menu dat wordt weer gegeven.

> [!div class="mx-imgBorder"]
> ![voor beeld van een door de machine geleerde entiteit met menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Entiteit labelen door tekenen met de cursor voor het entiteits palet

Het entiteits palet biedt een alternatief voor de vorige label ervaring. Hiermee kunt u tekst vervormen om deze direct te labelen met een entiteit.

1. Open het deel venster entiteit door in de rechter bovenhoek van de tabel utterance op het pictogram markeer stift te klikken.

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selecteer het onderdeel entiteit. Deze actie wordt visueel aangeduid met een nieuwe cursor. De cursor volgt de muis tijdens het verplaatsen van de portal.

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. In het voor beeld utterance _tekent_ u de entiteit met de cursor.

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Labelen van subonderdelen van een door een machine geleerde entiteit

Subonderdelen in entiteiten hebben hetzelfde label als entiteiten op het hoogste niveau. Wanneer u tekst selecteert, zijn de entiteiten die beschikbaar zijn in het pop-upvenster relatief ten opzichte van de context waarin de tekst wordt weer gegeven. Als u bijvoorbeeld een door de machine geleerde computer op 5 niveau hebt en u tekst selecteert die is gelabeld met het 1e en 2e niveau (aangeduid met een entiteits naam met een label onder het voor beeld utterance), zijn de entiteiten die beschikbaar zijn in het pop-upvenster beperkt tot de context van onderdelen van het derde niveau. Als u de tekst met andere entiteiten wilt labelen, selecteert u **label als andere entiteits** optie.

> [!div class="mx-imgBorder"]
> het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Subonderdelen kunnen alleen worden gelabeld als het bovenliggende item ook een label heeft.

## <a name="labeling-entity-roles"></a>Entiteits rollen labelen

Entiteits rollen worden aangeduid met het palet entity.

1. Selecteer op de detail pagina Details het **entiteits palet** in de context werkbalk.
1. Nadat het entiteits palet wordt geopend, selecteert u de entiteit in de lijst met entiteiten.
1. Ga naar de **entiteits controle**en selecteer een bestaande rol of maak een nieuwe rol.
1. In het voor beeld utterance tekst labelt u de tekst met de rol van entiteit.

## <a name="unlabel-entities"></a>Entiteiten onlabelen

Als u het label van een entiteit ongewijzigd wilt laten, selecteert u de naam van de entiteit onder de tekst en selecteert u **Label**opheffen. Als de entiteit waarvoor u een label wilt maken een label subonderdelen heeft, moeten de subonderdelen eerst worden ontlabeld.

## <a name="editing-labels-using-the-entity-palette"></a>Labels bewerken met het entiteits palet

Als u een fout maakt tijdens het labelen, is het entiteits palet een eenvoudig hulp middel waarmee u snel bewerkingen kunt uitvoeren. Als bijvoorbeeld een entiteit label een extra woord per ongeluk omvat en het al gelabelde subcomponenten heeft, kunt u het palet van de entiteit gebruiken om het gewenste kortere aantal woorden te verstrijken.

Bijvoorbeeld:

1. Pizza-type subcomponent omvat "kaas pizza met", inclusief een extra onjuist woord: "met"

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/edit-label-with-palette-1.png)

2. Gebruik het palet entiteit om pizza type en penseel over "kaas pizza" te kiezen. Het resultaat is dat alleen kaas pizza wordt aangeduid als pizza type nu.

    > [!div class="mx-imgBorder"]
    > het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Labels voor overeenkomende-tekst entiteiten

Overeenkomende-tekst entiteiten bevatten vooraf gemaakte entiteiten, reguliere expressie-entiteiten, lijst entiteiten en patroon. alle entiteiten. Deze worden automatisch gelabeld door LUIS, zodat ze niet hoeven hand matig te worden gelabeld door gebruikers.

## <a name="entity-prediction-errors"></a>Voorspellings fouten van entiteit

Voor de voor spelling van entiteits fouten wordt aangegeven dat de voorspelde entiteit niet overeenkomt met de entiteit met het label. Dit wordt gevisualiseerd met een waarschuwings indicator naast het utterance.

> [!div class="mx-imgBorder"]
> het kleuren palet ![voor door de computer geleerde entiteit](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik het [dash board](luis-how-to-use-dashboard.md) en [Bekijk het eind punt uitingen](luis-how-to-review-endpoint-utterances.md) om de kwaliteit van de voor spelling van uw app te verbeteren.