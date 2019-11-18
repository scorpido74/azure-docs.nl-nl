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
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 238b76040559148c48aa67b99e856a5987b71a7e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123157"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Juiste verkeerd gespelde woorden met Bing spellingcontrole

U kunt uw LUIS-app met integreren [Bing spellingcontrole controleren-API-versie 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) verkeerd gespelde woorden in uitingen corrigeren voordat de score en de entiteiten van de utterance LUIS worden voorspeld. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Maken van de eerste sleutel voor Bing spellingcontrole controleren versie 7
Uw [eerste sleutel voor Bing Spell Check-API voor Bing versie 7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) is gratis. 

![Gratis sleutel maken](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Eindpuntsleutel maken
Als uw gratis sleutel is verlopen, moet u de eindpuntsleutel van een maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer **een resource maken** in de linkerbovenhoek.

3. Typ `Bing Spell Check API V7` in het zoekvak.

    ![Zoeken naar Bing spellingcontrole-API versie 7 controleren](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecteer de service. 

5. Een paneel wordt weergegeven aan de rechterkant met gegevens, met inbegrip van de juridische kennisgeving. Selecteer **maken** om te beginnen met het proces voor het abonnement maken. 

6. Voer uw service-instellingen in het volgende deelvenster. Wachten op de service maken van het proces is voltooid.

    ![Voer de service-instellingen](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecteer **alle resources** onder de **Favorieten** titel op de navigatiebalk links.

8. Selecteer de nieuwe service. Het type **Cognitive Services** en de locatie is **globale**. 

9. Selecteer in het hoofdpaneel **sleutels** om te zien van uw nieuwe sleutels.

    ![Sleutels ophalen](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieer de eerste sleutel. U hoeft slechts een van de twee sleutels. 

## <a name="using-the-key-in-luis-test-panel"></a>Met behulp van de sleutel in Configuratiescherm van LUIS-test
Er zijn twee plaatsen in LUIS om de sleutel te gebruiken. De eerste is in de [test deelvenster](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). De sleutel wordt niet opgeslagen in LUIS, maar in plaats daarvan wordt een variabele van sessie. U moet de sleutel telkens wanneer u het deelvenster test toe te passen van de Bing Spell Check-API voor Bing versie 7-service op de utterance wilt instellen. Zie [instructies](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in het deelvenster test voor het instellen van de sleutel.

## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toe te voegen aan de eindpunt-URL
De eindpunt-query moet de sleutel die wordt doorgegeven in de queryreeks-parameters voor elke query die u wilt correctie toepassen. U hebt mogelijk een chatbot waarmee LUIS wordt aangeroepen of u mogelijk het eindpunt LUIS API rechtstreeks aanroepen. Ongeacht hoe het eindpunt wordt aangeroepen, moet elke aanroep bevatten de benodigde informatie voor spellingcorrecties goed te laten werken.

De eindpunt-URL heeft verschillende waarden die moeten correct worden doorgegeven. De sleutel van Bing Spell Check-API voor Bing versie 7 is gewoon een van deze. Moet u instellen de **spellingcontrole** parameter in op true en u moet de waarde van **bing-spellingcontrole-controle-subscription-key** aan de waarde van de sleutel:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespelde utterance verzenden naar LUIS
1. In een webbrowser, Kopieer de bovenstaande tekenreeks en vervang de `region`, `appId`, `luisKey`, en `bingKey` door uw eigen waarden. Zorg ervoor dat u het eindpunt van de regio als deze van uw publicatie verschilt [regio](luis-reference-regions.md).

2. Voeg een verkeerd gespelde utterance zoals 'hoe ver is de mountainn?'. In het Engels, `mountain`, met een `n`, is de juiste spelling. 

3. Selecteer invoeren voor het verzenden van de query naar LUIS.

4. LUIS reageert met een JSON-resultaat voor `How far is the mountain?`. Als de Bing Spell Check-API voor Bing versie 7 spelfout, detecteert de `query` bevat de oorspronkelijke query het veld in de JSON-antwoord van de LUIS-app en de `alteredQuery` veld bevat de gecorrigeerde query verzonden naar LUIS.

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
De [publishing](luis-how-to-publish-app.md) op de pagina een **inschakelen Bing spellingcontrole** selectievakje. Dit is nuttig om te maken van de sleutel en te begrijpen hoe de eindpunt-URL wordt gewijzigd. U moet nog steeds de juiste eindpunt-parameters gebruiken om spelling voor elke utterance gecorrigeerd. 

> [!div class="nextstepaction"]
> [Meer informatie over de voorbeeld-uitingen](luis-how-to-add-example-utterances.md)
