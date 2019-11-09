---
title: De Tekstanalyse-API aanroepen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Text Analytics REST API en postman kunt aanroepen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 5697ae4c8864e0b9c4cbfc9e1e1048e1c3d60f77
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837219"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>De Text Analytics aanroepen REST API

Aanroepen naar de **Text Analytics-API** zijn http post/Get-aanroepen, die u in elke taal kunt formuleren. In dit artikel gebruiken we REST en [postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) om de belangrijkste concepten te demonstreren.

Elke aanvraag moet uw toegangs sleutel en een HTTP-eind punt bevatten. Met het eind punt geeft u de regio op die u hebt gekozen tijdens de registratie, de service-URL en een resource die wordt gebruikt in de aanvraag: `sentiment`, `keyphrases`, `languages`en `entities`. 

Intrekken dat Text Analytics stateless is, zodat er geen gegevensassets zijn om te beheren. Uw tekst wordt geüpload, geanalyseerd na ontvangst en de resultaten worden direct naar de aanroepende toepassing geretourneerd.

> [!Tip]
> Voor eenmalige aanroepen om te zien hoe de API werkt, kunt u POST-aanvragen verzenden via de ingebouwde **API-test console**, die beschikbaar is op elke [API-document pagina](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Er is geen installatie en de enige vereisten zijn het plakken van een toegangs sleutel en de JSON-documenten in de aanvraag. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definitie van JSON-schema

De invoer moet JSON zijn in ongestructureerde tekst. XML wordt niet ondersteund. Het schema is eenvoudig, bestaande uit de elementen die in de volgende lijst worden beschreven. 

U kunt op dit moment dezelfde documenten verzenden voor alle Text Analytics bewerkingen: sentiment, sleutel woord groep, taal detectie en entiteits identificatie. (Het schema is waarschijnlijk in de toekomst afhankelijk van elke analyse.)

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevens type is teken reeks, maar in oefen document-Id's zijn meestal gehele getallen. | Vereist | Het systeem gebruikt de Id's die u opgeeft om de uitvoer te structureren. Taal codes, sleutel zinnen en sentiment-scores worden gegenereerd voor elke ID in de aanvraag.|
|`text` | Ongestructureerde onbewerkte tekst, Maxi maal 5.120 tekens. | Vereist | Voor taal detectie kan tekst in elke taal worden weer gegeven. Voor sentiment analyse, extractie van sleutel zinnen en Entiteits-ID moet de tekst in een [ondersteunde taal](../text-analytics-supported-languages.md)worden gesteld. |
|`language` | [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -code van 2 tekens voor een [ondersteunde taal](../text-analytics-supported-languages.md) | Varieert | Vereist voor sentiment analyse, extractie van sleutel zinnen en entiteits koppeling; optioneel voor taal detectie. Er is geen fout als u deze uitsluit, maar de analyse verzwakt. De taal code moet overeenkomen met de `text` die u opgeeft. |

Zie [Text Analytics overzicht > gegevens limieten](../overview.md#data-limits)voor meer informatie over limieten. 

## <a name="set-up-a-request-in-postman"></a>Een aanvraag instellen in postman

De service accepteert een aanvraag met een grootte van Maxi maal 1 MB. Als u Postman (of een ander web-API-test hulpprogramma) gebruikt, stelt u het eind punt in om de resource op te vragen die u wilt gebruiken en geeft u de toegangs sleutel op in een aanvraag header. Voor elke bewerking moet u de juiste resource toevoegen aan het eind punt. 

1. In postman:

   + Kies **bericht** als aanvraag type.
   + Plak het eind punt dat u hebt gekopieerd van de portal pagina.
   + Voeg een resource toe.

   Resource-eind punten zijn als volgt (uw regio kan variëren):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Stel de drie aanvraag headers in:

   + `Ocp-Apim-Subscription-Key`: uw toegangs sleutel, verkregen van Azure Portal.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Uw aanvraag moet er ongeveer uitzien als de volgende scherm afbeelding, uitgaande van een **/keyPhrases** -resource.

   ![Scherm opname van aanvraag met eind punt en kopteksten](../media/postman-request-keyphrase-1.png)

4. Klik op **hoofd tekst** en kies **RAW** voor de indeling.

   ![Scherm opname van aanvraag met hoofdtekst instellingen](../media/postman-request-body-raw.png)

5. Plak een aantal JSON-documenten in een indeling die geldig is voor de beoogde analyse. Zie de volgende onderwerpen voor meer informatie over een bepaalde analyse:

  + [Taal detectie](text-analytics-how-to-language-detection.md)  
  + [Extractie van sleutel woorden](text-analytics-how-to-keyword-extraction.md)  
  + [Sentiment analyse](text-analytics-how-to-sentiment-analysis.md)  
  + [Entiteit herkenning](text-analytics-how-to-entity-linking.md)  


6. Klik op **verzenden** om de aanvraag in te dienen. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor informatie over het aantal aanvragen dat u per minuut en seconde kunt verzenden.

   In postman wordt het antwoord in het volgende venster weer gegeven als één JSON-document met een item voor elke document-ID die in de aanvraag is opgenomen.

## <a name="see-also"></a>Zie ook 

 [Overzicht van Text Analytics](../overview.md)  
 [Veelgestelde vragen](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Taal detecteren](text-analytics-how-to-language-detection.md)
