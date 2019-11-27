---
title: Verkeerd gespelde woorden corrigeren-LUIS
titleSuffix: Azure Cognitive Services
description: Juiste verkeerd gespelde woorden in uitingen door toe te voegen Bing spellingcontrole controleren-API-versie 7 op query's van LUIS-eindpunt.
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225451"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Juiste verkeerd gespelde woorden met Bing spellingcontrole

U kunt uw LUIS-app integreren met [Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) om verkeerd gespelde woorden in uitingen te corrigeren voordat Luis de score en entiteiten van de utterance voor spelt. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Maken van de eerste sleutel voor Bing spellingcontrole controleren versie 7

Uw [eerste Bing spellingcontrole-API V7-sleutel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) is gratis. 

![Gratis sleutel maken](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Eindpuntsleutel maken
Als uw gratis sleutel is verlopen, moet u de eindpuntsleutel van een maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer **een resource maken** in de linkerbovenhoek.

3. Typ `Bing Spell Check API V7` in het zoekvak.

    ![Zoeken naar Bing spellingcontrole-API versie 7 controleren](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecteer de service. 

5. Een paneel wordt weergegeven aan de rechterkant met gegevens, met inbegrip van de juridische kennisgeving. Selecteer **maken** om het proces voor het maken van het abonnement te starten. 

6. Voer uw service-instellingen in het volgende deelvenster. Wachten op de service maken van het proces is voltooid.

    ![Voer de service-instellingen](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecteer **alle resources** onder de titel **Favorieten** in de navigatie aan de linkerkant.

8. Selecteer de nieuwe service. Het type is **Cognitive Services** en de locatie is **globaal**. 

9. Selecteer in het hoofd paneel **sleutels** om uw nieuwe sleutels weer te geven.

    ![Sleutels ophalen](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieer de eerste sleutel. U hoeft slechts een van de twee sleutels. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toe te voegen aan de eindpunt-URL
De eindpunt-query moet de sleutel die wordt doorgegeven in de queryreeks-parameters voor elke query die u wilt correctie toepassen. U hebt mogelijk een chatbot waarmee LUIS wordt aangeroepen of u mogelijk het eindpunt LUIS API rechtstreeks aanroepen. Ongeacht hoe het eindpunt wordt aangeroepen, moet elke aanroep bevatten de benodigde informatie voor spellingcorrecties goed te laten werken.

De eindpunt-URL heeft verschillende waarden die moeten correct worden doorgegeven. De sleutel van Bing Spell Check-API voor Bing versie 7 is gewoon een van deze. U moet de para meter **Spelling** controle instellen op True en u moet de waarde van **Bing-spelling check-Subscription-Key** instellen op de sleutel waarde:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespelde utterance verzenden naar LUIS
1. Kopieer in een webbrowser de voor gaande teken reeks en vervang de `region`, `appId`, `luisKey`en `bingKey` door uw eigen waarden. Zorg ervoor dat u de regio van het eind punt gebruikt als deze niet overeenkomt met de publicatie [regio](luis-reference-regions.md).

2. Voeg een verkeerd gespelde utterance zoals 'hoe ver is de mountainn?'. In het Engels is `mountain`met één `n`de juiste spelling. 

3. Selecteer invoeren voor het verzenden van de query naar LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Spelfouten negeren

Als u de Bing Spellingcontrole-API V7-service niet wilt gebruiken, moet u de juiste en onjuiste spelling toevoegen. 

Twee oplossingen zijn:

* Voor beeld van een label uitingen die de verschillende spellingen hebben, zodat LUIS de juiste spelling en type fouten kan ontdekken. Deze optie vereist meer labelen inspanning dan het gebruik van een spellingcontrole.
* Een woordgroepen lijst maken met alle variaties van het woord. Met deze oplossing hoeft u geen label te maken voor de woord variaties in het voor beeld uitingen. 

## <a name="publishing-page"></a>Pagina publiceren
Op de [publicatie](luis-how-to-publish-app.md) pagina is het selectie vakje **Bing spelling controle inschakelen ingeschakeld** . Dit is nuttig om te maken van de sleutel en te begrijpen hoe de eindpunt-URL wordt gewijzigd. U moet nog steeds de juiste eindpunt-parameters gebruiken om spelling voor elke utterance gecorrigeerd. 

> [!div class="nextstepaction"]
> [Meer informatie over voorbeeld uitingen](luis-how-to-add-example-utterances.md)
