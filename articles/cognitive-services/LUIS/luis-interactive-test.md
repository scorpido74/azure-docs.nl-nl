---
title: App testen in de LUIS-Portal
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) om voortdurend aan uw toepassing te werken om deze te verfijnen en de taal inzichten te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500287"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Uw LUIS-app testen in de LUIS-Portal

Het [testen](luis-concept-test.md) van een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voor beeld-uitingen om te zien of de intenties en entiteiten goed zijn herkend. Als dat niet het geval is, moet u updates voor de LUIS-app, Train en test opnieuw uitvoeren. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Een utterance testen

1. U hebt toegang tot uw app door de naam ervan te selecteren op de pagina **mijn apps** . 

1. Als u toegang wilt krijgen tot het **toetsen paneel testen** , selecteert u **testen** in het bovenste paneel van de toepassing.

    ![App-pagina voor Train &-test](./media/luis-how-to-interactive-test/test.png)

1. Voer een utterance in het tekstvak in en selecteer ENTER. U kunt net zoveel test uitingen als u wilt in de **test**typen, maar slechts één utterance tegelijk.

1. De utterance, de belangrijkste intentie en de score worden toegevoegd aan de lijst met uitingen onder het tekstvak.

    ![Interactieve tests duiden het verkeerde doel aan](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Score controleren

U kunt de details van het test resultaat bekijken in het deel venster **inspecteren** . 
 
1. **Selecteer in** het deel venster voor het **testen** van de schuif regelaar naar een utterance die u wilt vergelijken. 

    ![Selecteer de knop controleren om meer informatie over de test resultaten weer te geven](./media/luis-how-to-interactive-test/inspect.png)

1. Het deel venster **inspectie** wordt weer gegeven. Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt het resultaat van de geselecteerde utterance weer gegeven.

    ![Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt het resultaat van de geselecteerde utterance weer gegeven.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Juiste score opzet corrigeren

1. Als het beste score doel onjuist is, selecteert u de knop **bewerken** .

1.  Selecteer in de vervolg keuzelijst de juiste intentie voor de utterance.

    ![Selecteer juiste intentie](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Sentiment-resultaten weer geven

Als **sentiment analyse** is geconfigureerd op de pagina **[publiceren](luis-how-to-publish-app.md#enable-sentiment-analysis)** , bevatten de test resultaten de sentiment die zijn gevonden in de utterance. 

![Afbeelding van test venster met analyse van sentiment](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Juist overeenkomend patroon intentie

Als u [patronen](luis-concept-patterns.md) gebruikt en de utterance overeenkomt met een patroon, maar de verkeerde intentie is voor speld, selecteert u de koppeling **bewerken** op basis van het patroon en selecteert u vervolgens de juiste intentie.

## <a name="compare-with-published-version"></a>Vergelijken met gepubliceerde versie

U kunt de actieve versie van uw app testen met de gepubliceerde versie van het [eind punt](luis-glossary.md#endpoint) . Selecteer in het deel venster **inspecteren** de optie **compare with published**. Elk testen op basis van het gepubliceerde model wordt in mindering gebracht op het quotum saldo van uw Azure-abonnement. 

![Vergelijken met gepubliceerde](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>JSON van eind punt weer geven in het test paneel
U kunt de eindpunt-JSON weer geven die is geretourneerd voor de vergelijking door de **weer gave JSON**weer geven te selecteren.

![Gepubliceerd JSON-antwoord](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Aanvullende instellingen in het test paneel

### <a name="luis-endpoint"></a>LUIS-eind punt

Als u meerdere LUIS-eind punten hebt, gebruikt u de koppeling **extra instellingen** in het gepubliceerde deel venster van de test om het eind punt te wijzigen dat wordt gebruikt voor het testen. Als u niet zeker weet welk eind punt u moet gebruiken, selecteert u de standaard **Starter_Key**. 

![Test paneel met de koppeling extra instellingen gemarkeerd](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Bing Spellingcontrole correcties bekijken in het deel venster testen

Vereisten voor het weer geven van de spelling correcties: 

* Gepubliceerde app
* [Service sleutel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)van Bing spellingcontrole. De service sleutel is niet opgeslagen en moet opnieuw worden ingesteld voor elke browser sessie. 

Gebruik de volgende procedure om de [Bing spellingcontrole V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -service toe te voegen aan de resultaten van het test venster. 

1. Voer een utterance in het deel venster **test** in. Wanneer de utterance is voor speld, selecteert u **[controleren](#inspect-score)** onder de utterance die u hebt ingevoerd. 

1. Wanneer het deel venster **inspecteren** wordt geopend, selecteert u **[vergelijken met gepubliceerd](#compare-with-published-version)** . 

1. Wanneer het deel venster **gepubliceerd** wordt geopend, selecteert u **[aanvullende instellingen](#additional-settings-in-test-panel)** .

1. Schakel in het pop-upvenster het selectie vakje **Bing spellingcontrole** in en voer de sleutel in en selecteer vervolgens **gereed**. 
    ![Bing Spellingcontrole service sleutel](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png) invoeren

1. Voer een query in met een onjuiste spelling, zoals `book flite to seattle` en selecteer ENTER. De onjuiste spelling van het woord `flite` wordt vervangen in de query die wordt verzonden naar LUIS en de resulterende JSON toont zowel de oorspronkelijke query als `query`, en de gecorrigeerde spelling in de query, als `alteredQuery`.

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

## <a name="batch-testing"></a>Batchgewijs testen
Zie [concepten](luis-concept-batch-test.md) voor batch tests en leer [hoe u](luis-how-to-batch-test.md) een batch-uitingen kunt testen.

## <a name="next-steps"></a>Volgende stappen

Als met testen wordt aangegeven dat uw LUIS-app de juiste intenties en entiteiten niet herkent, kunt u werken om de nauw keurigheid van de LUIS-app te verbeteren door meer uitingen te labelen of functies toe te voegen. 

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md) 
