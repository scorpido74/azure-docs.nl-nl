---
title: De Tekstanalyse-API aanroepen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Text Analytics REST API en Postman aanroepen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219300"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>De API voor Text Analytics REST aanroepen

Oproepen naar de **Text Analytics API** zijn HTTP POST/GET-oproepen, die u in elke taal formuleren. In dit artikel gebruiken we REST en [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) om belangrijke concepten te demonstreren.

Elke aanvraag moet uw toegangssleutel en een HTTP-eindpunt bevatten. Het eindpunt geeft het gebied op dat u hebt gekozen tijdens het `sentiment`aanmelden, `languages`de `entities`url van de service en een resource die op de aanvraag wordt gebruikt: , `keyphrases`, en . 

Bedenk dat Text Analytics stateloos is, zodat er geen gegevenselementen zijn om te beheren. Uw tekst wordt geüpload, geanalyseerd na ontvangst en de resultaten worden onmiddellijk teruggestuurd naar de oproeptoepassing.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Json-schemadefinitie

Invoer moet JSON zijn in ruwe ongestructureerde tekst. XML wordt niet ondersteund. Het schema is eenvoudig, bestaande uit de elementen die in de volgende lijst worden beschreven. 

U momenteel dezelfde documenten indienen voor alle Text Analytics-bewerkingen: sentiment, sleutelzin, taaldetectie en entiteitsidentificatie. (Het schema zal waarschijnlijk variëren voor elke analyse in de toekomst.)

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevenstype is tekenreeks, maar in de praktijk zijn document-id's meestal gehele getallen. | Vereist | Het systeem maakt gebruik van de id's die u verstrekt om de output te structureren. Taalcodes, sleutelzinnen en sentimentscores worden gegenereerd voor elke ID in het verzoek.|
|`text` | Ongestructureerde ruwe tekst, maximaal 5.120 tekens. | Vereist | Voor taaldetectie kan tekst in elke taal worden uitgedrukt. Voor sentimentanalyse, sleutelzinextractie en entiteitsidentificatie moet de tekst in een [ondersteunde taal](../text-analytics-supported-languages.md)staan. |
|`language` | ISO [639-1-code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) met 2 tekens voor een [ondersteunde taal](../text-analytics-supported-languages.md) | Varieert | Vereist voor sentimentanalyse, sleutelzinextractie en entiteitskoppeling; optioneel voor taaldetectie. Er is geen fout als u het uitsluit, maar de analyse is verzwakt zonder. De taalcode moet `text` overeenkomen met de door u opte ere-in-staat. |

Zie [Overzicht van tekstanalyse > Gegevenslimieten](../overview.md#data-limits)voor meer informatie over limieten. 

## <a name="set-up-a-request-in-postman"></a>Een aanvraag instellen in Postbode

De service accepteert een aanvraag tot 1 MB groot. Als u Postman (of een ander web-API-testhulpmiddel) gebruikt, stelt u het eindpunt in om de bron op te nemen die u wilt gebruiken en geeft u de toegangssleutel op in een aanvraagkoptekst. Elke bewerking vereist dat u de juiste resource aan het eindpunt toevoegt. 

1. In Postbode:

   + Kies **Posten** als aanvraagtype.
   + Plak in het eindpunt dat u hebt gekopieerd van de portalpagina.
   + Een resource toevoegen.

   Resourceeindpunten zijn als volgt (uw regio kan variëren):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Stel de drie aanvraagkoppen in:

   + `Ocp-Apim-Subscription-Key`: uw toegangssleutel, verkregen uit Azure-portal.
   + `Content-Type`: toepassing/json.
   + `Accept`: toepassing/json.

   Uw aanvraag moet er vergelijkbaar uitzien met de volgende schermafbeelding, uitgaande van een **/keyPhrases-bron.**

   ![Schermafbeelding aanvragen met eindpunt en kopteksten](../media/postman-request-keyphrase-1.png)

4. Klik **op Hoofdtekst** en kies **raw** voor het formaat.

   ![Schermafbeelding aanvragen met hoofdinstellingen](../media/postman-request-body-raw.png)

5. Plak sommige JSON-documenten in een indeling die geldig is voor de beoogde analyse. Zie de onderstaande onderwerpen voor meer informatie over een bepaalde analyse:

  + [Taaldetectie](text-analytics-how-to-language-detection.md)  
  + [Trefwoordextractie](text-analytics-how-to-keyword-extraction.md)  
  + [Sentimentanalyse](text-analytics-how-to-sentiment-analysis.md)  
  + [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md)  


6. Klik **op Verzenden** om de aanvraag in te dienen. Zie de sectie [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over het aantal aanvragen dat u per minuut en seconde verzenden.

   In Postman wordt het antwoord weergegeven in het volgende venster naar beneden, als één JSON-document, met een item voor elke document-id in de aanvraag.

## <a name="see-also"></a>Zie ook 

 [Overzicht van Text Analytics](../overview.md)  
 [Veelgestelde vragen (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Taal detecteren](text-analytics-how-to-language-detection.md)
