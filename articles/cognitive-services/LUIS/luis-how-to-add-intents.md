---
title: Intents toevoegen - LUIS
titleSuffix: Azure Cognitive Services
description: Voeg intenties toe aan uw LUIS-app om groepen vragen of opdrachten te identificeren die dezelfde intenties hebben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904326"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Intents toevoegen om de intentie van uitingen van de gebruiker te bepalen

Voeg [intenties](luis-concept-intent.md) toe aan uw LUIS-app om groepen vragen of opdrachten te identificeren die dezelfde intentie hebben. 

Intents worden beheerd vanuit de sectie **Build** van de bovenste navigatiebalk en vervolgens vanuit de **intents**van het linkerdeelvenster. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Intentie toevoegen

1. Selecteer in de [LUIS-voorbeeldportal](https://preview.luis.ai)De optie **Bouwen** om intenties weer te geven. 
1. Selecteer op de pagina **Intents** de optie **+ Maken**.
1. Voer in het dialoogvenster **Nieuwe intentie maken** bijvoorbeeld `ModifyOrder`de intentienaam in en selecteer **Gereed**.

    > [!div class="mx-imgBorder"]
    > ![Intentie toevoegen](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    De intentie heeft voorbeelduitingen nodig.

## <a name="add-an-example-utterance"></a>Een voorbeeldutterance toevoegen

Voorbeelduitingen zijn tekstvoorbeelden van gebruikersvragen of opdrachten. Als u Luis (Language Understanding) wilt leren wanneer u deze intentie moet voorspellen, moet u voorbeelduitingen toevoegen aan een intentie. LUIS heeft 15 tot 30 voorbeelduitingen nodig om de intentie te begrijpen. Voeg geen voorbeelduitingen in bulk toe. Elke uiting moet zorgvuldig worden gekozen voor hoe het anders is dan voorbeelden die al in de intentie staan. 

1. Voer op de pagina intentiedetails een relevante utterance in `Deliver a large cheese pizza` die u van uw gebruikers verwacht, zoals in het tekstvak onder de intentienaam, en druk op Enter.
 
    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de pagina Intents-details, met uiting gemarkeerd](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converteert alle uitingen naar kleine letters en voegt spaties toe rond [tokens](luis-language-support.md#tokenization) zoals koppeltekens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Fouten bij het voorspellen van intentie 

Een voorbeeldutterance in een intentie kan een intentievoorspellingsfout hebben tussen de intentie waarin de voorbeeldutterance zich momenteel bevindt en de intentie die tijdens de training wordt bepaald. 

Als u fouten in de utterancevoorspelling wilt vinden en deze wilt oplossen, gebruikt u de **filteropties** onjuist en onduidelijk in combinatie met de optie **Weergave** **van gedetailleerde weergave**. 

![Als u fouten in de utterancevoorspelling wilt vinden en deze wilt oplossen, gebruikt u de optie Filter.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Wanneer de filters en weergave worden toegepast en er voorbeelduitingen met fouten zijn, worden in de voorbeeldweergavelijst de uitingen en de problemen weergegeven.

> [!div class="mx-imgBorder"]
> ![! [Wanneer de filters en weergave worden toegepast en er voorbeelduitingen met fouten zijn, worden in de voorbeeldweergavelijst de uitingen en de problemen weergegeven.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Elke rij toont de voorspellingsscore van de huidige training voor de voorbeeldutterance, de score van de dichtstbijzijnde rivaal, wat het verschil is in deze twee scores. 

### <a name="fixing-intents"></a>Intenties herstellen

Gebruik het [overzichtsdashboard](luis-how-to-use-dashboard.md)voor meer informatie over het oplossen van fouten bij het oplossen van intentievoorspelling . Het overzichtsdashboard biedt analyse voor de laatste training van de actieve versie en biedt de belangrijkste suggesties om uw model te repareren.  

## <a name="using-the-contextual-toolbar"></a>De contextuele werkbalk gebruiken

De contextwerkbalk biedt andere acties:

* Voorbeeldutterance bewerken of verwijderen
* Voorbeeldutterance opnieuw toewijzen aan een andere intentie
* Filters en weergaven: toont alleen uitingen met gefilterde entiteiten of bekijk optionele details
* Voorbeelduitingen doorzoeken

## <a name="train-your-app-after-changing-model-with-intents"></a>Uw app trainen nadat u het model met intenties hebt gewijzigd

Nadat u intents hebt toegevoegd, bewerkt of verwijderd, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app, zodat uw wijzigingen worden toegepast op eindpuntquery's. Train niet na elke verandering. Train na een groep veranderingen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van [voorbeelduitingen](luis-how-to-add-example-utterances.md) met entiteiten. 
