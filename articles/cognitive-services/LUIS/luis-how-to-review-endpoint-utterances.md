---
title: Uitingen van gebruikers controleren - LUIS
titleSuffix: Azure Cognitive Services
description: Controleer uitingen die zijn vastgelegd door actief leren om intentie te selecteren en entiteiten te markeren voor leeswerelduitingen; wijzigingen accepteren, trainen en publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219850"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>De LUIS-app verbeteren door eindpuntuitingen te bekijken

Het proces van het controleren van eindpuntuitingen voor juiste voorspellingen wordt [Actief leren](luis-concept-review-endpoint-utterances.md)genoemd. Actief leren legt eindpuntquery's vast en selecteert de eindpuntuitingen van de gebruiker waarvan deze niet zeker is. U controleert deze uitingen om de intentie te selecteren en entiteiten voor deze leeswerelduitingen te markeren. Accepteer deze wijzigingen in uw voorbeelduitingen en train en publiceer. LUIS identificeert vervolgens uitingen nauwkeuriger.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Actief leren inschakelen

Als u actief leren wilt inschakelen, moet u gebruikersquery's registreren. Dit wordt bereikt door de [eindpuntquery](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) aan te roepen met de parameter en waarde van de `log=true` querytekenreeks.

Gebruik de LUIS-portal om de juiste eindpuntquery te construeren.

1. Selecteer uw app in de [voorbeeldpoort luis](https://preview.luis.ai/)in de lijst met apps.
1. Ga naar de sectie **Beheren** en selecteer **Azure-resources**.
1. Selecteer **Queryparameters wijzigen**voor de toegewezen voorspellingsbron .

    > [!div class="mx-imgBorder"]
    > ![Gebruik LUIS-portal om logboeken op te slaan, wat nodig is voor actief leren.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Logboeken **opslaan** inschakelen en vervolgens opslaan door **Gereed te**selecteren .

    > [!div class="mx-imgBorder"]
    > ![Gebruik LUIS-portal om logboeken op te slaan, wat nodig is voor actief leren.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Met deze actie wordt de `log=true` voorbeeld-URL gewijzigd door de parameter querystring toe te voegen. Kopieer en gebruik de gewijzigde voorbeeldquery-URL bij het maken van voorspellingsquery's naar het eindpunt runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Intentievoorspellingen corrigeren om uitingen uit te lijnen

Elke utterance heeft een voorgestelde intentie die wordt weergegeven in de **kolom Uitgelijnde intentie.**

> [!div class="mx-imgBorder"]
> [![Eindpuntuitingen controleren waarvan LUIS niet zeker is](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Als u het eens bent met die intentie, selecteert u het vinkje. Als u het niet eens bent met de suggestie, selecteert u de juiste intentie in de vervolgkeuzelijst met uitgelijnde intentie en selecteert u op het vinkje rechts van de uitgelijnde intentie. Nadat u het vinkje hebt geselecteerd, wordt de utterance naar de intentie verplaatst en verwijderd uit de lijst **Eindpuntuitingen controleren.**

> [!TIP]
> Het is belangrijk om naar de pagina Intentiedetails te gaan om de entiteitsvoorspellingen uit alle voorbeelduitingen uit de lijst **Eindpuntuitingen controleren** en corrigeren.

## <a name="delete-utterance"></a>Utterance verwijderen

Elke utterance kan uit de beoordelingslijst worden verwijderd. Eenmaal verwijderd, wordt deze niet meer in de lijst weergegeven. Dit geldt zelfs als de gebruiker dezelfde utterance vanaf het eindpunt invoert.

Als u niet zeker weet of u de utterance moet verwijderen, verplaatst u `miscellaneous` deze naar de intentie Geen of maakt u een nieuwe intentie zoals en verplaatst u de utterance naar die intentie.

## <a name="disable-active-learning"></a>Actief leren uitschakelen

Als u actief leren wilt uitschakelen, moet u gebruikersquery's niet registreren. Dit wordt bereikt door de [eindpuntquery](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) in te stellen met de `log=false` parameter querystring en de waarde of de querytekenreekswaarde niet te gebruiken omdat de standaardwaarde onwaar is.

## <a name="next-steps"></a>Volgende stappen

Als u wilt testen hoe de prestaties verbeteren nadat u voorgestelde uitingen hebt gelabeld, hebt u toegang tot de testconsole door **Testen** in het bovenste deelvenster te selecteren. Zie Uw app trainen [en testen](luis-interactive-test.md)voor instructies over het testen van uw app met de testconsole.
