---
title: Gebruikers uitingen bekijken-LUIS
titleSuffix: Azure Cognitive Services
description: Met het actieve leer proces worden eindpunt query's vastgelegd en wordt het uitingen van de gebruiker geselecteerd. U kunt deze uitingen bekijken om de intentie te selecteren en entiteiten te markeren voor deze uitingen met lees-of-wereld. Ga akkoord met deze wijzigingen in uw voor beeld uitingen en klik vervolgens op publiceren. LUIS identificeert uitingen nauw keuriger.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: c617e4aa62ce2ff468545bef0b2ebe2c4d0e4f03
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382361"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Endpoint uitingen in LUIS-Portal controleren voor actief leren

Met het [actieve leer proces](luis-concept-review-endpoint-utterances.md) worden eindpunt query's vastgelegd en wordt het uitingen van de gebruiker geselecteerd. U kunt deze uitingen bekijken om de intentie te selecteren en entiteiten te markeren voor deze uitingen met lees-of-wereld. Ga akkoord met deze wijzigingen in uw voor beeld uitingen en klik vervolgens op publiceren. LUIS identificeert uitingen nauw keuriger.


## <a name="enable-active-learning"></a>Actief leren inschakelen

Meld gebruikers query's om actief leren in te scha kelen. Dit wordt bereikt door de [eindpunt query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen `log=true` met de para meter en waarde van de query.

## <a name="disable-active-learning"></a>Actief leren uitschakelen

Als u actief leren wilt uitschakelen, moet u geen gebruikers query's registreren. Dit wordt bereikt door de [eindpunt query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen `log=false` met de para meter en waarde van de query.

## <a name="filter-utterances"></a>Uitingen filteren

1. Open uw app (bijvoorbeeld TravelAgent) door te selecteren van de naam op **mijn Apps** pagina en selecteer vervolgens **bouwen** in de bovenste balk.

1. Onder de **verbeterde app-prestaties**, selecteer **bekijken eindpunt uitingen**.

1. Op de **bekijken eindpunt uitingen** weergeeft, schakelt u in de **filterlijst met opzet of entiteit** in het tekstvak. Deze vervolgkeuzelijst bevat alle intents onder **INTENTS** en alle entiteiten in **entiteiten**.

    ![Uitingen filteren](./media/label-suggested-utterances/filter.png)

1. Selecteer een categorie (intents of entiteiten) in de vervolgkeuzelijst en bekijk de uitingen.

    ![Intentie uitingen](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Label-entiteiten
LUIS vervangt entiteit tokens (woorden) met de namen van entiteiten in het blauw is gemarkeerd. Als een utterance is zonder label entiteiten, kunt u ze in de utterance label. 

1. Selecteer op de woorden in de utterance. 

1. Selecteer een entiteit in de lijst.

    ![Label-entiteit](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Één utterance uitlijnen

Elke utterance heeft een voorgestelde bedoeling weergegeven in de **uitgelijnd bedoeling** kolom. 

1. Als u akkoord met deze suggestie gaat, selecteert u op het vinkje hebt geklikt.

    ![Uitgelijnde intentie behouden](./media/label-suggested-utterances/align-intent-check.png)

1. Als u niet eens met het voorstel bent, selecteert u het juiste doel in de uitgelijnde, intentie vervolgkeuzelijst en selecteer op het vinkje aan de rechterkant van het uitgelijnde doel. 

    ![Bedoeling uitlijnen](./media/label-suggested-utterances/align-intent.png)

1. Nadat u hebt geselecteerd op het vinkje hebt geklikt, wordt de utterance verwijderd uit de lijst. 

## <a name="align-several-utterances"></a>Verschillende uitingen uitlijnen

Om te worden uitgelijnd verschillende uitingen, schakel het selectievakje aan de linkerkant van de uitingen en selecteer vervolgens op de **toevoegen geselecteerd** knop. 

![Verschillende uitlijnen](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Controleer of uitgelijnde doel

U kunt controleren of de utterance is afgestemd op de juiste intentie door te gaan naar de **Intents** pagina, selecteert u de naam van de intentie en controleren van de uitingen. De utterance van **bekijken eindpunt uitingen** is in de lijst.

## <a name="delete-utterance"></a>Utterance verwijderen

Elke utterance kan worden verwijderd uit de lijst controleren. Zodra verwijderd, wordt deze niet weergegeven in de lijst met het opnieuw. Dit geldt zelfs als de gebruiker de dezelfde utterance van het eindpunt voert. 

Als u niet zeker of u de utterance verwijdert, verplaatsen naar de intentie, geen of maak een nieuw doel, zoals "gemengde" en de utterance verplaatsen naar dit doel. 

## <a name="delete-several-utterances"></a>Verschillende uitingen verwijderen

Als u wilt verwijderen van verschillende uitingen, selecteert u elk item en selecteer in de Prullenbak rechts van de **toevoegen geselecteerd** knop.

![Verschillende verwijderen](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Volgende stappen

Als u wilt testen hoe prestaties worden verbeterd wanneer u de voorgestelde uitingen labels, kunt u de testconsole openen door het selecteren van **testen** in het bovenste deelvenster. Zie voor instructies over het testen van uw app met behulp van de testconsole [trainen en testen van uw app](luis-interactive-test.md).
