---
title: Voorspelling scores - LUIS
description: Een voorspellingsscore geeft de mate van vertrouwen aan die de LUIS API-service heeft voor voorspellingsresultaten, op basis van een uiting van een gebruiker.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 709a34f0a278d8a17267c7544583798d54167dad
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382367"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Voorspellingsscores geven voorspellingsnauwkeurigheid voor intentie en entiteiten aan

Een voorspellingsscore geeft de mate van vertrouwen aan die LUIS heeft voor voorspellingsresultaten van een uiting van een gebruiker.

Een voorspellingsscore ligt tussen nul (0) en één (1). Een voorbeeld van een zeer zelfverzekerde LUIS-score is 0,99. Een voorbeeld van een score van laag vertrouwen is 0,01.

|Scorewaarde|Betrouwbaarheid|
|--|--|
|1|bepaalde overeenkomst|
|0.99|hoog vertrouwen|
|0,01|weinig vertrouwen|
|0|definitief niet te evenaren|

## <a name="top-scoring-intent"></a>Intentie met hoogste score

Elke utterancevoorspelling retourneert een intentie die topscorer wordt. Deze voorspelling is een numerieke vergelijking van voorspellingsscores.

## <a name="proximity-of-scores-to-each-other"></a>Nabijheid van scores aan elkaar

De top 2 scores kunnen een zeer klein verschil tussen hen hebben. LUIS geeft niet aan dat deze nabijheid anders is dan het retourneren van de hoogste score.

## <a name="return-prediction-score-for-all-intents"></a>Return voorspelling score voor alle doeleinden

Een test- of eindpuntresultaat kan alle intenties bevatten. Deze configuratie is ingesteld op het eindpunt met behulp van de juiste querystringnaam/waardepaar.

|Voorspellings-API|Querytekenreeksnaam|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Intenties met vergelijkbare scores bekijken

Het controleren van de score voor alle intenties is een goede manier om te controleren of niet alleen de juiste intentie is geïdentificeerd, maar dat de volgende geïdentificeerde intentiescore aanzienlijk en consistent lager is voor uitingen.

Als meerdere intenties close prediction scores hebben, gebaseerd op de context van een utterance, kan LUIS schakelen tussen de intenties. Om deze situatie op te lossen, blijven uitingen toevoegen aan elke intentie met een breder scala aan contextuele verschillen of u de clienttoepassing, zoals een chatbot, programmatische keuzes laten maken over hoe om te gaan met de twee belangrijkste intents.

De 2 intenties, die te nauw zijn gescoord, kunnen omkeren als gevolg van **niet-deterministische training**. De hoogste score zou de tweede hoogste kunnen worden en de tweede hoogste score zou de eerste hoogste score kunnen worden. Als u deze situatie wilt voorkomen, voegt u voorbeelduitingen toe aan elk van de twee belangrijkste intenties voor die utterance met woordkeuze en context die de twee intenties onderscheidt. De twee intents moeten ongeveer hetzelfde aantal voorbeelduitingen hebben. Een vuistregel voor de scheiding om inversie als gevolg van training te voorkomen, is een verschil van 15% in scores.

U de **niet-deterministische training** uitschakelen door [te trainen met alle gegevens.](luis-how-to-train.md#train-with-all-data)

## <a name="differences-with-predictions-between-different-training-sessions"></a>Verschillen met voorspellingen tussen verschillende trainingssessies

Wanneer u hetzelfde model traint in een andere app en de scores niet hetzelfde zijn, is dit verschil omdat er **niet-deterministische training** is (een element van willekeur). Ten tweede betekent elke overlapping van een utterance naar meer dan één intentie dat de bovenste intentie voor dezelfde utterance kan veranderen op basis van training.

Als uw chatbot een specifieke LUIS-score vereist om vertrouwen in een intentie aan te geven, moet u het scoreverschil tussen de bovenste twee intents gebruiken. Deze situatie biedt flexibiliteit voor variaties in opleiding.

U de **niet-deterministische training** uitschakelen door [te trainen met alle gegevens.](luis-how-to-train.md#train-with-all-data)

## <a name="e-exponent-notation"></a>E (exponent) notatie

Voorspellingscores kunnen exponentnotatie gebruiken, die boven het `9.910309E-07`0-1-bereik _worden weergegeven,_ zoals . Deze score is een indicatie van een zeer **klein** aantal.

|E notatiescore |Werkelijke score|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Toepassingsinstellingen

Gebruik [toepassingsinstellingen](luis-reference-application-settings.md) om te bepalen hoe diacritics en interpunctie van invloed zijn op voorspellingsscores.

## <a name="next-steps"></a>Volgende stappen

Zie [Entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw LUIS-app.
