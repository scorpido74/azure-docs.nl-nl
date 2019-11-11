---
title: Intenties toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg intenties toe aan uw LUIS-app om groepen vragen of opdrachten te identificeren die dezelfde bedoelingen hebben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904326"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Intenties toevoegen om te bepalen wat de gebruikers intentie van uitingen zijn

Voeg [intenties](luis-concept-intent.md) toe aan uw Luis-app om groepen vragen of opdrachten te identificeren die hetzelfde doel hebben. 

Intenties worden beheerd vanuit de **opbouw** sectie van de bovenste navigatie balk en vervolgens vanuit de **intenties**van het linkerdeel venster. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Intentie toevoegen

1. Selecteer in de [Luis preview-Portal](https://preview.luis.ai)de optie **Build** om de intenties weer te geven. 
1. Selecteer op de pagina **intenties** **+ maken**.
1. In het dialoog venster **nieuwe intentie maken** voert u de naam van de doel groep in, bijvoorbeeld `ModifyOrder`, en selecteert u **gereed**.

    > [!div class="mx-imgBorder"]
    > ](./media/luis-how-to-add-intents/Addintent-dialogbox.png) ![toevoegen

    De bedoeling is bijvoorbeeld uitingen.

## <a name="add-an-example-utterance"></a>Een voor beeld-utterance toevoegen

Voor beelden van uitingen zijn tekst voorbeelden van gebruikers vragen of-opdrachten. Als u Language Understanding wilt leren (LUIS) wanneer u dit opzet wilt voors pellen, moet u bijvoorbeeld uitingen toevoegen aan een intentie. LUIS moet binnen het bereik van 15 tot 30 voor beeld uitingen zijn om te beginnen met de bedoeling. Voeg geen voor beeld-uitingen toe. Elke utterance moet zorgvuldig worden gekozen voor de manier waarop deze afwijkt van de voor beelden die al in de bedoeling zijn. 

1. Voer op de pagina Details van intentie een relevante utterance in die u van uw gebruikers verwacht, zoals `Deliver a large cheese pizza` in het tekstvak onder de naam van de intentie en druk vervolgens op ENTER.
 
    > [!div class="mx-imgBorder"]
    > ![scherm opname van de pagina Details van intenties, met gemarkeerde utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converteert alle uitingen naar kleine letters en voegt spaties rondom [tokens](luis-language-support.md#tokenization) , zoals afbreek streepjes, toe.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Voorspellings fouten in intentie 

Een voor beeld van een utterance in een intentie heeft mogelijk een intentie Voorspellings fout tussen de bedoeling van het voor beeld utterance is op dit moment en de intentie die tijdens de training is bepaald. 

Als u utterance-Voorspellings fouten wilt vinden en deze wilt corrigeren, gebruikt u de **filter** opties onjuist en onduidelijk gecombineerd met de optie **weer** geven van **gedetailleerde weer gave**. 

![Gebruik de filter optie om utterance Voorspellings fouten te vinden en deze op te lossen.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Wanneer de filters en weer gave worden toegepast en er voorbeeld uitingen zijn met fouten, worden de uitingen en de problemen weer gegeven in de lijst voor beeld utterance.

> [!div class="mx-imgBorder"]
> ![! [Wanneer de filters en weer gave worden toegepast en er voorbeeld uitingen zijn met fouten, worden de uitingen en de problemen weer gegeven in de lijst voor beeld utterance.] (./Media/Luis-How-to-add-intents/Find-Errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Elke rij toont de huidige Voorspellings Score van de training voor het voor beeld utterance, het dichtstbijzijnde aantal punten, het verschil in deze twee scores. 

### <a name="fixing-intents"></a>Intenties herstellen

Gebruik het [dash board samen vatting](luis-how-to-use-dashboard.md)voor meer informatie over het oplossen van problemen met het Voorspellings beleid. Het dash board samen vatting bevat een analyse voor de laatste training van de actieve versie en biedt de beste suggesties voor het oplossen van uw model.  

## <a name="using-the-contextual-toolbar"></a>De contextuele werk balk gebruiken

De context werkbalk bevat andere acties:

* Voor beeld-utterance bewerken of verwijderen
* Voor beeld-utterance opnieuw toewijzen aan een ander doel
* Filters en weer gaven: alleen uitingen weer geven met gefilterde entiteiten of optionele details weer geven
* Doorzoek bijvoorbeeld uitingen

## <a name="train-your-app-after-changing-model-with-intents"></a>Uw app trainen nadat u een model met intenties hebt gewijzigd

Nadat u intenties hebt toegevoegd, bewerkt of verwijderd, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app zodat uw wijzigingen worden toegepast op eindpunt query's. Niet trainen na elke wijziging. Train na een groep wijzigingen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van [voor beeld-uitingen](luis-how-to-add-example-utterances.md) met entiteiten. 
