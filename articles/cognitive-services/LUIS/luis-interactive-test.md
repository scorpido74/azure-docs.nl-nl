---
title: App testen in LUIS-portal
description: Gebruik Language Understanding (LUIS) om continu aan uw toepassing te werken om deze te verfijnen en het taalbegrip ervan te verbeteren.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219817"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Uw LUIS-app testen in de LUIS-portal

[Het testen van](luis-concept-test.md) een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voorbeelduitingen om te zien of de intenties en entiteiten correct worden herkend. Als dit niet het geval is, moet u de LUIS-app bijwerken, trainen en opnieuw testen.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Trainen voor het testen

Als u wilt testen op de meest recente versie van de actieve app, selecteert u **Trainen** in het bovenste menu voordat u deze test.

## <a name="test-an-utterance"></a>Een utterance testen

De testutterance mag niet precies hetzelfde zijn als voorbeelduitingen in de app. De testutterance moet woordkeuze, woordlengte en entiteitsgebruik bevatten dat u voor een gebruiker verwacht.

1. Toegang tot uw app door de naam ervan te selecteren op de pagina **Mijn apps.**

1. Als u toegang wilt krijgen tot het deelvenster Dia-out **testen,** selecteert u **Testen** in het bovenste deelvenster van uw toepassing.

    > [!div class="mx-imgBorder"]
    > ![Pagina & test-app trainen](./media/luis-how-to-interactive-test/test.png)

1. Voer een utterance in het tekstvak in en selecteer Enter. U zoveel testuitingen typen als u wilt in de **test,** maar slechts één utterance tegelijk.

1. De utterance, de bovenste intentie en de score worden toegevoegd aan de lijst met uitingen onder het tekstvak.

    ![Interactieve tests identificeert de verkeerde intentie](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Score inspecteren

U inspecteert de details van het testresultaat in het **deelvenster Inspect.**

1. Als het deelvenster Uitschuifing **testen** is geopend, selecteert u **Inspecteren** voor een utterance die u wilt vergelijken.

    ![De knop Inspecteren selecteren om meer details over de testresultaten te bekijken](./media/luis-how-to-interactive-test/inspect.png)

1. Het **inspectiepanel** verschijnt. Het paneel bevat de intentie voor de hoogste score en alle geïdentificeerde entiteiten. Het deelvenster toont het resultaat van de geselecteerde utterance.

    ![Het paneel bevat de intentie voor de hoogste score en alle geïdentificeerde entiteiten. Het deelvenster toont het resultaat van de geselecteerde utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Intentie voor topscores corrigeren

1. Als de intentie voor de hoogste score onjuist is, selecteert u de knop **Bewerken.**

1.  Selecteer in de vervolgkeuzelijst de juiste intentie voor de utterance.

    ![De juiste intentie selecteren](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Sentimentresultaten weergeven

Als **sentimentanalyse** is geconfigureerd op de pagina **[Publiceren,](luis-how-to-publish-app.md#enable-sentiment-analysis)** bevatten de testresultaten het sentiment in de utterance.

![Afbeelding van het deelvenster Testen met sentimentanalyse](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>De intentie van het overeenkomende patroon corrigeren

Als u [Patronen](luis-concept-patterns.md) gebruikt en de utterance overeenkomt met een patroon, maar de verkeerde intentie is voorspeld, selecteert u de koppeling **Bewerken** door het patroon en selecteert u de juiste intentie.

## <a name="compare-with-published-version"></a>Vergelijken met gepubliceerde versie

U de actieve versie van uw app testen met de gepubliceerde [eindpuntversie.](luis-glossary.md#endpoint) Selecteer **Vergelijken** met **gepubliceerd**in het deelvenster Inspecteren . Alle tests op het gepubliceerde model worden afgetrokken van uw Azure-abonnementsquotumsaldo.

![Vergelijk met gepubliceerd](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Endpoint JSON weergeven in testpaneel
U het eindpunt JSON weergeven dat is geretourneerd voor de vergelijking door de **weergave JSON weergeven te selecteren.**

![Gepubliceerde JSON-reactie](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Aanvullende instellingen in het testpaneel

### <a name="luis-endpoint"></a>LUIS-eindpunt

Als u meerdere LUIS-eindpunten hebt, gebruikt u de koppeling **Extra instellingen** in het deelvenster Gepubliceerd van de test om het eindpunt te wijzigen dat wordt gebruikt voor het testen. Als u niet zeker weet welk eindpunt u wilt gebruiken, selecteert u de **standaardStarter_Key**.

> [!div class="mx-imgBorder"]
> ![Testpaneel met koppeling Extra instellingen gemarkeerd](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batchgewijs testen
Bekijk [batchtestconcepten](luis-concept-batch-test.md) en leer [hoe u](luis-how-to-batch-test.md) een batch uitingen testen.

## <a name="next-steps"></a>Volgende stappen

Als uit testen blijkt dat uw LUIS-app de juiste intenties en entiteiten niet herkent, u de nauwkeurigheid van uw LUIS-app verbeteren door meer uitingen te labelen of functies toe te voegen.

* [Voorgestelde uitingen labelen met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)
