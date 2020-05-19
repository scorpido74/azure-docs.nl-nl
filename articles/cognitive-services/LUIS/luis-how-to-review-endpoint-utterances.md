---
title: Gebruikers uitingen bekijken-LUIS
titleSuffix: Azure Cognitive Services
description: Bekijk de uitingen die door actief leren zijn vastgelegd om intentie te selecteren en entiteiten te markeren voor Read-World uitingen; wijzigingen accepteren, trainen en publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: c976d3b74badc4eeb5978af352fe425089f2fbfb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584960"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>De LUIS-app verbeteren door eind punt uitingen te controleren

Het proces voor het controleren van het eind punt uitingen voor de juiste voor spellingen wordt [actief leren](luis-concept-review-endpoint-utterances.md)genoemd. Met het actieve leer proces worden eindpunt query's vastgelegd en wordt het uitingen van de gebruiker geselecteerd. U kunt deze uitingen bekijken om de intentie te selecteren en entiteiten te markeren voor deze uitingen met lees-of-wereld. Ga akkoord met deze wijzigingen in uw voor beeld uitingen en klik vervolgens op publiceren. LUIS identificeert uitingen nauw keuriger.

## <a name="enable-active-learning"></a>Actief leren inschakelen

Als u actief leren wilt inschakelen, moet u gebruikers query's registreren. Dit wordt bereikt door de query van het [eind punt](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) aan te roepen met de `log=true` para meter en waarde van de query reeks.

Gebruik de LUIS-Portal om de juiste eindpunt query te maken.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer uw **abonnement** en de resource voor het **ontwerpen** van de apps die zijn toegewezen aan die ontwerp bron.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Ga naar de sectie **beheren** en selecteer vervolgens **Azure-resources**.
1. Voor de toegewezen Voorspellings resource selecteert u **query parameters wijzigen**.

    > [!div class="mx-imgBorder"]
    > ![Gebruik de LUIS-Portal om logboeken op te slaan. Dit is vereist voor actief leren.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Schakel **logboeken opslaan** vervolgens opslaan door **gereed**te selecteren.

    > [!div class="mx-imgBorder"]
    > ![Gebruik de LUIS-Portal om logboeken op te slaan. Dit is vereist voor actief leren.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Met deze actie wordt de voorbeeld-URL gewijzigd door de `log=true` query string-para meter toe te voegen. Kopieer en gebruik de gewijzigde voorbeeld query-URL bij het maken van voorspellings query's voor het runtime-eind punt.

## <a name="correct-intent-predictions-to-align-utterances"></a>De intentie voorspellingen corrigeren om uitingen uit te lijnen

Elke utterance heeft een voorgestelde intentie die wordt weer gegeven in de kolom **uitgelijnd intents** .

> [!div class="mx-imgBorder"]
> [![Controleer uitingen van het eind punt dat LUIS niet zeker weet](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Schakel het selectie vakje in als u met die intentie instemt. Als u niet akkoord gaat met het voor stel, selecteert u in de vervolg keuzelijst uitgelijnde intentie de juiste intentie en selecteert u vervolgens op het vinkje rechts van de uitgelijnde intentie. Nadat u op het vinkje hebt geklikt, wordt de utterance naar het doel verplaatst en verwijderd uit de lijst **beoordeling eind punt uitingen** .

> [!TIP]
> Het is belang rijk dat u naar de pagina Details van intentie gaat om de voor spellingen van de entiteit te controleren en te corrigeren uit alle voor beelden uitingen uit de lijst met uitingen van het **controle punt** .

## <a name="delete-utterance"></a>Utterance verwijderen

Elk utterance kan worden verwijderd uit de controle lijst. Als de app eenmaal is verwijderd, wordt deze niet meer in de lijst weer gegeven. Dit geldt ook als de gebruiker dezelfde utterance van het eind punt opgeeft.

Als u niet zeker weet of u de utterance moet verwijderen, verplaatst u deze naar de geen intentie of maakt u een nieuwe intentie, zoals `miscellaneous` en verplaatst u de utterance naar die intentie.

## <a name="disable-active-learning"></a>Actief leren uitschakelen

Als u actief leren wilt uitschakelen, moet u geen gebruikers query's registreren. Dit wordt bereikt door de [eindpunt query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen met de `log=false` para meter en waarde van de query of niet met behulp van de query string waarde, omdat de standaard waarde False is.

## <a name="next-steps"></a>Volgende stappen

Als u wilt testen hoe de prestaties verbeteren nadat u het label gesuggereerde uitingen hebt, kunt u toegang krijgen tot de test console door **testen** te selecteren in het bovenste paneel. Zie [uw app trainen en testen](luis-interactive-test.md)voor instructies over het testen van uw app met behulp van de test console.
