---
title: Foutgespelde woorden corrigeren - LUIS
titleSuffix: Azure Cognitive Services
description: Corrigeer verkeerd gespelde woorden in uitingen door Bing Spell Check API V7 toe te voegen aan LUIS-eindpuntquery's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74225451"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Verkeerd gespelde woorden corrigeren met Bing Spell Check

U uw LUIS-app integreren met [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) om verkeerd gespelde woorden in uitingen te corrigeren voordat LUIS de score en entiteiten van de utterance voorspelt. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Eerste sleutel maken voor Bing Spell Check V7

Je [eerste Bing Spell Check API v7-toets](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) is gratis. 

![Gratis sleutel maken](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Eindpunttoets maken
Als uw gratis sleutel is verlopen, maakt u een eindpuntsleutel.

1. Log in bij de [Azure-portal](https://portal.azure.com). 

2. Selecteer **Een resource maken** in de linkerbovenhoek.

3. Typ `Bing Spell Check API V7` in het zoekvak.

    ![Zoeken naar Bing Spell Check API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecteer de service. 

5. Rechts verschijnt een informatiepaneel met informatie, waaronder de juridische kennisgeving. Selecteer **Maken** om het proces voor het maken van abonnementen te starten. 

6. Voer in het volgende deelvenster uw service-instellingen in. Wacht tot het proces voor het maken van service is voltooid.

    ![Service-instellingen invoeren](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecteer **Alle bronnen** onder de titel **Favorieten** aan de linkerkant.

8. Selecteer de nieuwe service. Het type is **Cognitive Services** en de locatie is **globaal.** 

9. Selecteer **Toetsen om** uw nieuwe toetsen te zien in het hoofdpaneel.

    ![Sleutels grijpen](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieer de eerste sleutel. Je hebt maar één van de twee sleutels nodig. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toevoegen aan de URL van het eindpunt
De eindpuntquery heeft de sleutel nodig die is doorgegeven in de querytekenreeksparameters voor elke query die u spellingcorrectie wilt toepassen. U hebt mogelijk een chatbot die LUIS aanroept of u de LUIS-eindpuntAPI rechtstreeks aanroepen. Ongeacht hoe het eindpunt wordt aangeroepen, moet elke oproep de vereiste informatie bevatten om spellingcorrecties goed te laten werken.

De URL van het eindpunt heeft verschillende waarden die correct moeten worden doorgegeven. De Bing Spell Check API v7-toets is gewoon een van deze. U moet de parameter **spellCheck** op true instellen en u moet de waarde van **bing-spell-check-subscription-key** instellen op de sleutelwaarde:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespelde uitingen naar LUIS verzenden
1. Kopieer in een webbrowser de voorgaande `region`tekenreeks `appId` `luisKey`en `bingKey` vervang de , , en met uw eigen waarden. Zorg ervoor dat u het eindpuntgebied gebruikt als dit verschilt van uw [publicatiegebied](luis-reference-regions.md).

2. Voeg een verkeerd gespelde uiting toe, zoals "Hoe ver is de mountainn?". In het `mountain`Engels, `n`met een , is de juiste spelling. 

3. Selecteer enter om de query naar LUIS te verzenden.

4. LUIS reageert met een `How far is the mountain?`JSON resultaat voor . Als Bing Spell Check API v7 `query` een spelfout detecteert, bevat het veld in het `alteredQuery` JSON-antwoord van de LUIS-app de oorspronkelijke query en bevat het veld de gecorrigeerde query die naar LUIS is verzonden.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Spelfouten negeren

Als u de Bing Spell Check API v7-service niet wilt gebruiken, moet u de juiste en onjuiste spelling toevoegen. 

Twee oplossingen zijn:

* Label voorbeeld uitingen die de alle verschillende spellingen hebben, zodat LUIS kan leren juiste spelling en typefouten. Deze optie vereist meer etiketteringsinspanning dan het gebruiken van een spellingscontrole.
* Maak een woordenlijst met alle varianten van het woord. Met deze oplossing hoeft u de woordvariaties in de voorbeelduitingen niet te labelen. 

## <a name="publishing-page"></a>Publicatiepagina
De [publicatiepagina](luis-how-to-publish-app.md) heeft een selectievakje **Bing-spellingcontrole inschakelen.** Dit is een gemak om de sleutel te maken en te begrijpen hoe de URL van het eindpunt verandert. U moet nog steeds de juiste eindpuntparameters gebruiken om de spelling voor elke utterance te laten corrigeren. 

> [!div class="nextstepaction"]
> [Meer informatie over voorbeelduitingen](luis-how-to-add-example-utterances.md)
