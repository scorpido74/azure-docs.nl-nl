---
title: Offline-evaluatie-persoonlijker
titleSuffix: Azure Cognitive Services
description: Meer informatie over het analyseren van uw leer proces met een offline-evaluatie
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884485"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analyseer uw leer proces met een offline-evaluatie

Meer informatie over het volt ooien van een offline-evaluatie en het begrijpen van de resultaten.

Met offline-evaluaties kunt u meten hoe effectief Personaler wordt vergeleken met het standaard gedrag van uw toepassing, hoe functies het meest aan persoonlijke voor keur hebben en nieuwe machine learning-instellingen automatisch detecteren.

Lees meer over [offline-evaluaties](concepts-offline-evaluation.md) voor meer informatie.


## <a name="prerequisites"></a>Vereisten

* Een geconfigureerde aangepaste lus
* De Personaler-lus moet beschikken over een representatieve hoeveelheid gegevens-als een indicatieve. we raden ten minste 50.000 gebeurtenissen in de logboeken aan voor zinvolle evaluatie resultaten. U kunt eventueel ook eerder de _Learning-beleids_ bestanden hebben geëxporteerd die u in dezelfde evaluatie wilt vergelijken en testen.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Stappen voor het starten van een nieuwe offline-evaluatie

1. Zoek uw persoonlijke bron in het [Azure Portal](https://azure.microsoft.com/free/).
1. Ga in het Azure Portal naar de sectie **evaluaties** en selecteer **evaluatie maken**.
    ![Ga in het Azure Portal naar de sectie * *-evaluaties * * en selecteer * * evaluatie maken * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Selecteer de volgende instellingen:

    * Een evaluatie naam
    * Begin-en eind datum: Dit zijn datums in het verleden die het bereik opgeven van de gegevens die in de evaluatie moeten worden gebruikt. Deze gegevens moeten aanwezig zijn in de logboeken, zoals opgegeven in de instelling voor het [bewaren van gegevens](how-to-settings.md) .
    * Optimalisatie detectie is ingesteld op **Ja**

    ![Instellingen voor offline-evaluatie kiezen](./media/offline-evaluation/create-an-evaluation-form.png)

1. Start de evaluatie door **OK**te selecteren. 

## <a name="results"></a>Resultaten

Het uitvoeren van evaluaties kan veel tijd in beslag nemen, afhankelijk van de hoeveelheid gegevens die moet worden verwerkt, het aantal trainings beleid dat moet worden vergeleken en of er een optimalisatie is aangevraagd.

Zodra het is voltooid, kunt u de evaluatie selecteren in de lijst met evaluaties. 

Vergelijkingen van het trainings beleid zijn onder andere:

* **Online beleid**: Het huidige leer beleid dat wordt gebruikt in Personaler
* **Basis lijn**: De standaard waarde van de toepassing (zoals bepaald door de eerste actie die in Rangings aanroepen wordt verzonden),
* **Wille keurig beleid**: Een imaginair positie gedrag dat altijd een wille keurige keuze van de acties retourneert uit de opgegeven records.
* **Aangepast beleid**: Er zijn extra leer beleid geüpload bij het starten van de evaluatie.
* **Geoptimaliseerd beleid**: Als de evaluatie is gestart met de optie om een geoptimaliseerd beleid te detecteren, wordt het ook vergeleken en kunt u het downloaden of het online leer beleid maken, waarbij de huidige wordt vervangen.

![Resultaten grafiek van instellingen voor offline-evaluatie](./media/offline-evaluation/evaluation-results.png)

Effectiviteit van [functies](concepts-features.md) voor acties en context.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het werken met offline-evaluaties](concepts-offline-evaluation.md).
