---
title: Uw LUIS-app testen
titleSuffix: Azure Cognitive Services
description: Testen is het proces van het verstrekken van voorbeelduitingen aan LUIS en het krijgen van een reactie van luis-erkende intents en entiteiten.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486680"
---
# <a name="testing-example-utterances-in-luis"></a>Voorbeelduitingen testen in LUIS

Testen is het proces van het verstrekken van voorbeelduitingen aan LUIS en het krijgen van een reactie van luis-erkende intents en entiteiten. 

U LUIS interactief testen, één utterance tegelijk, of uitingen geven. Tijdens het testen u de voorspellingsreactie van het huidige actieve model vergelijken met de voorspellingsrespons van het gepubliceerde model. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Wat is een score in het testen?
Zie [Voorspellingsscoreconcepten](luis-concept-prediction-score.md) voor meer informatie over voorspellingsscores.

## <a name="interactive-testing"></a>Interactief testen
Interactieve tests worden uitgevoerd vanuit het **testpaneel** van de LUIS-portal. U een utterance invoeren om te zien hoe intenties en entiteiten worden geïdentificeerd en gescoord. Als LUIS de intenties en entiteiten niet voorspelt zoals u verwacht bij een utterance in het testpaneel, kopieert u deze naar de **intent-pagina** als een nieuwe utterance. Label vervolgens de delen van die utterance voor entiteiten en train LUIS. 

## <a name="batch-testing"></a>Batchgewijs testen
Bekijk [batchtests](luis-concept-batch-test.md) als u meer dan één utterance tegelijk test.

## <a name="endpoint-testing"></a>Eindpunttesten
U testen met het [eindpunt](luis-glossary.md#endpoint) met maximaal twee versies van uw app. Voeg een tweede versie toe aan het eindpunt van de **uitvoering** van uw app als het eindpunt van de **productie.** Deze benadering geeft u drie versies van een utterance: het huidige model in het testvenster van de [LUIS-website](luis-reference-regions.md) en de twee versies op de twee verschillende eindpunten. 

Alle eindpunttests tellen mee voor uw gebruiksquotum. 

## <a name="do-not-log-tests"></a>Geen logboektests registreren
Als u test tegen een eindpunt en niet wilt dat `logging=false` de utterance wordt geregistreerd, moet u de configuratie van de querytekenreeks gebruiken.

## <a name="where-to-find-utterances"></a>Waar u uitingen vinden
LUIS slaat alle aangemelde uitingen op in het querylogboek, beschikbaar om te downloaden op de LUIS-portal vanaf de **lijstpagina Apps,** evenals de [LUIS-ontwerp-API's](https://go.microsoft.com/fwlink/?linkid=2092087). 

Alle uitingen waar LUIS niet zeker van is, worden vermeld op de pagina **[Eindpuntuitingen controleren](luis-how-to-review-endpoint-utterances.md)** van de [LUIS-website.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Vergeet niet om te trainen
Vergeet niet luis te [trainen](luis-how-to-train.md) nadat u wijzigingen aanbrengt in het model. Wijzigingen in de LUIS-app worden pas in de test gezien als de app is getraind. 

## <a name="best-practices"></a>Aanbevolen procedures
Leer [aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het testen van](luis-interactive-test.md) uw uitingen.
