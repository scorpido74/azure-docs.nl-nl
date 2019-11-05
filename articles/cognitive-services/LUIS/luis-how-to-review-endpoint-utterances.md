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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499078"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Endpoint uitingen in LUIS-Portal controleren voor actief leren

Met het [actieve leer proces](luis-concept-review-endpoint-utterances.md) worden eindpunt query's vastgelegd en wordt het uitingen van de gebruiker geselecteerd. U kunt deze uitingen bekijken om de intentie te selecteren en entiteiten te markeren voor deze uitingen met lees-of-wereld. Ga akkoord met deze wijzigingen in uw voor beeld uitingen en klik vervolgens op publiceren. LUIS identificeert uitingen nauw keuriger.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Actief leren inschakelen

Meld gebruikers query's om actief leren in te scha kelen. Dit wordt bereikt door de [eindpunt query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen met de `log=true` query string-para meter en-waarde.

## <a name="disable-active-learning"></a>Actief leren uitschakelen

Als u actief leren wilt uitschakelen, moet u geen gebruikers query's registreren. Dit wordt bereikt door de [eindpunt query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen met de `log=false` query string-para meter en-waarde.

## <a name="filter-utterances"></a>Uitingen filteren

1. Open uw app (bijvoorbeeld TravelAgent) door de naam ervan te selecteren op **mijn apps** pagina en vervolgens **Build** te selecteren in de bovenste balk.

1. Selecteer **uitingen endpoint controleren**onder de prestaties van de **app verbeteren**.

1. Selecteer op de pagina **eind punt uitingen** in het tekstvak **filteren op intentie of entiteit** . Deze vervolg keuzelijst bevat alle intenties onder **INtenties** en alle entiteiten onder **entiteiten**.

    ![Uitingen-filter](./media/label-suggested-utterances/filter.png)

1. Selecteer een categorie (intenties of entiteiten) in de vervolg keuzelijst en controleer de uitingen.

    ![Uitingen intentie](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Label entiteiten
LUIS vervangt entiteits tokens (woorden) met entiteits namen die blauw zijn gemarkeerd. Als een utterance entiteiten zonder label heeft, labelt u ze in de utterance. 

1. Selecteer een of meer woorden in het utterance. 

1. Selecteer een entiteit in de lijst.

    ![Entiteit labelen](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Eén utterance uitlijnen

Elke utterance heeft een voorgestelde intentie die wordt weer gegeven in de kolom **uitgelijnd intents** . 

1. Als u akkoord gaat met die suggestie, selecteert u op het vinkje.

    ![Uitgelijnd doel behouden](./media/label-suggested-utterances/align-intent-check.png)

1. Als u niet akkoord gaat met het voor stel, selecteert u in de vervolg keuzelijst uitgelijnde intentie de juiste intentie en selecteert u vervolgens op het vinkje rechts van de uitgelijnde intentie. 

    ![Intentie uitlijnen](./media/label-suggested-utterances/align-intent.png)

1. Nadat u op het vinkje hebt geklikt, wordt de utterance uit de lijst verwijderd. 

## <a name="align-several-utterances"></a>Meerdere uitingen uitlijnen

Als u verschillende uitingen wilt uitlijnen, schakelt u het selectie vakje links van de uitingen in en selecteert u op de knop **selectie toevoegen** . 

![Meerdere uitlijnen](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Uitgelijnde intentie controleren

U kunt controleren of de utterance is uitgelijnd met de juiste intentie door naar de pagina **intenties** te gaan, de naam van de doel groep te selecteren en de uitingen te controleren. De utterance van het **controle-eind punt uitingen** bevindt zich in de lijst.

## <a name="delete-utterance"></a>Utterance verwijderen

Elk utterance kan worden verwijderd uit de controle lijst. Als de app eenmaal is verwijderd, wordt deze niet meer in de lijst weer gegeven. Dit geldt ook als de gebruiker dezelfde utterance van het eind punt opgeeft. 

Als u niet zeker weet of u de utterance moet verwijderen, verplaatst u deze naar de geen intentie of maakt u een nieuwe intentie zoals ' Diversen ' en verplaatst u de utterance naar dat doel. 

## <a name="delete-several-utterances"></a>Meerdere uitingen verwijderen

Als u meerdere uitingen wilt verwijderen, selecteert u elk item en selecteert u op de Prullenbak rechts van de knop **geselecteerde toevoegen** .

![Meerdere verwijderen](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Volgende stappen

Als u wilt testen hoe de prestaties verbeteren nadat u het label gesuggereerde uitingen hebt, kunt u toegang krijgen tot de test console door **testen** te selecteren in het bovenste paneel. Zie [uw app trainen en testen](luis-interactive-test.md)voor instructies over het testen van uw app met behulp van de test console.
