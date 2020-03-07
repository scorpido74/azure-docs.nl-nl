---
title: Test-app in de portal van LUIS
description: Met Language Understanding (LUIS) kunt werken continu op uw toepassing het verfijnen en verbeteren van de taal begrijpen.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361099"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Uw LUIS-app testen in de LUIS-Portal

Het [testen](luis-concept-test.md) van een app is een iteratief proces. Na het trainen van uw LUIS-app, door deze te testen met voorbeeldgegevens uitingen om te controleren of de intenties en entiteiten correct worden herkend. Als ze niet zijn, moet u opnieuw updates voor de LUIS-app, trainen en testen.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Trainen voor testen

Als u wilt testen op de meest recente versie van de actieve app, selecteert u **trainen** in het bovenste menu voordat u gaat testen.

## <a name="test-an-utterance"></a>Een utterance testen

De test utterance mag niet exact hetzelfde zijn als een voor beeld van een uitingen in de app. De test utterance moet de woord keuze, de woord lengte en het entiteits gebruik bevatten die u verwacht voor een gebruiker.

1. U hebt toegang tot uw app door de naam ervan te selecteren op de pagina **mijn apps** .

1. Als u toegang wilt krijgen tot het **toetsen paneel testen** , selecteert u **testen** in het bovenste paneel van de toepassing.

    > [!div class="mx-imgBorder"]
    > ![Train & app-pagina testen](./media/luis-how-to-interactive-test/test.png)

1. Voer een utterance in het tekstvak in en selecteer Enter. U kunt net zoveel test uitingen als u wilt in de **test**typen, maar slechts één utterance tegelijk.

1. De utterance, de belangrijkste doel en de score zijn toegevoegd aan de lijst met uitingen onder het tekstvak.

    ![Interactieve testen identificeert de verkeerde bedoelingen](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Controleren van score

U kunt de details van het test resultaat bekijken in het deel venster **inspecteren** .

1. **Selecteer in** het deel venster voor het **testen** van de schuif regelaar naar een utterance die u wilt vergelijken.

    ![Selecteer de knop controleren om meer informatie over de test resultaten weer te geven](./media/luis-how-to-interactive-test/inspect.png)

1. Het deel venster **inspectie** wordt weer gegeven. Het deelvenster bevat de scoring-doel, evenals de geïdentificeerde entiteiten boven. Het deelvenster toont het resultaat van de geselecteerde utterance.

    ![Het deelvenster bevat de scoring-doel, evenals de geïdentificeerde entiteiten boven. Het deelvenster toont het resultaat van de geselecteerde utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Juiste boven scoring-doel

1. Als het beste score doel onjuist is, selecteert u de knop **bewerken** .

1.  Selecteer in de vervolgkeuzelijst het juiste type voor de utterance.

    ![Juiste doel selecteren](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Sentiment-resultaten weergeven

Als **sentiment analyse** is geconfigureerd op de pagina **[publiceren](luis-how-to-publish-app.md#enable-sentiment-analysis)** , bevatten de test resultaten de sentiment die zijn gevonden in de utterance.

![Afbeelding van het testvenster met sentimentanalyse](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corrigeer de bedoeling komt overeen met patroon

Als u [patronen](luis-concept-patterns.md) gebruikt en de utterance overeenkomt met een patroon, maar de verkeerde intentie is voor speld, selecteert u de koppeling **bewerken** op basis van het patroon en selecteert u vervolgens de juiste intentie.

## <a name="compare-with-published-version"></a>Vergelijken met de gepubliceerde versie

U kunt de actieve versie van uw app testen met de gepubliceerde versie van het [eind punt](luis-glossary.md#endpoint) . Selecteer in het deel venster **inspecteren** de optie **compare with published**. Alle testen op basis van het gepubliceerde model wordt afgetrokken van saldo van uw Azure-abonnement quotum.

![Vergelijken met gepubliceerd](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Eindpunt JSON in test-deelvenster weergeven
U kunt de eindpunt-JSON weer geven die is geretourneerd voor de vergelijking door de **weer gave JSON**weer geven te selecteren.

![Gepubliceerde JSON-antwoord](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Aanvullende instellingen in het Configuratiescherm van de test

### <a name="luis-endpoint"></a>LUIS-eindpunt

Als u meerdere LUIS-eind punten hebt, gebruikt u de koppeling **extra instellingen** in het gepubliceerde deel venster van de test om het eind punt te wijzigen dat wordt gebruikt voor het testen. Als u niet zeker weet welk eind punt u moet gebruiken, selecteert u de standaard **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![test paneel met de koppeling extra instellingen gemarkeerd](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batchgewijs testen
Zie [concepten](luis-concept-batch-test.md) voor batch tests en leer [hoe u](luis-how-to-batch-test.md) een batch-uitingen kunt testen.

## <a name="next-steps"></a>Volgende stappen

Als testen geeft aan dat uw LUIS-app niet wordt herkend door de juiste intenties en entiteiten, kunt u uw LUIS-app om nauwkeurigheid te verbeteren door meer uitingen labels of het toevoegen van functies werken.

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)
