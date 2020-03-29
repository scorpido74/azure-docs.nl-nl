---
title: Offline evaluatie uitvoeren - Personalizer
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u offline evaluatie gebruiken om de effectiviteit van uw app te meten en uw leerlus te analyseren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622783"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analyseer uw leerlus met een offline evaluatie

Meer informatie over het voltooien van een offline evaluatie en het begrijpen van de resultaten.

Met offlineevaluaties u meten hoe effectief Personalizer is in vergelijking met het standaardgedrag van uw toepassing, leren welke functies het meest bijdragen aan personalisatie en automatisch nieuwe machine learning-waarden ontdekken.

Lees meer over [offline evaluaties](concepts-offline-evaluation.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een geconfigureerde personalizerlus
* De Personalizer-lus moet een representatieve hoeveelheid gegevens hebben - als honkbalveld raden we ten minste 50.000 gebeurtenissen in de logboeken aan voor zinvolle evaluatieresultaten. Optioneel u ook eerder geëxporteerde _leerbeleidsbestanden_ hebben die u vergelijken en testen in dezelfde evaluatie.

## <a name="run-an-offline-evaluation"></a>Een offline evaluatie uitvoeren

1. Zoek in de [Azure-portal](https://azure.microsoft.com/free/)uw Personalizer-bron.
1. Ga in de Azure-portal naar de sectie **Evaluaties** en selecteer **Evaluatie maken**.
    ![Ga in de Azure-portal naar de sectie **Evaluaties** en selecteer **Evaluatie maken**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configureer de volgende waarden:

    * Een evaluatienaam.
    * Begin- en einddatum - dit zijn datums die het bereik van de gegevens opgeven dat moet worden gebruikt in de evaluatie. Deze gegevens moeten aanwezig zijn in de logboeken, zoals aangegeven in de waarde [voor gegevensbewaring.](how-to-settings.md)
    * Optimalisatie Discovery ingesteld op **ja**.

    > [!div class="mx-imgBorder"]
    > ![Offline evaluatie-instellingen kiezen](./media/offline-evaluation/create-an-evaluation-form.png)

1. Start de evaluatie door **Ok te**selecteren .

## <a name="review-the-evaluation-results"></a>De evaluatieresultaten bekijken

Evaluaties kunnen lang duren, afhankelijk van de hoeveelheid gegevens die moet worden verwerkt, het aantal leerbeleidte vergelijken en of een optimalisatie is aangevraagd.

Na voltooiing u de evaluatie selecteren in de lijst met evaluaties en **vervolgens de score van uw toepassing vergelijken met andere potentiële leerinstellingen selecteren.** Selecteer deze functie wanneer u wilt zien hoe uw huidige leerbeleid presteert in vergelijking met een nieuw beleid.

1. Bekijk de uitvoering van het [leerbeleid](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Evaluatieresultaten bekijken](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selecteer **Toepassen** om het beleid toe te passen dat het model het beste voor uw gegevens verbetert.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hoe offline evaluaties werken.](concepts-offline-evaluation.md)
