---
title: Test-app in de portal van LUIS
titleSuffix: Azure Cognitive Services
description: Met Language Understanding (LUIS) kunt werken continu op uw toepassing het verfijnen en verbeteren van de taal begrijpen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221740"
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

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Aanvullende instellingen in het Configuratiescherm van de test

### <a name="luis-endpoint"></a>LUIS-eindpunt

Als u meerdere LUIS-eind punten hebt, gebruikt u de koppeling **extra instellingen** in het gepubliceerde deel venster van de test om het eind punt te wijzigen dat wordt gebruikt voor het testen. Als u niet zeker weet welk eind punt u moet gebruiken, selecteert u de standaard **Starter_Key**. 

> [!div class="mx-imgBorder"]
> ![test paneel met de koppeling extra instellingen gemarkeerd](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Batchgewijs testen
Zie [concepten](luis-concept-batch-test.md) voor batch tests en leer [hoe u](luis-how-to-batch-test.md) een batch-uitingen kunt testen.

## <a name="next-steps"></a>Volgende stappen

Als testen geeft aan dat uw LUIS-app niet wordt herkend door de juiste intenties en entiteiten, kunt u uw LUIS-app om nauwkeurigheid te verbeteren door meer uitingen labels of het toevoegen van functies werken. 

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md) 
