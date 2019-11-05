---
title: Verkeerd gespelde woorden corrigeren-LUIS
titleSuffix: Azure Cognitive Services
description: Corrigeer verkeerd gespelde woorden in uitingen door Bing Spellingcontrole-API V7 toe te voegen aan LUIS-eindpunt query's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499020"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Verkeerd gespelde woorden corrigeren met Bing Spellingcontrole

U kunt uw LUIS-app integreren met [Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) om verkeerd gespelde woorden in uitingen te corrigeren voordat Luis de score en entiteiten van de utterance voor spelt. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Eerste sleutel voor Bing Spellingcontrole V7 maken
Uw [eerste Bing spellingcontrole-API V7-sleutel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) is gratis. 

![Gratis sleutel maken](./media/luis-tutorial-bing-spellcheck/free-key.png)

< een naam ' Create-Subscription-key ' ></a>
## <a name="create-endpoint-key"></a>Eindpunt sleutel maken
Als uw gratis sleutel is verlopen, maakt u een eindpunt sleutel.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer **een resource maken** in de linkerbovenhoek.

3. Typ `Bing Spell Check API V7` in het zoekvak.

    ![Zoeken naar Bing Spellingcontrole-API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecteer de service. 

5. Er wordt rechts een informatie venster weer gegeven met daarin informatie, inclusief de juridische kennisgeving. Selecteer **maken** om het proces voor het maken van het abonnement te starten. 

6. Geef in het volgende deel venster uw service-instellingen op. Wacht totdat het proces voor het maken van de service is voltooid.

    ![Service-instellingen invoeren](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecteer **alle resources** onder de titel **Favorieten** in de navigatie aan de linkerkant.

8. Selecteer de nieuwe service. Het type is **Cognitive Services** en de locatie is **globaal**. 

9. Selecteer in het hoofd paneel **sleutels** om uw nieuwe sleutels weer te geven.

    ![Sleutels ophalen](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieer de eerste sleutel. U hebt slechts een van de twee sleutels nodig. 

## <a name="using-the-key-in-luis-test-panel"></a>De sleutel gebruiken in LUIS test panel
Er zijn twee locaties in LUIS om de sleutel te gebruiken. De eerste bevindt zich in het [test paneel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). De sleutel wordt niet opgeslagen in LUIS maar in plaats daarvan is een sessie variabele. U moet de sleutel telkens instellen wanneer u wilt dat in het test paneel de Bing Spellingcontrole-API V7-service op de utterance wordt toegepast. Zie de [instructies](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in het test paneel voor het instellen van de sleutel.

## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toevoegen aan de eind punt-URL
Voor de eindpunt query moet de sleutel worden door gegeven in de query teken reeks parameters voor elke query waarop u de spelling correctie wilt Toep assen. Mogelijk hebt u een chatbot die LUIS aanroept of u kunt de LUIS endpoint API rechtstreeks aanroepen. Ongeacht hoe het eind punt wordt aangeroepen, moeten elke aanroep de vereiste informatie bevatten die de spelling correcties goed kunnen uitvoeren.

De eind punt-URL heeft verschillende waarden die correct moeten worden door gegeven. De Bing Spellingcontrole-API V7-sleutel is slechts een van deze. U moet de para meter **Spelling** controle instellen op True en u moet de waarde van **Bing-spelling check-Subscription-Key** instellen op de sleutel waarde:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespeld utterance naar LUIS verzenden
1. Kopieer in een webbrowser de voor gaande teken reeks en vervang de `region`, `appId`, `luisKey`en `bingKey` door uw eigen waarden. Zorg ervoor dat u de regio van het eind punt gebruikt als deze niet overeenkomt met de publicatie [regio](luis-reference-regions.md).

2. Voeg een verkeerd gespelde utterance toe, zoals ' hoe ver is de mountainn? '. In het Engels is `mountain`met één `n`de juiste spelling. 

3. Selecteer ENTER om de query naar LUIS te verzenden.

4. LUIS reageert met een JSON-resultaat voor `How far is the mountain?`. Als Bing Spellingcontrole-API V7 een verkeerde spelling detecteert, bevat het veld `query` in het JSON-antwoord van de LUIS-app de oorspronkelijke query en bevat het veld `alteredQuery` de gecorrigeerde query die wordt verzonden naar LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Spel fouten negeren

Als u de Bing Spellingcontrole-API V7-service niet wilt gebruiken, moet u de juiste en onjuiste spelling toevoegen. 

Twee oplossingen zijn:

* Voor beeld van een label uitingen die de verschillende spellingen hebben, zodat LUIS de juiste spelling en type fouten kan ontdekken. Voor deze optie is meer inspanning vereist dan het gebruik van een spelling controle.
* Een woordgroepen lijst maken met alle variaties van het woord. Met deze oplossing hoeft u geen label te maken voor de woord variaties in het voor beeld uitingen. 

## <a name="publishing-page"></a>Publicatie pagina
Op de [publicatie](luis-how-to-publish-app.md) pagina is het selectie vakje **Bing spelling controle inschakelen ingeschakeld** . Dit is een handige manier om de sleutel te maken en te begrijpen hoe de eind punt-URL verandert. U moet nog steeds de juiste eindpunt parameters gebruiken om de spelling voor elke utterance te corrigeren. 

> [!div class="nextstepaction"]
> [Meer informatie over voorbeeld uitingen](luis-how-to-add-example-utterances.md)
