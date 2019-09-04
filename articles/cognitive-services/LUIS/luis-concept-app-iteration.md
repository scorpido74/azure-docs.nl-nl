---
title: Herhalend app-ontwerp-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256919"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ontwerp cyclus voor uw LUIS-app
LUIS leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's. 

![Ontwerpcyclus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Het bouwen van een LUIS-model
Doel van het model is om te achterhalen wat de gebruiker wordt gevraagd (de bedoeling of kunt u lezen wat) en bepaalt u welke onderdelen van de vraag geven details (entiteiten) die helpen bij het bepalen van het antwoord. 

Het model moet specifiek zijn voor het app-domein om te bepalen van woorden en woordgroepen dat zijn relevant zijn als normale word bestellen. 

Het model vereist intenties en _moet entiteiten hebben_ . 

## <a name="add-training-examples"></a>Voorbeelden van training toevoegen
LUIS moet voorbeeld uitingen in de intents. De voorbeelden moeten voldoende variatie van word keuze en woordvolgorde kunnen om te bepalen op welk doel de utterance is bedoeld voor. Elke voorbeeld-utterance moet alle vereiste gegevens gelabeld als entiteiten bevatten. 

U LUIS negeren uitingen die niet relevant zijn voor het domein van uw app door toe te wijzen de utterance te instrueren de **geen** intentie. Woorden of zinsdelen u niet hoeft opgehaald uit een utterance hoeft te worden met het label. Er is geen label naar woorden of zinsdelen te negeren. 

## <a name="train-and-publish-the-app"></a>De app trainen en publiceren
Wanneer u 15 tot 30 verschillende uitingen in elke intentie hebt, met de vereiste entiteiten met het label, moet u de [training](luis-how-to-train.md) vervolgens [publiceren](luis-how-to-publish-app.md). Gebruik de koppeling om op te halen van de eindpunten van de melding publiceren geslaagd. Zorg ervoor dat u uw app maakt en publiceert zodat deze beschikbaar is in de [eindpunt regio's](luis-reference-regions.md) die u nodig hebt. 

## <a name="https-prediction-endpoint-testing"></a>HTTPS-voor spelling-eindpunt test
U kunt uw LUIS-app testen vanuit het HTTPS prediction-eind punt. Door te testen vanaf het Voorspellings eindpunt kan LUIS een wille keurige uitingen met lage betrouw baarheid kiezen voor [controle](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Prullenbak

Wanneer u klaar bent met een cyclus van het ontwerpen, kunt u opnieuw beginnen. Begin met het [controleren van voorspellings eindpunt uitingen](luis-how-to-review-endpoint-utterances.md) Luis gemarkeerd met lage betrouw baarheid. Controleer deze uitingen voor zowel de intentie en de entiteit. Zodra u uitingen bekijkt, moet de beoordeling-lijst niet leeg zijn.  

U kunt de huidige versie in een nieuwe versie [klonen](luis-concept-version.md#clone-a-version) en vervolgens de ontwerp wijzigingen in de nieuwe versie starten. 

## <a name="batch-testing"></a>Batchgewijs testen

[Batch tests](luis-concept-batch-test.md) zijn een manier om te zien hoeveel voor beeld-uitingen worden beoordeeld door Luis. De voorbeelden moeten bekend bent met LUIS en correct moeten worden voorzien van intentie en entiteiten die u wilt dat LUIS om te zoeken. De testresultaten geven aan hoe goed LUIS op die set met uitingen zou uitvoeren. 

## <a name="next-steps"></a>Volgende stappen

Kennis met concepten over [samenwerking](luis-concept-keys.md).
