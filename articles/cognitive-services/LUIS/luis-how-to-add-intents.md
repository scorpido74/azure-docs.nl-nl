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
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467546"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Intenties toevoegen om te bepalen wat de gebruikers intentie van uitingen zijn

Voeg [intenties](luis-concept-intent.md) toe aan uw Luis-app om groepen vragen of opdrachten te identificeren die hetzelfde doel hebben. 

Intenties worden beheerd vanuit de **opbouw** sectie van de bovenste navigatie balk en vervolgens vanuit de **intenties**van het linkerdeel venster. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Intentie toevoegen

1. Selecteer **Create new intent** op de pagina **Intents**.

1. Voer in het dialoog venster **nieuwe intentie maken** de naam van de doel groep in `GetEmployeeInformation`in en klik op **gereed**.

    ![Intentie toevoegen](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Een voor beeld-utterance toevoegen

Voor beelden van uitingen zijn tekst voorbeelden van gebruikers vragen of-opdrachten. Als u Language Understanding wilt leren (LUIS), moet u bijvoorbeeld uitingen toevoegen aan een intentie.

1. Voer op de pagina Details van de **GetEmployeeInformation** -intentie een relevante utterance in die u van uw gebruikers verwacht, zoals `Does John Smith work in Seattle?` in het tekstvak onder de naam van de intentie en druk vervolgens op ENTER.
 
    ![Scherm afbeelding van de pagina met details van de intenties, met utterance gemarkeerd](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converteert alle uitingen naar kleine letters en voegt spaties rondom tokens, zoals afbreek streepjes, toe.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Voorspellings fouten in intentie 

Een voor beeld van een utterance in een intentie heeft mogelijk een intentie Voorspellings fout tussen de bedoeling van het voor beeld utterance is op dit moment en de Voorspellings intentie die tijdens de training is bepaald. 

Als u utterance-Voorspellings fouten wilt vinden en deze wilt corrigeren, gebruikt u de **evaluatie** opties van de **filter** optie onjuist en onduidelijk gecombineerd met de **weergave** optie van **gedetailleerde weer gave**. 

![Gebruik de filter optie om utterance Voorspellings fouten te vinden en deze op te lossen.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Wanneer de filters en weer gave worden toegepast en er voorbeeld uitingen zijn met fouten, worden de uitingen en de problemen weer gegeven in de lijst voor beeld utterance.

![! [Wanneer de filters en weer gave worden toegepast en er voorbeeld uitingen zijn met fouten, worden de uitingen en de problemen weer gegeven in de lijst voor beeld utterance.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Elke rij toont de huidige Voorspellings Score van de training voor het voor beeld utterance, het dichtstbijzijnde aantal punten, het verschil in deze twee scores. 

### <a name="fixing-intents"></a>Intenties herstellen

Gebruik het [dash board samen vatting](luis-how-to-use-dashboard.md)voor meer informatie over het oplossen van problemen met het Voorspellings beleid. Het dash board samen vatting bevat een analyse voor de laatste training van de actieve versie en biedt de beste suggesties voor het oplossen van uw model.  

## <a name="add-a-custom-entity"></a>Een aangepaste entiteit toevoegen

Zodra een utterance is toegevoegd aan een intentie, kunt u tekst selecteren in de utterance om een aangepaste entiteit te maken. Een aangepaste entiteit is een manier om tekst te labelen voor extractie, samen met de juiste intentie. 

Zie [entiteit toevoegen aan utterance](luis-how-to-add-example-utterances.md) voor meer informatie.

## <a name="entity-prediction-discrepancy-errors"></a>Discrepantie-fouten bij de voor spelling van de entiteit 

De entiteit wordt rood onderstreept om de voor spelling van een [entiteit](luis-how-to-add-example-utterances.md#entity-status-predictions)te bepalen. Omdat dit het eerste exemplaar van een entiteit is, zijn er niet voldoende voor beelden voor LUIS om een hoge betrouw baarheid te hebben waarop deze tekst is gelabeld met de juiste entiteit. Dit verschil wordt verwijderd wanneer de app wordt getraind. 

![Scherm afbeelding van de pagina Details van de intenties, de naam van de aangepaste entiteit is blauw gemarkeerd](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

De tekst wordt blauw gemarkeerd, wat een entiteit aangeeft.  

## <a name="add-a-prebuilt-entity"></a>Een vooraf samengestelde entiteit toevoegen

Zie [prebuiled entity](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)(Engelstalig) voor meer informatie.

## <a name="using-the-contextual-toolbar"></a>De contextuele werk balk gebruiken

Wanneer een of meer voor beeld-uitingen in de lijst zijn geselecteerd door het selectie vakje links van een utterance in te scha kelen, kunt u op de werk balk boven de lijst utterance de volgende acties uitvoeren:

* Intentie opnieuw toewijzen: utterance (s) verplaatsen naar ander doel
* Utterance (s) verwijderen
* Entiteits filters: alleen uitingen weer geven met gefilterde entiteiten
* Alleen alle/fouten weer geven: uitingen weer geven met Voorspellings fouten of alle uitingen weer geven
* Weer gave entiteiten/tokens: entiteiten weer geven met entiteits namen of onbewerkte tekst van utterance weer geven
* Vergroot glas: zoeken naar uitingen met specifieke tekst

## <a name="working-with-an-individual-utterance"></a>Werken met een afzonderlijke utterance

De volgende acties kunnen worden uitgevoerd op een afzonderlijke utterance in het menu met het weglatings teken rechts van de utterance:

* Bewerken: de tekst van de utterance wijzigen
* Verwijderen: Verwijder de utterance uit de intentie. Als u de utterance nog steeds wilt, kunt u de methode het beste verplaatsen naar de **geen** intentie. 
* Een patroon toevoegen: een patroon biedt u de mogelijkheid om een gemeen schappelijk utterance te maken en vervang bare tekst te markeren en om te voor komen dat u de nood zaak van meer uitingen in de bedoeling vermindert. 

Met de kolom **labelen** in kunt u de bedoeling van de utterance wijzigen.

## <a name="train-your-app-after-changing-model-with-intents"></a>Uw app trainen nadat u een model met intenties hebt gewijzigd

Nadat u intenties hebt toegevoegd, bewerkt of verwijderd, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app zodat uw wijzigingen worden toegepast op eindpunt query's. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van [voor beeld-uitingen](luis-how-to-add-example-utterances.md) met entiteiten. 
